

==================== PR #7734 [OPEN] feat(daemon): import enriched snapshots
https://github.com/ChainSafe/forest/pull/7734

-- REVIEW COMMENTED @ 2026-10-19T10:59:10Z
-- REVIEW COMMENTED @ 2026-10-19T11:12:15Z
-- REVIEW COMMENTED @ 2026-10-19T11:13:44Z
-- REVIEW COMMENTED @ 2026-10-19T11:18:26Z
-- REVIEW COMMENTED @ 2026-10-19T11:20:49Z
-- REVIEW COMMENTED @ 2026-10-19T11:22:31Z
-- REVIEW COMMENTED @ 2026-10-19T11:24:52Z
-- REVIEW COMMENTED @ 2026-10-19T11:26:04Z
-- INLINE id=4313021415 src/daemon/db_util.rs:1 @ 2026-10-19T10:59:10Z
```diff

```
>>> I don't think `db_util` is a good place for this; `*util` type of modules are supposed to be small utilities, not full features. It should likely be a separate file.
-- INLINE id=4313135332 src/daemon/db_util.rs:403 @ 2026-10-19T11:12:15Z
```diff
+) -> anyhow::Result<tempfile::TempPath> {
+    use sha2::{Digest as _, Sha256};
+
+    anyhow::ensure!(
+        !file.filename.is_empty() && !file.filename.contains(['/', '\\', '?', '#']),
+        "invalid extended snapshot filename: {}",
+        file.filename
+    );
```
>>> Kind of a weird assertion; can we just check if it matches a simple regular expression?
-- INLINE id=4313148544 src/daemon/db_util.rs:409 @ 2026-10-19T11:13:44Z
```diff
+        file.filename
+    );
+    let url = snapshot_url.join(&file.filename)?;
+    let path = new_forest_car_temp_path_in(temp_dir)?;
+    info!("Downloading extended snapshot: {url}");
+    download_to(&url, &path, DownloadFileOption::Resumable, None).await?;
+
+    let actual = crate::utils::encoding::hex::encode(Sha256::digest(tokio::fs::read(&path).await?));
```
>>> I'm going to bring it up once again. 

> Cryptopals Rule
Always operate on raw bytes, never on encoded strings. Only use hex and base64 for pretty-printing.
-- INLINE id=4313186138 src/daemon/db_util.rs:384 @ 2026-10-19T11:18:26Z
```diff
+        debug!("snapshot metadata not found at {metadata_url}");
+        return Ok(None);
+    }
+    let metadata: SnapshotMetadataFile = response
+        .error_for_status()?
+        .json()
+        .await
+        .with_context(|| format!("failed to parse snapshot metadata at {metadata_url}"))?;
```
>>> I don't exactly understand why you'd handle 404 in special way. If the metadata can't be downloaded for any reason, then it should just fail, regardless if it's `not found` or `i'm a teapot`.
-- INLINE id=4313206351 src/daemon/db_util.rs:421 @ 2026-10-19T11:20:49Z
```diff
+        file.sha256
+    );
+    Ok(path)
+}
+
+/// Imports message receipts and events from a receipts and events snapshot into `db`,
+/// committing [`EXTENDED_SNAPSHOT_IMPORT_BATCH_SIZE`] blocks at a time.
+async fn import_receipts_events_snapshot<DB>(db: &Arc<DB>, path: &Path) -> anyhow::Result<usize>
```
>>> Is the performance gain relevant enough to make up for a complex logic? If we'd reuse what we already have for importing block data without fancy batching, what'd be the total ingestion time difference?
-- INLINE id=4313220199 src/daemon/db_util.rs:478 @ 2026-10-19T11:22:31Z
```diff
+
+    let db = db.clone();
+    tokio::task::spawn_blocking(move || {
+        let hamt: Hamt<_, TipsetKey, ChainEpoch> =
+            Hamt::load_with_bit_width(&hamt_root, &store, TIPSET_LOOKUP_HAMT_BIT_WIDTH)
+                .context("failed to load tipset lookup HAMT")?;
+        // Validate all entries before writing any of them.
+        let mut entries = vec![];
```
>>> Do we have a rough idea of the collection size ahead of time?
-- INLINE id=4313239570 src/db/mod.rs:128 @ 2026-10-19T11:24:52Z
```diff
@@ -123,6 +123,18 @@ pub trait EthMappingsStore {
     fn set_tipset_key_at_epoch_raw(&self, epoch: ChainEpoch, tsk: &TipsetKey)
     -> anyhow::Result<()>;
 
+    /// Writes the tipset keys for the given epochs(heights) to the store. Implementations
+    /// should commit them in a single batch.
+    fn set_tipset_keys_at_epochs_raw(
```
>>> It's kind of weird this is a part of this trait, but it's outside of the scope of this PR to redesign it.
-- INLINE id=4313249257 src/daemon/mod.rs:161 @ 2026-10-19T11:26:04Z
```diff
@@ -155,6 +158,15 @@ async fn maybe_import_snapshot(
         .await?;
         ctx.db
             .read_only_files(std::iter::once(car_db_path.clone()))?;
+        if let Ok(snapshot_url) = url::Url::parse(&path.display().to_string()) {
```
>>> Seems off to use the `Display` result to put into a `Url`. Is this the best approach?


==================== PR #7705 [OPEN] chore(tests): serve snapshot-test drand rounds from FakeDrandServer
https://github.com/ChainSafe/forest/pull/7705

-- REVIEW CHANGES_REQUESTED @ 2026-10-05T08:54:35Z
-- REVIEW CHANGES_REQUESTED @ 2026-10-05T15:23:06Z
-- INLINE id=4182207860 src/beacon/tests/fake_drand_server.rs:84 @ 2026-10-05T08:49:11Z
```diff
@@ -81,6 +81,18 @@ impl FakeDrandChain {
                     signature: "b9e7e1e3d7d9cf17a9f4703abfae4c137acfbef1fdb45715a98422c244a499ea381c7fd759851ed8eeb8a03d778959b3".into(),
                     previous_signature: None,
                 },
+                BeaconEntryJson {
```
>>> why did you have to add those?
-- INLINE id=4182248597 src/beacon/tests/drand.rs:27 @ 2026-10-05T08:54:31Z
```diff
 use std::time::Duration;
 
 /// Serves recorded `quicknet` responses, so no test below reaches a public relay.
-static FAKE_DRAND: LazyLock<FakeDrandServer> =
+pub static FAKE_DRAND: LazyLock<FakeDrandServer> =
     LazyLock::new(|| FakeDrandServer::start(vec![FakeDrandChain::quicknet()]));
 
+/// Points the node's `quicknet` config at [`FAKE_DRAND`] through `FOREST_DRAND_QUICKNET_CONFIG`.
```
>>> As CodeRabbit also noted, this is a brittle design relying on an environmental variable and its unsafe modification (although behind a lazylock so it should be fine). Surely we can do a more robust approach without parsing JSONs. Do you have other ideas?
-- INLINE id=4185680634 (reply to 4182207860) src/beacon/tests/fake_drand_server.rs:84 @ 2026-10-05T15:23:02Z
```diff
@@ -81,6 +81,18 @@ impl FakeDrandChain {
                     signature: "b9e7e1e3d7d9cf17a9f4703abfae4c137acfbef1fdb45715a98422c244a499ea381c7fd759851ed8eeb8a03d778959b3".into(),
                     previous_signature: None,
                 },
+                BeaconEntryJson {
```
>>> In this case you should comment it - which entry is needed by which test. Right now it seems like a bunch of unrelated beacon entries. 

Unfortunately this makes RPC snapshots brittle and against the way they were supposed to work. RPC snapshots are supposed to be self-contained, with DB entries, index and so on. It seems you are introducing something that makes them highly coupled with some random util in `src/beacon/tests/fake_drand_server.rs`; now if I generate a new RPC snapshot that requires a specific beacon entry, it will fail miserably with some cryptic message (most likely). Then I would have to figure it out and hardcode it here. Afterwards, if the snapshot gets removed or changed, it's likely the entries here will become stale.

The way I see it,`struct RpcTestSnapshot` would have to be enriched by (optional) beacon entries. Let me know if it's unclear.


==================== PR #7683 [MERGED] chore(beacon/tests): setup `MockDrandServer`
https://github.com/ChainSafe/forest/pull/7683

-- REVIEW CHANGES_REQUESTED @ 2026-09-30T08:08:54Z
-- REVIEW COMMENTED @ 2026-09-30T16:14:01Z
-- REVIEW COMMENTED @ 2026-10-01T10:54:02Z
-- REVIEW COMMENTED @ 2026-10-01T12:31:57Z
-- REVIEW COMMENTED @ 2026-10-01T12:32:05Z
-- REVIEW COMMENTED @ 2026-10-01T12:33:25Z
-- REVIEW COMMENTED @ 2026-10-01T12:34:01Z
-- REVIEW COMMENTED @ 2026-10-01T12:34:32Z
-- REVIEW COMMENTED @ 2026-10-01T12:35:11Z
-- REVIEW COMMENTED @ 2026-10-01T12:36:29Z
-- REVIEW APPROVED @ 2026-10-02T08:00:49Z
-- INLINE id=4142300597 src/beacon/tests/fixtures/drand_mainnet.json:1 @ 2026-09-30T08:08:51Z
```diff

```
>>> IMO it's an overkill for a fake drand implementation; these should just be hardcoded. It's not like anyone's going to modify them, re-use them or read them without basic programming syntax knowledge.
-- INLINE id=4146773322 src/beacon/tests/fake_drand_server.rs:43 @ 2026-09-30T16:14:01Z
```diff
+                    "8990e7a9aaed2ffed73dbd7092123d6f289930540d7651336225dc172e51b2ce",
+                ),
+                group_hash: Cow::Borrowed(
+                    "176f93498eac9ca337150b46d21dd58673ea4e3581185f869672e59fa4cb390a",
+                ),
+            },
+            entries: vec![
+                json!({ "round": 1, "randomness": "101297f1ca7dc44ef6088d94ad5fb7ba03455dc33d53ddb412bbc4564ed986ec", "signature": "8d61d9100567de44682506aea1a7a6fa6e5491cd27a0a0ed349ef6910ac5ac20ff7bc3e09d7c046566c9f7f3c6f3b10104990e7cb424998203d8f7de586fb7fa5f60045417a432684f85093b06ca91c769f0e7ca19268375e659c2a2352b4655" }),
```
>>> I'm lost on the need for JSON and parsing JSON here. If there's a compelling reason for it, please elaborate.
-- INLINE id=4154585556 src/beacon/tests/drand.rs:23 @ 2026-10-01T10:54:03Z
```diff
@@ -18,19 +19,18 @@ use std::borrow::Cow;
 use std::sync::LazyLock;
 use std::time::Duration;
 
+/// Serves recorded `mainnet` and `quicknet` responses, so no test below reaches a public
+/// relay. Chain info and signatures are recorded from the real networks, so verification is real.
```
>>> ```suggestion
/// relay.
```

If those are are recorded then they are obviously real.
-- INLINE id=4155363682 src/beacon/drand.rs:240 @ 2026-10-01T12:31:57Z
```diff
 /// JSON beacon entry format. This matches the `drand` round JSON serialization
 /// API reference: <https://drand.love/developer/http-api/#public-round>.
 pub struct BeaconEntryJson {
-    round: u64,
-    randomness: String,
-    signature: String,
-    previous_signature: Option<String>,
+    pub(crate) round: u64,
```
>>> ```suggestion
    pub(in crate::beacon) round: u64,
```
-- INLINE id=4155364826 (reply to 4155363682) src/beacon/drand.rs:240 @ 2026-10-01T12:32:05Z
```diff
 /// JSON beacon entry format. This matches the `drand` round JSON serialization
 /// API reference: <https://drand.love/developer/http-api/#public-round>.
 pub struct BeaconEntryJson {
-    round: u64,
-    randomness: String,
-    signature: String,
-    previous_signature: Option<String>,
+    pub(crate) round: u64,
```
>>> same for others
-- INLINE id=4155377376 src/beacon/tests/fake_drand_server.rs:52 @ 2026-10-01T12:33:25Z
```diff
+                    signature: "8d61d9100567de44682506aea1a7a6fa6e5491cd27a0a0ed349ef6910ac5ac20ff7bc3e09d7c046566c9f7f3c6f3b10104990e7cb424998203d8f7de586fb7fa5f60045417a432684f85093b06ca91c769f0e7ca19268375e659c2a2352b4655".into(),
+                    previous_signature: None,
+                },
+                BeaconEntryJson {
+                    round: 2,
+                    randomness: "e8fee7dac6eb2b89df97d631cfccedbada7d5d05495bb546eef462e4145fdf8f".into(),
+                    signature: "aa18facd2d51b616511d542de6f9af8a3b920121401dad1434ed1db4a565f10e04fad8d9b2b4e3e0094364374caafe9b10478bf75650124831509c638b5a36a7a232ec70289f8751a2adb47fc32eb70b57dc81c39d48cbcac9fec46cdfc31663".into(),
+                    previous_signature: None,
```
>>> is this correct? If it's a recorded traffic and mainnet, then I'd assume there should be a previous signature due to it being chained, or not?
-- INLINE id=4155383062 src/beacon/tests/fake_drand_server.rs:291 @ 2026-10-01T12:34:01Z
```diff
+            .send()
+            .await
+            .unwrap()
+    }
+
+    #[tokio::test]
+    async fn serves_chain_info() {
+        let server: FakeDrandServer = start();
```
>>> ```suggestion
        let server = start();
```
-- INLINE id=4155387429 src/beacon/tests/fake_drand_server.rs:274 @ 2026-10-01T12:34:32Z
```diff
+            round,
+            randomness: format!("aa{round:02x}"),
+            signature: format!("bb{round:02x}"),
+            previous_signature: None,
+        }
+    }
+
+    fn start() -> FakeDrandServer {
```
>>> ```suggestion
    fn start_drand_server() -> FakeDrandServer {
```
-- INLINE id=4155392905 src/beacon/tests/fake_drand_server.rs:180 @ 2026-10-01T12:35:12Z
```diff
+        Some(entry) => Json(entry.clone()).into_response(),
+        None => StatusCode::NOT_FOUND.into_response(),
+    }
+}
+
+#[cfg(test)]
+mod tests {
+    use super::*;
```
>>> some duplicate imports from parent
-- INLINE id=4155403919 src/beacon/tests/fake_drand_server.rs:223 @ 2026-10-01T12:36:29Z
```diff
+
+    pub fn url(&self) -> &Url {
+        &self.url
+    }
+}
+
+impl Drop for FakeDrandServer {
+    fn drop(&mut self) {
```
>>> when would this run, realistically? not a huge deal, though


==================== PR #7636 [MERGED] fix(state_manager): pick the beacon entry algorithm by network version
https://github.com/ChainSafe/forest/pull/7636

-- REVIEW COMMENTED @ 2026-09-18T10:08:56Z
-- REVIEW COMMENTED @ 2026-09-18T10:13:01Z
-- REVIEW COMMENTED @ 2026-09-28T10:06:53Z
-- REVIEW COMMENTED @ 2026-09-28T10:10:53Z
-- REVIEW COMMENTED @ 2026-09-28T10:15:26Z
-- REVIEW CHANGES_REQUESTED @ 2026-09-29T09:19:52Z
-- REVIEW APPROVED @ 2026-09-29T14:47:41Z
-- CONVERSATION @ 2026-09-18T15:10:19Z
no green checkmark!
-- CONVERSATION @ 2026-09-18T15:25:01Z
@EclesioMeloJunior Did you run it through some Claude review? I glimpsed locally and it complained about quite a bit of correctness and coverage gaps.
-- INLINE id=4045783910 src/state_manager/chain_rand.rs:115 @ 2026-09-18T10:08:56Z
```diff
+        let network_version = self.chain_config.network_version(epoch);
+        if network_version >= NetworkVersion::V14 {
+            self.beacon_entry_for_epoch_v3(epoch, network_version)
+        } else if network_version == NetworkVersion::V13 {
+            self.latest_beacon_entry_for_epoch(epoch, false)
+        } else {
+            self.latest_beacon_entry_for_epoch(epoch, true)
+        }
```
>>> ```suggestion
        } else {
            let lookback = network_version != NetworkVersion::V13;
            self.latest_beacon_entry_for_epoch(epoch, lookback)
        }
```
this should be marginally faster (or not) but most importantly it doesn't confuse the reader what are those `false` and `true` params passed to `latest_beacon_entry_for_epoch`
-- INLINE id=4045810638 src/state_manager/chain_rand.rs:133 @ 2026-09-18T10:13:01Z
```diff
-    pub fn extract_beacon_entry_for_epoch(&self, epoch: ChainEpoch) -> anyhow::Result<BeaconEntry> {
+    fn beacon_entry_for_epoch_v3(
+        &self,
+        epoch: ChainEpoch,
+        network_version: NetworkVersion,
+    ) -> anyhow::Result<BeaconEntry> {
+        if epoch < 0 {
+            return self.latest_beacon_entry_for_epoch(epoch, false);
```
>>> let's make a variable `let lookback = false`, this is very confusing to read (I understand it's pre-existing but let's make it better). Alternatively, this entire method shouldn't accept a boolean but an enum so that you'd read `self.latest_beacon_entry_for_epoch(epoch, Lookback::Enabled)` or something like this.

In general, bool params to methods are an indication of a code smell.
-- INLINE id=4120916078 src/state_manager/chain_rand.rs:111 @ 2026-09-28T10:06:53Z
```diff
 
-        let beacon_entry = self.extract_beacon_entry_for_epoch(round)?;
-        Ok(digest(beacon_entry.signature()))
-    }
+        let mut resolve = ResolveNullTipset::TakeNewer;
+        if network_version < NetworkVersion::V13 {
+            resolve = ResolveNullTipset::TakeOlder
+        }
```
>>> ```suggestion
      let resolve = if network_version < NetworkVersion::V13 { TakeOlder } else { TakeNewer };
```
-- INLINE id=4120946907 src/state_manager/chain_rand.rs:117 @ 2026-09-28T10:10:53Z
```diff
+        self.latest_beacon_entry_for_epoch(epoch, resolve)
     }
 
-    /// Gets 32 bytes of randomness for `ChainRand` parameterized by the
-    /// `DomainSeparationTag`, `ChainEpoch`, Entropy from the latest beacon
-    /// entry.
-    pub fn get_beacon_randomness_blocking(
+    fn latest_beacon_entry_for_epoch(
```
>>> the naming is a bit awkward, `latest_beacon_entry_for_epoch` vs `beacon_entry_for_epoch_v3`. Seems like a better naming would be something along the lines of `beacon_entry_for_epoch_legacy` and `beacon_entry_for_epoch_exact` so that the reader has a mild hint what to use.

edit:

Or maybe not `_legacy`, I see it's used in `beacon_entry_for_epoch_v3`. In any case, some better naming would be helpful; perhaps just rename `beacon_entry_for_epoch_v3` to `..._post_nv14`
-- INLINE id=4120984578 src/state_manager/chain_rand.rs:125 @ 2026-09-28T10:15:26Z
```diff
+    ) -> anyhow::Result<BeaconEntry> {
+        let rand_ts = self.get_beacon_randomness_tipset_blocking(epoch, resolve)?;
+        Ok(self.chain_index.latest_beacon_entry(rand_ts)?)
     }
 
-    pub fn extract_beacon_entry_for_epoch(&self, epoch: ChainEpoch) -> anyhow::Result<BeaconEntry> {
-        let mut rand_ts: Tipset = self.get_beacon_randomness_tipset_blocking(epoch, false)?;
+    fn beacon_entry_for_epoch_v3(
```
>>> Some docs would be helpful, especially that it seems that it can block.
-- INLINE id=4131719359 src/rpc/methods/state.rs:1854 @ 2026-09-29T09:19:20Z
```diff
@@ -1848,7 +1850,9 @@ impl RpcMethod<2> for StateGetRandomnessDigestFromTickets {
     ) -> Result<Self::Ok, ServerError> {
         let tipset = ctx.chain_store().load_required_tipset_or_heaviest(&tsk)?;
         let chain_rand = ctx.state_manager.chain_rand(tipset);
-        let digest = chain_rand.get_chain_randomness(rand_epoch, false).await?;
+        let digest = chain_rand
+            .get_chain_randomness(rand_epoch, ResolveNullTipset::TakeNewer)
```
>>> Is this logic correct for mainnet? As in, there's a separate handling based on network version in `chain_rand.rs`. Would calling this (and some other methods here) with some old tipset on mainnet, pre-NV14 (so not calibnet) produce the same result in both Forest & Lotus?


==================== PR #7621 [MERGED] fix(ci): test a published dataset day in the external RPC checks
https://github.com/ChainSafe/forest/pull/7621

-- REVIEW COMMENTED @ 2026-09-15T08:14:02Z
-- REVIEW CHANGES_REQUESTED @ 2026-09-15T11:31:42Z
As discussed, a more robust change would be to check if the data from yesterday is available. If not, try the one from two days ago. If there's none, there's an issue with the dataset production and should be raised to the maintainer. This eliminates all timing assumptions and entire class of issues that come from it.

I think the cleanest solution would be to extend the dataset check image to probe for data for a given epoch, without doing an actual verification.
-- REVIEW COMMENTED @ 2026-09-16T15:59:34Z
-- REVIEW COMMENTED @ 2026-09-16T16:00:30Z
-- REVIEW COMMENTED @ 2026-09-16T16:11:43Z
-- REVIEW COMMENTED @ 2026-09-16T16:13:07Z
we're getting there!
-- REVIEW APPROVED @ 2026-09-17T10:08:31Z
-- INLINE id=4013464453 scripts/tests/external-rpc-checks/init.sh:12 @ 2026-09-15T08:14:02Z
```diff
@@ -9,7 +9,8 @@ apt-get update -qq
 apt-get install -y -qq --no-install-recommends curl jq
 
 # The dataset lags the chain, so yesterday's snapshot is the one to test against.
-day=$(date -u -d '1 day ago' +%F)
+days_ago=$(( $(date -u +%-H) < 13 ? 2 : 1 ))
```
>>> 13 is a magic number; I know where it comes from but it'd help the readers to know what it is.
-- INLINE id=4028075024 scripts/tests/external-rpc-checks/docker-compose.yaml:14 @ 2026-09-16T15:59:34Z
```diff
@@ -10,7 +10,8 @@ x-forest-service: &forest-service
   user: 0:0
 
 services:
-  # Resolves yesterday's snapshot, imports it and back-fills the chain index.
+  # Resolves the snapshot to test against, then imports it and back-fills the
+  # chain index (see init.sh).
```
>>> ```suggestion
```

It's some init stuff, if the reader is interested they can go to the file directly. A good way to avoid docs drift is to not overdo the docs 🧠
-- INLINE id=4028083451 scripts/tests/external-rpc-checks/docker-compose.yaml:63 @ 2026-09-16T16:00:30Z
```diff
@@ -59,7 +60,7 @@ services:
     environment:
       FOREST_RPC_URL: forest:${FOREST_RPC_PORT}/rpc/v1
 
-  # The image entrypoints into `bundle exec ruby check_rpc.rb`, so only the epochs are appended.
+  # The image entrypoints into `bundle exec ruby check_rpc.rb`
```
>>> ```suggestion
```
same as above, I think the user hardly cares that some bundler executes a ruby script
-- INLINE id=4028189655 scripts/tests/external-rpc-checks/init.sh:15 @ 2026-09-16T16:11:44Z
```diff
-apt-get install -y -qq --no-install-recommends curl jq
+# Records the URL and head epoch of the newest calibnet snapshot from DAYS_AGO
+# days ago. Snapshot names end in the epoch of their head tipset; ./setup.sh
+# reads it back to pick the range to check.
+resolve() {
+  # The Forest image ships neither curl nor jq.
+  apt-get update -qq
+  apt-get install -y -qq --no-install-recommends curl jq
```
>>> This is not an optimal design; such utils are meant to be cached so that we don't harass ubuntu/debian apt repositories. We can just have an image on top of the existing one.


==================== PR #7603 [MERGED] chore: enable mainnet on external-rpc-checks
https://github.com/ChainSafe/forest/pull/7603

-- REVIEW COMMENTED @ 2026-09-15T11:49:54Z
-- REVIEW COMMENTED @ 2026-09-15T11:53:22Z
-- CONVERSATION @ 2026-09-21T11:56:21Z
I'll take this over, there are a couple outstanding things that I'd like to be here.
-- CONVERSATION @ 2026-09-21T15:06:44Z
@akaladarshi @sudo-shashank I pushed a couple of fixes, please review.

Successful on-demand mainnet run https://github.com/ChainSafe/forest/actions/runs/35611543720/job/106374207553
-- CONVERSATION @ 2026-09-21T15:36:00Z
Not my fault all mirrors and repos we use are down. ;( I'll bump the CI tomorrow
-- CONVERSATION @ 2026-09-22T08:15:55Z
@akaladarshi green now!
-- INLINE id=4015230429 .github/workflows/external-rpc-checks.yml:39 @ 2026-09-15T11:49:55Z
```diff
@@ -26,10 +26,11 @@ jobs:
   build:
     name: Build Forest
     if: |-
-      ${{ github.event_name == 'workflow_dispatch'
+      ${{ inputs.forest_image == ''
```
>>> It's a pretty convoluted condition; let's comment it for those of us that don't have PhD in De Morghan laws.
-- INLINE id=4015259533 scripts/tests/external-rpc-checks/init.sh:8 @ 2026-09-15T11:53:22Z
```diff
@@ -4,27 +4,30 @@
 
 set -euo pipefail
 
+chain=${FOREST_CHAIN:?FOREST_CHAIN is not set}
+epochs=${EPOCHS:?EPOCHS is not set}
```
>>> It's a pretty obscure syntax of already obscure bash; let's just use `if -z` thing that most of us at least recognize. I appreciate the one-liners, though (just not in bash).


==================== PR #7578 [MERGED] chore(release): prepare 0.36.1
https://github.com/ChainSafe/forest/pull/7578

-- REVIEW CHANGES_REQUESTED @ 2026-09-01T13:18:32Z
no green checkmark, no release!
-- REVIEW COMMENTED @ 2026-09-01T16:52:08Z
-- REVIEW COMMENTED @ 2026-09-01T19:08:32Z
-- REVIEW APPROVED @ 2026-09-01T19:32:47Z
-- INLINE id=3906327725 CHANGELOG.md:39 @ 2026-09-01T16:43:36Z
```diff
+### Changed
+
+### Removed
+
+### Fixed
+
+## Forest v0.36.1 "Kaaguy"
+
```
>>> Missing short summary.
-- INLINE id=3907475334 CHANGELOG.md:40 @ 2026-09-01T19:08:32Z
```diff
+
+### Removed
+
+### Fixed
+
+## Forest v0.36.1 "Kaaguy"
+
+Non-mandatory release for all node operators. Mostly fixes and small improvements, with the message pool and the Ethereum RPC methods getting the most attention. It also continues the previous release's block-production work, so Forest can sit behind lotus-miner or curio.
```
>>> `Mostly` and `most` in the same sentence sound weird and repetitive. Also, block production was mentioned earlier, no need to celebrate the same thing too often (that said, it should've been Curio/Lotus miner sitting behind Forest and no the other way around).
```suggestion
Non-mandatory release for all node operators. Mostly fixes and improvements around RPC methods.
```


==================== PR #7577 [MERGED] chore(docs): include important warn on release checklist
https://github.com/ChainSafe/forest/pull/7577

-- REVIEW APPROVED @ 2026-09-01T13:16:53Z


==================== PR #7570 [MERGED] fix(ci): set the forest entrypoint in the external RPC checks
https://github.com/ChainSafe/forest/pull/7570

-- REVIEW APPROVED @ 2026-09-01T08:32:52Z


==================== PR #7569 [CLOSED] chore(release): prepare 0.37.0
https://github.com/ChainSafe/forest/pull/7569

-- REVIEW CHANGES_REQUESTED @ 2026-09-01T08:35:12Z
We are pre `1.y.z`, there are no breaking changes and we don't add support for anything major like a new network version. `0.36.1` is fine.


==================== PR #7544 [OPEN] feat(drand): fetch drand beacon entry from gossipsub
https://github.com/ChainSafe/forest/pull/7544

-- REVIEW COMMENTED @ 2026-08-31T10:55:17Z
-- REVIEW COMMENTED @ 2026-08-31T10:58:42Z
-- REVIEW COMMENTED @ 2026-08-31T10:59:39Z
-- REVIEW COMMENTED @ 2026-08-31T11:00:16Z
-- REVIEW COMMENTED @ 2026-09-01T10:13:19Z
-- REVIEW CHANGES_REQUESTED @ 2026-09-01T10:31:35Z
I'd love to see some empirical measurements, e.g., with wireshark on the number of calls to the drand relay against current `main`:
1. Under Forest-following the chain scenario on mainnet (there should be, ideally, zero calls). Please run the node for at least 12h and observe through logs/metrics/wireshark the behavior and report it.
2. Under Forest-block-producing node on devnet. I'd expect zero calls with your change and quite a bit of calls on `main`.
3. How many calls are made during tests (both `cargo test` and `cargo nextest`).

wireshark is the source of truth - our own metrics and logs might be like [Volkswagen reports](https://en.wikipedia.org/wiki/Volkswagen_emissions_scandal) so I don't trust them fully.

Also, are any new lines introduced missing coverage? If so, tests must be added.

You might want to use GH stacks to break up the change into more digestible chunks for reviewing, e.g., one for the gossipsub listener, another for cache integration etc.
-- REVIEW COMMENTED @ 2026-10-07T15:55:33Z
-- REVIEW COMMENTED @ 2026-10-07T15:58:25Z
-- REVIEW COMMENTED @ 2026-10-07T16:00:36Z
-- REVIEW COMMENTED @ 2026-10-07T16:01:25Z
-- REVIEW COMMENTED @ 2026-10-07T16:04:43Z
-- REVIEW COMMENTED @ 2026-10-09T11:38:20Z
-- REVIEW COMMENTED @ 2026-10-09T11:41:51Z
-- REVIEW COMMENTED @ 2026-10-09T11:44:09Z
-- REVIEW COMMENTED @ 2026-10-09T12:15:14Z
-- REVIEW COMMENTED @ 2026-10-09T12:20:05Z
-- CONVERSATION @ 2026-08-26T08:41:22Z
No green checkmark, no review!
-- CONVERSATION @ 2026-10-05T12:57:50Z
@EclesioMeloJunior If you could summarize what kind of tests and checks you did in the description, that'd be great and help pinpoint potential scenarios where an additional verification would be required.

E.g., have you verified via wireshark that Forest doesn't call drand relays on devnet (both Lotus-based and Forest-based), calibnet, mainnet etc.
-- INLINE id=3893809752 CHANGELOG.md:33 @ 2026-08-31T10:55:17Z
```diff
@@ -29,6 +29,8 @@
 
 ### Added
 
+- [#7414](https://github.com/ChainSafe/forest/issues/7414): Forest now subscribes to the drand gossipsub topic, verifying and caching the beacon entries it receives there. The drand HTTP endpoints are only queried when no entry has arrived for half a chain epoch.
+
```
>>> ```suggestion
```

This is an internal implementation detail; not really user-facing.
-- INLINE id=3893830222 proto/drand_pb.proto:6 @ 2026-08-31T10:58:42Z
```diff
@@ -0,0 +1,8 @@
+syntax = "proto3";
+
+package drand_pb;
+
+message PublicRandResponse {
```
>>> Worth linking (permalink) where'd you get that protobuf from.
-- INLINE id=3893835701 src/beacon/signatures/mod.rs:16 @ 2026-08-31T10:59:39Z
```diff
@@ -13,7 +13,7 @@ use rayon::prelude::*;
 pub use bls_signatures::{PublicKey as PublicKeyOnG1, Signature as SignatureOnG2};
 
 // See <https://www.ietf.org/archive/id/draft-irtf-cfrg-bls-signature-05.html#name-basic>
-const CSUITE_G1: &[u8] = b"BLS_SIG_BLS12381G1_XMD:SHA-256_SSWU_RO_NUL_";
+pub const CSUITE_G1: &[u8] = b"BLS_SIG_BLS12381G1_XMD:SHA-256_SSWU_RO_NUL_";
```
>>> I think the visibility could be somehow limited?
-- INLINE id=3893839184 src/beacon/tests/fake_drand.rs:37 @ 2026-08-31T11:00:16Z
```diff
+                    group_hash: "00".repeat(32).into(),
+                },
+                network_type: DrandNetwork::Quicknet, // unchained
+            },
+        }
+    }
+
+    // sing H(round) on G1, exactly what `verifies_entries` checks for unchained
```
>>> 🎵
-- INLINE id=3903071125 src/chain_sync/chain_follower.rs:320 @ 2026-09-01T10:13:19Z
```diff
@@ -309,13 +317,68 @@ async fn chain_follower(
                                 debug!("Received invalid GossipSub message: {}", why);
                             }
                         }
+                        PubsubMessage::DrandEntry(entry) => {
```
>>> This method is already bloated and way too big. This should be refactored.
-- INLINE id=3903097955 src/chain_sync/chain_follower.rs:574 @ 2026-09-01T10:16:48Z
```diff
+    {
+        let last_seen = last_drand_entry.load(Ordering::Relaxed);
+        let now = Utc::now().timestamp().max(0) as u64;
+        if last_seen != 0 && now.saturating_sub(last_seen) < deadline.as_secs() {
+            consecutive_misses = 0;
+            if stale {
+                stale = false;
+                info!("drand gossipsub entries are flowing again");
```
>>> Operators don't care about it.
-- INLINE id=3903105810 src/chain_sync/chain_follower.rs:583 @ 2026-09-01T10:17:54Z
```diff
+            continue;
+        }
+
+        if !stale {
+            stale = true;
+            warn!(
+                deadline_secs = deadline.as_secs(),
+                "no verified drand entry over gossipsub within the deadline, falling back to HTTP"
```
>>> Operators don't care about it, in case of a bad gossipsub performance, they'd get flooded with non-actionable warnings.
-- INLINE id=3903117542 src/chain_sync/chain_follower.rs:542 @ 2026-09-01T10:19:32Z
```diff
@@ -473,6 +536,91 @@ async fn chain_follower(
     Ok(())
 }
 
+/// Watch the `drand` `gossipsub` topic for staleness: if a `drand` beacon entry
+/// is not received in half a chain epoch then we consider it stale for
+/// that epoch and fall back to fetching the beacon over HTTP.
+async fn drand_gossip_watchdog(
```
>>> This seems untested and has some pretty non-trivial logic.
-- INLINE id=4209026801 scripts/devnet-forest-miner/.env:15 @ 2026-10-07T15:55:34Z
```diff
@@ -12,6 +12,8 @@ LOTUS_P2P_PORT=1235
 MINER_RPC_PORT=2345
 FOREST_RPC_PORT=3456
 FOREST_P2P_PORT=4903
+# drand quicknet gossipsub relays (space-separated)
```
>>> Let's have a short (really short) explanation why it's needed. It's obvious for us at the moment, but not obvious to other readers a year from now.
-- INLINE id=4209057840 src/libp2p/tests/drand_gossip_tests.rs:37 @ 2026-10-07T15:58:25Z
```diff
+    let drand = FakeDrand::new(vec![], FAKE_DRAND_PERIOD, FAKE_DRAND_GENESIS_TIME);
+
+    let beacon = drand.beacon(TEST_FIL_GENESIS_TIME, TEST_FIL_BLOCK_DELAY);
+    let hash = drand.chain_info_hash();
+
+    let topic = IdentTopic::new(format!("{PUBSUB_DRAND_STR}/{hash}"));
+
+    // `PubsubTopicCfg` borrows, so these have to outlive the swarm construction.
```
>>> ```suggestion
```

No need to put ballads of our epic battles with the borrow checker.
-- INLINE id=4209080710 src/libp2p/tests/drand_gossip_tests.rs:97 @ 2026-10-07T16:00:36Z
```diff
+    assert!(
+        beacon
+            .verify_entries(&received, &BeaconEntry::default())
+            .unwrap()
+    );
+
+    // verify every round is now served from cache.
+    for round in 1..=5u64 {
```
>>> do you really need to put the type explicitly?
-- INLINE id=4209089532 src/libp2p/tests/drand_gossip_tests.rs:66 @ 2026-10-07T16:01:25Z
```diff
+
+    relay.behaviour_mut().subscribe(&topic).unwrap();
+    node.behaviour_mut().subscribe(&topic).unwrap();
+
+    wait_until_meshed(&mut node, &mut relay, &topic).await;
+
+    let mut received = Vec::new();
+    for round in 1..=5u64 {
```
>>> you repeat `5` several times already; should be a constant (as in, just have `let test_rounds = 5;`)
-- INLINE id=4209122588 src/beacon/drand_pb.rs:1 @ 2026-10-07T16:04:43Z
```diff

```
>>> it'd be a nice to mark generated code as generated so it's obvious it shouldn't be reviewed in any way unless changes are expected, e.g., https://github.com/ChainSafe/forest/blob/main/src/rpc/snapshots/.gitattributes
-- INLINE id=4229683714 CHANGELOG.md:1 @ 2026-10-09T11:38:20Z
```diff

```
>>> Incorrect.
-- INLINE id=4229710084 src/networks/mod.rs:507 @ 2026-10-09T11:41:51Z
```diff
+        }
+    }
 
+    pub fn drand_gossip_chain_hashes(&self) -> Vec<String> {
+        self.drand_points()
+            .filter(|p| p.config.network_type.is_unchained())
+            .take(1)
+            .map(|p| p.config.chain_info.hash.to_string())
```
>>> > Always operate on raw bytes, never on encoded strings. Only use hex and base64 for pretty-printing.

https://cryptopals.com/sets/1/challenges/1

Unless you're saving a lot of CPU, I recommend keeping it with its proper type until really needed for the string representation.
-- INLINE id=4229728007 src/libp2p/gossip_params.rs:117 @ 2026-10-09T11:44:09Z
```diff
+
+pub(in crate::libp2p) fn build_peer_score_params(cfg: PubsubTopicCfg<'_>) -> PeerScoreParams {
     #[allow(clippy::disallowed_types)]
     let mut psp_topics = std::collections::HashMap::new();
 
-    for topic in PubsubTopic::iter() {
-        let params = match topic {
+    for (variant, topic) in pubsub_topics(cfg) {
```
>>> Why do we need this config now and didn't need it before?
-- INLINE id=4229972685 scripts/devnet-forest-miner/.env:16 @ 2026-10-09T12:15:15Z
```diff
@@ -12,6 +12,9 @@ LOTUS_P2P_PORT=1235
 MINER_RPC_PORT=2345
 FOREST_RPC_PORT=3456
 FOREST_P2P_PORT=4903
+# drand relays (space-separated) Forest keeps connected so beacon entries arrive over
+# gossipsub instead of HTTP. Each `/dnsaddr/` name resolves to the relays behind it.
```
>>> ```suggestion
# gossipsub instead of HTTP.
```

No need to explain what is `dnsaddr`.
-- INLINE id=4230016807 src/libp2p/tests/gossipsub_filter_test.rs:24 @ 2026-10-09T12:20:05Z
```diff
+use crate::libp2p::{
+    Gossipsub, PubsubTopicCfg, build_gossipsub, build_subscription_filter, pubsub_topics,
+};
+use crate::networks::GenesisNetworkName;
 
 const NETWORK: &str = "testnetname";
+/// quicknet, the one unchained drand network Forest subscribes to.
+const DRAND_HASH: &str = "52db9ba70e0cc0f6eaf7803dd07447a1f5477735fd3f661792ba94600c84e971";
```
>>> what's the point of declaring a constant globally in a module and then using it in a single place? Why not use it directly?


==================== PR #7533 [MERGED] feat(drand): include drand metrics around HTTP retrieval
https://github.com/ChainSafe/forest/pull/7533

-- REVIEW COMMENTED @ 2026-08-24T09:25:59Z
-- REVIEW COMMENTED @ 2026-08-25T08:49:50Z
-- REVIEW COMMENTED @ 2026-08-26T08:36:30Z
-- REVIEW COMMENTED @ 2026-08-27T08:44:36Z
-- REVIEW APPROVED @ 2026-08-27T12:29:59Z
-- INLINE id=3842183324 src/beacon/metrics.rs:1 @ 2026-08-24T09:25:59Z
```diff

```
>>> My point is kind of similar to the one in https://github.com/ChainSafe/forest/pull/7532/changes#r3842118238

We must not bloat the metrics. I see value only in the number of HTTP calls given we want to limit them. Errors, duration... this is somehow a debug information.
-- INLINE id=3851160026 src/beacon/tests/drand.rs:315 @ 2026-08-25T08:49:50Z
```diff
         );
     }
 }
+
+/// The counter tracks rounds that had to leave the process: a cache miss increments it,
+/// a hit does not. Compared as a delta so the test does not depend on what else ran.
+#[tokio::test]
+async fn http_fetch_metric_counts_only_cache_misses() {
```
>>> Is there a data race in this test?
-- INLINE id=3861005667 src/beacon/drand.rs:430 @ 2026-08-26T08:36:30Z
```diff
@@ -426,6 +427,7 @@ impl Beacon for DrandBeacon {
             entry.round()
         );
         self.cache_fetched_entry(&entry);
+        metrics::DRAND_HTTP_FETCH_TOTAL.inc();
```
>>> The placement of this metric is incorrect. Why?
-- INLINE id=3870127224 src/beacon/drand.rs:379 @ 2026-08-27T08:44:36Z
```diff
@@ -374,6 +375,9 @@ impl Beacon for DrandBeacon {
             return Ok(Arc::unwrap_or_clone(cached_entry));
         }
 
+        // counting when cache miss and needed to go fetch via HTTP
+        metrics::DRAND_HTTP_FETCH_TOTAL.inc();
```
>>> The placement of this metric is not ideal; it's correct-ish (it'd still miss hits in case of, e.g., retries due to bad signature) but only if you analyze the logic in the following lines. This wouldn't really be need if you'd put right next to the place where the fetch is actually happening; right now below line 390.


==================== PR #7532 [MERGED] fix:(drand): `SizeTrackingCache` cache name per drand network name
https://github.com/ChainSafe/forest/pull/7532

-- REVIEW COMMENTED @ 2026-08-24T09:16:30Z
-- REVIEW COMMENTED @ 2026-08-25T08:57:25Z
What happens if someone turns on caching for another drand network variant?
-- REVIEW COMMENTED @ 2026-08-26T08:30:18Z
-- REVIEW COMMENTED @ 2026-08-26T08:30:44Z
-- REVIEW COMMENTED @ 2026-08-26T08:32:14Z
-- REVIEW COMMENTED @ 2026-08-27T08:23:10Z
-- REVIEW COMMENTED @ 2026-08-27T08:27:32Z
-- REVIEW APPROVED @ 2026-08-27T15:04:11Z
-- INLINE id=3842118238 src/beacon/drand.rs:275 @ 2026-08-24T09:16:30Z
```diff
@@ -271,7 +272,10 @@ impl DrandBeacon {
             drand_gen_time: config.chain_info.genesis_time as u64,
             fil_round_time: interval,
             fil_gen_time: genesis_ts,
-            verified_beacons: SizeTrackingCache::new_with_metrics("verified_beacons", CACHE_SIZE),
+            verified_beacons: SizeTrackingCache::new_with_metrics(
```
>>> While it's technically correct, it's kind of not pragmatic. Incentinet and mainnet are a thing of the past and we should expect it to always show zero, with the exception of someone serving some really old archival data.

Prometheus metrics are scraped every X seconds, then persisted. We should keep only metrics useful to operators and ourselves.
-- INLINE id=3860961811 (reply to 3856259592) src/beacon/drand.rs:282 @ 2026-08-26T08:30:17Z
```diff
             fil_gen_time: genesis_ts,
-            verified_beacons: SizeTrackingCache::new_with_metrics("verified_beacons", CACHE_SIZE),
+            verified_beacons: config.use_cache.then(|| {
+                SizeTrackingCache::new_with_metrics(
+                    format!("verified_beacons_{}", config.network_type),
+                    CACHE_SIZE,
+                )
+            }),
```
>>> This wouldn't mitigate the issue in practice; metrics can be registered outside of this `SizeTrackingCache`, so a sanity check that we're not adding a duplicate metric should be on the highest level possible.
-- INLINE id=3860965019 (reply to 3856259592) src/beacon/drand.rs:282 @ 2026-08-26T08:30:44Z
```diff
             fil_gen_time: genesis_ts,
-            verified_beacons: SizeTrackingCache::new_with_metrics("verified_beacons", CACHE_SIZE),
+            verified_beacons: config.use_cache.then(|| {
+                SizeTrackingCache::new_with_metrics(
+                    format!("verified_beacons_{}", config.network_type),
+                    CACHE_SIZE,
+                )
+            }),
```
>>> But I'd say it's outside of the scope of this PR and should be done separately.
-- INLINE id=3860975357 src/beacon/drand.rs:259 @ 2026-08-26T08:32:14Z
```diff
@@ -252,8 +255,8 @@ pub struct DrandBeacon {
     fil_gen_time: u64,
     fil_round_time: u64,
 
-    /// Keeps track of verified beacon entries.
-    verified_beacons: SizeTrackingCache<u64, Arc<BeaconEntry>>,
+    /// Keeps track of verified beacon entries. `None` when `use_cache` is unset.
+    verified_beacons: Option<SizeTrackingCache<u64, Arc<BeaconEntry>>>,
```
>>> I think we kind of misunderstood each other. The cache should still be there, we just shouldn't care about metrics around it.
-- INLINE id=3869980046 src/beacon/drand.rs:274 @ 2026-08-27T08:23:11Z
```diff
@@ -271,7 +271,11 @@ impl DrandBeacon {
             drand_gen_time: config.chain_info.genesis_time as u64,
             fil_round_time: interval,
             fil_gen_time: genesis_ts,
-            verified_beacons: SizeTrackingCache::new_with_metrics("verified_beacons", CACHE_SIZE),
+            verified_beacons: if config.network_type.is_unchained() {
```
>>> This is still a brittle condition. The exactly same bug would be necromanced if tomorrow someone introduces `lightspeednet` that is also unchained by chance, e.g., `static LIGHTSPEEDNET: LazyLock<DrandBeacon> = LazyLock::new(new_beacon_lightspeednet);` - the metric would be duplicated.

IMO a design that wouldn't be brittle is:
1. an additional parameter to the c-tor, either a positional one or via the `DrandConfig` if its serialized form isn't somehow important. This forces the instance creator to decide whether the metrics should be enabled or not.
2. The `verified_beacons` cache should have the name in it. I guess it should be fine to put the `DrandNetwork` value there and not expect another network with exactly the same name to be created.

An extra step (outside of this PR) would be to add a global hashset of identifiers and check if a duplicate is not getting introduced; the memory overhead should be minimal but at least we could get warnings if something like this happens.
-- INLINE id=3870009773 CHANGELOG.md:42 @ 2026-08-27T08:27:32Z
```diff
@@ -39,6 +39,8 @@
 
 - [#7537](https://github.com/ChainSafe/forest/pull/7537): Self-destructed EVM contracts no longer report stale state in the trace RPCs. `Forest.EthTraceCall` (`trace_call`) `stateDiff` and `Forest.EthDebugTraceTransaction` (`debug_traceTransaction`) `prestateTracer` now report a zero nonce and empty storage for them, matching `Filecoin.EthGetTransactionCount`, `Filecoin.EthGetCode`, `Filecoin.EthGetStorageAt` and Lotus. Previously a contract that self-destructed during the traced message showed no storage change at all.
 
+- [#7414](https://github.com/ChainSafe/forest/issues/7414): Forest no longer exports duplicate `cache_verified_beacons_*` metric families. Every drand point in a network's schedule registered its own collector under that one name (three of them on mainnet), which most Prometheus parsers reject. Only the active drand beacon reports these metrics now; the historical ones still cache, they are just not observed.
```
>>> ```suggestion
- [#7414](https://github.com/ChainSafe/forest/issues/7414): Forest no longer exports duplicate `cache_verified_beacons_*` metric families.
```

Let's skip the narrative. The first part is questionable (I don't recall scrapers rejecting our metrics, at most silently ignoring some of them) . The second is an internal detail.


==================== PR #7509 [MERGED] feat: external-rpc-checks runs on dispatch and on labeled PRs
https://github.com/ChainSafe/forest/pull/7509

-- REVIEW COMMENTED @ 2026-08-19T09:07:41Z
-- REVIEW COMMENTED @ 2026-08-21T08:24:43Z
-- REVIEW CHANGES_REQUESTED @ 2026-08-21T08:44:39Z
I don't quite get the dance around the platforms; this should be invisible. If there are some limitations, let's just publish a multi-arch image of the checker, it should be trivial and cheap to do (it's just a ruby script).
-- REVIEW COMMENTED @ 2026-08-24T15:48:19Z
-- REVIEW DISMISSED @ 2026-08-24T15:49:36Z
-- REVIEW COMMENTED @ 2026-08-25T08:35:27Z
-- REVIEW COMMENTED @ 2026-08-25T11:53:37Z
-- REVIEW COMMENTED @ 2026-08-26T08:39:30Z
-- REVIEW COMMENTED @ 2026-08-27T15:03:31Z
-- REVIEW COMMENTED @ 2026-08-31T08:04:11Z
-- REVIEW APPROVED @ 2026-08-31T12:23:52Z
-- CONVERSATION @ 2026-08-20T10:35:25Z
@EclesioMeloJunior Let's fix the conflicts first.
-- CONVERSATION @ 2026-08-24T09:39:12Z
@EclesioMeloJunior Did you address the Coderabbit comments? They might or might not be useful; if they're not, just mark them as resolved.
-- INLINE id=3811550815 .github/workflows/external-rpc-checks.yml:27 @ 2026-08-19T09:01:21Z
```diff
+  # scheduled runs skip this and test the published image instead
+  build:
+    name: Build Forest
+    if: |-
+      ${{ github.event_name == 'workflow_dispatch'
+      || (github.event_name == 'pull_request'
+      && (contains(github.event.pull_request.labels.*.name, 'RPC')
+      || contains(github.event.pull_request.labels.*.name, 'Snapshot')
```
>>> ```suggestion
```

I mentioned `Snapshot` but it was just an example. `Snapshot` label is meant for some changes that could potentially change the snapshot format or contents.
-- INLINE id=3811551649 .github/workflows/external-rpc-checks.yml:29 @ 2026-08-19T09:01:28Z
```diff
+    name: Build Forest
+    if: |-
+      ${{ github.event_name == 'workflow_dispatch'
+      || (github.event_name == 'pull_request'
+      && (contains(github.event.pull_request.labels.*.name, 'RPC')
+      || contains(github.event.pull_request.labels.*.name, 'Snapshot')
+      || contains(github.event.pull_request.labels.*.name, 'Release'))) }}
+    runs-on: ubuntu-24.04
```
>>> arm?
-- INLINE id=3828638356 scripts/tests/external-rpc-checks/docker-compose.yaml:53 @ 2026-08-21T08:24:43Z
```diff
@@ -49,6 +49,8 @@ services:
   # Probes the node for the epochs the checks are about to query.
   verify:
     image: ghcr.io/chainsafe/forest-rpc-checks:latest
+    # Published for amd64 only; emulated when the host is arm64.
+    platform: linux/amd64
```
>>> I don't quite get it, why is it needed?
-- INLINE id=3845111003 .github/workflows/external-rpc-checks.yml:14 @ 2026-08-24T15:48:19Z
```diff
+  group: ${{ github.workflow }}-${{ github.ref }}
+  cancel-in-progress: ${{ github.ref != 'refs/heads/main' }}
 
 on:
   workflow_dispatch:
+    inputs:
+      forest_image:
+        description: "Test this image instead of building one. Must be a `fat` image, so that the filecoin proof parameters are already present."
```
>>> Not really. If it's not the fat image the proofs will get downloaded from R2 our IPFS gateway. This just speeds up things.
-- INLINE id=3851007273 scripts/sanitize_image.rb:1 @ 2026-08-25T08:35:27Z
```diff

```
>>> Is this code really needed? There are exactly 4 invokers of the remote dispatch (us) and if the image pull fails we'll get exactly an error saying that the image is garbage (perhaps a bit later during the workflow but that's an unusual situation anyway).
-- INLINE id=3852677754 (reply to 3851007273) scripts/sanitize_image.rb:1 @ 2026-08-25T11:53:36Z
```diff

```
>>> Let's not take AI suggestions for granted, be it CodeRabbit or Claude.
-- INLINE id=3861027019 (reply to 3845111003) .github/workflows/external-rpc-checks.yml:14 @ 2026-08-26T08:39:30Z
```diff
+  group: ${{ github.workflow }}-${{ github.ref }}
+  cancel-in-progress: ${{ github.ref != 'refs/heads/main' }}
 
 on:
   workflow_dispatch:
+    inputs:
+      forest_image:
+        description: "Test this image instead of building one. Must be a `fat` image, so that the filecoin proof parameters are already present."
```
>>> How is this resolved?
-- INLINE id=3873001749 (reply to 3845111003) .github/workflows/external-rpc-checks.yml:14 @ 2026-08-27T15:03:31Z
```diff
+  group: ${{ github.workflow }}-${{ github.ref }}
+  cancel-in-progress: ${{ github.ref != 'refs/heads/main' }}
 
 on:
   workflow_dispatch:
+    inputs:
+      forest_image:
+        description: "Test this image instead of building one. Must be a `fat` image, so that the filecoin proof parameters are already present."
```
>>> The description is misleading. It doesn't have to be a `fat` image. I'd just omit the second sentence.
-- INLINE id=3892706994 .github/workflows/external-rpc-checks.yml:84 @ 2026-08-31T08:04:11Z
```diff
     name: RPC checks against the data.riba.plus dataset
-    runs-on: warp-ubuntu-2404-x64-8x
+    runs-on: warp-ubuntu-2404-arm64-8x
+    needs: build
+    # `build` only runs for dispatches and labelled pull requests; scheduled runs skip
+    # it and test `edge-fat`. Unlabelled pull requests do not run the checks at all.
+    if: |-
+      ${{ always()
```
>>> what's the point of `always()`?


==================== PR #7492 [MERGED] docs: document `FOREST_F3_SIDECAR_FFI_BUILD_OPT_OUT` in `env_variables.md`
https://github.com/ChainSafe/forest/pull/7492

-- REVIEW APPROVED @ 2026-08-17T08:39:44Z


==================== PR #7478 [MERGED] feat(ci): verifies that the committed Go FFI binding
https://github.com/ChainSafe/forest/pull/7478

-- REVIEW COMMENTED @ 2026-08-12T07:51:52Z
-- REVIEW CHANGES_REQUESTED @ 2026-08-12T07:59:34Z
-- REVIEW COMMENTED @ 2026-08-13T08:42:33Z
-- REVIEW COMMENTED @ 2026-08-13T08:45:42Z
-- REVIEW COMMENTED @ 2026-08-13T08:48:12Z
-- REVIEW COMMENTED @ 2026-08-13T08:49:56Z
-- REVIEW COMMENTED @ 2026-08-13T08:50:58Z
-- REVIEW COMMENTED @ 2026-08-17T08:58:53Z
-- REVIEW DISMISSED @ 2026-08-17T09:09:03Z
-- REVIEW APPROVED @ 2026-08-17T19:42:40Z
-- INLINE id=3764552679 .github/workflows/go-lint.yml:56 @ 2026-08-12T07:51:52Z
```diff
-              - 'interop-tests/src/tests/**'
+              - 'interop-tests/**'
   lint-go:
     name: Go lint checks
     needs: changes
     if: ${{ needs.changes.outputs.changesFound == 'true' || github.event_name == 'workflow_dispatch' }}
-    runs-on: ubuntu-slim
+    runs-on: ubuntu-24.04
```
>>> The house rule is to use the `arm` version unless not possible (cheaper & faster).
-- INLINE id=3764560746 .github/workflows/go-lint.yml:61 @ 2026-08-12T07:53:13Z
```diff
     if: ${{ needs.changes.outputs.changesFound == 'true' || github.event_name == 'workflow_dispatch' }}
-    runs-on: ubuntu-slim
+    runs-on: ubuntu-24.04
     steps:
       - uses: actions/checkout@v7
       - uses: jdx/mise-action@v4
+      - name: Apt Dependencies
+        run: sudo apt-get install -y libclang-dev # required by rust2go's bindgen
```
>>> best to wrap it with retry, see `rust-lint.yml`. Ubuntu apt repos are known to fail.
-- INLINE id=3764569965 interop-tests/build.rs:18 @ 2026-08-12T07:54:48Z
```diff
-        // })
-        .build();
+    
+    let mut builder = rust2go::Builder::default().with_go_src("./src/tests/go_app");
+
+    // the generated Go file has been committed to the git repository
+    // set the var to regenerate the file, CI sets this var to verify freshness.
+    if std::env::var_os("FOREST_REGENERATE_GO_FFI").is_some() {
```
>>> new flags should be added to env variable reference in docs.
-- INLINE id=3764583824 interop-tests/build.rs:1 @ 2026-08-12T07:56:59Z
```diff

```
>>> There's also the root repo's `build.rs` that has similar logic - what about it?
-- INLINE id=3773770671 CHANGELOG.md:43 @ 2026-08-13T08:42:33Z
```diff
@@ -39,6 +39,8 @@
 
 ### Changed
 
+- [#7467](https://github.com/ChainSafe/forest/issues/7467): CI now verifies that the committed Go FFI bindings `interop-tests/src/tests/go_app/ffi_gen.go` and `f3-sidecar/ffi_gen.go` stay in sync with their `go_ffi.rs` sources through the `FOREST_REGENERATE_GO_FFI` environment variable, included `mise run lint:go-ffi`.
+
```
>>> ```suggestion
```

https://github.com/ChainSafe/forest/blob/8cf2b87d8c5ad7b6d5c12b3d3f969edf6cfbf506/CHANGELOG.md?plain=1#L17-L18
-- INLINE id=3773791373 docs/docs/users/reference/env_variables.md:91 @ 2026-08-13T08:45:42Z
```diff
@@ -88,6 +88,12 @@ the binary.
 By default, the Go f3-sidecar is built and linked into Forest binary unless environment
 variable `FOREST_F3_SIDECAR_FFI_BUILD_OPT_OUT=1` is set.
 
+### `FOREST_REGENERATE_GO_FFI`
```
>>> This should go to the table above. The content below is supposed to expand info so that the description doesn't bloat the column. I see the previous entry also isn't in the table, worth putting it there in a separate PR.
-- INLINE id=3773808942 (reply to 3769129216) src/f3/go_ffi.rs:18 @ 2026-08-13T08:48:11Z
```diff
@@ -16,8 +14,8 @@ pub trait GoF3Node {
         jwt: String,
         f3_rpc_endpoint: String,
         initial_power_table: String,
-        bootstrap_epoch: ChainEpoch,
-        finality: ChainEpoch,
+        bootstrap_epoch: i64,
+        finality: i64,
```
>>> That's a pretty weird behavior; worth raising to `rust2go` maintainers as an issue.
-- INLINE id=3773823037 AGENTS.md:357 @ 2026-08-13T08:49:56Z
```diff
@@ -354,6 +354,7 @@ lldb target/debugging/forest
 - `RUST_LOG` - Logging configuration (e.g., `debug`, `forest=trace`)
 - `FULLNODE_API_INFO` - RPC endpoint and authentication token
 - `FOREST_F3_SIDECAR_FFI_BUILD_OPT_OUT` - Disable F3 sidecar build (for debugging profile)
+- `FOREST_REGENERATE_GO_FFI` - when set to `1` forces the regeneration of all Go FFI bindings based on their current Rust definitions, MUST USE when changing any `go_ffi.rs`
```
>>> Not sure it's worth bloating the context with this. The info is already in docs and we really rarely need to regenerate Go FFI. As in, after initial development, we never had to.
-- INLINE id=3773832504 build.rs:24 @ 2026-08-13T08:50:58Z
```diff
@@ -19,6 +19,16 @@ fn main() {
     // whitelist the cfg for cargo clippy
     println!("cargo::rustc-check-cfg=cfg(f3sidecar)");
 
+    println!("cargo:rerun-if-env-changed=FOREST_REGENERATE_GO_FFI");
+    if is_env_truthy("FOREST_REGENERATE_GO_FFI")
+        && (is_docs_rs() || is_env_truthy("FOREST_F3_SIDECAR_FFI_BUILD_OPT_OUT"))
```
>>> I don't quite get what docs have to do with this. Can you elaborate?
-- INLINE id=3795030355 docs/docs/users/reference/env_variables.md:82 @ 2026-08-17T08:58:53Z
```diff
@@ -79,6 +79,7 @@ process.
 | `FOREST_ETH_RPC_COMPUTE_BLOOM_ON_MISS`                           | 1 or true                        | false                                                      | 1                                                             | Allows `eth` block RPC methods to compute (and store) the block `logsBloom` when it is not already stored, otherwise such blocks report an all-ones bloom                                                                                                                      |
 | `FOREST_RPC_METRICS_DISABLED`                                    | 1 or true                        | false                                                      | 1                                                             | Disable per-method JSON-RPC metrics only, leaving the metrics endpoint and all other metrics (cache, sync, database, ...) intact. To turn off metrics entirely, disable the endpoint instead with `--no-metrics`                                                               |
 | `FOREST_STATE_COMPUTE_MAX_RANGE`                                 | positive integer                 | 2000                                                       | 100                                                           | The maximum `nEpochs` value `Forest.StateCompute` accepts                                                                                                                                                                                                                      |
+| `FOREST_REGENERATE_GO_FFI`                                       | 1 or true                        | empty                                                      | 1                                                             | Regenerate the committed Go FFI bindings from their `go_ffi.rs` sources. Only useful when building; the CI checks use it to verify the bindings are in sync                                                                                                                    |
```
>>> ```suggestion
| `FOREST_F3_REGENERATE_GO_FFI`                                       | 1 or true                        | empty                                                      | 1                                                             | Regenerate the committed Go FFI bindings from their `go_ffi.rs` sources. Only useful when building; the CI checks use it to verify the bindings are in sync                                                                                                                    |
```

In theory we might have more bindings, but I really hope we won't. I think it's worth renaming it across the board.


==================== PR #7477 [MERGED] feat(scripts): include `ribasushi-rpc-checks`
https://github.com/ChainSafe/forest/pull/7477

-- REVIEW CHANGES_REQUESTED @ 2026-08-12T07:49:31Z
Please read the linked issue. _Something_ is missing.
-- REVIEW CHANGES_REQUESTED @ 2026-08-13T07:58:43Z
-- REVIEW COMMENTED @ 2026-08-17T10:33:25Z
-- REVIEW COMMENTED @ 2026-08-18T09:39:55Z
Mostly nits, but we're getting there!
-- REVIEW COMMENTED @ 2026-08-18T13:40:35Z
-- REVIEW APPROVED @ 2026-08-18T19:34:44Z
LGTM, let's see if it works fine in practice.
-- INLINE id=3764459610 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:39 @ 2026-08-12T07:37:08Z
```diff
+        file=$$(basename "$$url")
+        head=$$(echo "$$file" | sed 's/.*_height_\([0-9]*\)\..*/\1/')
+        case "$$head" in ''|*[!0-9]*) echo "could not parse a height from '$$file'"; exit 1;; esac
+        [ "$$EPOCHS" -le "$$head" ] || { echo "EPOCHS ($$EPOCHS) exceeds head ($$head)"; exit 1; }
+
+        # only downloads if the snapshot is not present
+        [ -f "/snapshots/$$file" ] ||
+          curl -sSfL --connect-timeout 10 --retry 3 -o "/snapshots/$$file" "$$url"
```
>>> `cURL` is pretty bad at downloading large files such as snapshots - a better option would be to use `aria2c` with, e.g., `-x5` set.
-- INLINE id=3764463548 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:21 @ 2026-08-12T07:37:50Z
```diff
+    volumes:
+      - ./snapshots:/snapshots
+    entrypoint: ["/bin/sh", "-euc"]
+    command:
+      - |
+        apk add --no-cache curl jq >/dev/null
+
+        # validates DAYS_AGO and EPOCHS
```
>>> pretty complex on its own - best to put it into a dedicated bash script so that shellcheck linter can nicely check it. It's also pretty obscure unreadable bash  (not your fault, it's just bash) with regexes, so plenty of comments are needed to assert the logic is sound.
-- INLINE id=3764502145 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:27 @ 2026-08-12T07:43:53Z
```diff
+
+        # validates DAYS_AGO and EPOCHS
+        case "$$DAYS_AGO" in ''|*[!0-9]*) echo "DAYS_AGO must be a non-negative integer: '$$DAYS_AGO'"; exit 1;; esac
+        case "$$EPOCHS" in ''|*[!0-9]*) echo "EPOCHS must be a non-negative integer: '$$EPOCHS'"; exit 1;; esac
+        [ "$$EPOCHS" -ge 1 ] || { echo "EPOCHS must be at least 1"; exit 1; }
+
+        day=$$(date -u -d "@$$(( $$(date -u +%s) - DAYS_AGO * 86400 ))" +%F)
+        url=$$(curl -sSf --connect-timeout 10 --retry 3 \
```
>>> unless a flag is obvious, e.g., `rm -rf`, I recommend using full versions of the params, e.g., `--silent` instead of `-s`. Then a reader doesn't need a PhD in cURL to parse the file.
-- INLINE id=3764510002 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:48 @ 2026-08-12T07:45:09Z
```diff
+        # define env vars used later on the next services
+        printf 'SNAPSHOT=%s\nSTART=%s\nEND=%s\nBACKFILL=%s\n' \
+          "$$file" "$$((head - EPOCHS))" "$$((head - 1))" "$$((EPOCHS + 1))" > /snapshots/env
+        cat /snapshots/env
+
+  # The RPC port opens only after the backfill finishes, so "healthy" means ready.
+  forest:
+    image: ghcr.io/chainsafe/forest:latest
```
>>> Re-think your options. Hint: `latest` is a bad option - why? 

https://docs.forest.chainsafe.io/knowledge_base/docker_tips#tags
-- INLINE id=3764515707 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:62 @ 2026-08-12T07:46:06Z
```diff
+    ports:
+      - "2345:2345"
+    entrypoint: ["/bin/sh", "-euc"]
+    command:
+      - |
+        . /snapshots/env
+        exec forest-tool api serve "/snapshots/$$SNAPSHOT" --chain "$$CHAIN" \
+          --port 2345 --height 0 --index-backfill-epochs "$$BACKFILL"
```
>>> why `--height 0`?
-- INLINE id=3764519479 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:78 @ 2026-08-12T07:46:44Z
```diff
+    depends_on:
+      forest:
+        condition: service_healthy
+    environment:
+      FOREST_RPC_URL: forest:2345/rpc/v1
+    volumes:
+      - ./snapshots:/snapshots:ro
+    entrypoint: ["/bin/sh", "-euc"]
```
>>> can you just use the image directly?
-- INLINE id=3764527093 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:67 @ 2026-08-12T07:48:02Z
```diff
+        . /snapshots/env
+        exec forest-tool api serve "/snapshots/$$SNAPSHOT" --chain "$$CHAIN" \
+          --port 2345 --height 0 --index-backfill-epochs "$$BACKFILL"
+    healthcheck:
+      test: ["CMD", "forest-cli", "chain", "head"]
+      interval: 15s
+      timeout: 10s
+      retries: 480
```
>>> So... potentially 20h for the probe to fail?
-- INLINE id=3773334302 .github/RIBASUSHI_RPC_CHECKS_ISSUE_TEMPLATE.md:2 @ 2026-08-13T07:35:21Z
```diff
@@ -0,0 +1,8 @@
+---
+title: "[automated] Ribasushi RPC checks failure"
```
>>> I'd change the naming across to board to `data.riba.plus` dataset. Ribasushi is a person. :)
-- INLINE id=3773418072 (reply to 3764527093) scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:67 @ 2026-08-13T07:49:34Z
```diff
+        . /snapshots/env
+        exec forest-tool api serve "/snapshots/$$SNAPSHOT" --chain "$$CHAIN" \
+          --port 2345 --height 0 --index-backfill-epochs "$$BACKFILL"
+    healthcheck:
+      test: ["CMD", "forest-cli", "chain", "head"]
+      interval: 15s
+      timeout: 10s
+      retries: 480
```
>>> First thing - I don't see how this job can possibly be failing for so long and recover. After 2h the patient is likely dead. Another, this healthcheck is too simplistic; it checks that the node responds with something. We have better ones. https://docs.forest.chainsafe.io/guides/monitoring/health_checks. Also, refer to [api-compare compose](https://github.com/ChainSafe/forest/blob/main/scripts/tests/api_compare/docker-compose.yml) there are a lot of gotchas and best we don't resurrect old bugs. 

Third one, I'd rather we use regular forest daemon and not the offline version of it. It's what's actually is used in production.
-- INLINE id=3773420959 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:39 @ 2026-08-13T07:50:04Z
```diff
+    healthcheck:
+      test: ["CMD", "forest-cli", "chain", "head"]
+      interval: 15s
+      timeout: 10s
+      retries: 480
+
+  rpc-checks:
+    image: ghcr.io/chainsafe/forest-rpc-checks:2026-08-11-43c77da
```
>>> As mentioned on Slack, pinning is not a good idea for nightly checks.
-- INLINE id=3773425929 .github/workflows/external-rpc-checks.yml:18 @ 2026-08-13T07:50:51Z
```diff
+  schedule:
+    - cron: "0 13 * * *" # Runs every day at 13:00 UTC
+
+jobs:
+  rpc-checks:
+    name: RPC checks against the Ribasushi dataset
+    runs-on: warp-ubuntu-2404-x64-8x
+    timeout-minutes: 180
```
>>> why would it take so long? how long does this take normally?
-- INLINE id=3773433253 .github/workflows/external-rpc-checks.yml:27 @ 2026-08-13T07:52:03Z
```diff
+      run:
+        working-directory: scripts/tests/ribasushi-rpc-checks
+    steps:
+      - name: Checkout Sources
+        uses: actions/checkout@v7
+      - name: Free up disk space
+        working-directory: .
+        run: ./scripts/optimize_disk_space_gh_worker.sh
```
>>> why?
-- INLINE id=3773434256 .github/workflows/external-rpc-checks.yml:28 @ 2026-08-13T07:52:13Z
```diff
+        working-directory: scripts/tests/ribasushi-rpc-checks
+    steps:
+      - name: Checkout Sources
+        uses: actions/checkout@v7
+      - name: Free up disk space
+        working-directory: .
+        run: ./scripts/optimize_disk_space_gh_worker.sh
+      - name: Install aria2
```
>>> retry
-- INLINE id=3773442495 .github/workflows/external-rpc-checks.yml:34 @ 2026-08-13T07:53:35Z
```diff
+        run: ./scripts/optimize_disk_space_gh_worker.sh
+      - name: Install aria2
+        run: sudo apt-get install -y aria2
+      - name: Prepare the snapshot and pin the images
+        run: |
+          # Images are tagged with their build date and the short commit hash.
+          tag="$(TZ=UTC git show -s --format=%cd --date=format-local:%Y-%m-%d HEAD)-$(git rev-parse --short=7 HEAD)"
+          echo "Testing ghcr.io/chainsafe/forest:$tag"
```
>>> Imagine you merged something at 1 PM. The docker image has not yet been published. What happens?
-- INLINE id=3773459920 scripts/tests/ribasushi-rpc-checks/docker-compose.yaml:8 @ 2026-08-13T07:56:28Z
```diff
+name: rpc-checks
+
+# WARNING: this file is not usable on its own. `./prepare.sh` downloads the
+# snapshot and writes the `.env` file every `${...}` below is interpolated from,
+# so it has to run first. Without it the services come up with empty images and
+# arguments.
+#
+#   ./prepare.sh
```
>>> The entire thing should be dockerized and not pollute the host. Can you think of better way of doing that?
-- INLINE id=3773473786 CHANGELOG.md:40 @ 2026-08-13T07:58:37Z
```diff
@@ -37,6 +37,8 @@
 
 - [#7408](https://github.com/ChainSafe/forest/pull/7408): Impl `Forest.IndexBackfill`, `Forest.IndexBackfillStatus` and `Forest.IndexBackfillCancel` RPC methods and the `forest-cli index backfill` command to back-fill the chain index (Ethereum mappings, events, block blooms) through the running daemon, so the node no longer needs to be stopped. An interrupted or cancelled run can be resumed with `--resume`.
 
+- [#7270](https://github.com/ChainSafe/forest/issues/7270): Include `scripts/tests/ribasushi-rpc-checks` that tests Forest against the external Ribasushi dataset, run daily in CI at 13:00 UTC
```
>>> Does this check affect the user? https://github.com/ChainSafe/forest/blob/4363d7a511722f3ce0bce285fe51be1a8571a3f9/CHANGELOG.md?plain=1#L17-L18
-- INLINE id=3795613159 scripts/tests/external-rpc-checks/setup.sh:13 @ 2026-08-17T10:33:25Z
```diff
+
+PARENT_PATH=$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd -P)
+pushd "${PARENT_PATH}"
+
+ARCHIVE_URL="https://forest-archive.chainsafe.dev/list/calibnet/latest-v2?format=json"
+RPC_URL="http://127.0.0.1:2345/rpc/v1"
+
+# GNU and BSD `date` spell date arithmetic differently, only GNU has `--version`.
```
>>> You can avoid such tricks if you'd just make an init container. That was my point in making it all dockerized, a single `docker compose up` contraption. The less you're relying on the host OS the better.
-- INLINE id=3802781001 .github/workflows/external-rpc-checks.yml:10 @ 2026-08-18T09:26:48Z
```diff
+  contents: read
+  issues: write
+
+concurrency:
+  group: ${{ github.workflow }}
+
+on:
+  workflow_dispatch:
```
>>> Could be a follow-up but a couple of goodies I'd love to have here:
- `workflow_dispatch` should accept a branch and run against it, potentially building a local docker image. This would allow targeting a branch that is "suspicious" and could use some additional checks,
- An extension over that would be to allow labeling a branch to include such checks, the same way we have with `RPC` and `Snapshot` labels (+ `Release`).
-- INLINE id=3802800347 docs/docs/developers/guides/rpc_api_compatibility.md:62 @ 2026-08-18T09:29:32Z
```diff
+Forest is also checked against the external `data.riba.plus` dataset of recorded
+RPC responses. The `data.riba.plus RPC checks` workflow runs daily
+at 13:00 UTC: it imports a calibnet snapshot into a regular Forest daemon, waits
+for the node to be ready, back-fills the chain index for the epochs under test
+and replays the dataset's queries against them. To reproduce it locally, run
+`./setup.sh` in `scripts/tests/external-rpc-checks`, which drives the whole
+sequence. It needs Docker, `curl` and `jq` on the host; everything else, the
+snapshot included, stays in containers.
```
>>> As all documentation, it drifted away from truth; I recommend trimming it down and just pointing to relevant workflow. It's developer guide, hand-holding not needed.
-- INLINE id=3802809656 scripts/tests/external-rpc-checks/docker-compose.yaml:15 @ 2026-08-18T09:30:46Z
```diff
+    # keeps the database in the container's volume, shared across containers
+    - FOREST_PATH=/data
+  user: 0:0
+
+services:
+  # Resolves yesterday's snapshot, imports it and back-fills the chain index.
+  init:
+    <<: *forest-service
```
>>> Nice one, we could potentially do this in some other compose files.
-- INLINE id=3802822111 scripts/tests/external-rpc-checks/docker-compose.yaml:28 @ 2026-08-18T09:32:31Z
```diff
+  forest:
+    <<: *forest-service
+    environment:
+      - FOREST_PATH=/data
+      - FOREST_CHAIN_INDEXER_ENABLED=1
+    ports:
+      - "2345:2345"
+      - "2346:2346"
```
>>> do we really need to expose those ports?
-- INLINE id=3802829487 scripts/tests/external-rpc-checks/docker-compose.yaml:56 @ 2026-08-18T09:33:31Z
```diff
+      start_period: 5m
+      start_interval: 5s
+
+  # The image entrypoints into `bundle exec ruby check_rpc.rb`, so only the epochs are appended.
+  rpc-checks:
+    image: ghcr.io/chainsafe/forest-rpc-checks:latest
+    environment:
+      FOREST_RPC_URL: forest:2345/rpc/v1
```
>>> `2345` and `2346` hardcoded in may places - not a huge deal, but worth just having it under on constant/variable.
-- INLINE id=3802844124 scripts/tests/external-rpc-checks/init.sh:34 @ 2026-08-18T09:35:30Z
```diff
+
+forest --chain=calibnet --encrypt-keystore=false --no-gc \
+  --height=-50 --import-snapshot="${url}" --halt-after-import
+
+# Indexes the 1000 epochs below the snapshot head; `--from` counts back
+# inclusively, so one extra tipset covers the whole range. The offline
+# back-fill already recomputes missing state and indexes up to the head.
+forest-tool index backfill --chain=calibnet --from="${epoch}" --n-tipsets=1001
```
>>> I think @sudo-shashank fixed this and it could be `1000`?
-- INLINE id=3802864136 scripts/tests/external-rpc-checks/init.sh:29 @ 2026-08-18T09:38:27Z
```diff
+# Snapshot names end in the epoch of their head tipset. ./setup.sh reads it back
+# to pick the range to check.
+epoch=${url##*_height_}
+epoch=${epoch%%.*}
+printf '%s\n' "${epoch}" > /data/snapshot-epoch
+
+forest --chain=calibnet --encrypt-keystore=false --no-gc \
+  --height=-50 --import-snapshot="${url}" --halt-after-import
```
>>> ```suggestion
forest --chain=calibnet --encrypt-keystore=false \
  --import-snapshot="${url}" --halt-after-import
```

GC fires by default after a week or so, it's fine to keep it; also I think it's fine to assume the snapshot is okay and validate it too much.
-- INLINE id=3802869714 scripts/tests/external-rpc-checks/setup.sh:16 @ 2026-08-18T09:39:16Z
```diff
+
+RPC_URL="http://127.0.0.1:2345/rpc/v1"
+
+# Confirms the back-fill indexed an epoch, which is what the checks query.
+verify_epoch_indexed() {
+  curl --silent --show-error --fail --retry 5 --retry-delay 5 --connect-timeout 10 \
+    --header 'Content-Type: application/json' \
+    --data "{\"jsonrpc\":\"2.0\",\"id\":1,\"method\":\"eth_getBlockByNumber\",\"params\":[\"$(printf '0x%x' "$1")\",false]}" \
```
>>> nit: can we avoid those nasty escapes?
-- INLINE id=3804582565 (reply to 3802829487) scripts/tests/external-rpc-checks/docker-compose.yaml:56 @ 2026-08-18T13:40:35Z
```diff
+      start_period: 5m
+      start_interval: 5s
+
+  # The image entrypoints into `bundle exec ruby check_rpc.rb`, so only the epochs are appended.
+  rpc-checks:
+    image: ghcr.io/chainsafe/forest-rpc-checks:latest
+    environment:
+      FOREST_RPC_URL: forest:2345/rpc/v1
```
>>> Where are we calling it from? Another container in the compose, no? So they can just belong to the same network and not mess with the host.

Why I'm asking for this - I often have Forest running on my own machine; I don't want to turn it off because of some needless port conflicts.
-- INLINE id=3807305780 scripts/tests/external-rpc-checks/verify.rb:1 @ 2026-08-18T19:34:14Z
```diff

```
>>> welcome to Ruby sect


==================== PR #7453 [MERGED] fix(forest-cli): `sync wait -w` no longer leaves a stale `Status:` line
https://github.com/ChainSafe/forest/pull/7453

-- REVIEW COMMENTED @ 2026-08-06T10:40:31Z
-- REVIEW APPROVED @ 2026-08-07T07:05:46Z
LGTM
-- REVIEW CHANGES_REQUESTED @ 2026-08-07T07:10:34Z
🐰 comment might be valid, I did some resizing while the `sync wait -w` was running and ended up with

```
❯ forest-cli sync wait -w
Status: Syncing (3861 epochs behind)
Node Head: Epoch 3954812 ([bafy2bzacebmet6slu6aoznsmvqdxqnxbbvt32vmiykg
Status: Syncing (3846 epochs behind)
Node Head: Epoch 3954827 ([bafy2bzaceasstg4lg6rfa7s3iwjrq7gbg57wmmomv4i5xpvb3dm22qqv63vb4, ...])
Network Head: Epoch 3958673
Last Update: 2026-08-07T07:09:48.671036974+00:00
Active Sync Tasks:
  - Fork Target: 3958673 ([bafy2bzacedzursoxvdbugyvcsdw42yxsrhvdfdcl7j4emd46pkmgkl3un3te4, ...]), Stage: Validating Tipsets, Syncing Range: [3954828..3958673] (3845 epochs)
```

This gets properly cleared when I resize back to the original width.
-- REVIEW APPROVED @ 2026-08-10T14:38:26Z
It still behaves weird on resizes (and aggregates previous data, spamming the terminal buffer) but let's not spend more time on it - might be too much work to do it without pulling some specialized crate. It's better than what it was.
-- CONVERSATION @ 2026-08-07T15:10:57Z
> and after the fix it is pretty solid against terminal resizes

@EclesioMeloJunior It's not fixed.

```
❯ forest-cli sync wait -w
Status: Syncing (2654 epochs behind)
Status: Syncing (2650 epochs behind)
Status: Syncing (2631 epochs behind)
Status: Syncing (2616 epochs behind)
Status: Syncing (2597 epochs behind)
Status: Syncing (2587 epochs behind)
Status: Syncing (2564 epochs behind)
Node Head: Epoch 3957065 ([bafy2bzacea63eha42apg7isczdctbce6fwolsxu24d6apwyshnlpyvamarjag, ...])
Network Head: Epoch 3959629
Last Update: 2026-08-07T15:07:34.259477872+00:00
Active Sync Tasks:
  - Fork Target: 3959629 ([bafy2bzacedjfefn7pur7ugpra42tnftshglxmvpmyudyqaze2r3dnmgriwetc, ...]), Stage: Validating Tipsets, Syncing Range: [3957066..3959629] (2563 epochs)
```
I reproduce it by going from my terminal full screen to floating window (alt down/up on GNOME, alacritty, Fedora Linux 44 (Workstation Edition).

```
❯ forest-cli --version
forest-filecoin 0.35.0+git.dfb1d4e8a
```
-- INLINE id=3728085179 src/cli/subcommands/sync_cmd.rs:131 @ 2026-08-06T10:40:31Z
```diff
+/// Writes the sync status report and returns the number of terminal *rows* it
+/// occupies.
+///
+/// Rows, not lines: a line wider than the terminal wraps onto several rows, and
+/// [`clear_previous_lines`] moves the cursor by rows. Counting `writeln!` calls
+/// instead leaves the topmost row of each frame behind on narrow terminals.
+/// See <https://github.com/ChainSafe/forest/issues/7366>.
+fn print_sync_report_details(
```
>>> High level question, do we need to do this line arithmetic dance ourselves? Do we have some crates in the tree that'd already do it for us?


==================== PR #7441 [MERGED] fix(blocks): validate all beacon entries for all covered epochs on quicknet 
https://github.com/ChainSafe/forest/pull/7441

-- REVIEW COMMENTED @ 2026-08-05T08:38:38Z
-- REVIEW COMMENTED @ 2026-08-05T08:49:34Z
-- REVIEW COMMENTED @ 2026-08-05T08:51:18Z
-- REVIEW COMMENTED @ 2026-08-05T13:19:39Z
-- REVIEW COMMENTED @ 2026-08-05T14:55:22Z
-- REVIEW COMMENTED @ 2026-08-05T14:56:12Z
-- REVIEW COMMENTED @ 2026-08-05T15:22:07Z
-- REVIEW COMMENTED @ 2026-08-06T07:10:29Z
-- REVIEW COMMENTED @ 2026-08-06T15:33:20Z
-- REVIEW COMMENTED @ 2026-08-06T15:34:06Z
-- REVIEW COMMENTED @ 2026-08-06T15:35:01Z
-- REVIEW DISMISSED @ 2026-08-06T15:35:16Z
LGTM
-- REVIEW APPROVED @ 2026-08-07T10:49:08Z
We should limit the drand relay calls in tests, but that's a precedent not introduced here. Otherwise LGTM
-- CONVERSATION @ 2026-08-05T13:46:58Z
nit: there are some [partially covered lines](https://app.codecov.io/gh/ChainSafe/forest/pull/7441?src=pr&el=tree) in the coverage you might want to cover (they're fairly trivial to reason about, so not a huge deal).

I'll approve once CI is green.
-- CONVERSATION @ 2026-08-07T10:46:20Z
I had to do a bump commit, the CI seemed to got stuck due to yesterday's GH outage.
-- INLINE id=3719152207 src/blocks/header.rs:184 @ 2026-08-05T08:38:38Z
```diff
         }
 
+        // An unchained beacon carries no link between consecutive entries, so nothing
+        // but these checks ties them to the epochs they are supposed to cover. A block
+        // covers every epoch since its parent - normally just its own, but null rounds
+        // can happen and it must carry exactly one entry per covered epoch, in
+        // ascending order.
+        if curr_beacon.network().is_unchained() {
```
>>> Given it's a consensus-affecting change, can we have a source to Lotus code with this logic?
-- INLINE id=3719221687 src/blocks/header.rs:207 @ 2026-08-05T08:49:34Z
```diff
+                ));
+            }
+
+            // Lengths match, so `zip` pairs every entry with the epoch it must cover.
+            for (beacon_entry, lookup_epoch) in self
+                .beacon_entries
+                .iter()
+                .zip((parent_epoch + 1)..=self.epoch)
```
>>> nit: I think you could simplify this via `izip!` https://docs.rs/itertools/latest/itertools/macro.izip.html

and end up with, e.g., `... in izip!(self.beacon_entries.iter(), (parent_epoch+1)..)`.
-- INLINE id=3719232382 src/blocks/header.rs:526 @ 2026-08-05T08:51:18Z
```diff
@@ -432,4 +477,42 @@ mod tests {
             *FILECOIN_GENESIS_CID
         );
     }
+
+    #[tokio::test]
```
>>> do we have a failing case?
-- INLINE id=3720883258 src/blocks/header.rs:207 @ 2026-08-05T13:19:39Z
```diff
 
             // Lengths match, so `zip` pairs every entry with the epoch it must cover.
-            for (beacon_entry, lookup_epoch) in self
-                .beacon_entries
-                .iter()
-                .zip((parent_epoch + 1)..=self.epoch)
+            for (beacon_entry, lookup_epoch) in izip!(
+                self.beacon_entries.iter(), (parent_epoch+1)..=self.epoch)
```
>>> Didn't you already assert the lenghts match? In this case the `=self.epoch` shouldn't be needed, no?
-- INLINE id=3721619528 src/blocks/header.rs:203 @ 2026-08-05T14:55:22Z
```diff
+                    format!(
+                        "expected {covered_epochs} beacon entries for epochs {}..={}, got: {found_entries}",
+                        parent_epoch + 1,
+                        self.epoch,
+                    )
+                    .into(),
+                ));
+            }
```
>>> I wonder if it's even needed. There's no Lotus equivalent for this. Wouldn't this logic be exercised via the loop below?
-- INLINE id=3721627056 src/blocks/header.rs:483 @ 2026-08-05T14:56:12Z
```diff
@@ -432,4 +477,87 @@ mod tests {
             *FILECOIN_GENESIS_CID
         );
     }
+
+    #[tokio::test]
+    async fn validate_beacon_entries_on_quicknet() {
+        // (acse name, parent epoch, its beacon round, block epoch, rounds the header must carry, expect fail)
```
>>> ```suggestion
        // (case name, parent epoch, its beacon round, block epoch, rounds the header must carry, expect fail)
```
-- INLINE id=3721813541 (reply to 3721627056) src/blocks/header.rs:483 @ 2026-08-05T15:22:07Z
```diff
@@ -432,4 +477,87 @@ mod tests {
             *FILECOIN_GENESIS_CID
         );
     }
+
+    #[tokio::test]
+    async fn validate_beacon_entries_on_quicknet() {
+        // (acse name, parent epoch, its beacon round, block epoch, rounds the header must carry, expect fail)
```
>>> btw you might be interested in some `rstest` applications we're doing in some other tests.
-- INLINE id=3726633810 src/blocks/header.rs:511 @ 2026-08-06T07:10:29Z
```diff
+    // the block's own entry is missing, so the last entry isn't at `max_round`
+    #[case::null_round_missing_own_entry(BeaconEntriesCase {
+        parent_epoch: 6216198,
+        prev_round: 30662982,
+        epoch: 6216200,
+        rounds: vec![30662992],
+        accepted: false,
+    })]
```
>>> ```rust
#[case::extra_trailing_entry(BeaconEntriesCase {
    parent_epoch: 6216199,
    prev_round: 30662992,
    epoch: 6216200,
    rounds: vec![30663002, 30663002],
    accepted: false,
})]
#[case::null_round_extra_trailing_entry(BeaconEntriesCase {
    parent_epoch: 6216198,
    prev_round: 30662982,
    epoch: 6216200,
    rounds: vec![30662992, 30663002, 30663002],
    accepted: false,
})]
```

Would these pass? If not, why?
-- INLINE id=3730155280 src/blocks/header.rs:184 @ 2026-08-06T15:33:20Z
```diff
@@ -180,6 +181,37 @@ impl RawBlockHeader {
             ));
         }
 
+        // An unchained beacon carries no link between consecutive entries, so nothing
```
>>> I'd trim those comments down, pretty long comments for such short code.
-- INLINE id=3730160442 src/blocks/header.rs:197 @ 2026-08-06T15:34:06Z
```diff
+        if curr_beacon.network().is_unchained() {
+            // We already made sure that the last beacon entry matches the current block
+            // epoch, and this loop covers a possible gap between the parent block and
+            // the current one. The epoch range is deliberately unbounded so that every
+            // entry is checked: an entry past `self.epoch` has no epoch to cover and is
+            // rejected against the round of the epoch that would follow this block.
+            for (beacon_entry, lookup_epoch) in
+                izip!(self.beacon_entries.iter(), (parent_epoch + 1)..)
```
>>> nit: at this point I think it makes sense to just go with standard `.enumerate()` which everyone knows
-- INLINE id=3730167134 src/blocks/header.rs:519 @ 2026-08-06T15:35:01Z
```diff
+    #[case::extra_trailing_entry(BeaconEntriesCase {
+        parent_epoch: 6216199,
+        prev_round: 30662992,
+        epoch: 6216200,
+        rounds: vec![30663002, 30663002],
+        accepted: false,
+    })]
+    #[case::null_round_extra_trailing_entry(BeaconEntriesCase {
```
>>> nit: it's the only one lacking comment, but subjectively those comments are not that useful - test cases names are already pretty self-explanatory


==================== PR #7427 [MERGED] fix `MinerGetBaseInfo` returns the correct `BeaconEntries`
https://github.com/ChainSafe/forest/pull/7427

-- REVIEW COMMENTED @ 2026-08-03T10:41:05Z
-- REVIEW COMMENTED @ 2026-08-03T10:44:07Z
-- REVIEW COMMENTED @ 2026-08-03T10:47:34Z
Aside from some nits, looks good! Let's have a changelog entry. This  is not a transparent change.
-- REVIEW CHANGES_REQUESTED @ 2026-08-03T15:24:10Z
-- REVIEW COMMENTED @ 2026-08-03T15:25:10Z
-- REVIEW COMMENTED @ 2026-08-04T12:22:41Z
-- REVIEW COMMENTED @ 2026-08-04T12:25:42Z
-- REVIEW COMMENTED @ 2026-08-04T12:25:51Z
-- REVIEW APPROVED @ 2026-08-04T13:12:02Z
-- CONVERSATION @ 2026-08-03T12:02:12Z
@EclesioMeloJunior There's also another thing that'd be great to tackle, basically answering a question "why wasn't it caught before"; the RPC parity tests should've eventually caught it. Perhaps we're missing some test cases there?

https://github.com/ChainSafe/forest/blob/81f6cba58d118b622ab8758805740f3e3941bc11/src/tool/subcommands/api_cmd/api_compare_tests.rs#L1129-L1133
-- INLINE id=3703203898 src/beacon/drand.rs:117 @ 2026-08-03T10:41:05Z
```diff
             prev.round()
         };
 
-        // We only ever need one entry after drand quicknet upgrade (FIP-0063)
         if curr_beacon.network().is_unchained() {
-            let entry = curr_beacon.entry(max_round).await?;
-            Ok(vec![entry])
+            let mut out = Vec::new();
```
>>> nit: we could use `SmallVec`; we _know_ there should be one, maaaybe two entries (more is quite improbable on mainnet). This will allocate it on the stack with option to extend if need be.
-- INLINE id=3703220423 src/beacon/tests/drand.rs:172 @ 2026-08-03T10:44:07Z
```diff
+        let prev_beacon_entry = prev_beacon.entry(prev_epoch_round).await.unwrap();
+
+        let entries = schedule
+            .beacon_entries_for_block(NetworkVersion::V22, epoch, prev_epoch, &prev_beacon_entry)
+            .await
+            .unwrap();
+
+        let rounds: Vec<u64> = entries.iter().map(BeaconEntry::round).collect();
```
>>> ```suggestion
        let rounds = entries.iter().map(BeaconEntry::round).collect_vec();
```
some code golf. You might need to pull `itertools` prelude if it's not in scope
-- INLINE id=3705265373 src/beacon/drand.rs:118 @ 2026-08-03T15:18:02Z
```diff
@@ -113,21 +115,30 @@ impl BeaconSchedule {
             prev.round()
         };
 
-        // We only ever need one entry after drand quicknet upgrade (FIP-0063)
+        let mut out: SmallVec::<[BeaconEntry; 2]> = SmallVec::new();
```
>>> ```suggestion
        let mut out: SmallVec::<[BeaconEntry; 1]> = SmallVec::new();
```
best to optimize for the most commonscenario
-- INLINE id=3705291817 src/beacon/drand.rs:126 @ 2026-08-03T15:20:52Z
```diff
-            Ok(vec![entry])
+            for covered_epoch in (parent_epoch + 1)..=epoch {
+                let round = curr_beacon.max_beacon_round_for_epoch(network_version, covered_epoch);
+                out.push(
+                    curr_beacon
+                        .entry(round)
+                        .await
+                        .context(format!(
```
>>> this will allocate a string **always** - you should prefer lazy evaluation via `.with_context`
-- INLINE id=3705309236 src/beacon/drand.rs:141 @ 2026-08-03T15:22:39Z
```diff
                 // Push all entries from rounds elapsed since the last chain epoch.
                 let entry = curr_beacon.entry(cur).await?;
                 cur = entry.round() - 1;
                 out.push(entry);
             }
             out.reverse();
-            Ok(out)
+            Ok(out.to_vec())
```
>>> Oh, I didn't notice we'd need to allocate on a heap regardless. In this case this optimization doesn't make that much sense unless we propagate the smallvec further.
-- INLINE id=3705321359 src/beacon/tests/drand.rs:174 @ 2026-08-03T15:24:00Z
```diff
+        let prev_beacon_entry = prev_beacon.entry(prev_epoch_round).await.unwrap();
+
+        let entries = schedule
+            .beacon_entries_for_block(NetworkVersion::V22, epoch, prev_epoch, &prev_beacon_entry)
+            .await
+            .unwrap();
+
+        let rounds: Vec<u64> = entries.iter().map(BeaconEntry::round).collect_vec();
```
>>> ```suggestion
        let rounds = entries.iter().map(BeaconEntry::round).collect_vec();
```

compiler should figure out `u64` just fine. `Vec` is implied via `collect_vec`
-- INLINE id=3705331073 (reply to 3705309236) src/beacon/drand.rs:141 @ 2026-08-03T15:25:09Z
```diff
                 // Push all entries from rounds elapsed since the last chain epoch.
                 let entry = curr_beacon.entry(cur).await?;
                 cur = entry.round() - 1;
                 out.push(entry);
             }
             out.reverse();
-            Ok(out)
+            Ok(out.to_vec())
```
>>> Let's just use `Vec` for now in this case
-- INLINE id=3712380563 CHANGELOG.md:40 @ 2026-08-04T12:22:41Z
```diff
@@ -37,6 +37,8 @@
 
 ### Changed
 
+- [#7412](https://github.com/ChainSafe/forest/issues/7412): Changes quicknet "unchained" logic to fetch the `max_beacon_round` for all covered epochs
```
>>> It's a fix, not an arbitrary change.
-- INLINE id=3712408590 src/beacon/drand.rs:124 @ 2026-08-04T12:25:42Z
```diff
         if curr_beacon.network().is_unchained() {
-            let entry = curr_beacon.entry(max_round).await?;
-            Ok(vec![entry])
+            for covered_epoch in (parent_epoch + 1)..=epoch {
+                let round = curr_beacon.max_beacon_round_for_epoch(network_version, covered_epoch);
+                out.push(curr_beacon.entry(round).await?);
+            }
+            Ok(out.to_vec())
```
>>> `out` is already a `Vec`, so you're needlessly cloning the entire thing now.
-- INLINE id=3712409919 src/beacon/drand.rs:134 @ 2026-08-04T12:25:51Z
```diff
                 // Push all entries from rounds elapsed since the last chain epoch.
                 let entry = curr_beacon.entry(cur).await?;
                 cur = entry.round() - 1;
                 out.push(entry);
             }
             out.reverse();
-            Ok(out)
+            Ok(out.to_vec())
```
>>> same