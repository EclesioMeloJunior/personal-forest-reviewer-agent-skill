

==================== PR #5798 by hanabi1224 [closed merged] chore: bump Go deps
https://github.com/ChainSafe/forest/pull/5798
review sequence: APPROVED


==================== PR #5799 by hanabi1224 [closed merged] fix(f3): mute some p2p logs
https://github.com/ChainSafe/forest/pull/5799
review sequence: APPROVED


==================== PR #5802 by sudo-shashank [closed merged] Update known blocks
https://github.com/ChainSafe/forest/pull/5802
review sequence: APPROVED


==================== PR #5805 by elmattic [closed merged] Add support for granular export
https://github.com/ChainSafe/forest/pull/5805
review sequence: COMMENTED, DISMISSED, DISMISSED, APPROVED
-- INLINE src/tool/subcommands/archive_cmd.rs:145
```diff
+        /// Export only lite snapshots.
+        #[arg(long, default_value_t = false, conflicts_with = "only_diffs")]
+        only_lites: bool,
+        /// Export only diff snapshots.
+        #[arg(long, default_value_t = false, conflicts_with = "only_lites")]
+        only_diffs: bool,
```
>>> Do you think it'd be easier to maintain if we make a single arg with enum? For example:
```rust
enum ExportOptions {
/// Exports all types of snapshots
#[default]
All,
/// 
Lite,
Diff,
```


==================== PR #5811 by hanabi1224 [closed merged] chore(deps): bump libp2p
https://github.com/ChainSafe/forest/pull/5811
review sequence: APPROVED


==================== PR #5815 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/5815
review sequence: APPROVED


==================== PR #5817 by Raveesh1007 [closed] feat: add network version Prometheus metric (#5141)
https://github.com/ChainSafe/forest/pull/5817
-- CONVERSATION
@Raveesh1007 Given you're on Windows, your editor likely added [BOM](https://en.wikipedia.org/wiki/Byte_order_mark) or other kinds of OS-specific whitespace, which made the diff huge. You can check it with a hex editor.
-- CONVERSATION
I recommend closing this PR and opening a new one only when you are confident that your changes are correct and that they are passing the various tests locally on your machine. I wouldn't trust AI to do this; AI is an okay tool only if you fully understand what is happening.


==================== PR #5818 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/5818
review sequence: COMMENTED, APPROVED
-- INLINE (reply) docs/docs/users/reference/cli.md:1517
```diff
+          - all:  Export all types of snapshots
+          - lite: Export only lite snapshots
+          - diff: Export only diff snapshots
+
+  -h, --help
+          Print help (see a summary with '-h')
```
>>> bad bot


==================== PR #5821 by hanabi1224 [closed merged] chore: replace `once_cell` utils with std equivalents
https://github.com/ChainSafe/forest/pull/5821
review sequence: APPROVED


==================== PR #5822 by hanabi1224 [closed merged] chore: remove mimalloc feature
https://github.com/ChainSafe/forest/pull/5822
review sequence: APPROVED


==================== PR #5823 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 3 updates
https://github.com/ChainSafe/forest/pull/5823
review sequence: APPROVED


==================== PR #5824 by hanabi1224 [closed merged] feat: util for calculating multihash from byte stream
https://github.com/ChainSafe/forest/pull/5824
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/utils/multihash.rs:99
```diff
+                }
+            }
+            Ok(hasher.finalize())
+        }
+
+        Ok(match self {
```
>>> nit: do you think it's a good opportunity for a macro to avoid code duplication?
-- INLINE src/utils/multihash.rs:173
```diff
+            Self::Ripemd320 => {
+                let mut hasher = multihash_codetable::Ripemd320::default();
+                self.wrap(hash(&mut hasher, bytes)?)?
+            }
+            _ => {
+                unimplemented!("{self:?}")
```
>>> That's a bit scary - we shouldn't crash the entire Forest on a bad reader.
-- INLINE src/utils/multihash.rs:229
```diff
+
+    #[test]
+    fn test_digest_byte_stream() {
+        use MultihashCode::*;
+
+        let mut bytes = vec![0; 10000];
```
>>> if we're doing a bit of a fuzzy tests, perhaps it would make sense to randomize the input size as well.
-- INLINE src/utils/multihash.rs:96
```diff
+                }
+                if let Some(b) = buf.get(0..n) {
+                    hasher.update(b);
+                }
+            }
+            Ok(hasher.finalize())
```
>>> ```suggestion
            while let Some(n) = bytes.read(&mut buf).ok() {
                if n == 0 {
                    break;
                }
                hasher.update(&buf[..n]);
            }
            Ok(hasher.finalize())
```
nit: some code golf if you want
-- INLINE (reply) src/utils/multihash.rs:99
```diff
+                }
+            }
+            Ok(hasher.finalize())
+        }
+
+        Ok(match self {
```
>>> No strong position here, I don't expect this code to be updated with many new algorithms anytime soon in the future. We can leave it as is.
-- INLINE (reply) src/utils/multihash.rs:229
```diff
+
+    #[test]
+    fn test_digest_byte_stream() {
+        use MultihashCode::*;
+
+        let mut bytes = vec![0; 10000];
```
>>> Or just manually check some edge conditions; input size of zero, 1, 1024. Also, there's an uncovered path with an unknown hashing algorithm.
-- INLINE (reply) src/utils/multihash.rs:96
```diff
+                }
+                if let Some(b) = buf.get(0..n) {
+                    hasher.update(b);
+                }
+            }
+            Ok(hasher.finalize())
```
>>> Let's leave the original version, then.
-- INLINE (reply) src/utils/multihash.rs:173
```diff
+            Self::Ripemd320 => {
+                let mut hasher = multihash_codetable::Ripemd320::default();
+                self.wrap(hash(&mut hasher, bytes)?)?
+            }
+            _ => {
+                unimplemented!("{self:?}")
```
>>> let's add a test case for it for completeness?


==================== PR #5825 by hanabi1224 [closed merged] chore: upgrade `go-f3` to v0.8.8
https://github.com/ChainSafe/forest/pull/5825
review sequence: COMMENTED, APPROVED
-- INLINE (reply) f3-sidecar/go.mod:47
```diff
 	github.com/gorilla/websocket v1.5.3 // indirect
 	github.com/hashicorp/golang-lru v1.0.2 // indirect
 	github.com/hashicorp/golang-lru/v2 v2.0.7 // indirect
 	github.com/huin/goupnp v1.3.0 // indirect
-	github.com/ipfs/boxo v0.32.0 // indirect
+	github.com/ipfs/boxo v0.30.0 // indirect
```
>>> @hanabi1224 is it actionable or not useful?


==================== PR #5828 by dependabot[bot] [closed merged] chore(deps): bump mermaid from 11.7.0 to 11.8.1 in /docs
https://github.com/ChainSafe/forest/pull/5828
review sequence: APPROVED


==================== PR #5829 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 3 updates
https://github.com/ChainSafe/forest/pull/5829
review sequence: APPROVED


==================== PR #5830 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 4 updates
https://github.com/ChainSafe/forest/pull/5830
review sequence: APPROVED


==================== PR #5832 by hanabi1224 [closed merged] chore: bump `nom`
https://github.com/ChainSafe/forest/pull/5832
review sequence: APPROVED


==================== PR #5834 by akaladarshi [closed merged] docs: add chain follower working details document
https://github.com/ChainSafe/forest/pull/5834
review sequence: COMMENTED, COMMENTED, APPROVED
-- REVIEW APPROVED
LGTM, thanks!
-- INLINE docs/docs/users/knowledge_base/chain_follower.md:1
```diff

```
>>> I'd put this file in the developer documentation and not the user documentation. Users shouldn't care about the internals.
-- INLINE (reply) docs/docs/users/knowledge_base/chain_follower.md:1
```diff

```
>>> Feel free to create new categories there if need be.


==================== PR #5835 by hanabi1224 [closed merged] feat: add `--format` flag to `forest-cli snapshot export`
https://github.com/ChainSafe/forest/pull/5835
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW CHANGES_REQUESTED
It's looking good. There are some outstanding comments that I'd like to get resolved first so we don't merge it by mistake.
-- REVIEW COMMENTED
LGTM. Let's add some documentation for newly created methods and traits, especially when they are marked with `pub`.
-- CONVERSATION
For future reference; I checked and there are no speed regressions (I checked on calibnet).

this branch
```
❯ time forest-cli snapshot export
./forest_snapshot_calibnet_2025-07-18_height_2850827.forest.car.zst: 6.29GB
Export completed.

________________________________________________________
Executed in   70.68 secs      fish           external
   usr time   45.42 millis  753.00 micros   44.67 millis
   sys time   29.64 millis  823.00 micros   28.82 millis

❯ time forest-cli snapshot export --v2
./forest_snapshot_calibnet_2025-07-18_height_2850829.forest.car.zst: 6.29GB
Export completed.

________________________________________________________
Executed in   71.04 secs      fish           external
   usr time   50.34 millis    1.30 millis   49.04 millis
   sys time   25.84 millis    0.16 millis   25.67 millis
```

main
```
❯ time forest-cli snapshot export
./forest_snapshot_calibnet_2025-07-18_height_2850836.forest.car.zst: 6.29GB
Export completed.

________________________________________________________
Executed in   70.17 secs      fish           external
   usr time   59.28 millis    0.27 millis   59.00 millis
   sys time   36.68 millis    1.04 millis   35.64 millis
```
-- INLINE src/chain/mod.rs:74
```diff
@@ -73,3 +83,117 @@ pub async fn export<D: Digest>(
 
     Ok(digest)
 }
+
+pub async fn export_v2<D: Digest>(
```
>>> We don't have unit tests for the original export, but it'd be great to have some for `v2`.
-- INLINE src/cli/subcommands/snapshot_cmd.rs:40
```diff
         /// How many state-roots to include. Lower limit is 900 for `calibnet` and `mainnet`.
         #[arg(short, long)]
         depth: Option<crate::chain::ChainEpochDelta>,
+        /// Export snapshot in the experimental v2 format(FRC-0108).
+        #[arg(long)]
+        v2: bool,
```
>>> For the sake of future implementations and operations, I think it'd be nicer to provide an enumeration with allowed versions: `v1` and `v2`. If we add another one, we'd just need add a valueto the enum (and not handle cases like `--v2 --v3`).
-- INLINE src/db/car/forest.rs:129
```diff
+        })
+    }
+
+    pub fn metadata(&self) -> &Option<FilecoinSnapshotMetadata> {
+        self.metadata.get_or_init(|| {
+            if self.header.roots.len() == 1 {
```
>>> Some explanations would be nice.
-- INLINE src/db/car/forest.rs:168
```diff
         Ok((header, footer))
     }
 
-    pub fn roots(&self) -> &NonEmpty<Cid> {
-        &self.roots
+    pub fn head_tipset_key(&self) -> &NonEmpty<Cid> {
```
>>> Let's have some documentation or comments around it, pointing to the relevant FRC for more details.
-- INLINE src/db/car/plain.rs:1
```diff

```
>>> Same comments as in the ForestCAR.
-- INLINE src/tool/subcommands/archive_cmd.rs:906
```diff
-        ArchiveInfo::from_store(&store, "ManyCAR".to_string(), heaviest_tipset.clone(), None)?;
+    let info = ArchiveInfo::from_store(
+        &store,
+        "ManyCAR".to_string(),
+        heaviest_tipset.clone(),
+        1,
```
>>> It's difficult to say what this `1` means without going deeper into the implementation. It'd be nice to have it named.
-- INLINE src/chain/mod.rs:168
```diff
+#[derive(Debug, Serialize, Deserialize, PartialEq, Eq)]
+#[serde(rename_all = "PascalCase")]
+pub struct FilecoinSnapshotMetadata {
+    pub version: u64,
+    pub head_tipset_key: NonEmpty<Cid>,
+    pub f3_data: Option<Cid>,
```
>>> I know it's in the FRC, but it'd be nice to have those fields documented regardless. Perhaps even some copy-pasta from the spec. At the end of the day, pointing someone to our Rust docs is nice and AI tooling will also appreciate it.
-- INLINE src/chain/mod.rs:166
```diff
+
+/// <https://github.com/filecoin-project/FIPs/blob/master/FRCs/frc-0108.md#snapshotmetadata>
+#[derive(Debug, Serialize, Deserialize, PartialEq, Eq)]
+#[serde(rename_all = "PascalCase")]
+pub struct FilecoinSnapshotMetadata {
+    pub version: u64,
```
>>> it'd be nice to have an enum here (with u64 representation). This way, we can have more idiomatic Rust code elsewhere with exhaustive pattern matching.
-- INLINE src/rpc/methods/chain.rs:298
```diff
     }
 }
 
+pub enum ChainExportV2 {}
+impl RpcMethod<1> for ChainExportV2 {
+    const NAME: &'static str = "Filecoin.ChainExportV2";
```
>>> Is it agreed with FilOz that we add this RPC method? Or should we add an optional field in the params (which should also be agreed upon with them)?
-- INLINE src/blocks/header.rs:36
```diff
     "bafyreiaqpwbbyjo4a42saasj36kkrpv4tsherf2e7bvezkert2a7dhonoi"
         .parse()
         .expect("Infallible")
 });
 
+#[cfg(test)]
```
>>> Perhaps this is not something to tackle in this PR, given that it was already present, but I'd love for all these lingering test artefacts to live somewhere else. Test code shouldn't be mixed too much with production code.
-- INLINE src/chain/mod.rs:74
```diff
+        skip_checksum,
+    )
+    .await
+}
+
+pub async fn export_v2<D: Digest>(
```
>>> Some documentation would be nice to have. In the current context the `export_v2` might be obvious, but a year from now someone new might struggle to understand it.
-- INLINE src/chain/mod.rs:1
```diff

```
>>> can we split this file to few more dedicated ones? It's usually nice to have the `mod.rs` only resolve around what to export from a crate.

I can see at least `export.rs`, `snapshot_format.rs` from a quick glimpse.
-- INLINE src/db/car/mod.rs:36
```diff
@@ -33,6 +33,9 @@ pub type CacheKey = u64;
 
 type FrameOffset = u64;
 
+/// According to FRC-0108, v2 snapshots have exactly one root pointing to metadata
```
>>> ```suggestion
/// According to [FRC-0108](https://github.com/filecoin-project/FIPs/blob/98e33b9fa306959aa0131519eb4cc155522b2081/FRCs/frc-0108.md), v2 snapshots have exactly one root pointing to metadata
```
-- INLINE src/db/car/plain.rs:191
```diff
+        })
+    }
+
+    pub fn head_tipset_key(&self) -> &NonEmpty<Cid> {
+        // head tipset key is stored in v2 snapshot metadata
+        // See <https://github.com/filecoin-project/FIPs/blob/master/FRCs/frc-0108.md#v2-specification>
```
>>> It should be more robust to paste links as permalinks in case something changes.


==================== PR #5836 by elmattic [closed merged] Add `forest api test-stateful` subcommand
https://github.com/ChainSafe/forest/pull/5836
review sequence: CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
Any reason why those tests are not invoked in CI?
-- CONVERSATION
> > Any reason why those tests are not invoked in CI?
> 
> They don't pass for Forest, only for Lotus. Do you think we should add a script to invoke them only for Lotus?

There is no point in testing Lotus. I think we should treat them like any other tests, mark the failing ones as `ignored`, and have tracking issues for them. The currently passing ones should still be a part of CI.
-- CONVERSATION
@elmattic it'd be great to go through AI reviewer comments and decide whether they are valid or not, but not leave it hanging.
-- CONVERSATION
stateful tests seems to be failing. No green checkmark, no review!
-- CONVERSATION
no green checkmark!
-- CONVERSATION
the stateful check is failing! no review! Let's ensure it's not flaky.
-- INLINE documentation/src/developer_documentation/rpc_stateful_tests.md:52
```diff
+
+To extend test coverage for another RPC method or cover more semantics:
+
+1. Add the RPC method to Forest if not yet implemented, following the guidance in [RPC compatibility guide](./rpc_api_compatibility.md).
+2. Create a new test scenario in:
+`src/tool/subcommands/api_cmd/api_run_tests.rs`
```
>>> It'd be nice to use actual links to have the CI break if the file is moved.
-- INLINE src/rpc/methods/eth.rs:217
```diff
         )))
     }
+
+    pub fn to_hex_string(&self) -> String {
+        format!("0x{:x}", self.0)
+    }
```
>>> nit: as far as I recall, the `format!` performance is not great, especially compared to dedicated crates like `hex`. I'd rather we use the latter.

It might make sense to consider https://lib.rs/crates/faster-hex in the future to replace the `hex` as well.
-- INLINE src/tool/subcommands/api_cmd.rs:205
```diff
+    /// test eth_newBlockFilter works ... ok
+    /// test eth_newPendingTransactionFilter works ... ok
+    /// test eth_getFilterLogs works ... ok
+    /// test result: ok. 7 passed; 0 failed; 0 ignored; 0 filtered out
+    /// ```
+    Run {
```
>>> I don't think the name and invocation of the command is very expressive. As I understand when looking into it in the description, it should serve some API, similar to `forest-tool api serve`.

I recommend to changing it to something obvious to what it actually does. For example, `forest-tool api test-eth-stateful` (there might be a better name...)
-- INLINE src/tool/subcommands/api_cmd.rs:213
```diff
+        /// Test Transaction `to` address
+        #[arg(long)]
+        to: String,
+        /// Test Transaction `from` address
+        #[arg(long)]
+        from: String,
```
>>> In the land of Rust, let's use strong typing and not strings when possible.
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:291
```diff
+
+                let smsg = client
+                    .call(MpoolPushMessage::request((message, None))?)
+                    .await?;
+
+                sleep(Duration::from_secs(2)).await;
```
>>> `sleeps` in tests are suspicious. They make them slow and flaky (especially when run on shared CPUs). Can we make it more deterministic?
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:233
```diff
+                Ok::<(), crate::rpc::ClientError>(())
+            };
+            verify_hashes(&prev_hashes).await?;
+
+            // Wait till the next block arrive
+            sleep(Duration::from_secs(EPOCH_DURATION_SECONDS as u64)).await;
```
>>> Again, I'd avoid sleeps. What if Forest hiccups for some reason? Or maybe there's a re-org? Can we do it in more deterministic (and potentially faster) way?
-- INLINE (reply) src/tool/subcommands/api_cmd.rs:205
```diff
+    /// test eth_newBlockFilter works ... ok
+    /// test eth_newPendingTransactionFilter works ... ok
+    /// test eth_getFilterLogs works ... ok
+    /// test result: ok. 7 passed; 0 failed; 0 ignored; 0 filtered out
+    /// ```
+    Run {
```
>>> Yeah, sounds good. But let's put it under the `api` subcommand.
-- INLINE (reply) src/tool/subcommands/api_cmd.rs:213
```diff
+        /// Test Transaction `to` address
+        #[arg(long)]
+        to: String,
+        /// Test Transaction `from` address
+        #[arg(long)]
+        from: String,
```
>>> I think it's doable, but given that it's just a test method and apparently we haven't done it in the past, it's okay for now.
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:197
```diff
+            let json: serde_json::Value = serde_json::from_str(&text)?;
+
+            if let Some(id) = json.get("result") {
+                channel_id = Some(id.clone());
+            } else {
+                let method = json!("xrpc.ch.val");
```
>>> Some comments would be lovely. I don't know what this is.
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:174
```diff
+    }
+
+    let mut url = client.base_url().clone();
+    url.set_scheme("ws")
+        .map_err(|_| anyhow::anyhow!("failed to set scheme"))?;
+    url.set_path("rpc/v0");
```
>>> why `v0`? Is it not available in `v1`? `v0` is considered legacy from what I recall.
-- INLINE (reply) src/tool/subcommands/api_cmd/api_run_tests.rs:276
```diff
+        let last_block = client.call(EthBlockNumber::request(())?).await?;
+
+        let filter_spec = EthFilterSpec {
+            from_block: Some(EthUint64(last_block.0 - BLOCK_RANGE).to_hex_string()),
+            to_block: Some(last_block.to_hex_string()),
+            ..Default::default()
```
>>> @elmattic is it valid?
-- INLINE (reply) src/tool/subcommands/api_cmd/api_run_tests.rs:271
```diff
+                    ws_stream.close(None).await?;
+                    anyhow::bail!("expecting params");
+                }
+            }
+        }
+    }
```
>>> @elmattic, why is this not valid? I mean, do we ignore errors here?
-- INLINE .github/workflows/forest.yml:390
```diff
+        with:
+          path: "${{ env.FIL_PROOFS_PARAMETER_CACHE }}"
+          key: proof-params-keys
+      - name: Checkout Sources
+        uses: actions/checkout@v4
+      - uses: actions/download-artifact@v4
```
>>> ```suggestion
      - uses: actions/download-artifact@v5
```
-- INLINE (reply) scripts/tests/calibnet_api_test_stateful_check.sh:7
```diff
+# This script tests RPC API stateful tests on a live forest node.
+# It requires both `forest`, `forest-wallet` and `forest-tool` to be in the PATH.
+
+set -euxo pipefail
+
+source "$(dirname "$0")/harness.sh"
```
>>> @elmattic, is this actionable?
-- INLINE scripts/tests/calibnet_api_test_stateful_check.sh:30
```diff
+$FOREST_WALLET_PATH --remote-wallet import preloaded_wallet.key
+
+TO_ADDRESS="t410fp6e7drelxau7nf76tcn6gva22t5jafefhevubwi"
+FROM_ADDRESS="t410f2avianksmit2cl2bqk53qant7nm7rdmk63twa5y"
+PAYLOAD="40c10f190000000000000000000000007f89f1c48bb829f697fe989be3541ad4fa9014850000000000000000000000000000000000000000000000000de0b6b3a7640000"
+TOPIC="0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef"
```
>>> It'd be nice to add some comments; what is this payload and topic?
-- INLINE scripts/tests/harness.sh:1
```diff

```
>>> @elmattic let's mark the coderabbit comments as resolved if they're not actionable or just pure hallucinations.
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:1
```diff

```
>>> should we be more specific and name it `stateful_tests.rs`?
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:26
```diff
+
+type TestRunner = Arc<
+    dyn Fn(Arc<rpc::Client>) -> Pin<Box<dyn Future<Output = anyhow::Result<()>> + Send>>
+        + Send
+        + Sync,
+>;
```
>>> is it Rust or Boost templates? :rofl:
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:160
```diff
+    println!(
+        "test result: {status}. {passed} passed; {failed} failed; {ignored} ignored; {filtered} filtered out"
+    );
+    if failed > 0 {
+        anyhow::bail!(format!("{failed} test(s) failed"));
+    }
```
>>> ```suggestion
    ensure!(failed == 0, "{failed} test(s) failed");
```
nit(code golf): I _think_ this should work
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:283
```diff
+                // Ignore other message types
+            }
+        }
+    }
+
+    // Unreachable: loop always returns within the branches above.
```
>>> Let's panic (it's fine, these are tests) if it reaches the unreachable. Your comment might hold now, but someone might add something in the loop that would break this assumption.
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:296
```diff
+            .await?;
+
+        if pending.0.iter().any(|msg| msg.cid() == message_cid) {
+            break Ok(());
+        }
+        if retries == 0 {
```
>>> nit: use `ensure!`
-- INLINE src/tool/subcommands/api_cmd/api_run_tests.rs:112
```diff
+            continue;
+        }
+        if test.ignore.is_some() {
+            ignored += 1;
+            continue;
+        }
```
>>> I'd print this as well, just to confirm to the developer that the methods were filtered/ignored.
-- INLINE src/tool/subcommands/api_cmd.rs:219
```diff
+    /// that rely on internal state.
+    ///
+    /// Use `--filter` to run only tests that interact with a specific RPC method.
+    ///
+    /// Example output:
+    /// ```markdown
```
>>> ```suggestion
    /// ```text
```
ni:t You're not really using any markdown here. Perhaps `console` would be more fitting (but I'm not sure it works in rust docs).
-- INLINE src/tool/subcommands/api_cmd/contracts/erc20/roberto.sol:1
```diff

```
>>> It's probably obvious what it does, but it would be nice to add a comment on top that describes its purpose (why do we need it at all).
-- INLINE scripts/tests/harness.sh:1
```diff

```
>>> Do we need to handle the tokens? It's saved and consumed already from a default location, as introduced in https://github.com/ChainSafe/forest/pull/5629


==================== PR #5839 by hanabi1224 [closed merged] fix(ci): link checker 403 Forbidden errors
https://github.com/ChainSafe/forest/pull/5839
review sequence: APPROVED


==================== PR #5841 by hanabi1224 [closed merged] feat: add `SizeTrackingLruCache` for tracking cache size metrics
https://github.com/ChainSafe/forest/pull/5841
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW APPROVED
LGTM
-- INLINE src/utils/cache/lru.rs:61
```diff
+            cache_id: ID_GENERATOR.fetch_add(1, Ordering::Relaxed),
+            cache_name,
+            cache: Arc::new(RwLock::new(LruCache::new(capacity))),
+            size_in_bytes: Default::default(),
+        }
+    }
```
>>> Hm, why do we need a non-registered metric?
-- INLINE src/utils/cache/lru.rs:32
```diff
+
+#[derive(Debug, Clone)]
+pub struct SizeTrackingLruCache<K, V>
+where
+    K: GetSize + Debug + Send + Sync + Hash + PartialEq + Eq + Clone + 'static,
+    V: GetSize + Debug + Send + Sync + Clone + 'static,
```
>>> Seems those are repeated a bit. IT might make sense to make a super-trait around it.
-- INLINE (reply) src/utils/cache/lru.rs:61
```diff
+            cache_id: ID_GENERATOR.fetch_add(1, Ordering::Relaxed),
+            cache_name,
+            cache: Arc::new(RwLock::new(LruCache::new(capacity))),
+            size_in_bytes: Default::default(),
+        }
+    }
```
>>> If we use it only for unit tests, let's compile it only for tests.
-- INLINE src/utils/cache/lru.rs:86
```diff
+        self.size_in_bytes
+            .inc_by(k.get_size().saturating_add(v.get_size()) as _);
+        let old = self.cache.write().push(k, v);
+        if let Some((old_k, old_v)) = &old {
+            self.size_in_bytes
+                .dec_by(old_k.get_size().saturating_add(old_v.get_size()) as _);
```
>>> I'm not sure about this. Caches are supposed to be as fast as possible with minimal overhead, but this seems to add quite a bit of action on every push. If we adopt this tracked cache everywhere, the performance drop might be significant, especially if the struct is complex (like block header).
-- INLINE (reply) src/utils/cache/lru.rs:86
```diff
+        self.size_in_bytes
+            .inc_by(k.get_size().saturating_add(v.get_size()) as _);
+        let old = self.cache.write().push(k, v);
+        if let Some((old_k, old_v)) = &old {
+            self.size_in_bytes
+                .dec_by(old_k.get_size().saturating_add(old_v.get_size()) as _);
```
>>> That said, we can _probably_ go ahead with it and later optimise if needed. It might be a good opportunity for a benchmark test, though.


==================== PR #5843 by hanabi1224 [closed merged] chore(deps): bump deps in docs
https://github.com/ChainSafe/forest/pull/5843
review sequence: APPROVED


==================== PR #5846 by xihuwenhua [closed] chore: remove redundant words in comment
https://github.com/ChainSafe/forest/pull/5846
-- CONVERSATION
Happy to re-open once the commits are signed.


==================== PR #5848 by hanabi1224 [closed merged] feat: more lru cache metrics
https://github.com/ChainSafe/forest/pull/5848
review sequence: APPROVED
-- REVIEW APPROVED
It'd be great to add a Grafana dashboard with all of those tracked metrics there for an easy check of total caches size.


==================== PR #5851 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/5851
review sequence: APPROVED
-- REVIEW APPROVED
Maybe GH workers were having a bad time yesterday. Let's merge; we can always revert if it starts failing the nightly cron.
-- CONVERSATION
Hm, sigterm with no logs. I'll re-trigger.


==================== PR #5853 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 6 updates
https://github.com/ChainSafe/forest/pull/5853
review sequence: DISMISSED


==================== PR #5857 by hanabi1224 [closed] feat: size tracking flume channel
https://github.com/ChainSafe/forest/pull/5857
review sequence: COMMENTED, COMMENTED, COMMENTED
-- CONVERSATION
@hanabi1224
-- INLINE src/utils/flume/mod.rs:95
```diff
+        },
+        SizeTrackingReceiver { receiver, tracker },
+    )
+}
+
+#[derive(Debug)]
```
>>> It'd be great to put some docs around this (even with doc examples), especially given its ubiquitous usage across the codebase. Same goes for other introduces `SizeTracking*` data structures.

I'd also consider putting them (flume, cache and I think there was one more) in a dedicated module, i.e., `src/utils/size_tracking/*`. This could be done in a separate PR if you decide it makes sense.
-- INLINE src/utils/flume/mod.rs:1
```diff

```
>>> tests?
-- INLINE src/utils/get_size/mod.rs:14
```diff
+where
+    V: AsRef<[T]>,
+{
+    std::mem::size_of_val(slice.as_ref())
+}
+
```
>>> Can you help me understand the correctness of this? This seems to work at most with shallow collections, but something like this will totally not work:

https://play.rust-lang.org/?version=stable&mode=debug&edition=2024&gist=a8109b6ba152d250b00a46a026ccb91d


==================== PR #5859 by hanabi1224 [closed merged] fix: size calculation for zstd frame cache
https://github.com/ChainSafe/forest/pull/5859
review sequence: COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- INLINE src/db/car/mod.rs:44
```diff
+        if let Ok(size) = value.parse::<NonZeroUsize>() {
+            let size = size.get();
+            tracing::info!("zstd frame max size is set to {size} via {ENV_KEY}");
+            return size;
+        } else {
+            tracing::warn!("Failed to parse {ENV_KEY}={value}, value should be a positive integer");
```
>>> ```suggestion
            tracing::error!("Failed to parse {ENV_KEY}={value}, value should be a positive integer");
```
-- INLINE src/db/car/mod.rs:89
```diff
 
     /// Insert entry into lru-cache and evict pages if `max_size` has been exceeded.
-    pub fn put(&mut self, offset: FrameOffset, key: CacheKey, index: HashMap<Cid, Vec<u8>>) {
-        fn size_of_entry(entry: &HashMap<Cid, Vec<u8>>) -> usize {
-            entry.values().map(Vec::len).sum::<usize>()
+    pub fn put(&self, offset: FrameOffset, key: CacheKey, index: HashMap<CidWrapper, Vec<u8>>) {
```
>>> I'd love it if we could have some coverage here, both on the logic level and size calculation correctness.
-- INLINE src/utils/cache/lru.rs:73
```diff
             cache_name,
-            cache: Arc::new(RwLock::new(LruCache::new(capacity))),
+            cache: Arc::new(RwLock::new(
+                capacity
+                    .map(LruCache::new)
+                    .unwrap_or_else(LruCache::unbounded),
```
>>> Can we ban the `LruCache::unbounded` and mark this as exception with proper comment?


==================== PR #5860 by hanabi1224 [closed merged] fix: remove write_cache from ForestCar and PlainCar
https://github.com/ChainSafe/forest/pull/5860
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/cid_collections/hash_map.rs:5
```diff
@@ -1,6 +1,8 @@
 // Copyright 2019-2025 ChainSafe Systems
 // SPDX-License-Identifier: Apache-2.0, MIT
 
+#![allow(dead_code)]
+
```
>>> ```suggestion
```

why is it needed?
-- INLINE src/db/car/forest.rs:242
```diff
-{
-    fn put_keyed_persistent(&self, k: &Cid, block: &[u8]) -> anyhow::Result<()> {
-        self.put_keyed(k, block)
+    /// Not supported, use [`super::ManyCar`] instead.
+    fn put_keyed(&self, _: &Cid, _: &[u8]) -> anyhow::Result<()> {
+        anyhow::bail!("ForestCar is read-only, use ManyCar instead");
```
>>> I think we should panic at this point, as it's an implementation issue if the code goes here.
-- INLINE (reply) src/db/car/forest.rs:242
```diff
-{
-    fn put_keyed_persistent(&self, k: &Cid, block: &[u8]) -> anyhow::Result<()> {
-        self.put_keyed(k, block)
+    /// Not supported, use [`super::ManyCar`] instead.
+    fn put_keyed(&self, _: &Cid, _: &[u8]) -> anyhow::Result<()> {
+        anyhow::bail!("ForestCar is read-only, use ManyCar instead");
```
>>> Same in some other places. Probably we could use `unimplemented!` macro.
-- INLINE (reply) src/db/car/forest.rs:242
```diff
-{
-    fn put_keyed_persistent(&self, k: &Cid, block: &[u8]) -> anyhow::Result<()> {
-        self.put_keyed(k, block)
+    /// Not supported, use [`super::ManyCar`] instead.
+    fn put_keyed(&self, _: &Cid, _: &[u8]) -> anyhow::Result<()> {
+        anyhow::bail!("ForestCar is read-only, use ManyCar instead");
```
>>> Or even better, `unreachable!`
-- INLINE (reply) src/cid_collections/hash_map.rs:5
```diff
@@ -1,6 +1,8 @@
 // Copyright 2019-2025 ChainSafe Systems
 // SPDX-License-Identifier: Apache-2.0, MIT
 
+#![allow(dead_code)]
+
```
>>> I'd mark individual items, not the entire file, then.


==================== PR #5861 by akaladarshi [closed merged] feat: add support for system actor in state decode params API
https://github.com/ChainSafe/forest/pull/5861
review sequence: APPROVED


==================== PR #5862 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/5862
review sequence: APPROVED


==================== PR #5866 by sudo-shashank [closed merged] bump go version in docker
https://github.com/ChainSafe/forest/pull/5866
review sequence: APPROVED


==================== PR #5868 by hanabi1224 [closed merged] fix: reduce db read ops in chain export
https://github.com/ChainSafe/forest/pull/5868
review sequence: APPROVED
-- CONVERSATION
Do you have some metrics for mainnet?
-- CONVERSATION
Yes, please measure it. Let's have at least five runs before the change and five runs after, optimally from exactly the same tipset.


==================== PR #5869 by hanabi1224 [closed merged] feat: print average speed in forest-cli snapshot export
https://github.com/ChainSafe/forest/pull/5869
review sequence: APPROVED


==================== PR #5870 by akaladarshi [closed merged] feat: add reason for the API failure in compare cmd
https://github.com/ChainSafe/forest/pull/5870
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:112
```diff
@@ -108,6 +109,22 @@ pub enum TestSummary {
     Valid,
 }
 
+impl fmt::Display for TestSummary {
```
>>> Any reason for not deriving `strum::Display`?
-- INLINE src/tool/subcommands/api_cmd/report.rs:331
```diff
+                        }
+
+                        let reasons_str = reasons
+                            .iter()
+                            .map(|s| s.as_str())
+                            .collect::<Vec<_>>()
```
>>> ```suggestion
                            .collect_vec()
```
nit: code golf


==================== PR #5871 by sudo-shashank [closed merged] Add support for datacap actor in state decode params API
https://github.com/ChainSafe/forest/pull/5871
-- CONVERSATION
I'll let the state decode params expert, @akaladarshi, review this one.


==================== PR #5875 by akaladarshi [closed merged] feat: add support for power actor methods in `StateDecodeParams` API
https://github.com/ChainSafe/forest/pull/5875
review sequence: COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
no green checkmark!
-- INLINE (reply) src/tool/subcommands/api_cmd/api_compare_tests.rs:1967
```diff
+            Address::POWER_ACTOR,
+            Method::MinerRawPowerExported as u64, // frc42_dispatch::method_hash!("MinerPower"),
+            to_vec(&power_miner_raw_params)?,
+            tipset.key().into(),
+        ))?),
+        // Not supported by the lotus
```
>>> Let's add this link to the comment.
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1939
```diff
+            to_vec(&power_create_miner_params)?,
+            tipset.key().into(),
+        ))?),
+        RpcTest::identity(StateDecodeParams::request((
+            Address::POWER_ACTOR,
+            Method::UpdateClaimedPower as u64, // frc42_dispatch::method_hash!("MinerPower"),
```
>>> Are those comments helpful?


==================== PR #5878 by akaladarshi [closed merged] chore: use buildjet ubuntu in calibnet rpc check ci
https://github.com/ChainSafe/forest/pull/5878
review sequence: APPROVED
-- CONVERSATION
what about the daily RPC checks? https://github.com/ChainSafe/forest/blob/main/.github/workflows/rpc-parity.yml
-- CONVERSATION
> > what about the daily RPC checks? https://github.com/ChainSafe/forest/blob/main/.github/workflows/rpc-parity.yml
> 
> Will be pushing that for as well, just wanted to see how it works in CI first.
> 
> Just a thought, I haven't seen daily RPC check failed at all (should we still change it?)

If it works in one workflow and doesn't in another we should understand it. They are mostly the same; the only difference is that one takes the compiled binary and another extracts the binary from docker image. Lotus tag is the same.

But maybe it's just bad luck for PRs.


==================== PR #5881 by hanabi1224 [closed merged] chore(deps): bump go-f3 and Go deps
https://github.com/ChainSafe/forest/pull/5881
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE (reply) interop-tests/src/tests/go_app/go.mod:129
```diff
+	golang.org/x/sync v0.16.0 // indirect
+	golang.org/x/sys v0.34.0 // indirect
+	golang.org/x/text v0.27.0 // indirect
 	golang.org/x/time v0.12.0 // indirect
-	golang.org/x/tools v0.34.0 // indirect
+	golang.org/x/tools v0.35.0 // indirect
```
>>> @hanabi1224 is it a valid concern?
-- INLINE (reply) interop-tests/src/tests/go_app/go.mod:129
```diff
+	golang.org/x/sync v0.16.0 // indirect
+	golang.org/x/sys v0.34.0 // indirect
+	golang.org/x/text v0.27.0 // indirect
 	golang.org/x/time v0.12.0 // indirect
-	golang.org/x/tools v0.34.0 // indirect
+	golang.org/x/tools v0.35.0 // indirect
```
>>> @hanabi1224 are you sure? there were failures around it over the weekend https://github.com/ChainSafe/forest/pull/5866


==================== PR #5882 by Olexandr88 [closed] chore(docs): edited the engines in README
https://github.com/ChainSafe/forest/pull/5882
-- CONVERSATION
?


==================== PR #5886 by hanabi1224 [closed merged] feat: integrate with `go-f3` and implement F3 data export and import
https://github.com/ChainSafe/forest/pull/5886
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/cli_shared/cli/client.rs:108
```diff
         self.data_dir.join("token")
     }
+
+    pub fn rpc_v1_endpoint(&self) -> String {
+        format!("http://{}/rpc/v1", self.rpc_address)
+    }
```
>>> Hm, why do we need this? Why was it not needed before?
-- INLINE src/daemon/db_util.rs:134
```diff
@@ -130,6 +131,9 @@ pub async fn import_chain_as_forest_car(
     from_path: &Path,
     forest_car_db_dir: &Path,
     import_mode: ImportMode,
+    rpc_endpoint: String,
```
>>> `Url`?
-- INLINE src/daemon/db_util.rs:135
```diff
@@ -130,6 +131,9 @@ pub async fn import_chain_as_forest_car(
     from_path: &Path,
     forest_car_db_dir: &Path,
     import_mode: ImportMode,
+    rpc_endpoint: String,
+    f3_root: String,
```
>>> Can this be strongly typed?
-- INLINE src/db/car/forest.rs:218
```diff
             ..self
         }
     }
+
+    /// Gets a reader of the block data by its `Cid`
+    pub fn get_reader(&self, k: Cid) -> anyhow::Result<Option<impl Read>> {
```
>>> Could we have more exhaustive tests for this?
-- INLINE src/f3/go_ffi.rs:22
```diff
@@ -18,4 +18,6 @@ pub trait GoF3Node {
         finality: i64,
         f3_root: String,
     ) -> bool;
+
+    fn import_snap(f3_rpc_endpoint: String, f3_root: String, snapshot_path: String) -> String;
```
>>> `Url`, (what's the type of f3 root? cid?), `Path/PathBuf`
-- INLINE (reply) src/f3/go_ffi.rs:22
```diff
@@ -18,4 +18,6 @@ pub trait GoF3Node {
         finality: i64,
         f3_root: String,
     ) -> bool;
+
+    fn import_snap(f3_rpc_endpoint: String, f3_root: String, snapshot_path: String) -> String;
```
>>> Or maybe it doesn't make sense here given it's FFI and it'd only make things weird.
-- INLINE src/f3/mod.rs:120
```diff
 
+pub fn import_f3_snapshot(
+    chain_config: &ChainConfig,
+    _rpc_endpoint: String,
+    _f3_root: String,
+    _snapshot: String,
```
>>> nit: `_` prefix is kind of deceiving here - it is actually used, but not on all features. I feel more expressive would be to explicitly allow unused params.
-- INLINE src/f3/snapshot.rs:27
```diff
+pub struct F3SnapshotHeader {
+    pub version: u64,
+    pub first_instance: u64,
+    pub latest_instance: u64,
+    pub initial_power_table: Vec<F3PowerEntry>,
+}
```
>>> It'd be nice to have some docs around it.
-- INLINE src/rpc/methods/f3.rs:637
```diff
+        let mut params = ArrayParams::new();
+        params.insert(path)?;
+        let LotusJson(cid): LotusJson<Cid> = client
+            .request("Filecoin.F3ExportLatestSnapshot", params)
+            .await?;
+        Ok(cid)
```
>>> I'm not sure it's worth extracting this logic to `run` and then just calling it.
-- INLINE (reply) src/tool/subcommands/archive_cmd.rs:648
```diff
+        let store = AnyCar::try_from(filecoin.as_path())?;
+        anyhow::ensure!(
+            store.metadata().is_none(),
+            "The filecoin snapshot is not in v1 format"
+        );
+    }
```
>>> Wouldn't it be more self-explanatory to `drop(store)`?
-- INLINE src/tool/subcommands/archive_cmd.rs:156
```diff
+        #[arg(long)]
+        filecoin: PathBuf,
+        /// Path to the F3 snapshot
+        #[arg(long)]
+        f3: PathBuf,
+        /// Snapshot output filename
```
>>> ```suggestion
        /// Snapshot output file path
```
-- INLINE src/tool/subcommands/archive_cmd.rs:152
```diff
     },
+    /// Merge a v1 Filecoin snapshot with an F3 snapshot into a v2 Filecoin snapshot in `.forest.car.zst` format
+    MergeF3 {
+        /// Path to the Filecoin snapshot
+        #[arg(long)]
+        filecoin: PathBuf,
```
>>> Can a `v2` snapshot also be provided here? If not, the `v1` name should probably be used instead.
-- INLINE src/rpc/methods/f3.rs:635
```diff
+    pub async fn run(path: String) -> anyhow::Result<Cid> {
+        let client = get_rpc_http_client()?;
+        let mut params = ArrayParams::new();
+        params.insert(path)?;
+        let LotusJson(cid): LotusJson<Cid> = client
+            .request("Filecoin.F3ExportLatestSnapshot", params)
```
>>> why is there `"Filecoin.F3ExportLatestSnapshot"` and `"F3.ExportLatestSnapshot"`?


==================== PR #5888 by akaladarshi [closed merged] feat: add support for reward actor in state decode params API
https://github.com/ChainSafe/forest/pull/5888
review sequence: APPROVED


==================== PR #5893 by hanabi1224 [closed merged] chore: refactor and simplify `stream_graph` and `unordered_stream_graph`
https://github.com/ChainSafe/forest/pull/5893
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/ipld/util.rs:352
```diff
+/// # Arguments
+///
+/// * `db` - A database that implements [`Blockstore`] interface.
+/// * `tipset_iter` - An iterator of [`Tipset`], descending order `$child -> $parent`.
+/// * `stateroot_limit` - An epoch that signifies how far back we need to inspect tipsets, in-depth.
+///   This has to be pre-calculated using this formula: `$cur_epoch - $depth`, where `$depth` is the
```
>>> what's with `$`? what does it do?
-- INLINE (reply) src/ipld/util.rs:352
```diff
+/// # Arguments
+///
+/// * `db` - A database that implements [`Blockstore`] interface.
+/// * `tipset_iter` - An iterator of [`Tipset`], descending order `$child -> $parent`.
+/// * `stateroot_limit` - An epoch that signifies how far back we need to inspect tipsets, in-depth.
+///   This has to be pre-calculated using this formula: `$cur_epoch - $depth`, where `$depth` is the
```
>>> Copied from where?
-- INLINE (reply) src/ipld/util.rs:352
```diff
+/// # Arguments
+///
+/// * `db` - A database that implements [`Blockstore`] interface.
+/// * `tipset_iter` - An iterator of [`Tipset`], descending order `$child -> $parent`.
+/// * `stateroot_limit` - An epoch that signifies how far back we need to inspect tipsets, in-depth.
+///   This has to be pre-calculated using this formula: `$cur_epoch - $depth`, where `$depth` is the
```
>>> Ah okay, sorry. I didn't look at the left side of the diff it seems. :sweat_smile:


==================== PR #5895 by akaladarshi [closed merged] feat: add support for multisig actor in state decode params API
https://github.com/ChainSafe/forest/pull/5895
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/registry/actors/multisig.rs:87
```diff
+        12 => register_multisig_v9_plus!(registry, cid, fil_actor_multisig_state::v12),
+        13 => register_multisig_v9_plus!(registry, cid, fil_actor_multisig_state::v13),
+        14 => register_multisig_v9_plus!(registry, cid, fil_actor_multisig_state::v14),
+        15 => register_multisig_v9_plus!(registry, cid, fil_actor_multisig_state::v15),
+        16 => register_multisig_v9_plus!(registry, cid, fil_actor_multisig_state::v16),
+        _ => {}
```
>>> same as in the other PR - we shouldn't silently fail.
-- INLINE src/tool/subcommands/api_cmd/test_snapshots.txt:146
```diff
@@ -143,6 +143,15 @@ filecoin_statedecodeparams_1753985928338431.rpcsnap.json.zst
 filecoin_statedecodeparams_1753985928337192.rpcsnap.json.zst
 filecoin_statedecodeparams_1753985928337443.rpcsnap.json.zst
 filecoin_statedecodeparams_1753985928337618.rpcsnap.json.zst
+filecoin_statedecodeparams_1754230255606413.rpcsnap.json.zst
```
>>> let's add `multisig` somewhere?


==================== PR #5896 by akaladarshi [closed merged] feat: add support for verified reg actor in state decode params
https://github.com/ChainSafe/forest/pull/5896
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, COMMENTED, APPROVED, COMMENTED, APPROVED
-- REVIEW APPROVED
LGTM
-- CONVERSATION
no green checkmark, no review!
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1
```diff

```
>>> It's getting huge, I'd split it into a dedicated method.
-- INLINE src/tool/subcommands/api_cmd/test_snapshots.txt:157
```diff
+filecoin_statedecodeparams_1754401651146860.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651146948.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651147022.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651147091.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651147157.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651147231.rpcsnap.json.zst
```
>>> let's embed verified registry in the name?
-- INLINE src/rpc/registry/actors/verified_reg.rs:249
```diff
+        12 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v12, v12),
+        13 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v13, v13),
+        14 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v14, v14),
+        15 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v15, v15),
+        16 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v16, v16),
+        _ => {}
```
>>> it'd be better to actually report some error in this case
-- INLINE (reply) src/tool/subcommands/api_cmd/test_snapshots.txt:157
```diff
+filecoin_statedecodeparams_1754401651146860.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651146948.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651147022.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651147091.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651147157.rpcsnap.json.zst
+filecoin_statedecodeparams_1754401651147231.rpcsnap.json.zst
```
>>> Yeah.
-- INLINE (reply) src/rpc/registry/actors/verified_reg.rs:249
```diff
+        12 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v12, v12),
+        13 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v13, v13),
+        14 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v14, v14),
+        15 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v15, v15),
+        16 => register_verified_reg_v12_plus!(registry, cid, fil_actor_verifreg_state::v16, v16),
+        _ => {}
```
>>> Hm, okay. I'd love if we wouldn't use integers here and matching on them. But it doesn't have to be in this PR.

Imagine a scenario that will come in a month or so; a new actors version is released: 17. A nice thing to have is let the compiler tell you where you need to update your code with the new version. A good thing about matching on an enum is that whenever a new variant is introduced, the entire build fails for places where it's matched non-exhaustively.

The alternative is finding the issues in runtime, which is not ideal and brings us closer to Python land (a bad place).
-- INLINE Cargo.toml:227
```diff
@@ -224,6 +224,7 @@ zstd = "0.13"
 
 # optional dependencies
 console-subscriber = { version = "0.4", features = ["parking_lot"], optional = true }
+fvm_actor_utils = "14.0.0"
```
>>> Can you please summarise why it is needed?
-- INLINE src/tool/subcommands/api_cmd/test_snapshots.txt:196
```diff
+filecoin_verified_reg_statedecodeparams_1754401651146860.rpcsnap.json.zst
+filecoin_verified_reg_statedecodeparams_1754401651146948.rpcsnap.json.zst
+filecoin_verified_reg_statedecodeparams_1754401651147022.rpcsnap.json.zst
+filecoin_verified_reg_statedecodeparams_1754401651147091.rpcsnap.json.zst
+filecoin_verified_reg_statedecodeparams_1754401651147157.rpcsnap.json.zst
+filecoin_verified_reg_statedecodeparams_1754401651147231.rpcsnap.json.zst
```
>>> not a blocker, but for future it might make sense to add method names here. This, or we should prioritize https://github.com/ChainSafe/forest/issues/5894 for structured input.
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:2278
```diff
@@ -2362,6 +2284,312 @@ fn datacap_actor_state_decode_params_tests(tipset: &Tipset) -> anyhow::Result<Ve
     Ok(tests)
 }
 
+fn multisig_actor_state_decode_params_tests(tipset: &Tipset) -> anyhow::Result<Vec<RpcTest>> {
```
>>> it'd be nice to refactor those to separate files - this file is getting quite large. Not a blocker here, can be a part of future refactor.
-- INLINE (reply) Cargo.toml:227
```diff
@@ -224,6 +224,7 @@ zstd = "0.13"
 
 # optional dependencies
 console-subscriber = { version = "0.4", features = ["parking_lot"], optional = true }
+fvm_actor_utils = "14.0.0"
```
>>> It probably should be a re-export in `fil-actor-states`, then. Not a blocker, obviously.


==================== PR #5897 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group in /docs with 3 updates
https://github.com/ChainSafe/forest/pull/5897
review sequence: APPROVED


==================== PR #5899 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 3 updates
https://github.com/ChainSafe/forest/pull/5899
review sequence: APPROVED


==================== PR #5900 by sudo-shashank [closed] Forest Slasher Service
https://github.com/ChainSafe/forest/pull/5900
-- CONVERSATION
@sudo-shashank


==================== PR #5901 by hanabi1224 [closed merged] chore: refactor cur_tipset in MessagePool to use RwLock instead Mutex
https://github.com/ChainSafe/forest/pull/5901
review sequence: APPROVED


==================== PR #5902 by akaladarshi [closed merged] Revert to ubuntu-24.04 from `buildjet` in rpc parity workflow
https://github.com/ChainSafe/forest/pull/5902
review sequence: COMMENTED
-- INLINE .github/workflows/rpc-parity.yml:9
```diff
@@ -6,7 +6,7 @@ on:
 jobs:
   rpc-parity:
     name: RPC parity tests
-    runs-on: buildjet-8vcpu-ubuntu-2204
+    runs-on: ubuntu-24.04
```
>>> You need to revive some parts of https://github.com/ChainSafe/forest/pull/5892 as well.

Additionally, we need to understand why this is happening in the first place. It's not very logical. Could you please create a tracking issue?


==================== PR #5903 by elmattic [closed merged] Remove noisy prints
https://github.com/ChainSafe/forest/pull/5903
review sequence: APPROVED
-- CONVERSATION
no green checkmark :point_right: no review :x:
-- CONVERSATION
Nice. I actually wanted to complain about it before. :rofl:


==================== PR #5906 by hanabi1224 [closed merged] chore: remove `serde_tuple` in favor of `fvm_ipld_encoding::tuple`
https://github.com/ChainSafe/forest/pull/5906
review sequence: APPROVED


==================== PR #5908 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/5908
review sequence: APPROVED


==================== PR #5910 by hanabi1224 [closed merged] chore(ci): add go linter
https://github.com/ChainSafe/forest/pull/5910
review sequence: COMMENTED, DISMISSED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
no green checkmark!
-- INLINE .github/workflows/go-lint.yml:1
```diff
@@ -0,0 +1,29 @@
+name: Go code linters
```
>>> Does it make sense to invoke this exclusively for changes in the Go source code directories + `go.work` etc?
-- INLINE Makefile:1
```diff
@@ -1,3 +1,5 @@
+GOLANGCI_LINT_VERSION=v2.3.1
```
>>> does it have to be global for the makefile? can it be just part of the `go run...` command?
-- INLINE (reply) interop-tests/src/tests/go_app/gen.go:27
```diff
+import (
+	"runtime"
+	"unsafe"
+
+	"github.com/ihciah/rust2go/asmcall"
+)
```
>>> @hanabi1224 I think this comment makes sense.
-- INLINE (reply) Makefile:1
```diff
@@ -1,3 +1,5 @@
+GOLANGCI_LINT_VERSION=v2.3.1
```
>>> Yeah, but it's not used anywhere else. It adds unnecessary noise for anyone NOT upgrading it.
-- INLINE (reply) interop-tests/src/tests/go_app/gen.go:27
```diff
+import (
+	"runtime"
+	"unsafe"
+
+	"github.com/ihciah/rust2go/asmcall"
+)
```
>>> @hanabi1224 ?
-- INLINE interop-tests/src/tests/go_app/ffi_gen.go:1
```diff

```
>>> is this not generated?
-- INLINE (reply) interop-tests/src/tests/go_app/ffi_gen.go:1
```diff

```
>>> So, could we not add it to the source?


==================== PR #5912 by hanabi1224 [closed merged] feat: track TipsetStateCache size with prometheus metrics
https://github.com/ChainSafe/forest/pull/5912
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/blocks/tipset.rs:105
```diff
 }
 
 impl From<NonEmpty<Cid>> for TipsetKey {
-    fn from(value: NonEmpty<Cid>) -> Self {
+    fn from(mut value: NonEmpty<Cid>) -> Self {
+        // When `value.capacity() > value.len()`, it takes more heap memory.
```
>>> I like it. :brain:
-- INLINE src/cid_collections/mod.rs:106
```diff
 
-    #[derive(Debug, Clone, Copy, Hash, Eq, PartialEq, Ord, PartialOrd)]
+    #[derive(Debug, Clone, Copy, Hash, Eq, PartialEq, Ord, PartialOrd, GetSize)]
     #[repr(transparent)]
     pub struct Uncompactable {
+        #[get_size(ignore)]
```
>>> Why?


==================== PR #5913 by hanabi1224 [closed merged] chore: upgrade rust toolchain to 1.89.0
https://github.com/ChainSafe/forest/pull/5913
review sequence: COMMENTED, APPROVED
-- REVIEW APPROVED
clippy is a blessing
-- INLINE (reply) src/db/blockstore_with_read_cache.rs:31
```diff
-    fn len(&self) -> usize {
-        self.len()
-    }
 }
 
+#[allow(dead_code)]
```
>>> @hanabi1224 is it valid?


==================== PR #5914 by hanabi1224 [closed merged] test: `BeaconEntry::get_heap_size`
https://github.com/ChainSafe/forest/pull/5914
review sequence: APPROVED
-- REVIEW APPROVED
Nice, I didn't know about this crate. I guess it might've been useful for profiling, though I'm not sure it adds to any other tools I used.
-- CONVERSATION
no green checkmark, no review!


==================== PR #5920 by hanabi1224 [closed merged] feat: tracking size of all LruCache with SizeTrackingLruCache
https://github.com/ChainSafe/forest/pull/5920
review sequence: COMMENTED, DISMISSED, APPROVED
-- INLINE src/shim/address.rs:265
```diff
     }
 }
 
+impl GetSize for Address {
+    fn get_heap_size(&self) -> usize {
+        0
```
>>> ```suggestion
        0 // all variants of the internal payload are stack-allocated
```


==================== PR #5921 by hanabi1224 [closed] test: dhat test for lru and hashlink
https://github.com/ChainSafe/forest/pull/5921
review sequence: COMMENTED
-- REVIEW COMMENTED
Does it make sense to merge this at all?
-- CONVERSATION
As mentioned earlier, given this and the benchmark you've made, I'm okay with switching to hashlink; the perf seems OK, and the benefit is lower memory usage.


==================== PR #5922 by hanabi1224 [closed merged] fix: use hashlink::LruCache in SizeTrackingLruCache
https://github.com/ChainSafe/forest/pull/5922
review sequence: DISMISSED, DISMISSED, APPROVED


==================== PR #5925 by hanabi1224 [closed merged] chore(deps): bump jsonrpsee
https://github.com/ChainSafe/forest/pull/5925
review sequence: APPROVED


==================== PR #5926 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 4 updates
https://github.com/ChainSafe/forest/pull/5926
review sequence: APPROVED


==================== PR #5927 by dependabot[bot] [closed merged] chore(deps): bump actions/checkout from 4 to 5
https://github.com/ChainSafe/forest/pull/5927
review sequence: APPROVED


==================== PR #5928 by sudo-shashank [closed merged] Add support for evm actor in state decode params API
https://github.com/ChainSafe/forest/pull/5928
-- CONVERSATION
I'll let the decode params expert review it: @akaladarshi


==================== PR #5930 by hanabi1224 [closed merged] test: refactor `rpc_regression_tests` into individual tests
https://github.com/ChainSafe/forest/pull/5930
review sequence: COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
I guess we can delete this?

https://github.com/ChainSafe/forest/blob/cc5c4c1d065a30170579dcfda2d6e60f8cf74210/.config/nextest.toml#L34-L36
-- CONVERSATION
no green checkmark, no review!
-- INLINE src/tool/subcommands/api_cmd/test_snapshot.rs:235
```diff
+        let start = Instant::now();
+        run_test_from_snapshot(&path).await.unwrap();
+        println!(
+            "  succeeded, took {}.",
+            humantime::format_duration(start.elapsed())
+        );
```
>>> Do we need the manual timings? Isn't it redundant by what `nextest` provides already?


==================== PR #5931 by hanabi1224 [closed merged] fix(test): transient failure in test_get_size_beacon_entry
https://github.com/ChainSafe/forest/pull/5931
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/beacon/beacon_entries.rs:42
```diff
+        // Avoid shrink_to_fit: it's a non-binding hint.
+        let signature = signature.into_boxed_slice().into_vec();
+        debug_assert_eq!(
+            signature.len(),
+            signature.capacity(),
+            "BeaconEntry::signature should be right-sized"
```
>>> What does it mean right-sized?
-- INLINE (reply) src/beacon/beacon_entries.rs:38
```diff
 
 impl BeaconEntry {
     pub fn new(round: u64, signature: Vec<u8>) -> Self {
+        // Drop any excess capacity to make heap usage deterministic across allocators.
+        // Avoid shrink_to_fit: it's a non-binding hint.
+        let signature = signature.into_boxed_slice().into_vec();
```
>>> @hanabi1224 I assume this behaviour is confimred by the test that was failing? Did you try running it, e.g., 100x times?
-- INLINE (reply) src/beacon/beacon_entries.rs:38
```diff
 
 impl BeaconEntry {
     pub fn new(round: u64, signature: Vec<u8>) -> Self {
+        // Drop any excess capacity to make heap usage deterministic across allocators.
+        // Avoid shrink_to_fit: it's a non-binding hint.
+        let signature = signature.into_boxed_slice().into_vec();
```
>>> If it works, then it works.


==================== PR #5933 by elmattic [closed merged] Add support for `market` actor in StateDecodeParams API
https://github.com/ChainSafe/forest/pull/5933
review sequence: APPROVED


==================== PR #5936 by AlexeyKrasnoperov [closed merged] Add RPC API to usage examples, improve self-confidence
https://github.com/ChainSafe/forest/pull/5936
review sequence: COMMENTED, DISMISSED, APPROVED
-- REVIEW DISMISSED
LGTM, perhaps it's soon time for a 1.0.0 release?!
-- INLINE README.md:18
```diff
@@ -15,7 +15,8 @@
 
 Forest is a [Filecoin] node written in [Rust]. With Forest, you can:
 
-- Send and receive FIL (at your own risk - Forest is experimental),
+- Send and receive FIL,
```
>>> ```suggestion
- Transfer FIL,
```
nit: word golf


==================== PR #5938 by sudo-shashank [closed] Update V0 and V1 documentation link
https://github.com/ChainSafe/forest/pull/5938
-- CONVERSATION
Redudant with https://github.com/ChainSafe/forest/pull/5936


==================== PR #5945 by hanabi1224 [closed merged] fix(test): increase nextest timeout for rpc snapshot tests
https://github.com/ChainSafe/forest/pull/5945
review sequence: APPROVED


==================== PR #5946 by hanabi1224 [closed merged] feat: add `--n-epochs` to `forest-cli state compute`
https://github.com/ChainSafe/forest/pull/5946
review sequence: COMMENTED, DISMISSED, COMMENTED, DISMISSED, COMMENTED, APPROVED
-- REVIEW DISMISSED
It'd be nice to have some tests for this feature. That said, given the utility nature of this, it's not a blocker (for me).
-- INLINE src/rpc/methods/state.rs:1471
```diff
+                anyhow::Ok(ts)
+            });
+        }
 
-        Ok(state_root)
+        let mut results = vec![];
```
>>> Can we _guess_ the capacity of the vector to avoid re-allocations? It should be `n_epochs`, no? 
```suggestion
        let mut results = Vec::with_capacity(n_epochs);
```
-- INLINE CHANGELOG.md:42
```diff
@@ -37,6 +37,10 @@
 
 - [#5867](https://github.com/ChainSafe/forest/pull/5867) Added `--unordered` to `forest-cli snapshot export` for exporting `CAR` blocks in non-deterministic order for better performance with more parallelization.
 
+- [#5946](https://github.com/ChainSafe/forest/pull/5946) Added `--n-epochs` to `forest-cli state compute` for computating state trees in batch.
```
>>> There is also a breaking change to the `Forest.StateCompute` API, no?
-- INLINE (reply) CHANGELOG.md:42
```diff
@@ -37,6 +37,10 @@
 
 - [#5867](https://github.com/ChainSafe/forest/pull/5867) Added `--unordered` to `forest-cli snapshot export` for exporting `CAR` blocks in non-deterministic order for better performance with more parallelization.
 
+- [#5946](https://github.com/ChainSafe/forest/pull/5946) Added `--n-epochs` to `forest-cli state compute` for computating state trees in batch.
```
>>> We don't guarantee it, but it's still worth mentioning in case someone relied on it.


==================== PR #5947 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/5947
review sequence: APPROVED


==================== PR #5950 by dependabot[bot] [closed merged] chore(deps): bump actions/download-artifact from 4 to 5
https://github.com/ChainSafe/forest/pull/5950
review sequence: APPROVED
-- REVIEW APPROVED
Seems to be working fine.


==================== PR #5951 by dependabot[bot] [closed merged] chore(deps-dev): bump typescript from 5.8.3 to 5.9.2 in /docs
https://github.com/ChainSafe/forest/pull/5951
review sequence: APPROVED


==================== PR #5954 by kks-code [closed merged] chore: fix a couple of typos
https://github.com/ChainSafe/forest/pull/5954
review sequence: APPROVED
-- CONVERSATION
Thanks for the contribution. Can you please sign the CLA?


==================== PR #5955 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/5955
review sequence: APPROVED


==================== PR #5956 by hanabi1224 [closed merged] feat: add `forest-tool archive f3-header` command
https://github.com/ChainSafe/forest/pull/5956
review sequence: APPROVED


==================== PR #5960 by hanabi1224 [closed merged] fix(test): mitigate rpc snapshot test timeout by adding retry to snapshot downloading
https://github.com/ChainSafe/forest/pull/5960
review sequence: COMMENTED, APPROVED
-- INLINE src/tool/subcommands/api_cmd/test_snapshot.rs:230
```diff
+                })),
+                max_retries: Some(5),
+                ..Default::default()
+            },
+            || async {
+                download_file_with_cache(&url, &cache_dir, DownloadFileOption::NonResumable).await
```
>>> Any reason for this to be `NonResumable`?


==================== PR #5962 by varunpareek690 [closed] chore: extend spellcheck to all markdown files and update CI
https://github.com/ChainSafe/forest/pull/5962
review sequence: COMMENTED, CHANGES_REQUESTED
-- REVIEW COMMENTED
Thanks for looking into it. Some changes are needed.
-- REVIEW CHANGES_REQUESTED
There's no point in updating the lockfile. Why did you add all of those dependencies there?
-- CONVERSATION
> Apologies for the repeated changes on what should have been a small task . Thank you for pointing out the corrections regarding the dictionary ordering, yarn.lock, and formatting. I’ve made the requested fixes and updated the PR accordingly.

Unfortunately, the requested changes were not addressed. Perhaps you didn't push them.
-- INLINE docs/dictionary.txt:125
```diff
 zstd
+Docusaurus
+CloudFlare
+Diátaxis
+OpenRPC
+eg
```
>>> _exempli gratia_ should be written `e.g.` https://dictionary.cambridge.org/dictionary/english/eg
-- INLINE docs/yarn.lock:1
```diff

```
>>> There is no reason to update the lockfile.
-- INLINE (reply) docs/package.json:16
```diff
@@ -13,7 +13,7 @@
     "write-translations": "docusaurus write-translations",
     "write-heading-ids": "docusaurus write-heading-ids",
     "typecheck": "tsc",
-    "spellcheck": "spellchecker -f \"./docs/**/*.md\" -l en-US -q  -d ./dictionary.txt",
+    "spellcheck": "spellchecker -f \"./**/*.md\" -l en-US -q  -d ./dictionary.txt",
```
>>> @varunpareek690 This suggestion is correct.


==================== PR #5963 by hanabi1224 [closed merged] feat: add `forest-cli f3 ready` to check if F3 is in sync
https://github.com/ChainSafe/forest/pull/5963
review sequence: COMMENTED, DISMISSED, APPROVED
-- INLINE src/cli/subcommands/f3_cmd.rs:185
```diff
+                            }
+                        }
+                        Err(e) if !wait => {
+                            anyhow::bail!("{e}");
+                        }
+                        _ => {}
```
>>> is this needed? Isn't it already exhaustive match?


==================== PR #5964 by hanabi1224 [closed merged] test: ci test for `forest-cli state compute`
https://github.com/ChainSafe/forest/pull/5964
review sequence: COMMENTED, APPROVED
-- INLINE scripts/tests/calibnet_other_check.sh:73
```diff
+if ! [[ "$head_epoch" =~ ^[0-9]+$ ]]; then
+  echo "Failed to parse numeric head epoch from 'chain head --format json': $head_epoch"
+  exit 1
+fi
+start_epoch=$(( head_epoch > 900 ? head_epoch - 900 : 0 ))
+$FOREST_CLI_PATH state compute --epoch "$start_epoch" -n 10 -v
```
>>> how long does it take?


==================== PR #5967 by xihuwenhua [closed merged] chore: remove redundant words in comment
https://github.com/ChainSafe/forest/pull/5967
review sequence: APPROVED


==================== PR #5969 by hanabi1224 [closed merged] fix: improve `forest-tool snapshot validate` error message
https://github.com/ChainSafe/forest/pull/5969
review sequence: DISMISSED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW COMMENTED
Might be worth adding a changelog entry to the tool.
-- INLINE src/ipld/util.rs:208
```diff
                                 ))));
                             };
                         }
                     }
-                    Iterate(cid_vec) => {
+                    Iterate(epoch, block_cid, ty, cid_vec) => {
```
>>> what's `ty`? let's use a more meaningful name.


==================== PR #5977 by hanabi1224 [closed merged] chore: deprecate `ticker` by favoring `tokio::time::interval`
https://github.com/ChainSafe/forest/pull/5977
review sequence: APPROVED
-- REVIEW APPROVED
nice!


==================== PR #5978 by hanabi1224 [closed merged] test: add CI cache for rpc snapshot tests to mitigate flackyness issue
https://github.com/ChainSafe/forest/pull/5978
review sequence: COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
fiasco! no green checkmark, no review!
```
 test tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_walletverify_1755692429792254 ... ok

failures:

---- tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_ethgetblocktransactioncountbyhash_1740132538001911 stdout ----
Testing filecoin_ethgetblocktransactioncountbyhash_1740132538001911.rpcsnap.json.zst ...
thread 'tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_ethgetblocktransactioncountbyhash_1740132538001911' panicked at src/tool/subcommands/api_cmd/test_snapshot.rs:244:45:
called `Result::unwrap()` on an `Err` value: expected value at line 1 column 1
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace

---- tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_stategetrandomnessfromtickets_1737546933236620 stdout ----
Testing filecoin_stategetrandomnessfromtickets_1737546933236620.rpcsnap.json.zst ...
thread 'tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_stategetrandomnessfromtickets_1737546933236620' panicked at src/tool/subcommands/api_cmd/test_snapshot.rs:244:45:
called `Result::unwrap()` on an `Err` value: expected value at line 1 column 1

---- tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_statelookuprobustaddress_1737546941473578 stdout ----
Testing filecoin_statelookuprobustaddress_1737546941473578.rpcsnap.json.zst ...
thread 'tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_statelookuprobustaddress_1737546941473578' panicked at src/tool/subcommands/api_cmd/test_snapshot.rs:244:45:
called `Result::unwrap()` on an `Err` value: expected value at line 1 column 1


failures:
    tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_ethgetblocktransactioncountbyhash_1740132538001911
    tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_stategetrandomnessfromtickets_1737546933236620
    tool::subcommands::api_cmd::test_snapshot::tests::rpc_snapshot_test_filecoin_statelookuprobustaddress_1737546941473578

test result: FAILED. 220 passed; 3 failed; 0 ignored; 0 measured; 566 filtered out; finished in 103.66s
```
-- INLINE .config/nextest.toml:43
```diff
 # Jitter is enabled to avoid [thundering herd issues](https://en.wikipedia.org/wiki/Thundering_herd_problem).
 [[profile.default.overrides]]
-filter = 'test(rpc_test_)'
+filter = 'test(rpc_snapshot_test_)'
 slow-timeout = { period = "120s", terminate-after = 3 }
 retries = { backoff = "exponential", count = 4, delay = "5s", jitter = true }
```
>>> If this mitigates the flakiness, then the `retries` should not be needed anymore.
-- INLINE (reply) .config/nextest.toml:43
```diff
 # Jitter is enabled to avoid [thundering herd issues](https://en.wikipedia.org/wiki/Thundering_herd_problem).
 [[profile.default.overrides]]
-filter = 'test(rpc_test_)'
+filter = 'test(rpc_snapshot_test_)'
 slow-timeout = { period = "120s", terminate-after = 3 }
 retries = { backoff = "exponential", count = 4, delay = "5s", jitter = true }
```
>>> I just love the term `thundering herd`.


==================== PR #5981 by hanabi1224 [closed merged] fix: print actual response in `forest-cli sync check-bad`
https://github.com/ChainSafe/forest/pull/5981
review sequence: APPROVED


==================== PR #5994 by akaladarshi [closed merged] feat: add support for eam actor in state decode params API
https://github.com/ChainSafe/forest/pull/5994
review sequence: DISMISSED, DISMISSED, APPROVED


==================== PR #5995 by akaladarshi [closed merged] feat: add support for ethaccount actor in state decode params API 
https://github.com/ChainSafe/forest/pull/5995
review sequence: APPROVED


==================== PR #5996 by akaladarshi [closed merged] feat: add support for payment channel actor in state decode params API
https://github.com/ChainSafe/forest/pull/5996
review sequence: APPROVED
-- CONVERSATION
no green checkmark, no review!


==================== PR #5998 by dependabot[bot] [closed merged] chore(deps): bump mermaid from 11.10.0 to 11.10.1 in /docs in the patch-versions group
https://github.com/ChainSafe/forest/pull/5998
review sequence: APPROVED


==================== PR #6002 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/6002
review sequence: APPROVED
-- CONVERSATION
Blocked by https://github.com/ChainSafe/forest/pull/6003


==================== PR #6007 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/6007
review sequence: APPROVED


==================== PR #6009 by hanabi1224 [closed merged] fix: reduce allocations in chain traversal
https://github.com/ChainSafe/forest/pull/6009
review sequence: DISMISSED, APPROVED
-- CONVERSATION
it's a draft but I already want it


==================== PR #6014 by hanabi1224 [closed merged] chore: remove unordered chain traversal
https://github.com/ChainSafe/forest/pull/6014
review sequence: APPROVED


==================== PR #6015 by hanabi1224 [closed merged] fix: bug in fallback_de_ipld_dagcbor
https://github.com/ChainSafe/forest/pull/6015
review sequence: APPROVED, APPROVED
-- REVIEW APPROVED
Did this bug manifest anywhere? In snapshot validation or generation? If so, a changelog entry might be useful.


==================== PR #6018 by hanabi1224 [closed merged] fix: error computing state for a null epoch
https://github.com/ChainSafe/forest/pull/6018
review sequence: APPROVED


==================== PR #6019 by hanabi1224 [closed merged] chore(deps): bump `go-f3`
https://github.com/ChainSafe/forest/pull/6019
review sequence: APPROVED


==================== PR #6020 by dependabot[bot] [closed merged] chore(deps): bump tracing-subscriber from 0.3.19 to 0.3.20
https://github.com/ChainSafe/forest/pull/6020
review sequence: APPROVED


==================== PR #6025 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/6025
review sequence: APPROVED


==================== PR #6027 by dependabot[bot] [closed merged] chore(deps): bump tempfile from 3.20.0 to 3.21.0
https://github.com/ChainSafe/forest/pull/6027
review sequence: APPROVED


==================== PR #6030 by dependabot[bot] [closed] chore(deps): bump hashbrown from 0.15.5 to 0.16.0
https://github.com/ChainSafe/forest/pull/6030
review sequence: APPROVED
-- CONVERSATION
> This requires a new GetSize2 release that supports the new hashbrown version

Do we close it for now, then? @hanabi1224


==================== PR #6034 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/6034
review sequence: APPROVED


==================== PR #6036 by hanabi1224 [closed merged] chore(ci): update f3 snapshot to speed up f3 checks
https://github.com/ChainSafe/forest/pull/6036
review sequence: APPROVED


==================== PR #6037 by hanabi1224 [closed merged] test: track peak rss for CI state migration test
https://github.com/ChainSafe/forest/pull/6037
review sequence: DISMISSED, DISMISSED, APPROVED
-- REVIEW DISMISSED
LGTM, thank you!


==================== PR #6044 by dependabot[bot] [closed merged] chore(deps): bump actions/download-artifact from 4 to 5
https://github.com/ChainSafe/forest/pull/6044
review sequence: APPROVED


==================== PR #6046 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 2 updates
https://github.com/ChainSafe/forest/pull/6046
review sequence: APPROVED


==================== PR #6051 by hanabi1224 [closed merged] fix: OOM in `forest-tool archive diff`
https://github.com/ChainSafe/forest/pull/6051
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
I don't quite understand. Could you please explain what was the issue and how this change fixes it?
-- CONVERSATION
> > I don't quite understand. Could you please explain what was the issue and how this change fixes it?
> 
> @LesnyRumcajs Calling `pp_actor_state` for every actor state causes OOM on fuzzy(with 64GiB RAM), even with cacheless hamt iteration. Calling `pp_actor_state` on demand fixes the issue.

But why is that? Is it due to the number of calls to `pp_actor_state` or is there a particular state that causes it? I don't see how the memory grows here, even if called on all actors; `pp_actor_state` should claim memory and then release it, no?
-- CONVERSATION
> > even if called on all actors; pp_actor_state should claim memory and then release it, no?
> 
> @LesnyRumcajs I don't see memory leak in it, my best guess is, resolving some large IPLD on a single actor causes OOM (which is confirmed by monitoring `htop` on Fuzzy)

I see. So this will surface again if that actor has a diff. Something to keep in mind.


==================== PR #6057 by hanabi1224 [closed merged] feat: add `--no-progress-timeout` to `forest-cli f3 ready`
https://github.com/ChainSafe/forest/pull/6057
review sequence: COMMENTED, COMMENTED, DISMISSED, APPROVED
-- INLINE src/cli/subcommands/f3_cmd.rs:106
```diff
@@ -99,6 +103,9 @@ pub enum F3Commands {
         /// The threshold of the epoch gap between chain head and F3 head within which F3 is considered in sync.
         #[arg(long, default_value_t = 20)]
         threshold: usize,
+        /// Exit after F3 making no progress for this duration. It has no effect when `--wait` is not used.
```
>>> Not a blocker, but we could ensure that `--wait` is used without resorting to docs.

https://docs.rs/clap/latest/clap/struct.ArgGroup.html
-- INLINE src/cli/subcommands/f3_cmd.rs:219
```diff
+
+                    if last_progress + no_progress_timeout_duration < Instant::now() {
+                        eprintln!(
+                            "Warning: F3 made no progress in the past {no_progress_timeout}. Exiting..."
+                        );
+                        std::process::exit(3);
```
>>> Why 3?
-- INLINE (reply) src/cli/subcommands/f3_cmd.rs:219
```diff
+
+                    if last_progress + no_progress_timeout_duration < Instant::now() {
+                        eprintln!(
+                            "Warning: F3 made no progress in the past {no_progress_timeout}. Exiting..."
+                        );
+                        std::process::exit(3);
```
>>> Let's use some constants to easily indicate the exit code cause.


==================== PR #6060 by hanabi1224 [closed merged] fix: load actor bundles in `forest-tool archive diff`
https://github.com/ChainSafe/forest/pull/6060
review sequence: APPROVED
-- CONVERSATION
@akaladarshi


==================== PR #6062 by Signor1 [closed] Extend cargo-spellcheck to cover entire repository including .github directory
https://github.com/ChainSafe/forest/pull/6062
review sequence: CHANGES_REQUESTED, CHANGES_REQUESTED
-- REVIEW CHANGES_REQUESTED
The dictionary should not contain entries such as class or module names, different casing of wasm, implementer/implementor (just choose one) etc. Some features of hunspell could be used to limit the number of entries (refer to https://github.com/ChainSafe/forest/blob/main/.config/spellcheck.md). Please ensure all entries in the dictionary are correct and needed (refer to https://github.com/ChainSafe/forest/blob/main/.config/spellcheck.md#tips for stuff like NV22).

Also, if cargos-spellcheck is used across the board, the javascript one at docs/ should be removed - there's no point in having two. Refer to @hanabi1224 comment in your previus PR https://github.com/ChainSafe/forest/pull/6058#issuecomment-3275256425.
-- CONVERSATION
@Signor1 There are still a lot of entries that shouldn't be there. Types and variables should be in backticks (it should prevent the spellcheck from complaining about them). Some short forms shouldn't be short. F1..F28 - it's not an entry for the dictionary. Same for v0..v14 and others like this. Also, some names have bad casing, e.g., Devops should be DevOps.
-- CONVERSATION
Also - feel free to add https://github.com/ChainSafe/forest/tree/main/documentation to exceptions, i.e., don't run checks there. This is outdated and unmaintained; the pages there will be either migrated manually to new docs or removed.
-- CONVERSATION
Thanks for giving it a stab. I decided to close the spellcheck issues via https://github.com/ChainSafe/forest/pull/6708 which aligns more with what is needed.
-- INLINE .config/forest.dic:1
```diff
@@ -1,146 +1,682 @@
 73
```
>>> @Signor1 Thanks for contributing. This comment has to be addressed. You added a lot of entries that shouldn't have been there. Instead, fixes should be made in the offending places. Refer to https://github.com/ChainSafe/forest/blob/main/.config/spellcheck.md for tips.


==================== PR #6065 by hanabi1224 [closed merged] fix: remove invalid chars in prometheus metrics
https://github.com/ChainSafe/forest/pull/6065
review sequence: APPROVED


==================== PR #6068 by hanabi1224 [closed merged] feat: add `--index-backfill-epochs` to `forest-tool api serve`
https://github.com/ChainSafe/forest/pull/6068
review sequence: APPROVED
-- CONVERSATION
@akaladarshi


==================== PR #6070 by hanabi1224 [closed merged] fix(ci): ignore `eth_newBlockFilter` test in `forest-tool api test-stateful`
https://github.com/ChainSafe/forest/pull/6070
review sequence: APPROVED


==================== PR #6071 by hanabi1224 [closed merged] fix(ci): rpc parity test
https://github.com/ChainSafe/forest/pull/6071
review sequence: DISMISSED, APPROVED


==================== PR #6073 by hanabi1224 [closed merged] fix: improve error messages in `forest-cli state compute`
https://github.com/ChainSafe/forest/pull/6073
review sequence: APPROVED


==================== PR #6074 by hanabi1224 [closed merged] feat: forest-cli snapshot export-diff
https://github.com/ChainSafe/forest/pull/6074
review sequence: COMMENTED, DISMISSED, DISMISSED, APPROVED
-- REVIEW DISMISSED
LGTM
-- INLINE src/cli/subcommands/snapshot_cmd.rs:173
```diff
+                    calibnet::NETWORK_COMMON_NAME
+                } else {
+                    raw_network_name.as_str()
+                };
+
+                let depth = depth.unwrap_or_else(|| from - to);
```
>>> ```suggestion
                let depth = depth.unwrap_or_else(|| from - to);
                ensure!(depth > 0, "depth must be positive");
```


==================== PR #6076 by dependabot[bot] [closed merged] chore(deps): bump actions/setup-node from 4 to 5
https://github.com/ChainSafe/forest/pull/6076
review sequence: APPROVED


==================== PR #6077 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 3 updates
https://github.com/ChainSafe/forest/pull/6077
review sequence: APPROVED


==================== PR #6079 by hanabi1224 [closed merged] feat: add prometheus metrics `network_version` and `actor_version`
https://github.com/ChainSafe/forest/pull/6079
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW COMMENTED
Let's update https://docs.forest.chainsafe.io/reference/metrics?
-- REVIEW DISMISSED
LGTM
-- INLINE src/daemon/mod.rs:213
```diff
+        let get_chain_head_height = move || chain_store.heaviest_tipset().epoch();
+        let chain_store = ctx.state_manager.chain_store().clone();
+        let get_chain_head_network_version = move || {
+            let epoch = chain_store.heaviest_tipset().epoch();
+            chain_store.chain_config.network_version(epoch)
+        };
```
>>> Do you think it resolves the issue we had with calibnet nodes not updated? https://github.com/ChainSafe/forest/issues/5141#issuecomment-3291159452
-- INLINE (reply) src/daemon/mod.rs:213
```diff
+        let get_chain_head_height = move || chain_store.heaviest_tipset().epoch();
+        let chain_store = ctx.state_manager.chain_store().clone();
+        let get_chain_head_network_version = move || {
+            let epoch = chain_store.heaviest_tipset().epoch();
+            chain_store.chain_config.network_version(epoch)
+        };
```
>>> Let me rephrase; you're checking what the network is _supposed_ to be according to the schedule. What is more useful for monitoring the health of the nodes, is checking the _actual_ network version (though the expected version is also good to have, so consumers can set up an alarm if there's a mismatch).
-- INLINE docs/docs/users/reference/metrics.md:19
```diff
@@ -16,7 +16,11 @@ title: Metrics
 | `peer_failure_total`           | Counter   | Count   | Total number of failed peer requests                                                         |
 | `full_peers`                   | Gauge     | Count   | Number of healthy peers recognized by the node                                               |
 | `bad_peers`                    | Gauge     | Count   | Number of bad peers recognized by the node                                                   |
+| `network_height`               | Gauge     | Count   | The current network height                                                                   |
```
>>> It'd be good to explain the difference between those metrics in the docs (dedicated section).

```
head_epoch 3021420
network_height 3021421
expected_network_height 3023095
```
-- INLINE (reply) docs/docs/users/reference/metrics.md:19
```diff
@@ -16,7 +16,11 @@ title: Metrics
 | `peer_failure_total`           | Counter   | Count   | Total number of failed peer requests                                                         |
 | `full_peers`                   | Gauge     | Count   | Number of healthy peers recognized by the node                                               |
 | `bad_peers`                    | Gauge     | Count   | Number of bad peers recognized by the node                                                   |
+| `network_height`               | Gauge     | Count   | The current network height                                                                   |
```
>>> @hanabi1224 It's not exactly a duplicate, is it? I mean, the values differ, and from what I saw, `head_epoch` is not available just when the node starts.


==================== PR #6081 by hanabi1224 [closed merged] fix: some metrics get duplicated after GC
https://github.com/ChainSafe/forest/pull/6081
review sequence: DISMISSED, DISMISSED, APPROVED


==================== PR #6083 by hanabi1224 [closed merged] fix: prefer `Tipset::persist` over `Block::persist`
https://github.com/ChainSafe/forest/pull/6083
review sequence: APPROVED
-- REVIEW APPROVED
Does this fix anything in particular?


==================== PR #6086 by hanabi1224 [closed merged] feat: set nv27 mainnet upgrade epoch
https://github.com/ChainSafe/forest/pull/6086
review sequence: APPROVED
-- CONVERSATION
@hanabi1224 Could you please check that Forest outputs the same stateroot after the migration as Lotus?


==================== PR #6087 by akaladarshi [closed merged] refactor(actor_state): allow easy version upgrades
https://github.com/ChainSafe/forest/pull/6087
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
In general, anything blocking usage of the `Default` trait instead of the `default_latest_version` function?
-- INLINE src/lotus_json/filter_estimate.rs:31
```diff
-    }
+                #[cfg(test)]
+                fn snapshots() -> Vec<(serde_json::Value, Self)> {
+                    vec![(
+                        json!({
+                            "PositionEstimate": "0",
```
>>> Was this incorrect?
-- INLINE src/shim/actors/builtin/account/mod.rs:28
```diff
@@ -25,7 +25,7 @@ pub enum State {
 }
 
 impl State {
-    pub fn pubkey_address(&self) -> Address {
+    pub fn pubkey_address(&self) -> fvm_shared2::address::Address {
```
>>> Probably outside of this PR, but do we need to have this old type here? From performance perspective, we're mostly calling the latest actors, so we should avoid conversions on those types and make the "old" versions do conversions.
-- INLINE (reply) src/lotus_json/filter_estimate.rs:31
```diff
-    }
+                #[cfg(test)]
+                fn snapshots() -> Vec<(serde_json::Value, Self)> {
+                    vec![(
+                        json!({
+                            "PositionEstimate": "0",
```
>>> Can you help me understand this? If it was incorrect, why wasn't it caught in any tests?
-- INLINE (reply) src/shim/actors/builtin/account/mod.rs:28
```diff
@@ -25,7 +25,7 @@ pub enum State {
 }
 
 impl State {
-    pub fn pubkey_address(&self) -> Address {
+    pub fn pubkey_address(&self) -> fvm_shared2::address::Address {
```
>>> @coderabbitai create an issue to ensure shims are using the latest versions of the data types.


==================== PR #6088 by joemicky [closed merged] chore: fix a large number of spelling issues in comments
https://github.com/ChainSafe/forest/pull/6088
review sequence: APPROVED
-- CONVERSATION
Thanks!


==================== PR #6090 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/6090
review sequence: APPROVED


==================== PR #6095 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/6095
review sequence: APPROVED


==================== PR #6097 by hanabi1224 [closed merged] feat: allow export spine-only snapshots with `forest-cli snapshot export -d 0`
https://github.com/ChainSafe/forest/pull/6097
review sequence: COMMENTED, APPROVED
-- INLINE src/rpc/methods/chain.rs:335
```diff
@@ -332,14 +332,6 @@ impl RpcMethod<1> for ForestChainExport {
             return Err(anyhow::anyhow!("Another chain export job is still in progress").into());
         }
 
-        let chain_finality = ctx.chain_config().policy.chain_finality;
```
>>> It's still worth putting a warning. Zero-depth snapshots are a niche use case.


==================== PR #6098 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/6098
review sequence: APPROVED


==================== PR #6099 by hanabi1224 [closed merged] fix: CarStream should support large data blocks like F3 snap
https://github.com/ChainSafe/forest/pull/6099
review sequence: COMMENTED, DISMISSED, APPROVED
-- CONVERSATION
no green checkmark!
-- INLINE src/utils/db/car_stream.rs:334
```diff
     Some(header)
 }
 
+pub fn uvi_bytes() -> UviBytes {
+    let mut decoder = UviBytes::default();
+    decoder.set_max_len(usize::MAX);
```
>>> Is this reasoanble? Shouldn't we still put some bounds on it, like at most 4 GiB?


==================== PR #6100 by hanabi1224 [closed merged] feat: populate tipset_by_height cache on startup
https://github.com/ChainSafe/forest/pull/6100
review sequence: APPROVED
-- REVIEW APPROVED
Does this increase startup time?
-- CONVERSATION
@akaladarshi


==================== PR #6104 by sudo-shashank [closed merged] Fix test for eth_newFilter creation
https://github.com/ChainSafe/forest/pull/6104
review sequence: APPROVED


==================== PR #6106 by dependabot[bot] [closed merged] chore(deps): bump hadolint/hadolint-action from 3.2.0 to 3.3.0
https://github.com/ChainSafe/forest/pull/6106
review sequence: APPROVED


==================== PR #6107 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 7 updates
https://github.com/ChainSafe/forest/pull/6107
review sequence: DISMISSED, APPROVED
-- CONVERSATION
blocked by https://github.com/ChainSafe/fil-actor-states/pull/403


==================== PR #6108 by dependabot[bot] [closed merged] chore(deps): bump mermaid from 11.11.0 to 11.12.0 in /docs
https://github.com/ChainSafe/forest/pull/6108
review sequence: APPROVED


==================== PR #6109 by akaladarshi [closed merged] fix: Fix the `GasEstimateMessageGas` API
https://github.com/ChainSafe/forest/pull/6109
review sequence: COMMENTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW DISMISSED
LGTM
-- INLINE src/chain/store/chain_store.rs:664
```diff
@@ -652,7 +652,7 @@ where
 
             for message in unsigned_box.chain(signed_box) {
                 let from_address = &message.from();
-                if applied.contains_key(from_address) {
+                if !applied.contains_key(from_address) {
```
>>> nice
-- INLINE src/cli_shared/cli/config.rs:99
```diff
 }
 
+#[derive(Deserialize, Serialize, PartialEq, Eq, Debug, Clone)]
+#[cfg_attr(test, derive(derive_quickcheck_arbitrary::Arbitrary))]
+pub struct FeeConfig {
+    #[serde(with = "crate::lotus_json")]
```
>>> ```suggestion
    /// Indicates the default max fee for a message
    #[serde(with = "crate::lotus_json")]
```
-- INLINE src/cli_shared/cli/config.rs:105
```diff
+    pub max_fee: TokenAmount,
+}
+
+impl Default for FeeConfig {
+    fn default() -> Self {
+        // This indicates the default max fee for a message,
```
>>> ```suggestion
```
-- INLINE src/cli_shared/cli/config.rs:109
```diff
+    fn default() -> Self {
+        // This indicates the default max fee for a message,
+        // The code is taken from https://github.com/filecoin-project/lotus/blob/release/v1.34.1/node/config/def.go#L39
+        Self {
+            max_fee: TokenAmount::from_atto(
+                num_bigint::BigInt::from_str("70000000000000000").unwrap(),
```
>>> There are more performant initialization options for `TokenAmount` rather than parsing strings. Please check the `TokenAmount` interface.
-- INLINE src/networks/mod.rs:286
```diff
@@ -283,6 +283,7 @@ pub struct ChainConfig {
     pub f3_initial_power_table: Option<Cid>,
     pub enable_indexer: bool,
     pub enable_receipt_event_caching: bool,
+    pub default_max_fee: Option<TokenAmount>,
```
>>> Can you explain why is this an `Option`?
-- INLINE src/rpc/types/mod.rs:58
```diff
     #[schemars(with = "LotusJson<TokenAmount>")]
     #[serde(with = "crate::lotus_json")]
-    max_fee: TokenAmount,
+    pub max_fee: TokenAmount,
+
+    pub maximize_fee_cap: bool,
```
>>> The API was incorrect?
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:2118
```diff
+
+                    let diff = if val1 > val2 {
+                        val1 - val2.clone()
+                    } else {
+                        val2 - val1.clone()
+                    };
```
>>> You can simplify it to `.abs()` https://en.wikipedia.org/wiki/Absolute_difference
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:2122
```diff
+                        val2 - val1.clone()
+                    };
+
+                    // Calculate 5% of the larger value
+                    let max_val = val1.max(val2);
+                    let threshold = (max_val * 5u64).div_floor(100u64);
```
>>> nit: probably easier to reason about with

```
let threshold = 0.05 * val1.abs().max(val2.abs());
```
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:2112
```diff
+                    if val1.is_zero() && val2.is_zero() {
+                        return true;
+                    }
+                    if val1.is_zero() || val2.is_zero() {
+                        return false;
+                    }
```
>>> Worth commenting why this is needed.
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:2094
```diff
+            ))
+            .unwrap(),
+            |forest_api_msg, lotus_api_msg| {
+                let forest_msg = forest_api_msg.message;
+                let lotus_msg = lotus_api_msg.message;
+                // Validate that the gas limit is identical (should be deterministic)
```
>>> ```suggestion
                // Validate that the gas limit is identical (must be deterministic)
```
-- INLINE src/rpc/methods/gas.rs:69
```diff
-    out += msg.gas_premium();
+    let mut out: TokenAmount = fee_in_future.div_floor(1 << 8);
+    if !msg.gas_premium.is_zero() {
+        out += msg.gas_premium();
+    }
+
```
>>> Do we need additional logic here? Adding a zero is trivial.
-- INLINE src/rpc/methods/gas.rs:170
```diff
+// finds 55th percentile instead of median to put negative pressure on gas price
+fn median_gas_premium_calculation(mut prices: Vec<GasMeta>, blocks: u64) -> TokenAmount {
+    prices.sort_by(|a, b| b.price.cmp(&a.price));
+
+    let mut at = BLOCK_GAS_TARGET * blocks / 2;
+    at += BLOCK_GAS_TARGET * blocks / (2 * 20);
```
>>> why `2*20`? Why exactly 55th percentile? Can we use a dedicated crate for computing percentiles?
-- INLINE (reply) src/tool/subcommands/api_cmd/api_compare_tests.rs:2118
```diff
+
+                    let diff = if val1 > val2 {
+                        val1 - val2.clone()
+                    } else {
+                        val2 - val1.clone()
+                    };
```
>>> You can add a trivial `num_traits::Signed` implementation to the `shim::econ::TokenAmount`. Obviously, not a huge deal but it's usually a preference to have such things on a fundamental level and not think about custom abs implementation while reviewing higher-level code.

https://docs.rs/num-traits/0.2.19/num_traits/sign/trait.Signed.html
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:2125
```diff
+                let lotus_fee_cap = &lotus_msg.gas_fee_cap;
+                let forest_premium = &forest_msg.gas_premium;
+                let lotus_premium = &lotus_msg.gas_premium;
+
+                // Helper function to check if values are within 5% of each other
+                let within_bounds = |val1: &TokenAmount, val2: &TokenAmount| -> bool {
```
>>> I'd actually move it to a member function of `TokenAmount` with configurable delta. Then add some tests to the implementation.
-- INLINE (reply) src/rpc/methods/gas.rs:170
```diff
+// finds 55th percentile instead of median to put negative pressure on gas price
+fn median_gas_premium_calculation(mut prices: Vec<GasMeta>, blocks: u64) -> TokenAmount {
+    prices.sort_by(|a, b| b.price.cmp(&a.price));
+
+    let mut at = BLOCK_GAS_TARGET * blocks / 2;
+    at += BLOCK_GAS_TARGET * blocks / (2 * 20);
```
>>> Let's investigate this and ensure the implementation matches assumptions and we understand the reasoning behind magic numbers.
-- INLINE src/shim/econ.rs:90
```diff
+
+impl Neg for TokenAmount {
+    type Output = Self;
+
+    fn neg(self) -> Self::Output {
+        TokenAmount::from_atto(self.atto().neg())
```
>>> @akaladarshi Consider performance and number of calls. Do you see a way to make it more performant wihout sacrificing readability?
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:2131
```diff
+                    return false;
+                }
+
+                if forest_premium.is_negative() || lotus_premium.is_negative() {
+                    return false;
+                }
```
>>> nit: this could be one coalesced into condition
-- INLINE src/shim/econ.rs:434
```diff
+        if self.is_positive() {
+            Self::one()
+        } else if self.is_negative() {
+            -Self::one()
+        } else {
+            Self::zero()
```
>>> ```suggestion
        self.0.atto().signum().into()
```
`TokenAmount` is just a wrapper around bigint, so it _should_ be fine to just use its methods directly.
-- INLINE (reply) src/shim/econ.rs:434
```diff
+        if self.is_positive() {
+            Self::one()
+        } else if self.is_negative() {
+            -Self::one()
+        } else {
+            Self::zero()
```
>>> @akaladarshi same for other methods where you implement your own logic - the idea of a `shim` is to just pass the methods to underlying types, not handle it on our own (if possible).
-- INLINE src/rpc/methods/gas.rs:353
```diff
+    default_max_fee: &TokenAmount,
+    msg: &mut Message,
+    msg_spec: Option<MessageSendSpec>,
+) -> Result<()> {
+    let gas_limit = msg.gas_limit();
+    anyhow::ensure!(gas_limit > 0, "gas limit must be non-zero for fee capping");
```
>>> ```suggestion
    anyhow::ensure!(gas_limit > 0, "gas limit must be positive for fee capping");
```
-- INLINE src/rpc/methods/gas.rs:364
```diff
+        maximize_fee_cap = spec.maximize_fee_cap;
+        max_fee = spec.max_fee;
+    }
+
+    max_fee = if max_fee.is_zero() {
+        default_max_fee.clone()
```
>>> is cloning required here?
-- INLINE src/rpc/methods/gas.rs:370
```diff
+    } else {
+        max_fee
+    };
+
+    let total_fee = msg.gas_fee_cap() * gas_limit;
+    if !max_fee.is_zero() && (maximize_fee_cap || total_fee.gt(&max_fee)) {
```
>>> is usage of `.gt` needed? Can we use more expressive `>` ?


==================== PR #6112 by sudo-shashank [closed merged] Fix eth_new_block_filter test
https://github.com/ChainSafe/forest/pull/6112
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
Does this make sense @elmattic ?


==================== PR #6114 by akaladarshi [closed merged] spec: Add `ChainGetFinalizedTipset` API
https://github.com/ChainSafe/forest/pull/6114
review sequence: CHANGES_REQUESTED, APPROVED
-- INLINE src/rpc/methods/chain.rs:141
```diff
+    const NAME: &'static str = "Filecoin.ChainGetFinalizedTipSet";
+    const PARAM_NAMES: [&'static str; 0] = [];
+    const API_PATHS: BitFlags<ApiPaths> = make_bitflags!(ApiPaths::V1);
+    const PERMISSION: Permission = Permission::Read;
+    const DESCRIPTION: Option<&'static str> = Some(
+        "Returns the latest finalized tipset. It uses the f3 instance or Expected Consensus (EC) definition (head - 900 epochs) to get the finalized tipset.",
```
>>> ```suggestion
        "Returns the latest F3 finalized tipset, or falls back to EC finality if F3 is not operational on the node or if the F3 finalized tipset is further back than EC finalized tipset.",
```
Let's just use the text from https://github.com/filecoin-project/FIPs/pull/1199?
-- INLINE src/rpc/methods/chain.rs:133
```diff
 }
 
+/// Returns the latest finalized tipset.
+/// It uses the current F3 instance to determine the finalized tipset.
+/// If F3 is operational and finalizing in this node. If not, it will fall back
+/// to the Expected Consensus (EC) finality definition of head - 900 epochs.
```
>>> ```suggestion
```
redundant
-- INLINE src/rpc/methods/chain.rs:148
```diff
+
+    type Params = ();
+    type Ok = Tipset;
+
+    async fn handle(_ctx: Ctx<impl Blockstore>, (): Self::Params) -> Result<Self::Ok, ServerError> {
+        Err(anyhow::anyhow!("ChainGetFinalizedTipset is not yet implemented").into())
```
>>> There's a dedicated method for this.

https://github.com/ChainSafe/forest/blob/22d626ceb236426afc737593bea8fdcbbc8f8f83/src/rpc/error.rs#L63-L74


==================== PR #6117 by akaladarshi [closed merged] fix(links): update the script to use latest calibnet snapshot
https://github.com/ChainSafe/forest/pull/6117
review sequence: APPROVED
-- CONVERSATION
> @LesnyRumcajs I guess for `db-migration-checks` CI we will need to update the docker image to point to latest snapshot as it uses (auto-download-snapshot).

I think we can actually be more pragmatic. We don't expect anyone to run Forest pre `0.30.0` due to the recent network upgrades. Let's just bump the tag to `v0.30.0` and be done with it.


==================== PR #6128 by elmattic [closed merged] Add new subcommands for `forest-cli snasphot`
https://github.com/ChainSafe/forest/pull/6128
-- CONVERSATION
no green checkmark, no review!


==================== PR #6150 by hanabi1224 [closed merged] test: validate metrics compatibility with Grafana scrapper
https://github.com/ChainSafe/forest/pull/6150
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
@hanabi1224 Could you please verify that this check fails on the Forest version (AFAIR the one that was used for NV27 calibnet) that had invalid characters in the metrics?
-- REVIEW APPROVED
LGTM!
-- CONVERSATION
> @LesnyRumcajs I believe the invalid chars have been covered by this unit test: https://github.com/ChainSafe/forest/pull/6150/files#diff-086c82375fc31c5ee660c5cf4ab8b7da381735d9bf6cbed910f53f4333465d16R37

Yeah, but the unit test is a synthetic case here. I'd like to confirm that the test fails when Forest reports bad metrics. A one off manual check would suffice.


==================== PR #6167 by hanabi1224 [closed merged] feat: `forest-tool state compute`
https://github.com/ChainSafe/forest/pull/6167
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, CHANGES_REQUESTED, APPROVED
-- REVIEW CHANGES_REQUESTED
I understand those are short-lived commands, but panics are really a bad user experience. Let's propagate errors with helpful messages, by the book.
-- INLINE src/tool/subcommands/state_compute_cmd.rs:145
```diff
+                db.clone(),
+                db.clone(),
+                chain_config,
+                genesis_header,
+            )
+            .unwrap(),
```
>>> why `unwrap`? Is it infallible?
-- INLINE src/tool/subcommands/state_compute_cmd.rs:147
```diff
+                chain_config,
+                genesis_header,
+            )
+            .unwrap(),
+        );
+        let state_manager = Arc::new(StateManager::new(chain_store.clone()).unwrap());
```
>>> why `unwrap`? Is it infallible?
-- INLINE src/tool/subcommands/state_compute_cmd.rs:109
```diff
+        }
+        Ok(())
+    }
+}
+
+/// Replay state computation with a db snapshot
```
>>> ```suggestion
/// Replay state computation with a db snapshot
/// To be used in conjunction with `forest-tool state compute`.
```
-- INLINE src/tool/subcommands/state_compute_cmd.rs:90
```diff
+            chain_store.heaviest_tipset(),
+            ResolveNullTipset::TakeOlder,
+        )?;
+        let epoch = ts.epoch();
+        SettingsStoreExt::write_obj(&db.tracker, crate::db::setting_keys::HEAD_KEY, ts.key())?;
+        let state_manager = Arc::new(StateManager::new(chain_store.clone()).unwrap());
```
>>> why `unwrap`? Is it infallible?
-- INLINE src/tool/subcommands/state_compute_cmd.rs:81
```diff
+                db.clone(),
+                db.clone(),
+                chain_config,
+                genesis_header,
+            )
+            .unwrap(),
```
>>> why `unwrap`? Is it infallible?


==================== PR #6171 by akaladarshi [closed merged] feat: enable V2 API support for basic Eth and Filecoin RPC methods
https://github.com/ChainSafe/forest/pull/6171
review sequence: CHANGES_REQUESTED, APPROVED
-- INLINE src/rpc/reflect/mod.rs:140
```diff
     }
 
+    // Remove this helper once all RPC methods are migrated to V2.
+    // We're incrementally migrating methods to V2 support. Once complete,
+    // update all() to include V2 and remove this temporary helper.
+    pub const fn with_v2() -> BitFlags<Self> {
```
>>> The method name is a bit misleading. When calling `ApiPaths::with_v2` I'd expect **only** the `v2` to be set.

```suggestion
    pub const fn all_with_v2() -> BitFlags<Self> {
```

For the logic - it'd be cleaner to call `all` and just set `v2`.


==================== PR #6174 by ADobrodey [closed merged] feat: push PR images to GHCR when a custom label is provided
https://github.com/ChainSafe/forest/pull/6174
review sequence: CHANGES_REQUESTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW CHANGES_REQUESTED
As mentioned, PR branches must not overwrite the `edge` tag.
-- REVIEW APPROVED
LGTM
-- CONVERSATION
I think @hanabi1224 idea is quite a good one and definitely more flexible. So it'd be basically a workflow dispatch with some inputs that would build the image on anything.

>by specifying branch and tag name

I'd recommend specifying `commit` and not branch (or allow both) in case one would like to publish an image from an earlier commit in a given branch (perhaps to check for regressions).

@ADobrodey what do you think?
-- INLINE (reply) .github/workflows/docker.yml:203
```diff
         uses: docker/build-push-action@v6
         with:
           context: /tmp/forest
           file: ./Dockerfile-ci
-          tags: ${{ steps.metafat.outputs.tags }}
+          tags: ${{ contains(github.event.pull_request.labels.*.name, 'push-fat-to-ghcr') && steps.metafatpr.output.tags || steps.metafat.outputs.tags }}
```
>>> @ADobrodey I believe this comment is relevant.
-- INLINE .github/workflows/docker-dev.yml:23
```diff
+jobs:
+  build-and-push-docker-image:
+    name: Build image and push to GHCR
+    runs-on: ubuntu-24.04
+    # Run the job only if the PR is not a draft.
+    # This is done to limit the runner cost.
```
>>> This comment is incorrect. Leftover from copypasta?
-- INLINE .github/workflows/docker-dev.yml:59
```diff
+        with:
+          file: ./Dockerfile
+          tags: ${{ steps.metafat.outputs.tags }}
+          labels: ${{ steps.metafat.outputs.labels }}
+          push: true
+          # Compile Docker image only for AMD64 for a regular PR to save some CI time.
```
>>> This comment is incorrect (this workflow is NOT part of a CI). Leftover from copypasta?
-- INLINE .github/workflows/docker-dev.yml:30
```diff
+    timeout-minutes: 30
+    steps:
+      - name: Checkout code
+        uses: actions/checkout@v5
+        with:
+          ref: ${{ inputs.branch || inputs.commit }}
```
>>> Do we even need two inputs? It seems to me we can just do one, which would avoid checking which one is set. Plus, then you could mark it as required and avoid conditionals.


==================== PR #6175 by dependabot[bot] [closed merged] chore(deps): bump actions/setup-node from 5 to 6
https://github.com/ChainSafe/forest/pull/6175
review sequence: APPROVED


==================== PR #6176 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group in /docs with 5 updates
https://github.com/ChainSafe/forest/pull/6176
review sequence: APPROVED


==================== PR #6180 by hanabi1224 [closed merged] fix: ignore a few RUSTSECs
https://github.com/ChainSafe/forest/pull/6180
review sequence: APPROVED
-- CONVERSATION
Hopefully, we can remove the ignores (at least some of them) once https://github.com/Keats/tera/issues/978 is resolved and a new version is released. I believe it's the only package we depend on directly that uses those unmaintained crates.


==================== PR #6183 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/6183
review sequence: APPROVED


==================== PR #6186 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/6186
review sequence: APPROVED


==================== PR #6189 by hanabi1224 [closed merged] fix(rpc): improve performance of eth_GetBlockByNumber
https://github.com/ChainSafe/forest/pull/6189
review sequence: APPROVED
-- REVIEW APPROVED
LGTM! Might be worth a changelog entry.
-- CONVERSATION
@hanabi1224 did you check performance gain on your side?
-- CONVERSATION
> > @hanabi1224 did you check performance gain on your side?
> 
> @LesnyRumcajs Just checked calibnet@3141000 with the curl command in #5633 Forest: `Total: 0.001504s` Lotus: `Total: 0.007101s`

I mean, the performance gain between current `main` and this PR.


==================== PR #6191 by pennylees [closed merged] chore: remove repetitive word
https://github.com/ChainSafe/forest/pull/6191
review sequence: APPROVED, APPROVED
-- REVIEW APPROVED
Thanks.
-- CONVERSATION
@pennylees could you please sign the CLA?
-- CONVERSATION
@pennylees Your commits also need to be signed.
-- CONVERSATION
@pennylees Here's a guide how to do it https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits. Note that you will need to amend your previous commits so that they're signed - it should be as easy as `git rebase origin/main` once you have everything set up.


==================== PR #6192 by hanabi1224 [closed merged] doc: changelog for #6189
https://github.com/ChainSafe/forest/pull/6192
review sequence: APPROVED


==================== PR #6193 by hanabi1224 [closed merged] feat: rewind chain head on bad fork (wrong actor bundle)
https://github.com/ChainSafe/forest/pull/6193
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW APPROVED
LGTM
-- INLINE src/networks/actors_bundle.rs:122
```diff
     pub network: NetworkChain,
     pub version: String,
     #[serde_as(as = "DisplayFromStr")]
     pub bundle_cid: Cid,
+    #[educe(PartialEq(ignore))]
     pub manifest: BuiltinActorManifest,
```
>>> why do we ignore it for `PartialEq`?
-- INLINE src/networks/mod.rs:427
```diff
@@ -422,6 +424,26 @@ impl ChainConfig {
             .map(|(height, _)| *height)
     }
 
+    pub fn network_height_with_actor_bundle(
```
>>> Can we have some unit tests and docs here? Also, the return type is complex - could we contrive a type aggregating it?
-- INLINE src/shim/machine/manifest.rs:193
```diff
@@ -187,6 +190,18 @@ impl BuiltinActorManifest {
     pub fn builtin_actors(&self) -> impl ExactSizeIterator<Item = (BuiltinActor, Cid)> + '_ {
         self.builtin2cid.iter().map(|(k, v)| (*k, *v)) // std::iter::Copied doesn't play well with the tuple here
     }
+    /// Get the actor bundle metadata
```
>>> It's trivial, but a unit test for better coverage would be nice.
-- INLINE src/state_manager/mod.rs:212
```diff
+    /// Returns the currently tracked heaviest tipset.
+    pub fn heaviest_tipset(&self) -> Arc<Tipset> {
+        self.chain_store().heaviest_tipset()
+    }
+
+    /// Returns the currently tracked heaviest tipset and rewind to a most recent valid one if necessary.
```
>>> Could you expand on what valid means in this context?


==================== PR #6196 by dependabot[bot] [closed merged] chore(deps): bump actions/upload-artifact from 4 to 5
https://github.com/ChainSafe/forest/pull/6196
review sequence: APPROVED


==================== PR #6197 by dependabot[bot] [closed merged] chore(deps): bump mermaid from 11.12.0 to 11.12.1 in /docs in the patch-versions group
https://github.com/ChainSafe/forest/pull/6197
review sequence: APPROVED


==================== PR #6199 by dependabot[bot] [closed merged] chore(deps): bump actions/download-artifact from 5 to 6
https://github.com/ChainSafe/forest/pull/6199
review sequence: APPROVED
-- CONVERSATION
The checks seemed to have failed to slow download speed of proofs from CF. I re-triggered them.


==================== PR #6202 by hanabi1224 [closed merged] chore: replace deprecated paste crate with pastey
https://github.com/ChainSafe/forest/pull/6202
review sequence: APPROVED


==================== PR #6206 by hanabi1224 [closed merged] fix(rpc): batch request failures
https://github.com/ChainSafe/forest/pull/6206
review sequence: DISMISSED, APPROVED


==================== PR #6216 by hanabi1224 [closed merged] fix: mute go-f3 dht/RtRefreshManager log
https://github.com/ChainSafe/forest/pull/6216
review sequence: APPROVED


==================== PR #6217 by dependabot[bot] [closed merged] chore(deps): bump github.com/consensys/gnark-crypto from 0.19.0 to 0.19.1 in /f3-sidecar
https://github.com/ChainSafe/forest/pull/6217
review sequence: APPROVED


==================== PR #6219 by hanabi1224 [closed merged] fix(rpc): register RPC methods for different versions into different modules
https://github.com/ChainSafe/forest/pull/6219
review sequence: COMMENTED, DISMISSED
-- REVIEW DISMISSED
LGTM
-- INLINE src/rpc/methods/chain.rs:1000
```diff
+
+    type Params = (ApiTipsetKey,);
+    type Ok = Tipset;
+
+    async fn handle(_: Ctx<impl Blockstore>, _: Self::Params) -> Result<Self::Ok, ServerError> {
+        Err(anyhow::anyhow!("Filecoin.ChainGetTipSet for V2 is not yet implemented").into())
```
>>> should we rather return https://github.com/ChainSafe/forest/blob/a8e0dbca46e5c8385050bca62ea25e5067d6db79/src/rpc/error.rs#L76-L82?


==================== PR #6225 by hanabi1224 [closed merged] fix(rpc): method name of V0 only RPC methods
https://github.com/ChainSafe/forest/pull/6225
review sequence: APPROVED


==================== PR #6226 by hanabi1224 [closed merged] chore: upgrade rust to 1.91.0
https://github.com/ChainSafe/forest/pull/6226
review sequence: APPROVED
-- REVIEW APPROVED
❤️ 🦀


==================== PR #6227 by hanabi1224 [closed merged] fix(rpc): avoid unecessarily cloning Tipset
https://github.com/ChainSafe/forest/pull/6227
review sequence: APPROVED
-- REVIEW APPROVED
rock-solid


==================== PR #6228 by akaladarshi [closed merged] chore: update known blocks
https://github.com/ChainSafe/forest/pull/6228
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE .github/CHECKPOINT_ISSUE_TEMPLATE.md:23
```diff
     # download the latest snapshot.
     # =============================
-    # - calibnet ~3G, ~1min on a droplet
-    # - mainnet ~60G, ~15mins on a droplet
+    # - calibnet ~6G, ~5min on a droplet
+    # - mainnet ~74G, ~20mins on a droplet
```
>>> The math doesn't add up really. 😆
-- INLINE .github/CHECKPOINT_ISSUE_TEMPLATE.md:23
```diff
     # download the latest snapshot.
     # =============================
-    # - calibnet ~3G, ~1min on a droplet
-    # - mainnet ~60G, ~15mins on a droplet
+    # - calibnet ~6G, ~5min on a droplet
+    # - mainnet ~74G, ~60mins on a droplet
```
>>> It wasn't really my experience, but it doesn't really matter.


==================== PR #6229 by dependabot[bot] [closed merged] chore(deps): bump tar from 7.5.1 to 7.5.2 in /docs
https://github.com/ChainSafe/forest/pull/6229
review sequence: APPROVED


==================== PR #6231 by hanabi1224 [closed merged] feat(rpc): ChainGetTipset  v2
https://github.com/ChainSafe/forest/pull/6231
review sequence: CHANGES_REQUESTED, APPROVED
-- REVIEW APPROVED
LGTM
-- INLINE src/rpc/methods/chain/types/tests.rs:1
```diff

```
>>> What is this testing? We should trust that `Serialize` and `Deserialize` from `serde` work fine; no need to test our deps, especially on basic usage.
-- INLINE src/rpc/methods/chain/types.rs:36
```diff
+lotus_json_with_self!(TipsetSelector);
+
+impl TipsetSelector {
+    /// Validate ensures that the TipSetSelector is valid. It checks that only one of
+    /// the selection criteria is specified. If no criteria are specified, it returns
+    /// nil, indicating that the default selection criteria should be used as defined
```
>>> Is this copied from Lotus? There's no `nil`, no?
-- INLINE src/rpc/methods/chain/types.rs:54
```diff
+        }
+        if criteria != 1 {
+            anyhow::bail!(
+                "exactly one tipset selection criteria must be specified, found: {criteria}"
+            )
+        }
```
>>> `ensure!`
-- INLINE src/rpc/methods/chain/types.rs:37
```diff
+
+impl TipsetSelector {
+    /// Validate ensures that the TipSetSelector is valid. It checks that only one of
+    /// the selection criteria is specified. If no criteria are specified, it returns
+    /// nil, indicating that the default selection criteria should be used as defined
+    /// by the Lotus API Specification.
```
>>> Unit tests?
-- INLINE src/rpc/methods/chain/types.rs:36
```diff
+impl TipsetSelector {
+    /// Validate ensures that the TipSetSelector is valid. It checks that only one of
+    /// the selection criteria is specified. If no criteria are specified, it returns
+    /// nil, indicating that the default selection criteria should be used as defined
+    /// by the Lotus API Specification.
+    pub fn validate(&self) -> anyhow::Result<()> {
```
>>> The logic is a bit wasteful - if there's key AND there's tag then it's just wasteful to to height validation; we could break early with an error.

I guess a `match` would be a more idiomatic choice for this kind of logic.
-- INLINE src/rpc/methods/chain/types.rs:76
```diff
+    ///
+    /// A zero-valued height is considered to be valid.
+    pub fn validate(&self) -> anyhow::Result<()> {
+        if self.at.is_negative() {
+            anyhow::bail!("invalid tipset height: epoch cannot be less than zero");
+        }
```
>>> `ensure!`
-- INLINE src/rpc/methods/chain/types.rs:104
```diff
+    pub tag: Option<TipsetTag>,
+}
+lotus_json_with_self!(TipsetAnchor);
+
+impl TipsetAnchor {
+    /// Validate ensures that the TipSetAnchor is valid. It checks that at most one
```
>>> ```suggestion
    /// Validate ensures that the [`TipsetAnchor`] is valid. It checks that at most one
```
If we're using Rust docs, let's have proper links. There are more occurrences out there.
-- INLINE src/rpc/methods/chain/types.rs:108
```diff
+impl TipsetAnchor {
+    /// Validate ensures that the TipSetAnchor is valid. It checks that at most one
+    /// of TipSetKey or TipSetTag is specified. Otherwise, it returns an error.
+    ///
+    /// Note that a nil or a zero-valued anchor is valid, and is considered to be
+    /// equivalent to the default anchor, which is the tipset tagged as "finalized".
```
>>> Let's not copy over Lotus comments as they are - if we're translating code from Go to Rust, we might as well translate some concepts.
-- INLINE src/rpc/methods/chain/types.rs:114
```diff
+    pub fn validate(&self) -> anyhow::Result<()> {
+        if self.key.0.is_some() && self.tag.is_some() {
+            anyhow::bail!("invalid tipset anchor: at most one of key or tag must be specified");
+        }
+        // Zero-valued anchor is valid, and considered to be an equivalent to whatever the API decides the default to be.
+        Ok(())
```
>>> either `ensure!` or perhaps `match` (it's more idiomatic Rust than conditionals here)
-- INLINE src/rpc/methods/chain/types.rs:1
```diff

```
>>> Let's have some coverage for the logic in this file.
-- INLINE src/rpc/methods/chain.rs:57
```diff
@@ -54,6 +54,21 @@ use tokio_util::sync::CancellationToken;
 
 const HEAD_CHANNEL_CAPACITY: usize = 10;
 
+// SafeHeightDistance is the distance from the latest tipset, i.e. heaviest, that
```
>>> Again, let's not just copy over Lotus docs.
```suggestion
// `SAFE_HEIGHT_DISTANCE` is the distance from the latest tipset, i.e. heaviest, that
```
-- INLINE src/rpc/methods/chain.rs:1002
```diff
-        Ok(ts)
+        if let Some(tsk) = &tsk {
+            let ts = ctx.chain_index().load_required_tipset(tsk)?;
+            Ok(ts)
+        } else {
+            // Error message here matches lotus
```
>>> Do we need to match the Lotus error? This would actually be a very confusing message for me.
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:460
```diff
+            key: None.into(),
+            height: None,
+            tag: None,
+        },))?)
+        .policy_on_rejected(PolicyOnRejected::PassWithQuasiIdenticalError),
+        RpcTest::identity(ChainGetTipSetV2::request((TipsetSelector {
```
>>> Won't this test be flaky? What if we're at the epoch boundary, e.g., 29s and Forest's latest is X and Lotus's is X+1?


==================== PR #6235 by hanabi1224 [closed merged] fix: a potential deadlock in chain follower fork handling
https://github.com/ChainSafe/forest/pull/6235
review sequence: DISMISSED, APPROVED
-- REVIEW DISMISSED
changelog?


==================== PR #6236 by akaladarshi [closed merged] chore(release): prepare v0.30.3
https://github.com/ChainSafe/forest/pull/6236
review sequence: APPROVED, COMMENTED
-- CONVERSATION
https://github.com/ChainSafe/forest/blob/e31b3cb1355fbbd631ef1703cb077d69cbe34be2/documentation/src/developer_documentation/release_checklist.md?plain=1#L25
-- INLINE (reply) Cargo.toml:6
```diff
 name = "forest-filecoin"
-version = "0.30.2"
+version = "0.30.3"
 authors = ["ChainSafe Systems <info@chainsafe.io>"]
 repository = "https://github.com/ChainSafe/forest"
 edition = "2024"
```
>>> Bad bot


==================== PR #6238 by hanabi1224 [closed merged] chore(deps): bump go deps
https://github.com/ChainSafe/forest/pull/6238
review sequence: APPROVED


==================== PR #6239 by hanabi1224 [closed] fix: in chain follower, only merge freshly mined blocks
https://github.com/ChainSafe/forest/pull/6239
review sequence: COMMENTED
-- CONVERSATION
@hanabi1224 I think we're good without it, correct?
-- INLINE src/chain_sync/chain_follower.rs:651
```diff
+        let tipset_timestamp = tipset.first_block().header.timestamp as i64;
+        let now_timestamp = Utc::now().timestamp();
+        // Only merge blocks that are freshly mined
+        let mut merged_blocks: Option<HashSet<_>> = if tipset_timestamp
+            + self.cs.chain_config().block_delay_secs as i64 * 2
+            >= now_timestamp
```
>>> what if a bad fork arrives right after?


==================== PR #6240 by hanabi1224 [closed merged] fix: log chain exchange failures in chain follower
https://github.com/ChainSafe/forest/pull/6240
review sequence: APPROVED


==================== PR #6241 by hanabi1224 [closed merged] fix: do not mark time traveling blocks as bad
https://github.com/ChainSafe/forest/pull/6241
review sequence: APPROVED


==================== PR #6243 by hanabi1224 [closed merged] chore(release): prepare v0.30.4
https://github.com/ChainSafe/forest/pull/6243
review sequence: APPROVED


==================== PR #6244 by hanabi1224 [closed merged] fix: a few RUSTSEC(s)
https://github.com/ChainSafe/forest/pull/6244
review sequence: APPROVED


==================== PR #6250 by dependabot[bot] [closed merged] chore(deps-dev): bump spellchecker-cli from 7.0.0 to 7.0.1 in /docs in the patch-versions group
https://github.com/ChainSafe/forest/pull/6250
review sequence: APPROVED, APPROVED


==================== PR #6252 by efcking [closed merged] chore: minor improvement for docs
https://github.com/ChainSafe/forest/pull/6252
review sequence: APPROVED, CHANGES_REQUESTED, APPROVED
-- REVIEW APPROVED
LGTM, thanks.
-- REVIEW CHANGES_REQUESTED
Oh, I didn't notice it. Your commits need to be signed.


==================== PR #6253 by hanabi1224 [closed merged] fix: avoid unecessary clone(s) in some Eth RPC methods
https://github.com/ChainSafe/forest/pull/6253
review sequence: APPROVED


==================== PR #6254 by hanabi1224 [closed merged] fix: avoid cloning block headers in `validate_block`
https://github.com/ChainSafe/forest/pull/6254
review sequence: APPROVED


==================== PR #6255 by hanabi1224 [closed merged] fix: refine Arc params to reduce pointer cloning
https://github.com/ChainSafe/forest/pull/6255
review sequence: APPROVED


==================== PR #6256 by hanabi1224 [closed merged] chore(deps): bump `golang.org/x/crypto`
https://github.com/ChainSafe/forest/pull/6256
review sequence: APPROVED


==================== PR #6257 by hanabi1224 [closed merged] chore(deps): bump js deps to fix security warnings
https://github.com/ChainSafe/forest/pull/6257
review sequence: APPROVED


==================== PR #6258 by hanabi1224 [closed] chore: enable go greentea GC
https://github.com/ChainSafe/forest/pull/6258
review sequence: COMMENTED
-- REVIEW COMMENTED
I don't think we should enable experimental features unless proven to be working fine and give benefit.


==================== PR #6260 by hanabi1224 [closed merged] fix: error caused by --skip-load-actors
https://github.com/ChainSafe/forest/pull/6260
review sequence: APPROVED


==================== PR #6263 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/6263
-- CONVERSATION
Blocked until @akaladarshi resolves (at least temporarily) the RPC CI issue.
-- CONVERSATION
> I believe there is another issue with the image itself. It complains GLIBC not found

Good catch, I reported it to the Lotus team.
-- CONVERSATION
To be revisited once https://github.com/filecoin-project/lotus/pull/13441 is merged and a fixed image published.
-- CONVERSATION
Need to do first https://github.com/ChainSafe/forest/issues/6213, I'll tackle it later today.


==================== PR #6264 by dependabot[bot] [closed merged] chore(deps): bump tracing-appender from 0.2.3 to 0.2.4 in the patch-versions group
https://github.com/ChainSafe/forest/pull/6264
review sequence: COMMENTED, APPROVED
-- INLINE (reply) Cargo.toml:164
```diff
@@ -161,7 +161,7 @@ nunny = { version = "0.2", features = ["serde", "quickcheck", "schemars1"] }
 openrpc-types = "0.5"
 parity-db = { version = "0.5" }
 parking_lot = { version = "0.12", features = ["deadlock_detection"] }
-pastey = "0.1"
+pastey = "0.2"
```
>>> bad bot


==================== PR #6272 by akaladarshi [closed merged] chore: update know blocks
https://github.com/ChainSafe/forest/pull/6272
review sequence: APPROVED


==================== PR #6274 by hanabi1224 [closed merged] fix: avoid deep-cloning Tipset and FullTipset
https://github.com/ChainSafe/forest/pull/6274
review sequence: APPROVED
-- REVIEW APPROVED
LGTM!


==================== PR #6275 by akaladarshi [closed merged] chore: add `ChainGetEvents` to filter list
https://github.com/ChainSafe/forest/pull/6275
review sequence: COMMENTED, APPROVED
-- INLINE scripts/tests/api_compare/filter-list:4
```diff
@@ -1,2 +1,5 @@
 # This list contains potentially broken methods (or tests) that are ignored.
 # They should be considered bugged, and not used until the root cause is resolved.
+
+# Ignoring ChainGetEvents for now, as we are not sure if we should get duplicated events or not.
```
>>> Let's add a tracking issue to the comment.


==================== PR #6281 by hanabi1224 [closed merged] fix: avoid some unnecessary clones in chain follower
https://github.com/ChainSafe/forest/pull/6281
review sequence: APPROVED
-- REVIEW APPROVED
I like this fight against the clones!


==================== PR #6286 by akaladarshi [closed merged] fix: remove unique events logic from the `ChainGetEvents` API
https://github.com/ChainSafe/forest/pull/6286
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW DISMISSED
LGTM
-- CONVERSATION
> DB migration check in CI is failing because I've removed the `Indices` column from DB so need to create a new migration or maybe go back to old not sure right now.
> 
> I was thinking, if I can get some comments on the PR, to finalise before the migration, it would be helpful 🙏 @hanabi1224 @LesnyRumcajs

Before moving forward without the indices column, I'd like to first see some numbers showing the performance impact for affected methods.
-- CONVERSATION
@akaladarshi could you please mark @coderabbitai comments as irrelevant, incorrect or address them if they make sense?
-- CONVERSATION
Please ensure all @coderabbitai comments are closed.
-- CONVERSATION
@akaladarshi https://github.com/ChainSafe/forest/pull/6286#issuecomment-3745033734
-- INLINE src/rpc/methods/eth/filter/mod.rs:377
```diff
-            .collect();
+        let state_events =
+            match StampedEvent::get_events(ctx.chain_store().blockstore(), events_root) {
+                Ok(e) => e,
+                Err(e) => {
+                    return Err(anyhow::anyhow!("load events amt: {}", e));
```
>>> nit
```suggestion
                    bail!("load events amt: {e}");
```
-- INLINE (reply) src/rpc/methods/eth/filter/mod.rs:377
```diff
-            .collect();
+        let state_events =
+            match StampedEvent::get_events(ctx.chain_store().blockstore(), events_root) {
+                Ok(e) => e,
+                Err(e) => {
+                    return Err(anyhow::anyhow!("load events amt: {}", e));
```
>>> Actually, probably `let state_events = bla.context("failed getting events")?;` would make more sense.
-- INLINE src/shim/executor.rs:398
```diff
+        db: &DB,
+        events_root: &Cid,
+    ) -> anyhow::Result<Vec<StampedEvent>> {
+        let mut events = Vec::new();
+
+        // Try StampedEvent_v4 first (StampedEvent_v4 and StampedEvent_v3 are identical, use v4 here)
```
>>> are there other versions as well?
-- INLINE src/shim/executor.rs:400
```diff
+    ) -> anyhow::Result<Vec<StampedEvent>> {
+        let mut events = Vec::new();
+
+        // Try StampedEvent_v4 first (StampedEvent_v4 and StampedEvent_v3 are identical, use v4 here)
+        if let Ok(amt) = Amt::<StampedEvent_v4, _>::load(events_root, db) {
+            amt.for_each(|_, event| {
```
>>> `for_each_cacheless` is a better option yielding faster iteration if the AMT is not iterated multiple times.
-- INLINE src/state_manager/mod.rs:2015
```diff
             vm.apply_block_messages(&block_messages, epoch, callback)?;
 
-        // step 5: construct receipt root from receipts and flush the state-tree
-        let receipt_root = Amt::new_from_iter(chain_index.db(), receipts)?;
+        // step 5: construct receipt root from receipts
+        let receipt_root = Amtv0::new_from_iter(chain_index.db(), receipts)?;
```
>>> why v0?
-- INLINE src/state_manager/mod.rs:2017
```diff
-        // step 5: construct receipt root from receipts and flush the state-tree
-        let receipt_root = Amt::new_from_iter(chain_index.db(), receipts)?;
+        // step 5: construct receipt root from receipts
+        let receipt_root = Amtv0::new_from_iter(chain_index.db(), receipts)?;
+
+        // step 6: store events AMTs in the blockstore
```
>>> is there corresponding Lotus logic?
-- INLINE src/db/migration/migration_map.rs:159
```diff
@@ -155,6 +156,8 @@ pub(super) static MIGRATIONS: LazyLock<MigrationsMap> = LazyLock::new(|| {
 create_migrations!(
     "0.22.0" -> "0.22.1" @ Migration0_22_0_0_22_1,
     "0.25.3" -> "0.26.0" @ Migration0_25_3_0_26_0,
+    // v0.30.0 is the oldest supported version, and it uses the same schema as v0.26.0.
```
>>> ```suggestion
    // v0.30.5 is the oldest supported version, and it uses the same schema as v0.26.0.
```
The current version is the oldest supported version, no?
-- INLINE src/db/migration/migration_map.rs:160
```diff
@@ -155,6 +156,8 @@ pub(super) static MIGRATIONS: LazyLock<MigrationsMap> = LazyLock::new(|| {
 create_migrations!(
     "0.22.0" -> "0.22.1" @ Migration0_22_0_0_22_1,
     "0.25.3" -> "0.26.0" @ Migration0_25_3_0_26_0,
+    // v0.30.0 is the oldest supported version, and it uses the same schema as v0.26.0.
+    "0.30.0" -> "0.31.0" @ Migration0_26_0_0_31_0,
```
>>> ```suggestion
    "0.30.5" -> "0.31.0" @ Migration0_26_0_0_31_0,
```
-- INLINE src/db/migration/migration_map.rs:160
```diff
@@ -155,6 +156,8 @@ pub(super) static MIGRATIONS: LazyLock<MigrationsMap> = LazyLock::new(|| {
 create_migrations!(
     "0.22.0" -> "0.22.1" @ Migration0_22_0_0_22_1,
     "0.25.3" -> "0.26.0" @ Migration0_25_3_0_26_0,
+    // v0.30.0 is the oldest supported version, and it uses the same schema as v0.26.0.
+    "0.30.0" -> "0.31.0" @ Migration0_26_0_0_31_0,
```
>>> Not sure I get it, normally you are supposed to create LATEST_TO_NEW migration. So it should be rather `Migration0_30_5_0_31_0`.
-- INLINE src/db/migration/v0_31_0.rs:8
```diff
+
+//! Migration logic for databases with the v0.26.0 schema (including v0.30.0) to v0.31.0.
+//! The `Indices` column has been removed as events are now stored as `AMTs` in the blockstore.
+//!
+//! This migration is used for:
+//! - `0.30.0` to `0.31.0` (the oldest supported version)
```
>>> Same as in other comment; I don't see why `0.26.0` is even mentioned here. Forest will migrate from `0.26.0` to `0.30.5` for free anyway.
-- INLINE src/db/migration/v0_31_0.rs:80
```diff
+
+        // Open the new database to migrate data from the old one.
+        // The new database does NOT have the Indices column.
+        let new_db = paritydb_0_31_0::ParityDb::open(&temp_db)?;
+
+        for col in DbColumn::iter() {
```
>>> Aren't there any methods in `ParityDb` to just remove the column from an existing database?
-- INLINE CHANGELOG.md:52
```diff
@@ -49,17 +49,22 @@
 
 - [#6368](https://github.com/ChainSafe/forest/pull/6368): Migrated build and development tooling from Makefile to `mise`. Contributors should install `mise` and use `mise run` commands instead of `make` commands.
 
+- [#6286](https://github.com/ChainSafe/forest/pull/6286) Serve `Filecoin.ChainGetEvents` API directly from the blockstore instead of computing.
```
>>> ```suggestion
```

We don't put in the README things that are not visible to the user in any way.
-- INLINE src/db/migration/v0_31_0.rs:46
```diff
+        info!(
+            "Renaming database directory from {} to {}",
+            old_db.display(),
+            temp_db.display()
+        );
+        // Rename the database directory to the temporary directory
```
>>> ```suggestion
```
The code is pretty self-explanatory.


==================== PR #6289 by hanabi1224 [closed merged] feat: add eth_block_cache to improve performance of a few Eth RPC method
https://github.com/ChainSafe/forest/pull/6289
review sequence: COMMENTED, APPROVED
-- INLINE src/rpc/methods/eth.rs:1419
```diff
-    let block_hash: EthHash = block_cid.into();
-
-    let (state_root, msgs_and_receipts) = execute_tipset(&data, &tipset).await?;
+    static ETH_BLOCK_CACHE: LazyLock<SizeTrackingLruCache<CidWrapper, Block>> =
+        LazyLock::new(|| {
+            const DEFAULT_CACHE_SIZE: NonZeroUsize = nonzero!(500usize);
```
>>> What's the expected memory footprint of a maxed-out eth block cache?


==================== PR #6312 by hanabi1224 [closed merged] feat(rpc): V2 RPC methods StateGetActor and StateGetID
https://github.com/ChainSafe/forest/pull/6312
review sequence: APPROVED
-- REVIEW APPROVED
LGTM


==================== PR #6315 by hanabi1224 [closed merged] fix: do not wrap `Sender` with `Arc`
https://github.com/ChainSafe/forest/pull/6315
review sequence: APPROVED


==================== PR #6318 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 2 updates
https://github.com/ChainSafe/forest/pull/6318
review sequence: APPROVED


==================== PR #6320 by hanabi1224 [closed merged] fix(ci): use official lotus image for devnet check
https://github.com/ChainSafe/forest/pull/6320
review sequence: COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
@hanabi1224 what do we do with this draft?
-- CONVERSATION
@hanabi1224 as far as I remember, I'm not using an official lotus image because it doesn't contain some binaries; I don't remember which ones (lotus-seed maybe?)
-- CONVERSATION
> @LesnyRumcajs could you remind me why we needed a custom Lotus image for network upgrade?

I _think_ it was either because of missing binaries and/or lack of 2k network image. Probably the latter, which was resolved in https://github.com/filecoin-project/lotus/pull/12911.

That said, we still should keep our Lotus Dockerfile and workflows in case we need to work on a specific Lotus commit with some patches.
-- INLINE (reply) scripts/devnet/docker-compose.yml:57
```diff
         condition: service_completed_successfully
     healthcheck:
-      test: >-
-        curl -s -x post -h "content-type: application/json"
-        --data '{ "jsonrpc": "2.0", "method": "filecoin.chainhead", "params": [], "id": 1 }'
-        http://lotus_node:${LOTUS_RPC_PORT}/rpc/v0 || exit 1
```
>>> This doesn't seem correct. DNS name should be taken from the service name (`lotus_node` or overridden by `hostname`). Let me double check.
-- INLINE (reply) scripts/devnet/docker-compose.yml:57
```diff
         condition: service_completed_successfully
     healthcheck:
-      test: >-
-        curl -s -x post -h "content-type: application/json"
-        --data '{ "jsonrpc": "2.0", "method": "filecoin.chainhead", "params": [], "id": 1 }'
-        http://lotus_node:${LOTUS_RPC_PORT}/rpc/v0 || exit 1
```
>>> ah no, `container_name` also overrides it. All good.


==================== PR #6321 by dependabot[bot] [closed merged] chore(deps): bump tower-http from 0.6.7 to 0.6.8 in the patch-versions group
https://github.com/ChainSafe/forest/pull/6321
review sequence: APPROVED


==================== PR #6322 by dependabot[bot] [closed merged] chore(deps): bump prettier from 3.6.2 to 3.7.4 in /docs
https://github.com/ChainSafe/forest/pull/6322
review sequence: APPROVED


==================== PR #6323 by hanabi1224 [closed merged] feat(rpc): add v1 RPC method `Filecoin.FilecoinAddressToEthAddress`
https://github.com/ChainSafe/forest/pull/6323
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1470
```diff
+            FilecoinAddressToEthAddress::request((
+                "t410f744ma4xsq3r3eczzktfj7goal67myzfkusna2hy"
+                    .parse()
+                    .unwrap(),
+                None,
+            ))
```
>>> Let's add all supported categories of addresses to tests, `f0-f4`.
-- INLINE (reply) src/tool/subcommands/api_cmd/api_compare_tests.rs:1472
```diff
+                    .parse()
+                    .unwrap(),
+                None,
+            ))
             .unwrap(),
         ));
```
>>> @hanabi1224 I think the AI has a good point here. I'd rather we have explicit test cases.


==================== PR #6327 by sudo-shashank [closed merged] fix: forest returns 404 for all invalid api paths
https://github.com/ChainSafe/forest/pull/6327
review sequence: CHANGES_REQUESTED, APPROVED
-- REVIEW CHANGES_REQUESTED
This bugfix should have associated tests.
-- REVIEW APPROVED
LGTM


==================== PR #6331 by hanabi1224 [closed merged] feat: forest-tool benchmark blockstore
https://github.com/ChainSafe/forest/pull/6331
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW APPROVED
LGTM
-- CONVERSATION
The optimization for ParityDb is an interesting find, we could definitely try it out. I wouldn't like fjall to be in Forest's dependency tree if we don't use it, though. If you remove it and leave only the benchmark for ParityDb (it'd be easy to check out other DBs in the future), then it'd fine.

Ideally, this would be a part of `cargo bench` and not exposed to users at all.
-- CONVERSATION
> @LesnyRumcajs How about making `forest-tool benchmark` and `fjall` optional and behind a feature flag `benchmark-tools`?

I'd still omit `fjall`; it's something we won't use, most likely. I'd also avoid adding feature flags if possible - they make CI kind of annoying - you compile one thing for a release, then another for tests, then maybe you test with all features on... 

We could think of a `forest-dev` binary that's intended for development purposes and not included in the release artefacts at all.
-- INLINE src/tool/subcommands/benchmark_cmd.rs:253
```diff
@@ -227,6 +250,50 @@ async fn benchmark_exporting(
     Ok(())
 }
 
+async fn benchmark_blockstore(snapshot: PathBuf, db: DbType) -> anyhow::Result<()> {
```
>>> let's split it into `benchmark_import` and `benchmark_traversal`
-- INLINE (reply) src/tool/subcommands/benchmark_cmd.rs:253
```diff
@@ -227,6 +250,50 @@ async fn benchmark_exporting(
     Ok(())
 }
 
+async fn benchmark_blockstore(snapshot: PathBuf, db: DbType) -> anyhow::Result<()> {
```
>>> I understand. It'd just make the code easier to follow if it's on the same level of abstraction, e.g., you go to `benchmark_blockstore` and see it's:

```
fn benchmark_blockstore
  some_output = benchmark_import
  bechmark_traversal(some_output)
```
then you immediately know what is happening. With the current code, one would have to check the entire `benchmark_blockstore` to understand what it actually benchmarks.

Does it make sense? Do you agree?
-- INLINE (reply) src/tool/subcommands/benchmark_cmd.rs:402
```diff
+    }
+}
+
+fn is_dag_cbor_blake2b256(cid: &Cid) -> bool {
+    cid.codec() == DAG_CBOR && cid.hash().code() == u64::from(MultihashCode::Blake2b256)
+}
```
>>> @hanabi1224 is it relevant?


==================== PR #6333 by hanabi1224 [closed] fix(ci): run snap tests on CI for debug and codecov builds
https://github.com/ChainSafe/forest/pull/6333
review sequence: COMMENTED, COMMENTED, CHANGES_REQUESTED
-- REVIEW CHANGES_REQUESTED
There's lots of spam/debug logs in the fetch command. Perhaps you need to setup the logger?
-- INLINE Cargo.toml:276
```diff
 split-debuginfo = "unpacked"
 
+[profile.codecov]
+inherits = "test"
+opt-level = 1
+lto = "off"
```
>>> any reason to disable LTO?
-- INLINE src/wallet/main.rs:1
```diff

```
>>> It's orthogonal to the codecov issue, no? If so, let's have a separate PR for this change.


==================== PR #6336 by hanabi1224 [closed merged] chore: bump rust toolchain to 1.92.0
https://github.com/ChainSafe/forest/pull/6336
review sequence: COMMENTED, APPROVED
-- INLINE Cargo.toml:271
```diff
 [profile.dev]
-debug = 0
+debug = "line-tables-only"
 split-debuginfo = "unpacked"
 
+# Avoid generating any debug information for dependencies
```
>>> Oh cool, I wonder if we could now run the coverage on a regular GH worker?


==================== PR #6337 by hanabi1224 [closed merged] fix: setup tokio runtime with macro in forest-tool and forest-wallet
https://github.com/ChainSafe/forest/pull/6337
review sequence: APPROVED


==================== PR #6340 by hanabi1224 [closed merged] fix(test): speed up debug and codecov tests
https://github.com/ChainSafe/forest/pull/6340
review sequence: COMMENTED, APPROVED
-- REVIEW APPROVED
As discussed, the debug tests are kind of doing the same as coverage tests, so let's get rid of the former in a follow-up PR.
-- INLINE interop-tests/Cargo.toml:1
```diff

```
>>> nice


==================== PR #6344 by hanabi1224 [closed] fix(ci): deprecate quick cargo profile
https://github.com/ChainSafe/forest/pull/6344
review sequence: CHANGES_REQUESTED
-- REVIEW CHANGES_REQUESTED
As discussed, it slows down the local compilation so unless a viable alternative profile is provided, we shouldn't remove it.


==================== PR #6347 by dependabot[bot] [closed merged] chore(deps): bump actions/upload-artifact from 4 to 6
https://github.com/ChainSafe/forest/pull/6347
review sequence: APPROVED


==================== PR #6354 by akaladarshi [closed merged] fix: convert look back limit to actual epoch before searching for msg
https://github.com/ChainSafe/forest/pull/6354
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
LGTM, let's add a changelog entry.
-- CONVERSATION
no green checkmark, no review ❌
-- CONVERSATION
Also, no green checkmark ❌
-- CONVERSATION
no green checkmark, no review!
-- INLINE src/rpc/mod.rs:45
```diff
@@ -41,7 +41,7 @@ pub use methods::*;
 /// Protocol or transport-specific error
 pub use jsonrpsee::core::ClientError;
 
-const LOOKBACK_NO_LIMIT: ChainEpoch = -1;
+pub const LOOKBACK_NO_LIMIT: ChainEpoch = -1;
```
>>> Let's document this constant while we're at it; the name is not self-explanatory.
-- INLINE src/state_manager/mod.rs:1050
```diff
         &self,
         mut current: Tipset,
         message: &ChainMessage,
-        look_back_limit: Option<i64>,
-        allow_replaced: Option<bool>,
+        min_epoch: ChainEpoch,
```
>>> Perhaps `lookback_max_epoch`. `min` is theoretically correct, but with `lookback` to me the meaning is a bit inverted. Not a huge deal, if you don't agree let's leave it as is (but add `lookback` somewhere).
-- INLINE src/state_manager/mod.rs:1104
```diff
+    /// * `message` - The message to search for
+    /// * `look_back_limit` - Maximum number of epochs to search backwards:
+    ///   - `None` or `Some(-1)` (LOOKBACK_NO_LIMIT): Search all the way to genesis
+    ///   - `Some(0)`: No search performed, returns `None` immediately
+    ///   - `Some(N)` where `N > 0`: Search back at most N epochs
+    ///   - `Some(N)` where `N >= current.epoch()`: Same as unlimited (to genesis)
```
>>> ```suggestion
```

Let's not AI generate more documentation than there's actually code.
-- INLINE (reply) src/state_manager/mod.rs:1118
```diff
+            // Search all the way to genesis (epoch 0)
+            _ => 0,
+        };
+
+        self.check_search(current, message, min_epoch, allow_replaced)
     }
```
>>> @akaladarshi It'd be actually nice to implement some tests here.
-- INLINE (reply) src/state_manager/mod.rs:1115
```diff
+            // Limited search: calculate the inclusive lower bound
+            // For ex: limit = 5 at epoch 1000: min_epoch = 996, searches [996, 1000] = 5 epochs
+            Some(limit) if limit > 0 && limit < current_epoch => current_epoch - limit + 1,
+            // Search all the way to genesis (epoch 0)
+            _ => 0,
+        };
```
>>> @akaladarshi is it valid?
-- INLINE CHANGELOG.md:53
```diff
@@ -50,6 +50,7 @@
 ### Fixed
 
 - [#6327](https://github.com/ChainSafe/forest/pull/6327) Fixed: Forest returns 404 for all invalid api paths.
+- [#6354](https://github.com/ChainSafe/forest/pull/6354) Fixed: Correctly calculate the epoch range instead of directly using the look back limit value while searching for messages.
```
>>> ```suggestion

- [#6354](https://github.com/ChainSafe/forest/pull/6354) Fixed: Correctly calculate the epoch range instead of directly using the look back limit value while searching for messages.
```


==================== PR #6355 by hanabi1224 [closed merged] test: unit test for state compute
https://github.com/ChainSafe/forest/pull/6355
review sequence: APPROVED


==================== PR #6356 by AlexeyKrasnoperov [closed] Rename conformance tests policy validate -> custom
https://github.com/ChainSafe/forest/pull/6356
review sequence: COMMENTED
-- REVIEW COMMENTED
As discussed, my recommendation is to have a change across the board, i.e., basic -> validate_basic, validate -> validate_custom and identity to validate_identity which are a bit more expressive IMO.


==================== PR #6357 by hanabi1224 [closed merged] fix(ci): disable debug tests
https://github.com/ChainSafe/forest/pull/6357
review sequence: COMMENTED, APPROVED
-- INLINE Makefile:110
```diff
 	# see also lib.rs::doctest_private
 	cargo test --doc --features doctest-private
 
 test-release:
-	cargo nextest run --cargo-profile quick --workspace --no-fail-fast
+	cargo nextest run --release --workspace --no-fail-fast
```
>>> why not the quick profile?


==================== PR #6358 by hanabi1224 [closed] fix(ci): try codecov on hosted runner
https://github.com/ChainSafe/forest/pull/6358
-- CONVERSATION
@hanabi1224 have you tried with this script we're already using in other workflows? https://github.com/ChainSafe/forest/blob/main/scripts/optimize_disk_space_gh_worker.sh
-- CONVERSATION
It worked on the second pass, but it took 33 minutes. Not sure why it failed on the first one, the log messages are not something I've seen in the past.


==================== PR #6362 by hanabi1224 [closed merged] fix(f3): prepare for f3 network name change on calibnet
https://github.com/ChainSafe/forest/pull/6362
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
A changelog entry would be useful.


==================== PR #6363 by sudo-shashank [closed merged] fix eth_get_filter_logs test
https://github.com/ChainSafe/forest/pull/6363
review sequence: APPROVED
-- CONVERSATION
no green checkmark, no review


==================== PR #6371 by hanabi1224 [closed merged] fix(test): JSON snapshot tests for actor params (partial)
https://github.com/ChainSafe/forest/pull/6371
review sequence: APPROVED
-- CONVERSATION
What's the fix?
-- CONVERSATION
Ok, so they weren't run because they were in the impl block?
-- CONVERSATION
Oh yeah, now I see it. Thanks!


==================== PR #6372 by hanabi1224 [closed merged] fix(test): speed up test-release and codecov on CI
https://github.com/ChainSafe/forest/pull/6372
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED, COMMENTED
-- INLINE Makefile:118
```diff
@@ -115,7 +115,7 @@ test-release-docs:
 	cargo test --profile quick --doc --features doctest-private
 
 codecov:
-	cargo llvm-cov --workspace --codecov --output-path lcov.info
+	cargo llvm-cov -p forest-filecoin --no-default-features --codecov --output-path lcov.info
```
>>> we have some tests for other binaries too, no? Or at least we should. :)
-- INLINE (reply) Makefile:118
```diff
@@ -115,7 +115,7 @@ test-release-docs:
 	cargo test --profile quick --doc --features doctest-private
 
 codecov:
-	cargo llvm-cov --workspace --codecov --output-path lcov.info
+	cargo llvm-cov -p forest-filecoin --no-default-features --codecov --output-path lcov.info
```
>>> Interop probably not, but there are some tests in, e.g., `forest-cli`.
-- INLINE .github/workflows/coverage.yml:67
```diff
@@ -51,6 +63,8 @@ jobs:
           go-version-file: "go.work"
       - uses: taiki-e/install-action@cargo-llvm-cov
       - uses: taiki-e/install-action@nextest
+      - name: Fetch proof params
+        run: cargo run --bin forest-tool --no-default-features -- fetch-params --keys
```
>>> Would it be faster to fetch them via docker? I don't think there's a need to compile it unless we're reusing the same compilation units in the codecov stage compilation.
-- INLINE (reply) Makefile:118
```diff
-	cargo test --profile quick --doc --features doctest-private
+	cargo test --profile quick --doc --no-default-features --features doctest-private
 
 codecov:
-	cargo llvm-cov --workspace --codecov --output-path lcov.info
+	cargo llvm-cov -p forest-filecoin --no-default-features --codecov --output-path lcov.info
```
>>> Shouldn't we run tests with same features (and thus code paths) that the actual binary runs with?
-- INLINE src/bin/forest-dev.rs:1
```diff

```
>>> Should we add this binary to the docs script?
-- INLINE (reply) src/bin/forest-dev.rs:1
```diff

```
>>> No huge preference here, we can revisit once `forest-dev` gets larger.


==================== PR #6387 by sudo-shashank [closed merged] Add support for the Filecoin.EthGetTransactionCount V2
https://github.com/ChainSafe/forest/pull/6387
review sequence: APPROVED
-- REVIEW APPROVED
LGTM


==================== PR #6389 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 4 updates
https://github.com/ChainSafe/forest/pull/6389
review sequence: APPROVED


==================== PR #6391 by hanabi1224 [closed merged] fix: suppress cargo deny warnings
https://github.com/ChainSafe/forest/pull/6391
review sequence: APPROVED


==================== PR #6395 by 0xDevNinja [closed] feat(chainstore): add fallback blockstore skeleton with bitswap support
https://github.com/ChainSafe/forest/pull/6395
-- CONVERSATION
Closing for now due to lack of follow-up. Feel free to re-open if need be.


==================== PR #6397 by hanabi1224 [closed merged] fix(f3): skip importing F3 data when initial power table is not set in the F3 manifest
https://github.com/ChainSafe/forest/pull/6397
review sequence: APPROVED


==================== PR #6399 by hanabi1224 [closed merged] fix: warnings in `mise run install`
https://github.com/ChainSafe/forest/pull/6399
review sequence: APPROVED


==================== PR #6406 by hanabi1224 [closed merged] fix(ci): use large timeout for codecov CI job
https://github.com/ChainSafe/forest/pull/6406
review sequence: APPROVED


==================== PR #6410 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 5 updates
https://github.com/ChainSafe/forest/pull/6410
review sequence: APPROVED


==================== PR #6412 by akaladarshi [closed merged] feat: Implement `Forest.EthTraceCall`
https://github.com/ChainSafe/forest/pull/6412
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
As discussed, let's have a short guide in docs outlining its usage. This is important in light of various formats used across the Ethereum ecosystem for the output of this method.
-- INLINE docs/docs/developers/guides/trace_call_guide.md:1
```diff

```
>>> The developers guides are intended for Forest maintainers. This should be in user space. Looking briefly, it's less of a guide more of a something to add to the knowledge base section (perhaps in a /RPC subsection). It's fine but let's rename the title to something along the lines of `trace_call` usage.
-- INLINE docs/docs/developers/guides/trace_call_guide.md:185
```diff
+#### Starting Forest Node
+
+**Calibration Network (Testnet - Recommended for testing):**
+
+```bash
+forest --chain calibnet --auto-download-snapshot --encrypt-keystore false
```
>>> I know we did quite a bit of `--encrypt-keystore false` in the past but it's not really a good practice we should recommend in our guides. Let's just remove that bit.
-- INLINE (reply) docs/docs/developers/guides/trace_call_guide.md:185
```diff
+#### Starting Forest Node
+
+**Calibration Network (Testnet - Recommended for testing):**
+
+```bash
+forest --chain calibnet --auto-download-snapshot --encrypt-keystore false
```
>>> Also we should totally remove similar occurrences from the user guides, @coderabbitai create an issue to tackle it. Our guides should promote good security practices, and unencrypted keystores are not one of them.
-- INLINE docs/docs/developers/guides/trace_call_guide.md:42
```diff
+
+| Type        | Description                                                         |
+| ----------- | ------------------------------------------------------------------- |
+| `trace`     | Call hierarchy with inputs, outputs, gas used                       |
+| `stateDiff` | State changes (balance, nonce, code, storage)                       |
+| `vmTrace`   | Low-level EVM execution (not yet implemented, not supproted by FVM) |
```
>>> ```suggestion
| `vmTrace`   | Low-level EVM execution (not yet implemented, not supported by FVM) |
```
-- INLINE docs/docs/developers/guides/trace_call_guide.md:46
```diff
+| `stateDiff` | State changes (balance, nonce, code, storage)                       |
+| `vmTrace`   | Low-level EVM execution (not yet implemented, not supproted by FVM) |
+
+## Response Format (Parity Style)
+
+Forest uses the **Parity/OpenEthereum trace format**, which differs from Geth's debug API.
```
>>> Let's put a link to Geths API for completeness.
-- INLINE docs/docs/developers/guides/trace_call_guide.md:168
```diff
+The `Tracer.sol` contract provides various functions to test different tracing scenarios.
+
+### Contract Location
+
+```
+src/tool/subcommands/api_cmd/contracts/tracer/Tracer.sol
```
>>> Best to provide permalink in the repo. Later we can change it to regular link (otherwise link check will fail).
-- INLINE docs/docs/developers/guides/trace_call_guide.md:515
```diff
+    -H "Content-Type: application/json" \
+    -d '{"jsonrpc":"2.0","id":1,"method":"eth_getBalance","params":["0xACCOUNT","latest"]}' \
+    | jq '.result'
+```
+
+## Offical Resources
```
>>> nit: a table here seems like an overkill. I'd just put `Additional reading` and list those links.
-- INLINE docs/docs/developers/guides/trace_call_guide.md:200
```diff
+
+#### Deploying Contract on Forest
+
+To deploy the contract on Calibnet or Mainnet, you need:
+
+1. A funded wallet with FIL tokens for gas
```
>>> Bots like links, let's link our faucet.
-- INLINE docs/docs/developers/guides/trace_call_guide.md:207
```diff
+
+```bash
+# Deploy using forge (requires funded account)
+forge create src/tool/subcommands/api_cmd/contracts/tracer/Tracer.sol:Tracer \
+    --rpc-url http://localhost:2345/rpc/v1 \
+    --boradcast \
```
>>> ```suggestion
    --broadcast \
```
-- INLINE docs/docs/developers/guides/trace_call_guide.md:180
```diff
+
+---
+
+### Option 1: Testing with Forest Node
+
+#### Starting Forest Node
```
>>> Actually, let's just refer to https://655d17dc.forest-docs.pages.dev/getting_started/syncing, no need to repeat ourselves in the docs. Let's keep it DRY.
-- INLINE docs/docs/developers/guides/trace_call_guide.md:232
```diff
+>
+> If the result is `> 2`, the contract is deployed.
+
+---
+
+### Option 2: Testing with Anvil (Local Development)
```
>>> We should probably split this document into two chunks; one for users and another for Forest developers. Users don't don't need a guide on Anvil, they come here for Forest. ;)
-- INLINE (reply) docs/docs/developers/guides/trace_call_guide.md:168
```diff
+The `Tracer.sol` contract provides various functions to test different tracing scenarios.
+
+### Contract Location
+
+```
+src/tool/subcommands/api_cmd/contracts/tracer/Tracer.sol
```
>>> Also, should Forest be run with the chain indexer env variable set?
-- INLINE docs/docs/developers/guides/trace_call_guide.md:217
```diff
+
+If you don't want to deploy your own contract, you can use these pre-deployed addresses:
+
+| Network  | Contract Address                             | Notes                                       |
+| -------- | -------------------------------------------- | ------------------------------------------- |
+| Calibnet | `0x73a43475aa2ccb14246613708b399f4b2ba546c7` | Full Tracer.sol with storage diff functions |
```
>>> I deployed the contract on mainnet here https://filecoin.blockscout.com/tx/0xbedb6c04829778702d0eb563ab3e0ceba6603901bfab534c5eb881e33031ee3f, so we can add an entry. Perhaps with a link to blockscout, e.g., https://filecoin.blockscout.com/address/0x9BB686Ba6a50D1CF670a98f522a59555d4977fb2
-- INLINE docs/docs/developers/guides/trace_call_guide.md:354
```diff
+> **Note:**: Anvil has a different params format check above, request data is same as Forest.
+
+### 1. Basic Trace - setX(123)
+
+```bash
+curl -s -X POST "http://localhost:2345/rpc/v1" \
```
>>> I think it's easier to follow the guide if it assumes that some environmental variables are exported, e.g., `$CONTRACT_ADDRESS`. Then, locally, I can just set one and copy paste and run the command without any manual modifications.
-- INLINE (reply) docs/docs/developers/guides/trace_call_guide.md:1
```diff

```
>>> Okay, going through it all I think it should be split into two parts:
1. User guide/knowledge post with basic usage info.
2. Dev guide with all of the Anvil stuff and comparing things.

All in all, solid piece of work, seems to work fine.
-- INLINE scripts/tests/trace_call_integration_test.sh:110
```diff
+
+test_trace() {
+    local name="$1" data="$2" type="${3:-standard}"
+    echo -e "${BLUE}--- $name ---${NC}"
+
+    local f_params="[{\"from\":\"$FOREST_ACCOUNT\",\"to\":\"$FOREST_CONTRACT\",\"data\":\"$data\"},[\"trace\"],\"latest\"]"
```
>>> All of the escaping here is pretty painful. Can we avoid it with heredocs?
-- INLINE scripts/tests/trace_call_integration_test.sh:2
```diff
@@ -0,0 +1,313 @@
+#!/usr/bin/env bash
+set -e
```
>>> ```suggestion
set -euo pipefail
```
-- INLINE scripts/tests/trace_call_integration_test.sh:1
```diff
@@ -0,0 +1,313 @@
+#!/usr/bin/env bash
```
>>> some short summary of what the script does would be helpful.
-- INLINE src/rpc/methods/eth/types.rs:757
```diff
@@ -748,6 +755,98 @@ impl EthTrace {
     }
 }
 
+/// Represents a changed value with before and after states.
```
>>> Some trivial tests could be added for completeness.
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1625
```diff
@@ -1585,6 +1621,28 @@ fn eth_tests_with_tipset<DB: Blockstore>(store: &Arc<DB>, shared_tipset: &Tipset
     let block_hash: EthHash = block_cid.into();
 
     let mut tests = vec![
+        RpcTest::identity(
+            EthTraceCall::request((
```
>>> Not sure I understand. Lotus doesn't have an implementation of the `trace_call` method. What would the comparison be against?
-- INLINE src/tool/subcommands/api_cmd/contracts/tracer/Tracer.sol:1
```diff

```
>>> I guess it's okay, my Solidity-fu is too weak to review it. I'd add a disclaimer what is it used for, though.
-- INLINE src/tool/subcommands/api_cmd/contracts/tracer/Tracer.sol:1
```diff
@@ -0,0 +1,315 @@
+// SPDX-License-Identifier: MIT
```
>>> We have a dual license, please add Apache 2.0
-- INLINE src/state_manager/mod.rs:781
```diff
                 .ok_or_else(|| Error::Other("cant find actor in state tree".to_string()))?;
+
             message.set_sequence(from_actor.sequence);
-            vm.apply_message(message)
+            let (ret, duration) = vm.apply_message(message)?;
+            let state_root = vm.flush()?;
```
>>> This is a bit questionable.

1. I assume this will save things to the database. This might bloat the DB if uncontrolled and spammed by hundreds of calls. Riba mentioned this:
> way back when lotus implemented similar they had a problem with the ipld blocks from the "ghost state" ending up in the blockstore after every call, bloating the storage without good chance to GC until way later

> see that you do not have a similar problem: all the VMs instantiated for such override calls should have an in-ram blockstore to be dropped at the end of the scope
2. I think `flush` is not a negligible operation when it comes to performance. Does it make sense to pay the price if we don't care about the `state_root`? If not, the `state_root` calculation should be optional.
-- INLINE src/rpc/methods/eth.rs:4045
```diff
+    let mut addresses = HashSet::default();
+    extract_addresses_recursive(trace, &mut addresses);
+    addresses
+}
+
+fn extract_addresses_recursive(
```
>>> does it make sense to enforce some limit on recursive calls here? We don't want to stack overflow.
-- INLINE src/rpc/methods/eth.rs:4103
```diff
+
+    decode_payload(&return_data, CBOR).ok()
+}
+
+/// Extract all unique Ethereum addresses touched during execution from the trace.
+fn extract_touched_eth_addresses(trace: &crate::rpc::state::ExecutionTrace) -> HashSet<EthAddress> {
```
>>> Also, let's have some unit tests.
-- INLINE (reply) src/state_manager/mod.rs:781
```diff
                 .ok_or_else(|| Error::Other("cant find actor in state tree".to_string()))?;
+
             message.set_sequence(from_actor.sequence);
-            vm.apply_message(message)
+            let (ret, duration) = vm.apply_message(message)?;
+            let state_root = vm.flush()?;
```
>>> As discussed, short-term we can do this.
-- INLINE (reply) src/rpc/methods/eth.rs:4045
```diff
+    let mut addresses = HashSet::default();
+    extract_addresses_recursive(trace, &mut addresses);
+    addresses
+}
+
+fn extract_addresses_recursive(
```
>>> Ideally, we'd convert it to a stack-safe iteration with some bounds. Recursive approach is elegant but it somehow requires more mental overhead and can lead to issues.
-- INLINE (reply) scripts/tests/trace_call_integration_test.sh:66
```diff
+    ANVIL_CONTRACT=$(forge create "$CONTRACT_PATH:Tracer" \
+        --rpc-url "$ANVIL_RPC_URL" \
+        --private-key "$ANVIL_PRIVATE_KEY" \
+        --broadcast --json 2>/dev/null | jq -r '.deployedTo')
+    echo -e "Deployed to: ${GREEN}$ANVIL_CONTRACT${NC}"
+fi
```
>>> @akaladarshi does it make sense?
-- INLINE docs/docs/users/knowledge_base/rpc/trace_call.md:1
```diff

```
>>> I can imagine more methods being explained; let's put it into an `knowledge base/RPC` directory.
-- INLINE docs/dictionary.txt:1
```diff

```
>>> some of the entries don't look like something that should be added; if it's a variable name or a method name, it should be done inline within the doc.
-- INLINE src/rpc/methods/eth/types.rs:134
```diff
@@ -127,6 +131,7 @@ impl GetSize for EthAddress {
 }
 
 impl EthAddress {
+    #[allow(clippy::wrong_self_convention)]
```
>>> why?
-- INLINE src/rpc/methods/gas.rs:266
```diff
                 &mut chain_msg,
                 &prior_messages,
                 Some(ts.clone()),
                 trace_config,
                 StateLookupPolicy::Enabled,
+                false,
```
>>> I recommend not using booleans as parameters unless it's obvious what they do. When you're reading `call_with_gas` (and don't recall what's the method signature), you probably can imagine what `StateLookupPolicy::Enabled` signifies; on the other hand, `false` doesn't tell you anything.

I'd move it to an enumeration.
-- INLINE (reply) src/rpc/methods/gas.rs:266
```diff
                 &mut chain_msg,
                 &prior_messages,
                 Some(ts.clone()),
                 trace_config,
                 StateLookupPolicy::Enabled,
+                false,
```
>>> It's a minimal change to do here, and, from experience, some refactors never land. I'd rather we have it here, it's just a few lines of code.


==================== PR #6414 by hanabi1224 [closed merged] fix(f3): reset F3 on calibnet
https://github.com/ChainSafe/forest/pull/6414
review sequence: APPROVED
-- INLINE scripts/tests/calibnet_no_discovery_check.sh:38
```diff
+# echo "Test subcommands: f3 manifest"
+# $FOREST_CLI_PATH f3 manifest
+# echo "Test subcommands: f3 certs get"
+# $FOREST_CLI_PATH f3 certs list
+# echo "Test subcommands: f3 certs list"
+# $FOREST_CLI_PATH f3 certs get
```
>>> We don't have to disable them; we can just check if the current datetime is above XX.


==================== PR #6415 by hanabi1224 [closed merged] fix: add F3 pending activation status to healthcheck API
https://github.com/ChainSafe/forest/pull/6415
review sequence: APPROVED


==================== PR #6421 by hanabi1224 [closed merged] fix(rpc): backfill tipset messages from p2p network in a few RPC methods
https://github.com/ChainSafe/forest/pull/6421
review sequence: APPROVED, CHANGES_REQUESTED, COMMENTED, COMMENTED, APPROVED
-- INLINE docs/docs/users/reference/env_variables.md:56
```diff
@@ -53,6 +53,7 @@ process.
 | `FOREST_ZSTD_FRAME_CACHE_DEFAULT_MAX_SIZE`                | positive integer                 | 268435456                                      | 536870912                                                     | The default zstd frame cache max size in bytes                                                                        |
 | `FOREST_JWT_DISABLE_EXP_VALIDATION`                       | 1 or true                        | empty                                          | 1                                                             | Whether or not to disable JWT expiration validation                                                                   |
 | `FOREST_ETH_BLOCK_CACHE_SIZE`                             | positive integer                 | 500                                            | 1                                                             | The size of Eth block cache                                                                                           |
+| `FOREST_RPC_BACKFILL_FULL_TIPSET_FROM_NETWORK`            | 1 or true                        | false                                          | 1                                                             | Whether or not to backfill full tipsets from the p2p network                                                          |
```
>>> Actually, let's add some warning that enabling this might lead to excessive disk and bandwidth usage.

Also, let's add a CHANGELOG entry.
-- INLINE src/rpc/methods/chain.rs:1332
```diff
-    let full_tipset = tipset
-        .fill_from_blockstore(store)
-        .context("Failed to load full tipset")?;
+    static SHOULD_BACKFILL: LazyLock<bool> =
+        LazyLock::new(|| is_env_truthy("FOREST_RPC_BACKFILL_FULL_TIPSET_FROM_NETWORK"));
+    let full_tipset = if *SHOULD_BACKFILL {
```
>>> Wouldn't this cause a warning on every call?
-- INLINE (reply) src/rpc/methods/chain.rs:1332
```diff
-    let full_tipset = tipset
-        .fill_from_blockstore(store)
-        .context("Failed to load full tipset")?;
+    static SHOULD_BACKFILL: LazyLock<bool> =
+        LazyLock::new(|| is_env_truthy("FOREST_RPC_BACKFILL_FULL_TIPSET_FROM_NETWORK"));
+    let full_tipset = if *SHOULD_BACKFILL {
```
>>> @hanabi1224 I don't think we need this. If the user opted-in and set this variable then we should assume they read documentation and the warning on resource exhaustion. On top of that, if they made such decision consciously then the warning would just annoy them.


==================== PR #6425 by hanabi1224 [closed merged] chore(f3): bump go-f3
https://github.com/ChainSafe/forest/pull/6425
review sequence: APPROVED


==================== PR #6432 by hanabi1224 [closed merged] test: cover state compuation with fvm2 and fvm3
https://github.com/ChainSafe/forest/pull/6432
review sequence: COMMENTED, APPROVED
-- INLINE src/state_manager/utils.rs:1
```diff

```
>>> It'd be nice to mention in the comment why are we testing those particular epochs.


==================== PR #6435 by dependabot[bot] [closed merged] chore(deps): bump prettier from 3.7.4 to 3.8.0 in /docs
https://github.com/ChainSafe/forest/pull/6435
review sequence: APPROVED


==================== PR #6436 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 3 updates
https://github.com/ChainSafe/forest/pull/6436
review sequence: APPROVED


==================== PR #6437 by hanabi1224 [closed merged] fix: add fmt alias for mise format task
https://github.com/ChainSafe/forest/pull/6437
review sequence: APPROVED


==================== PR #6439 by hanabi1224 [closed merged] feat: sqlite utils and unit tests
https://github.com/ChainSafe/forest/pull/6439
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
LGTM, but probably best to keep it behind a feature flag until we actually use it.
-- INLINE Cargo.toml:199
```diff
@@ -196,6 +196,7 @@ similar = "2"
 slotmap = "1"
 smallvec = "1"
 smart-default = "0.7"
+sqlx = { version = "0.8", default-features = false, features = ["sqlite", "runtime-tokio", "macros"] }
```
>>> given we don't use it anywhere yet, let's hide it behind a feature flag
-- INLINE (reply) Cargo.toml:199
```diff
@@ -196,6 +196,7 @@ similar = "2"
 slotmap = "1"
 smallvec = "1"
 smart-default = "0.7"
+sqlx = { version = "0.8", default-features = false, features = ["sqlite", "runtime-tokio", "macros"] }
```
>>> Yeah, but until integrated, it's wasteful.


==================== PR #6444 by akaladarshi [closed merged] feat(rpc): Add support for the `EthTraceReplayBlockTransactions` V2 API
https://github.com/ChainSafe/forest/pull/6444
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/methods/eth.rs:3739
```diff
+    }
+}
 
-        let state = StateTree::new_from_root(ctx.store_owned(), &state_root)?;
+pub enum EthTraceReplayBlockTransactionsV2 {}
+impl RpcMethod<2> for EthTraceReplayBlockTransactionsV2 {
```
>>> description?
-- INLINE src/rpc/methods/eth.rs:3757
```diff
-            }
+    async fn handle(
+        ctx: Ctx<impl Blockstore + Send + Sync + 'static>,
+        (block_param, trace_types): Self::Params,
+    ) -> Result<Self::Ok, ServerError> {
+        if trace_types.as_slice() != ["trace"] {
```
>>> is there a point in using `anyhow` here? Seems to me it'd be easier to define a new method on `ServerError`, e.g., `ServerError::unsupported_params`. It'd be cheaper.
-- INLINE src/rpc/methods/eth/types.rs:670
```diff
@@ -667,11 +667,9 @@ lotus_json_with_self!(EthBlockTrace);
 #[serde(rename_all = "camelCase")]
 pub struct EthReplayBlockTransactionTrace {
     pub output: EthBytes,
-    #[serde(skip_serializing_if = "Option::is_none")]
```
>>> so this was incorrect?
-- INLINE (reply) src/rpc/methods/eth/types.rs:670
```diff
@@ -667,11 +667,9 @@ lotus_json_with_self!(EthBlockTrace);
 #[serde(rename_all = "camelCase")]
 pub struct EthReplayBlockTransactionTrace {
     pub output: EthBytes,
-    #[serde(skip_serializing_if = "Option::is_none")]
```
>>> what's the struct in Lotus?
-- INLINE (reply) src/rpc/methods/eth/types.rs:670
```diff
@@ -667,11 +667,9 @@ lotus_json_with_self!(EthBlockTrace);
 #[serde(rename_all = "camelCase")]
 pub struct EthReplayBlockTransactionTrace {
     pub output: EthBytes,
-    #[serde(skip_serializing_if = "Option::is_none")]
```
>>> What's the Lotus snapshot response?
-- INLINE (reply) src/rpc/methods/eth/types.rs:670
```diff
@@ -667,11 +667,9 @@ lotus_json_with_self!(EthBlockTrace);
 #[serde(rename_all = "camelCase")]
 pub struct EthReplayBlockTransactionTrace {
     pub output: EthBytes,
-    #[serde(skip_serializing_if = "Option::is_none")]
```
>>> Ok, cool, makes sense.


==================== PR #6445 by dependabot[bot] [closed merged] chore(deps): bump ra_ap_syntax from 0.0.315 to 0.0.316
https://github.com/ChainSafe/forest/pull/6445
review sequence: APPROVED


==================== PR #6453 by hanabi1224 [closed merged] fix: reduce repetitive code in enum match blocks
https://github.com/ChainSafe/forest/pull/6453
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED, COMMENTED
-- INLINE src/interpreter/vm.rs:277
```diff
-        match self {
-            VM::VM2(fvm_executor) => Ok(fvm_executor.flush()?),
-            VM::VM3(fvm_executor) => Ok(fvm_executor.flush()?),
-            VM::VM4(fvm_executor) => Ok(fvm_executor.flush()?),
-        }
+        Ok(delegate_vm!(self.flush()?))
```
>>> to my understanding, we can avoid the declarative macro via `#[delegate_impl]`, no?
-- INLINE (reply) src/interpreter/vm.rs:277
```diff
-        match self {
-            VM::VM2(fvm_executor) => Ok(fvm_executor.flush()?),
-            VM::VM3(fvm_executor) => Ok(fvm_executor.flush()?),
-            VM::VM4(fvm_executor) => Ok(fvm_executor.flush()?),
-        }
+        Ok(delegate_vm!(self.flush()?))
```
>>> Hm, indeed. I wonder if `VM` should've been a trait. Something to consider in the future, but outside of the scope of this PR.
-- INLINE src/shim/actors/builtin/account/mod.rs:33
```diff
-    pub fn pubkey_address(&self) -> fvm_shared2::address::Address {
-        match self {
-            State::V8(st) => st.address,
-            State::V9(st) => st.address,
-            State::V10(st) => from_address_v3_to_v2(st.address),
-            State::V11(st) => from_address_v3_to_v2(st.address),
```
>>> How does it do it now? What's the error message in case of a fiasco?
-- INLINE (reply) src/shim/actors/builtin/account/mod.rs:33
```diff
-    pub fn pubkey_address(&self) -> fvm_shared2::address::Address {
-        match self {
-            State::V8(st) => st.address,
-            State::V9(st) => st.address,
-            State::V10(st) => from_address_v3_to_v2(st.address),
-            State::V11(st) => from_address_v3_to_v2(st.address),
```
>>> It doesn't, but it panics with a certain string.
```rust
pub fn from_address_v3_to_v2(addr: AddressV3) -> AddressV2 {
    AddressV2::from_bytes(&addr.to_bytes())
        .expect("Couldn't convert between FVM3 and FVM2 addresses.")
}
```
Given the conversion is not trivial (one has to call `from_bytes` etc. and check if it worked), my question is what is generated under the hood.
-- INLINE (reply) src/shim/actors/builtin/account/mod.rs:33
```diff
-    pub fn pubkey_address(&self) -> fvm_shared2::address::Address {
-        match self {
-            State::V8(st) => st.address,
-            State::V9(st) => st.address,
-            State::V10(st) => from_address_v3_to_v2(st.address),
-            State::V11(st) => from_address_v3_to_v2(st.address),
```
>>> Ah okay, I missed the signature change. LGTM.


==================== PR #6454 by hanabi1224 [closed merged] chore(deps): bump libp2p-* and resolve RUSTSEC-2026-0002
https://github.com/ChainSafe/forest/pull/6454
review sequence: APPROVED
-- REVIEW APPROVED
libp2p bump is always welcome


==================== PR #6456 by hanabi1224 [closed merged] fix: whitelist nebula crawlers
https://github.com/ChainSafe/forest/pull/6456
review sequence: COMMENTED, APPROVED
-- INLINE src/libp2p/peer_manager.rs:228
```diff
         let mut locked = self.peer_ban_list.write().await;
         locked.insert(peer, duration.and_then(|d| Instant::now().checked_add(d)));
         let user_agent = get_user_agent(&peer);
+        // Whitelist nebula crawlers
+        if let Some(ua) = &user_agent
+            && ua.contains("nebula/")
```
>>> probably cheaper to use `starts_with`


==================== PR #6457 by dependabot[bot] [closed merged] chore(deps): bump diff from 5.2.0 to 5.2.2 in /docs
https://github.com/ChainSafe/forest/pull/6457
review sequence: APPROVED


==================== PR #6458 by dependabot[bot] [closed merged] chore(deps): bump tar from 7.5.3 to 7.5.6 in /docs
https://github.com/ChainSafe/forest/pull/6458
review sequence: APPROVED


==================== PR #6471 by hanabi1224 [closed merged] test: unit test for `validate_tipset`
https://github.com/ChainSafe/forest/pull/6471
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
LGTM. Let's have a changelog entry for this breaking change. I don't think anyone was actively using `forest-tool state compute` but in case they were, it'd be helpful to put an info.


==================== PR #6474 by hanabi1224 [closed merged] chore: more delegate_enum to reduce repetitive code
https://github.com/ChainSafe/forest/pull/6474
review sequence: APPROVED
-- REVIEW APPROVED
nice


==================== PR #6478 by hanabi1224 [closed merged] chore(deps): bump lodash-es
https://github.com/ChainSafe/forest/pull/6478
review sequence: APPROVED


==================== PR #6479 by hanabi1224 [closed merged] fix(ci): disable docs-deploy job for fork PRs
https://github.com/ChainSafe/forest/pull/6479
review sequence: DISMISSED, COMMENTED, APPROVED
-- INLINE .github/workflows/docs-deploy.yml:28
```diff
@@ -24,6 +24,8 @@ jobs:
   docs-deploy:
     name: Deploy to Cloudflare Pages
     runs-on: ubuntu-24.04-arm
+    # Enable workflow for non-PRs but disable workflow for fork PRs as Cloudflare secret does not work for fork PRs
+    if: ${{ github.event_name != 'pull_request' || github.event.pull_request.head.repo.full_name == github.repository }}
```
>>> Will this run on `push`?


==================== PR #6481 by hanabi1224 [closed merged] chore: more delegate_enum to reduce repetitive code
https://github.com/ChainSafe/forest/pull/6481
review sequence: COMMENTED, APPROVED
-- REVIEW APPROVED
LGTM
-- INLINE (reply) src/shim/actors/builtin/datacap/mod.rs:66
```diff
-            State::V13(state) => state.token.get_balance_opt(store, id),
-            State::V14(state) => state.token.get_balance_opt(store, id),
-            State::V15(state) => state.token.get_balance_opt(store, id),
-            State::V16(state) => state.token.get_balance_opt(store, id),
-            State::V17(state) => state.token.get_balance_opt(store, id),
-            _ => return Err(anyhow!("not supported in actors > v8")),
```
>>> nice


==================== PR #6483 by dependabot[bot] [closed merged] chore(deps): bump get-size2 from 0.7.3 to 0.7.4 in the patch-versions group
https://github.com/ChainSafe/forest/pull/6483
review sequence: APPROVED


==================== PR #6486 by hanabi1224 [closed merged] test: unit tests for state migrations
https://github.com/ChainSafe/forest/pull/6486
review sequence: APPROVED
-- CONVERSATION
no green checkmark, no review!


==================== PR #6493 by hanabi1224 [closed merged] test: unit test for `start_rpc`
https://github.com/ChainSafe/forest/pull/6493
review sequence: COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW DISMISSED
LGTM
-- INLINE (reply) src/tool/offline_server/server.rs:107
```diff
+        crate::auth::ADMIN.iter().map(ToString::to_string).collect(),
+        ki.private_key(),
+        token_exp,
+    )?;
+    info!("Admin token: {token}");
+    if let Some(path) = save_jwt_token {
```
>>> I lean to agree with it; perhaps changing it to `Admin token saved to <PATH>` would preserve the functionality.
-- INLINE src/daemon/mod.rs:410
```diff
+                let rpc_listener = tokio::net::TcpListener::bind(rpc_address)
+                    .await
+                    .map_err(|e| {
+                        anyhow::anyhow!("Unable to listen on RPC endpoint {rpc_address}: {e}")
+                    })
+                    .unwrap();
```
>>> I'd say the error mapping here is redundant if we unwrap anyway. Does it bring any additional info over just `expect`?
-- INLINE (reply) src/daemon/mod.rs:410
```diff
+                let rpc_listener = tokio::net::TcpListener::bind(rpc_address)
+                    .await
+                    .map_err(|e| {
+                        anyhow::anyhow!("Unable to listen on RPC endpoint {rpc_address}: {e}")
+                    })
+                    .unwrap();
```
>>> Ok, so my question is - is an error in binding with this mapping is more clear than just a plain `unwrap` on the upstream error?


==================== PR #6496 by hanabi1224 [closed merged] fix(ci): prefetch state computation snapshots to make codecov less flaky
https://github.com/ChainSafe/forest/pull/6496
review sequence: APPROVED


==================== PR #6508 by leshyatwork[bot] [closed merged] [automated] Add monthly RPC parity conformance report
https://github.com/ChainSafe/forest/pull/6508
review sequence: APPROVED
-- REVIEW APPROVED
good bot


==================== PR #6510 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 3 updates
https://github.com/ChainSafe/forest/pull/6510
review sequence: APPROVED


==================== PR #6512 by dependabot[bot] [closed merged] chore(deps): bump insta from 1.46.2 to 1.46.3 in the patch-versions group
https://github.com/ChainSafe/forest/pull/6512
review sequence: DISMISSED, APPROVED


==================== PR #6517 by hanabi1224 [closed merged] fix: improve logging of `decode_payload` failures
https://github.com/ChainSafe/forest/pull/6517
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/methods/eth/utils.rs:71
```diff
             let mut reader = cbor4ii::core::utils::SliceReader::new(payload.bytes());
             match Value::decode(&mut reader) {
                 Ok(Value::Bytes(bytes)) => Ok(EthBytes(bytes)),
-                _ => bail!("failed to decode params byte array"),
+                other => bail!(
+                    "failed to decode params byte array: {other:?}, codec: {codec}, payload: {}",
```
>>> I think the payload might be in certain cases pretty big, and a bad actor might flood the logs with it.
-- INLINE (reply) src/rpc/methods/eth/utils.rs:71
```diff
             let mut reader = cbor4ii::core::utils::SliceReader::new(payload.bytes());
             match Value::decode(&mut reader) {
                 Ok(Value::Bytes(bytes)) => Ok(EthBytes(bytes)),
-                _ => bail!("failed to decode params byte array"),
+                other => bail!(
+                    "failed to decode params byte array: {other:?}, codec: {codec}, payload: {}",
```
>>> I'd either omit it or limit the length .
-- INLINE (reply) src/rpc/methods/eth/utils.rs:71
```diff
             let mut reader = cbor4ii::core::utils::SliceReader::new(payload.bytes());
             match Value::decode(&mut reader) {
                 Ok(Value::Bytes(bytes)) => Ok(EthBytes(bytes)),
-                _ => bail!("failed to decode params byte array"),
+                other => bail!(
+                    "failed to decode params byte array: {other:?}, codec: {codec}, payload: {}",
```
>>> If a particular log is purely for debugging purposes, let's `tracing::debug` the payload. This way, it can be temporarily enabled to debug things and then brought to avoid log flood.


==================== PR #6523 by akaladarshi [closed merged] feat: add open rpc snap files
https://github.com/ChainSafe/forest/pull/6523
review sequence: APPROVED


==================== PR #6533 by hanabi1224 [closed merged] fix(rpc): make `EthGetBlockByHash` work with lotus-gateway
https://github.com/ChainSafe/forest/pull/6533
review sequence: APPROVED


==================== PR #6538 by hanabi1224 [closed merged] fix(ci): set CARGO_BUILD_JOBS=2 for unit tests
https://github.com/ChainSafe/forest/pull/6538
review sequence: DISMISSED, APPROVED


==================== PR #6540 by hanabi1224 [closed merged] fix(ci): move snapshot tests to using `cargo test`
https://github.com/ChainSafe/forest/pull/6540
review sequence: COMMENTED, APPROVED, COMMENTED
-- INLINE src/state_manager/utils.rs:1
```diff

```
>>> I don't think there's a need to include the `cargo_test` in the name.
-- INLINE (reply) src/state_manager/utils.rs:1
```diff

```
>>> Ok, makes sense.


==================== PR #6541 by akaladarshi [closed merged] fix: forge script bug
https://github.com/ChainSafe/forest/pull/6541
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
Let's have a changelog entry for this fix.
-- CONVERSATION
Blocked by https://github.com/ChainSafe/forest/pull/6547. Once it's merged, please rebase.


==================== PR #6542 by hanabi1224 [closed merged] fix(ci): do not cancel jobs in calibnet-api-test-stateful queue
https://github.com/ChainSafe/forest/pull/6542
review sequence: APPROVED
-- REVIEW APPROVED
Interesting, I can't find the default value for this key in the docs; I kind of assumed (and I guess @sudo-shashank as well) that if we put everywhere `true` it means that the default is `false`.


==================== PR #6544 by hanabi1224 [closed merged] fix(ci): run calibnet-api-test-stateful only for non-draft PR
https://github.com/ChainSafe/forest/pull/6544
review sequence: APPROVED


==================== PR #6546 by hanabi1224 [closed merged] fix(ci): unset CARGO_INCREMENTAL in workflows
https://github.com/ChainSafe/forest/pull/6546
review sequence: APPROVED
-- CONVERSATION
Let's elaborate here how this helps.


==================== PR #6550 by hanabi1224 [closed merged] fix: generate test snapshot with blocks in deterministic order
https://github.com/ChainSafe/forest/pull/6550
review sequence: APPROVED


==================== PR #6553 by hanabi1224 [closed merged] fix(rpc): make ChainGetTipset v2 work with lotus-gateway
https://github.com/ChainSafe/forest/pull/6553
review sequence: APPROVED
-- CONVERSATION
no green checkmark, no review


==================== PR #6566 by hanabi1224 [closed merged] fix(test): retry cargo test on CI
https://github.com/ChainSafe/forest/pull/6566
review sequence: APPROVED


==================== PR #6567 by hanabi1224 [closed merged] chore(release): prepare release v0.32.0
https://github.com/ChainSafe/forest/pull/6567
review sequence: COMMENTED, COMMENTED, COMMENTED, DISMISSED, COMMENTED, APPROVED
-- INLINE docs/docs/developers/guides/release_checklist.md:25
```diff
@@ -22,6 +22,11 @@ Make a pull request with the following changes:
   [crates.io](https://crates.io/).
 - Make sure to run `cargo publish --dry-run` and include the `Cargo.lock` crate
   version change in the release.
+- Make sure to update RPC specs by running
```
>>> Is it part of a release or just regular PR to `main`?
-- INLINE Cargo.toml:3
```diff
@@ -1,6 +1,6 @@
 [package]
 name = "forest-filecoin"
-version = "0.31.1"
+version = "0.31.2"
```
>>> There were a couple of breaking changes; I suggest we bump to `0.32.0`
-- INLINE CHANGELOG.md:40
```diff
+
+### Fixed
+
+## Forest v0.31.2 "Ember"
+
+This is a non-mandatory patch release for all node operators. It resets F3 on calibnet, also includes new V2 RPC methods and a few bug fixes, including `lotus-gateway` compatibility fixes.
```
>>> Do we consider it mandatory for calibnet operators?
-- INLINE CHANGELOG.md:38
```diff
+
+### Removed
+
+### Fixed
+
+## Forest v0.31.2 "Ember"
```
>>> ```suggestion
## Forest v0.32.0 "Ember"
```


==================== PR #6568 by hanabi1224 [closed merged] fix(tool): add retry to api compare tests
https://github.com/ChainSafe/forest/pull/6568
review sequence: APPROVED


==================== PR #6569 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/6569
review sequence: APPROVED


==================== PR #6570 by hanabi1224 [closed merged] test: add lotus-gateway parity test
https://github.com/ChainSafe/forest/pull/6570
review sequence: COMMENTED, APPROVED
-- CONVERSATION
no green checkmark! :x:
-- INLINE scripts/tests/api_compare/.env:8
```diff
@@ -5,6 +5,7 @@ FIL_PROOFS_PARAMETER_CACHE=/var/tmp/filecoin-proof-parameters
 LOTUS_RPC_PORT=1234
 FOREST_RPC_PORT=2345
 FOREST_OFFLINE_RPC_PORT=3456
+FOREST_VIA_GATEWAY_RPC_PORT=4567
```
>>> nice


==================== PR #6572 by dependabot[bot] [closed merged] chore(deps): bump toml from 0.8.23 to 0.9.11+spec-1.1.0
https://github.com/ChainSafe/forest/pull/6572
review sequence: APPROVED
-- CONVERSATION
all red! :scream:


==================== PR #6574 by hanabi1224 [closed merged] test: define state_compute tests in a yaml file
https://github.com/ChainSafe/forest/pull/6574
review sequence: APPROVED


==================== PR #6586 by hanabi1224 [closed merged] chore: migrate from yarn to pnpm
https://github.com/ChainSafe/forest/pull/6586
review sequence: DISMISSED, APPROVED


==================== PR #6589 by hanabi1224 [closed merged] fix(test): add --api-wait-lookback-limit 2000 to lotus-gateway
https://github.com/ChainSafe/forest/pull/6589
review sequence: DISMISSED, APPROVED


==================== PR #6590 by hanabi1224 [closed merged] fix(f3): set F3 initial power table on calibnet
https://github.com/ChainSafe/forest/pull/6590
review sequence: APPROVED


==================== PR #6591 by hanabi1224 [closed merged] chore(release): prepare release v0.32.1
https://github.com/ChainSafe/forest/pull/6591
review sequence: APPROVED


==================== PR #6592 by hanabi1224 [closed merged] fix(ci): speed up snapshot downloading on CI
https://github.com/ChainSafe/forest/pull/6592
review sequence: COMMENTED, APPROVED
-- INLINE scripts/tests/calibnet_stateless_mode_check.sh:27
```diff
@@ -24,6 +24,7 @@ cat <<- EOF > $CONFIG_PATH
 	kademlia = false
 EOF
 
+handle_auto_download_snapshot_env
```
>>> it's a very generic name; either let's come up with a good name or at least have some function comments/docs so that the reader doesn't have to go through the entire logic to get the gist of what does `handle` actually mean here.


==================== PR #6597 by hanabi1224 [closed merged] chore: bump go compiler and deps
https://github.com/ChainSafe/forest/pull/6597
review sequence: APPROVED


==================== PR #6599 by hanabi1224 [closed merged] fix(ci): use ubuntu-slim for trival jobs
https://github.com/ChainSafe/forest/pull/6599
review sequence: APPROVED
-- REVIEW APPROVED
Very nice


==================== PR #6600 by hanabi1224 [closed merged] chore: use clang-20 in Dockerfile
https://github.com/ChainSafe/forest/pull/6600
review sequence: COMMENTED, APPROVED
-- INLINE (reply) Dockerfile:17
```diff
 RUN apt-get update && \
-    apt-get install --no-install-recommends -y build-essential clang-14 curl git ca-certificates
+    apt-get install --no-install-recommends -y build-essential clang-20 curl git ca-certificates
 RUN update-ca-certificates
-ENV CC=clang-14 CXX=clang++-14
+ENV CC=clang-20 CXX=clang++-20
```
>>> You know nothing.


==================== PR #6601 by hanabi1224 [closed merged] fix(ci): disable F3 on lotus until a new lotus image with calibnet F3 initial power table is published
https://github.com/ChainSafe/forest/pull/6601
review sequence: APPROVED


==================== PR #6603 by hanabi1224 [closed merged] chore(deps): favor re-exported digest crate
https://github.com/ChainSafe/forest/pull/6603
review sequence: APPROVED


==================== PR #6615 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 4 updates
https://github.com/ChainSafe/forest/pull/6615
review sequence: APPROVED


==================== PR #6618 by dependabot[bot] [closed merged] chore(deps): bump toml from 1.0.1+spec-1.1.0 to 1.0.2+spec-1.1.0 in the patch-versions group
https://github.com/ChainSafe/forest/pull/6618
review sequence: APPROVED


==================== PR #6619 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/6619
review sequence: APPROVED
-- CONVERSATION
This Lotus version doesn't work on calibnet due to F3 reset; need to wait for a new release.
-- CONVERSATION
It should be good now, waiting for the green checkmark.


==================== PR #6625 by dependabot[bot] [closed] chore(deps): bump rand_chacha from 0.3.1 to 0.9.0
https://github.com/ChainSafe/forest/pull/6625
review sequence: APPROVED
-- CONVERSATION
Seems it introduces some dependency conflicts. Skipping for now
-- CONVERSATION
@dependabot ignore
-- CONVERSATION
@dependabot ignore this major version


==================== PR #6639 by hanabi1224 [closed merged] fix(gc): add random delay to avoid a cluster of nodes run GC and reboot RPC services at the same time
https://github.com/ChainSafe/forest/pull/6639
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
Should we add a note in the GC docs? And a changelog entry?


==================== PR #6643 by akaladarshi [closed merged] chore(release): prepare release v0.32.2
https://github.com/ChainSafe/forest/pull/6643
review sequence: APPROVED


==================== PR #6644 by hanabi1224 [closed merged] fix(RPC): backport F3 finality resolution to eth V1 methods
https://github.com/ChainSafe/forest/pull/6644
review sequence: COMMENTED, COMMENTED
-- INLINE docs/dictionary.txt:171
```diff
 tipset
 tipsets
 URL
+v0
+v1
+v2
```
>>> I'd avoid such entries, if possible.
-- INLINE src/rpc/methods/eth/tipset_resolver.rs:75
```diff
+    ///
+    /// The resolved `Tipset`, or an error if resolution fails.
+    async fn resolve_predefined_tipset(&self, tag: Predefined) -> anyhow::Result<Tipset> {
+        match self.api_version {
+            ApiPaths::V2 => self.resolve_predefined_tipset_v2(tag).await,
+            _ => self.resolve_predefined_tipset_v1(tag).await,
```
>>> I'd avoid wildcard here. Say we introduce `ApiPaths::V3`; this `match` wouldn't get caught by the compiler. Is `ApiPaths::V3` supposed to be handled like `V1` and not `V2`? Perhaps, but it must be a conscious decision.


==================== PR #6646 by hanabi1224 [closed merged] fix(rpc): pass http::Extensions to RpcMethod::handle
https://github.com/ChainSafe/forest/pull/6646
review sequence: APPROVED


==================== PR #6648 by hanabi1224 [closed merged] fix: `cargo deny check advisories` errors
https://github.com/ChainSafe/forest/pull/6648
review sequence: APPROVED


==================== PR #6649 by coderabbitai[bot] [closed] 📝 Add docstrings to `hm/f3-await-v1-rpc`
https://github.com/ChainSafe/forest/pull/6649
-- CONVERSATION
@hanabi1224 that's a lot of bloat


==================== PR #6650 by dependabot[bot] [closed merged] chore(deps): bump mermaid from 11.12.2 to 11.12.3 in /docs in the patch-versions group
https://github.com/ChainSafe/forest/pull/6650
review sequence: APPROVED


==================== PR #6651 by dependabot[bot] [closed] chore(deps): bump rand from 0.8.5 to 0.10.0
https://github.com/ChainSafe/forest/pull/6651
-- CONVERSATION
Seems it wanted to bump it only in the lockfile. AFAIK dependencies won't allow us to bump to `0.10`.


==================== PR #6652 by dependabot[bot] [closed merged] chore(deps): bump basic-ftp from 5.1.0 to 5.2.0 in /docs
https://github.com/ChainSafe/forest/pull/6652
review sequence: APPROVED
-- REVIEW APPROVED
just a regular JS update 
```
+5,492
-10,278
Lines changed: 5492 additions & 10278 deletions
```


==================== PR #6657 by hanabi1224 [closed merged] chore(deps): bump basic-ftp
https://github.com/ChainSafe/forest/pull/6657
review sequence: APPROVED


==================== PR #6663 by dependabot[bot] [closed merged] chore(deps-dev): bump @types/node from 22.19.11 to 25.3.0 in /docs
https://github.com/ChainSafe/forest/pull/6663
review sequence: APPROVED


==================== PR #6666 by hanabi1224 [closed merged] feat(gc): keep message receipts and events
https://github.com/ChainSafe/forest/pull/6666
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, COMMENTED, DISMISSED, APPROVED
-- INLINE src/chain/mod.rs:36
```diff
@@ -33,6 +33,8 @@ use tokio::io::{AsyncWrite, AsyncWriteExt, BufWriter};
 #[derive(Debug, Clone, Default)]
 pub struct ExportOptions {
     pub skip_checksum: bool,
+    pub message_receipts: bool,
```
>>> ```suggestion
    pub include_receipts: bool,
```
-- INLINE src/chain/mod.rs:37
```diff
@@ -33,6 +33,8 @@ use tokio::io::{AsyncWrite, AsyncWriteExt, BufWriter};
 #[derive(Debug, Clone, Default)]
 pub struct ExportOptions {
     pub skip_checksum: bool,
+    pub message_receipts: bool,
+    pub events: bool,
```
>>> ```suggestion
    pub include_events: bool,
```
-- INLINE src/db/gc/snapshot.rs:257
```diff
@@ -256,7 +256,9 @@ where
             file,
             Some(ExportOptions {
```
>>> looks like it'd make sense to impl Default for `ExportOptions`
-- INLINE (reply) src/ipld/util.rs:253
```diff
 
+        if self.events && !self.message_receipts {
+            return Poll::Ready(Some(Err(anyhow::anyhow!(
+                "message receipts must be included when events are included"
+            ))));
+        }
```
>>> does it make sense?
-- INLINE (reply) src/ipld/util.rs:253
```diff
 
+        if self.events && !self.message_receipts {
+            return Poll::Ready(Some(Err(anyhow::anyhow!(
+                "message receipts must be included when events are included"
+            ))));
+        }
```
>>> @hanabi1224
-- INLINE src/tool/subcommands/archive_cmd.rs:390
```diff
@@ -381,13 +386,25 @@ impl ArchiveInfo {
         let mut network: String = "unknown".into();
         let mut lowest_stateroot_epoch = root_epoch;
         let mut lowest_message_epoch = root_epoch;
+        let mut message_receipts = 0;
+        let mut events = 0;
```
>>> ```suggestion
        let mut message_receipts_count = 0;
        let mut events_count = 0;
```
-- INLINE (reply) src/db/gc/snapshot.rs:257
```diff
@@ -256,7 +256,9 @@ where
             file,
             Some(ExportOptions {
```
>>> Ah ok. The `seen` manual default implementation confused me. It should be rather `..Default::default()`


==================== PR #6670 by hanabi1224 [closed merged] fix(test): print some logs in test_rpc_server for troubleshooting timeout issue
https://github.com/ChainSafe/forest/pull/6670
review sequence: DISMISSED, APPROVED


==================== PR #6672 by hanabi1224 [closed merged] fix: avoid `clone` in `sort_by_key`
https://github.com/ChainSafe/forest/pull/6672
review sequence: APPROVED


==================== PR #6678 by leshyatwork[bot] [closed merged] [automated] Update Forest checkpoints
https://github.com/ChainSafe/forest/pull/6678
review sequence: APPROVED
-- REVIEW APPROVED
nice


==================== PR #6685 by akaladarshi [closed] feat(api): Implement debug_traceTransaction API
https://github.com/ChainSafe/forest/pull/6685
-- CONVERSATION
no green checkmark, no review :x: Also, make sure to address/resolve :rabbit: comments


==================== PR #6687 by hanabi1224 [closed merged] fix: inefficient Vec<u8> to BytesMut conversion 
https://github.com/ChainSafe/forest/pull/6687
review sequence: APPROVED


==================== PR #6688 by hanabi1224 [closed merged] fix: make Writable trait async and remove the write_via_buf bridge function
https://github.com/ChainSafe/forest/pull/6688
review sequence: APPROVED


==================== PR #6690 by hanabi1224 [closed merged] fix: support large index in forest car
https://github.com/ChainSafe/forest/pull/6690
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/db/car/forest/index/mod.rs:204
```diff
+
+impl<R: ReadAt> ZstdSkipFramesEncodedDataReader<R> {
+    pub fn new(reader: R) -> io::Result<Self> {
+        let mut offset = 0;
+        let mut skip_frame_header_offsets = vec![];
+        while let Ok(len) = reader.read_u32_at::<LittleEndian>(offset + 4) {
```
>>> why 4? even if it's magic header, let's not use a magic number
-- INLINE src/db/car/forest/index/mod.rs:250
```diff
+
+impl<R> ReadAt for ZstdSkipFramesEncodedDataReader<R>
+where
+    R: ReadAt,
+{
+    fn read_at(&self, pos: u64, buf: &mut [u8]) -> io::Result<usize> {
```
>>> let's document the steps here in plain English, it's not obvious what's happening here
-- INLINE (reply) src/db/car/forest/index/mod.rs:250
```diff
+
+impl<R> ReadAt for ZstdSkipFramesEncodedDataReader<R>
+where
+    R: ReadAt,
+{
+    fn read_at(&self, pos: u64, buf: &mut [u8]) -> io::Result<usize> {
```
>>> I don't think it's very helpful. I'd rather not have method-level documentation, but rather explanations in the code itself.


==================== PR #6696 by hanabi1224 [closed merged] fix(ci): enable go cache
https://github.com/ChainSafe/forest/pull/6696
review sequence: DISMISSED


==================== PR #6701 by leshyatwork[bot] [closed merged] [automated] Add monthly RPC parity conformance report
https://github.com/ChainSafe/forest/pull/6701
review sequence: APPROVED


==================== PR #6710 by sudo-shashank [closed merged] Added support for f4 addresses in forest-wallet
https://github.com/ChainSafe/forest/pull/6710
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/wallet/subcommands/wallet_cmd.rs:540
```diff
+                    } else {
+                        EVMMethod::InvokeContract as u64
+                    }
+                } else {
+                    METHOD_SEND
+                };
```
>>> The cyclomatic complexity of this logic is quite high. It'd be great to refactor it to be less of a cognitive effort and ensure high coverage, addressing edge cases.
-- INLINE scripts/tests/calibnet_wallet_check.sh:264
```diff
+done
+
+$FOREST_WALLET_PATH list
+$FOREST_WALLET_PATH --remote-wallet list
+
+: End delegated wallet tests
```
>>> It'd probably be easier to move it to a separate file instead of making sections.
-- INLINE src/message_pool/msgpool/msg_pool.rs:272
```diff
             return Err(Error::MessageTooBig);
         }
+        let to = msg.message().to();
+        if to.protocol() == Protocol::Delegated && EthAddress::from_filecoin_address(&to).is_err() {
+            return Err(Error::Other(format!(
+                "message recipient {to} is a delegated address but not a valid Eth Address"
```
>>> Can we not swallow the error from `from_filecoin_address`? It might contain some useful information.


==================== PR #6711 by hanabi1224 [closed merged] chore(deps): bump rust deps
https://github.com/ChainSafe/forest/pull/6711
review sequence: APPROVED
-- CONVERSATION
no green checkmark, no review :x:


==================== PR #6712 by hanabi1224 [closed merged] fix: update TipsetKey save and load logic to match Lotus
https://github.com/ChainSafe/forest/pull/6712
review sequence: COMMENTED
-- CONVERSATION
No :heavy_check_mark: no review!
-- INLINE src/blocks/tipset.rs:57
```diff
 
 impl TipsetKey {
     // Special encoding to match Lotus.
     pub fn cid(&self) -> anyhow::Result<Cid> {
-        Ok(Cid::from_cbor_blake2b256(&self.bytes())?)
+        Ok(self.car_block()?.cid)
```
>>> Why `car` block? Does it have to be represented as CAR?


==================== PR #6714 by hanabi1224 [closed merged] chore: prepare release 0.32.4
https://github.com/ChainSafe/forest/pull/6714
review sequence: APPROVED


==================== PR #6716 by leshyatwork[bot] [closed merged] [automated] Update Forest RPC reference docs
https://github.com/ChainSafe/forest/pull/6716
review sequence: APPROVED


==================== PR #6717 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/6717
review sequence: APPROVED


==================== PR #6718 by tsinglua [closed merged] chore: fix duplicate word in comment
https://github.com/ChainSafe/forest/pull/6718
review sequence: APPROVED
-- REVIEW APPROVED
Thanks.


==================== PR #6720 by hanabi1224 [open] feat: sqlite indexer
https://github.com/ChainSafe/forest/pull/6720
review sequence: COMMENTED, COMMENTED
-- CONVERSATION
No green checkmark and lots of :rabbit: to address! No review!
-- CONVERSATION
High level, though, I'd like to see some numbers to prove the feature works as intended, e.g., queries that would've been slow with regular logic without this indexer.
-- INLINE docs/docs/users/reference/env_variables.md:82
```diff
@@ -79,6 +79,7 @@ process.
 | `FOREST_ETH_RPC_COMPUTE_BLOOM_ON_MISS`                           | 1 or true                        | false                                                      | 1                                                             | Allows `eth` block RPC methods to compute (and store) the block `logsBloom` when it is not already stored, otherwise such blocks report an all-ones bloom                                                                                                                      |
 | `FOREST_RPC_METRICS_DISABLED`                                    | 1 or true                        | false                                                      | 1                                                             | Disable per-method JSON-RPC metrics only, leaving the metrics endpoint and all other metrics (cache, sync, database, ...) intact. To turn off metrics entirely, disable the endpoint instead with `--no-metrics`                                                               |
 | `FOREST_STATE_COMPUTE_MAX_RANGE`                                 | positive integer                 | 2000                                                       | 100                                                           | The maximum `nEpochs` value `Forest.StateCompute` accepts                                                                                                                                                                                                                      |
+| `FOREST_RPC_SQL_RANGE_THRESHOLD`                                 | positive integer                 | 500                                                        | 1000                                                          | The RPC tipset range threshold above which SQL index is prefered over in-memory event collection                                                                                                                                                                               |
```
>>> As agreed, let's have it disabled for now.
-- INLINE src/chain/store/indexer/ddls.rs:4
```diff
@@ -0,0 +1,126 @@
+// Copyright 2019-2026 ChainSafe Systems
+// SPDX-License-Identifier: Apache-2.0, MIT
+
+pub static DDLS: [&str; 10] = [
```
>>> If you took it from Lotus, let's have a permalink.


==================== PR #6722 by dependabot[bot] [closed merged] chore(deps): bump the docker group with 3 updates
https://github.com/ChainSafe/forest/pull/6722
review sequence: APPROVED


==================== PR #6727 by hanabi1224 [closed merged] test: check RPC batch request via websocket
https://github.com/ChainSafe/forest/pull/6727
review sequence: APPROVED


==================== PR #6728 by gorusys [closed] chore: uncomment GoldenWeek and Xxx in ForkUpgradeParams
https://github.com/ChainSafe/forest/pull/6728
-- CONVERSATION
Thanks for looking into it, but it's not a good issue for external contributors; the RPC snapshots are the ones listed in https://docs.forest.chainsafe.io/developers/guides/rpc_test_snapshot which require access to internal infrastructure.


==================== PR #6731 by akaladarshi [closed merged] refactor: prepare for the changes in debug trace transaction
https://github.com/ChainSafe/forest/pull/6731
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- INLINE src/rpc/methods/eth/trace.rs:678
```diff
@@ -659,6 +675,30 @@ fn trace_evm_private(
     }
 }
 
+/// Returns the effective nonce for an actor: EVM nonce for EVM actors, sequence otherwise.
```
>>> I think some of those methods could be potentially used outside of the `trace` module; would it make sense to add them to some utils module? Also, it might be slightly nicer to actually add a trait an implement it for `ActorState` instead of relying on some free functions.
-- INLINE src/rpc/methods/eth/types.rs:320
```diff
     pub gas_price: Option<EthBigInt>,
     #[serde(skip_serializing_if = "Option::is_none", default)]
     pub value: Option<EthBigInt>,
-    // Some clients use `input`, others use `data`. We have to support both.
-    #[serde(alias = "input", skip_serializing_if = "Option::is_none", default)]
+    // Some clients use `input`, others use `data`; both accepted, `input` takes precedence.
```
>>> 💩
-- INLINE src/rpc/methods/eth/types.rs:687
```diff
+    pub fn from_parity_traces(traces: Vec<EthTrace>) -> Self {
+        let output = traces
+            .first()
+            .map_or_else(EthBytes::default, |trace| match &trace.result {
+                TraceResult::Call(r) => r.output.clone(),
+                TraceResult::Create(r) => r.code.clone(),
```
>>> Could we avoid cloning given we're grabbing traces ownership anyway?
-- INLINE src/rpc/methods/eth.rs:3368
```diff
@@ -3396,49 +3365,91 @@ impl RpcMethod<1> for EthTraceBlock {
     }
 }
 
-async fn eth_trace_block<DB>(
+struct TipsetTraceEntry {
```
>>> If we're refactoring things, I think it'd make sense to move `trace`-related types to `trace/types.rs`. This module is already huge.
-- INLINE src/state_manager/mod.rs:2037
```diff
     // FVM, but that introduces some constraints, and possible deadlocks.
     stacker::grow(64 << 20, || -> anyhow::Result<StateOutput> {
-        let mut vm = create_vm(parent_state, epoch, tipset.min_timestamp())?;
+        let mut vm = exec.create_vm(parent_state, epoch, tipset.min_timestamp(), enable_tracing)?;
 
-        // step 4: apply tipset messages
```
>>> Let's keep the comments.
-- INLINE src/rpc/methods/eth/trace/parity.rs:2
```diff
@@ -0,0 +1,666 @@
+// Copyright 2019-2026 ChainSafe Systems
+// SPDX-License-Identifier: Apache-2.0, MIT
```
>>> It might be worth adding top-level documentation what this file is supposed to contain. Kind of useful in preventing others putting whatever there.


==================== PR #6732 by hanabi1224 [closed merged] fix: broadcast HeadChange::Revert(Tipset) in set_heaviest_tipset
https://github.com/ChainSafe/forest/pull/6732
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, DISMISSED, APPROVED
-- REVIEW COMMENTED
Any chance for a test for this fix/feature?
-- CONVERSATION
no green checkmark, no review!
-- INLINE src/chain/store/chain_store.rs:181
```diff
+                    reverts: vec![],
+                }
+            }
+        };
+
+        if self.publisher.send(changes).is_err() {
```
>>> Can we potentially disregard calculating changes if there are no receivers?
-- INLINE src/rpc/methods/chain.rs:133
```diff
-                            tracing::error!("Failed to fetch logs for tipset {}: {}", ts.key(), e);
+        while let Ok(changes) = subscriber.recv().await {
+            for ts in changes.applies {
+                match eth_logs_with_filter(&ctx, &ts, filter.clone(), None).await {
+                    Ok(logs) => {
+                        if !logs.is_empty()
```
>>> Same here; I wonder if we could avoid doing any work if there are no receivers. Unless we don't expect to be no receivers, then it's fine.
-- INLINE (reply) src/chain/store/chain_store.rs:181
```diff
+                    reverts: vec![],
+                }
+            }
+        };
+
+        if self.publisher.send(changes).is_err() {
```
>>> @hanabi1224 In this case, should we make the log more severe? If it's NOT subscribed then, from my understanding, something went terribly wrong, no?


==================== PR #6742 by hanabi1224 [closed merged] fix: use `auto_impl` to simplify trait implemention for (&, Box, Arc)
https://github.com/ChainSafe/forest/pull/6742
review sequence: APPROVED
-- REVIEW APPROVED
I like it


==================== PR #6743 by leshyatwork[bot] [closed merged] [automated] Update Forest checkpoints
https://github.com/ChainSafe/forest/pull/6743
review sequence: APPROVED
-- CONVERSATION
> No mainnet?

As discussed, all good. There's a skew. I think it might not have been there originally but the network got reset.

```
❯ fil_epoch_to_date calibnet (math 3456000+86400); fil_epoch_to_date mainnet (math 5788800 + 86400)
Sun Mar 15 06:13:00 PM UTC 2026
Thu Mar 26 10:00:00 PM UTC 2026
```


==================== PR #6744 by hanabi1224 [closed merged] fix: remove unnecessary async_trait usages
https://github.com/ChainSafe/forest/pull/6744
review sequence: APPROVED


==================== PR #6749 by hanabi1224 [closed merged] test(ci): upload CI test results to codecov
https://github.com/ChainSafe/forest/pull/6749
review sequence: APPROVED


==================== PR #6750 by hanabi1224 [closed merged] fix: optimize `collect_events` in Eth RPC
https://github.com/ChainSafe/forest/pull/6750
review sequence: COMMENTED
-- INLINE src/rpc/methods/eth/filter/mod.rs:330
```diff
@@ -334,18 +327,17 @@ impl EthEventHandler {
                         let ce = CollectedEvent {
                             entries,
                             emitter_addr: resolved,
-                            event_idx: event_count,
+                            event_idx: event_idx as _,
```
>>> as discussed, let's avoid this construct


==================== PR #6751 by akaladarshi [closed merged] feat(api): Implement debug_traceTransaction API
https://github.com/ChainSafe/forest/pull/6751
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED
-- INLINE src/rpc/methods/eth/trace/parity.rs:33
```diff
@@ -32,7 +30,7 @@ use num::FromPrimitive;
 use tracing::debug;
 
 /// Error string used in Parity-format traces.
-const PARITY_TRACE_REVERT_ERROR: &str = "Reverted";
+pub const PARITY_TRACE_REVERT_ERROR: &str = "Reverted";
```
>>> Kind of weird to expose only one of those. I guess if we assume those are public let's mark all of them as public.
-- INLINE src/rpc/methods/eth/trace/geth.rs:4
```diff
@@ -0,0 +1,450 @@
+// Copyright 2019-2026 ChainSafe Systems
+// SPDX-License-Identifier: Apache-2.0, MIT
+
+//! Geth-style trace construction from Filecoin execution traces.
```
>>> Let's mark the exact version of `geth` you were testing against.
-- INLINE src/rpc/methods/eth/trace/geth.rs:155
```diff
+/// In default mode, returns the pre-execution state of every touched account.
+/// Only storage slots that changed between pre and post state are included.
+///
+/// In diff mode, returns separate `pre` and `post` snapshots with unchanged
+/// fields and accounts stripped.
+pub fn build_prestate_frame<S: Blockstore, T: Blockstore>(
```
>>> Lot's of conditionals in this method. Please go through coverage report and check if there are paths not covered by unit tests.
-- INLINE src/state_manager/mod.rs:859
```diff
+    /// Replays a tipset up to a target message, capturing the state root before
+    /// and after execution.
+    pub async fn replay_for_prestate(
+        self: &Arc<Self>,
+        ts: Tipset,
+        target_mcid: Cid,
```
>>> what is `mcid`? `message_cid`? If so, let's use full name.
-- INLINE src/state_manager/mod.rs:921
```diff
+                            ApiInvocResult {
+                                msg_cid: cid,
+                                msg: msg.message().clone(),
+                                msg_rct: Some(ret.msg_receipt()),
+                                error: ret.failure_info().unwrap_or_default(),
+                                duration: duration.as_nanos().clamp(0, u64::MAX as u128) as u64,
```
>>> you'll probably get some clippy warnings if you rebase on top of `main` now
-- INLINE src/rpc/methods/eth.rs:3582
```diff
+                    transaction_position: entry.msg_position,
+                })
+                .collect();
+            Ok(GethTrace::FlatCall(traces))
+        }
+        _ => unreachable!("noopTracer and prestateTracer handled above"),
```
>>> Let's avoid panicking. Perhaps now it's handled but later one someone will change some code and Forest will start crashing out of nowhere. Prefer logging an error unless it's 100% obvious that it will never reach this condition.
-- INLINE src/rpc/methods/eth/trace/utils.rs:1
```diff
@@ -0,0 +1,27 @@
+// Copyright 2019-2026 ChainSafe Systems
```
>>> it should be easy to contrive some tests here
-- INLINE src/rpc/methods/eth/trace/types.rs:436
```diff
+    ///
+    /// This is not a complete check for reverted traces (there are other possible revert reasons).
+    pub fn is_reverted(&self) -> bool {
+        self.error
+            .as_deref()
+            .is_some_and(|e| e == PARITY_TRACE_REVERT_ERROR)
```
>>> I'm not sold on storing an error as a string. Can we have `EthTrace` errors as proper errors, e.g., via https://docs.rs/thiserror/latest/thiserror/? Less allocation overhead, cheaper operations and something actually strongly typed.


==================== PR #6761 by hanabi1224 [closed merged] perf: add lru cache for load_executed_tipset to speed up hot queries
https://github.com/ChainSafe/forest/pull/6761
review sequence: DISMISSED, APPROVED


==================== PR #6780 by hanabi1224 [closed merged] fix: track F3 finalized tipset in chain store
https://github.com/ChainSafe/forest/pull/6780
review sequence: COMMENTED, COMMENTED, DISMISSED, APPROVED
-- INLINE src/daemon/mod.rs:487
```diff
         });
+        tokio::task::spawn({
+            let chain_store = ctx.chain_store().clone();
+            async move {
+                // wait 1s to let F3 RPC server start
+                tokio::time::sleep(Duration::from_secs(1)).await;
```
>>> It's a bit shaky assumption; can we make it more resilient?
-- INLINE src/chain/store/index.rs:24
```diff
 
 type TipsetCache = SizeTrackingLruCache<TipsetKey, Tipset>;
 
+type TipsetHeightCache = SizeTrackingLruCache<ChainEpoch, TipsetKey>;
+
+type IsTipsetFinalizedFn = Arc<dyn Fn(&Tipset) -> bool + Send + Sync>;
```
>>> Why do we need an atomic shared pointer here? Can we have a simple fat pointer `Box` instead? I understand this might have been a copy-pasta from some existing code we have, so perhaps it's not needed there as well.


==================== PR #6785 by hanabi1224 [closed merged] feat: finality calculator
https://github.com/ChainSafe/forest/pull/6785
review sequence: APPROVED
-- REVIEW APPROVED
I believe in this math and do not believe in God. In both cases, I might be wrong.
-- CONVERSATION
@hanabi1224 I think it should be relatively easy to cover the remaining LoC with tests; those are some edge cases, but easily constructible.


==================== PR #6788 by sudo-shashank [closed merged] Revamp Message Pool Nonce calculation
https://github.com/ChainSafe/forest/pull/6788
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED
-- REVIEW COMMENTED
Apart from existing comments, no other big concerns. I'll let @hanabi1224 give a final sign-off.
-- CONVERSATION
@sudo-shashank Can you please summarise what are the effective logic changes?
-- INLINE docs/docs/developers/guides/nonce_handling.md:19
```diff
+from on-chain state and from pending (not-yet-included) messages.
+
+## State nonce calculation
+
+The **state nonce** is the next expected nonce derived from on-chain data. It is
+computed by `get_state_sequence` in `src/message_pool/msgpool/msg_pool.rs`:
```
>>> Those should be permalinks in case anything changes within the code, i.e., renaming.
-- INLINE docs/docs/developers/guides/nonce_handling.md:1
```diff

```
>>> Any reason not to put this information in the message pool Rust module as top-level Rust docs?

It kind of improves discoverability and maintenance burden.
-- INLINE (reply) docs/docs/developers/guides/nonce_handling.md:1
```diff

```
>>> An additional benefit there is that if you mention functions/constants in the docs via links, anyone that modifies them get immediate feedback from the compiler, which reduces code/docs drift.
-- INLINE docs/docs/developers/guides/nonce_handling.md:101
```diff
+reading gas estimates that both assume the same balance). The global lock
+prevents nonce collisions specifically. Separating them allows gas estimation to
+proceed in parallel for different senders while the nonce-critical section
+remains serialized.
+
+## Nonce persistence
```
>>> Why do we need to persiste them?
-- INLINE scripts/tests/calibnet_delegated_wallet_check.sh:33
```diff
@@ -30,7 +30,7 @@ $FOREST_WALLET_PATH export "$DELEGATE_ADDR_ONE" > delegated_wallet.key
 $FOREST_WALLET_PATH --remote-wallet import delegated_wallet.key
 
 # Fund delegated wallet from preloaded wallet
-DELEGATE_FUND_AMT="2 micro FIL"
+DELEGATE_FUND_AMT="3 micro FIL"
```
>>> why?
-- INLINE src/message_pool/msgpool/mod.rs:701
```diff
+    #[tokio::test]
+    async fn test_head_change_removes_via_resolved_address() {
+        let tma = TestApi::default();
+        let keystore = KeyStore::new(KeyStoreConfig::Memory).unwrap();
+        let mut wallet = Wallet::new(keystore);
+        let key_addr = wallet.generate_addr(SignatureType::Bls).unwrap();
```
>>> Seems like there's a bit of codee duplication in the tests; can we extract some common setup logic?
-- INLINE (reply) docs/docs/developers/guides/nonce_handling.md:101
```diff
+reading gas estimates that both assume the same balance). The global lock
+prevents nonce collisions specifically. Separating them allows gas estimation to
+proceed in parallel for different senders while the nonce-critical section
+remains serialized.
+
+## Nonce persistence
```
>>> Reached out on Slack; let's agree there.
-- INLINE scripts/tests/calibnet_delegated_wallet_check.sh:1
```diff

```
>>> Not a fault of this PR, but this bash script is horrible. 😆 We need to convert it to proper Rust code to make it maintainable.
-- INLINE (reply) scripts/tests/calibnet_delegated_wallet_check.sh:1
```diff

```
>>> @coderabbitai create an issue
-- INLINE src/message_pool/msgpool/provider.rs:108
```diff
+
+    // NOTE: Lotus resolves to deterministic address at finality to guarantee
+    // the ID→key mapping is reorg-stable. We currently resolve against the
+    // tipset's parent state, which is safe but does not provide the same
+    // reorg-safety guarantee.
+    // See https://github.com/filecoin-project/lotus/blob/006da4c7e1c1c29ac02b32112c0d205e4085ba35/chain/stmgr/stmgr.go#L347
```
>>> What can we do about it? Could the finality calculator integration from @hanabi1224 help? Should we have a follow-up issue?
-- INLINE src/message_pool/msgpool/provider.rs:117
```diff
+            .resolve_to_deterministic_addr(self.blockstore(), *addr)
+            .map_err(|e| Error::Other(e.to_string()))
+    }
+
+    fn messages_for_tipset(&self, ts: &Tipset) -> Result<Vec<ChainMessage>, Error> {
+        Ok((*ChainStore::messages_for_tipset(self, ts)?).clone())
```
>>> Not necessarily to do in this PR, but I wonder if cloning the messages around is necessary. It makes code easier to read but perhaps at a performance hit (it might be acceptable, though - is it done once per epoch?)
-- INLINE src/message_pool/mpool_locker.rs:36
```diff
+        }
+    }
+
+    /// Acquire an async lock for the given address. The returned guard must be
+    /// held for the duration of the nonce-assign + sign + push critical section.
+    pub async fn take_lock(&self, addr: Address) -> OwnedMutexGuard<()> {
```
>>> This map grows ounbounded. On a long-running public RPC node this could be a vector of attack.

A potential fix would be to drop entries for which there are no more `Arc` consumers (`strong_count == 0`).
-- INLINE src/message_pool/msgpool/msg_pool.rs:341
```diff
+    }
+
+    let actor = api.get_actor_after(addr, cur_ts)?;
+    let mut next_nonce = actor.sequence;
+
+    if let (Ok(resolved), Ok(messages)) = (
```
>>> should we have at least a warning if this fails?
-- INLINE src/message_pool/msgpool/mod.rs:350
```diff
     } else {
-        remove(from, pending, sequence, true)?;
+        let resolved = resolve_to_key(api, key_cache, from, ts)?;
+        remove(&resolved, pending, sequence, true)?;
     }
     Ok(())
```
>>> This could be simplified, no?
-- INLINE src/rpc/methods/mpool.rs:292
```diff
+        let required_funds = &message.value + &message.gas_fee_cap * message.gas_limit;
+        if balance < required_funds {
+            return Err(anyhow::anyhow!(
+                "mpool push: not enough funds: {} < {}",
+                balance,
+                required_funds
```
>>> ```suggestion
                "mpool push: not enough funds: {balance} < {required_funds}"
```
-- INLINE src/message_pool/msgpool/mod.rs:290
```diff
+                    &ts,
+                    &msg.from,
+                    pending,
+                    msg.sequence,
+                    rmsgs.borrow_mut(),
+                )?;
```
>>> Let's try not to bloat the function signature. Three parameters is already a stretch.
-- INLINE src/message_pool/msgpool/mod.rs:308
```diff
+    let cur_ts = cur_tipset.read().clone();
     for (_, hm) in rmsgs {
         for (_, msg) in hm {
-            let sequence = get_state_sequence(api, &msg.from(), &cur_tipset.read().clone())?;
+            let sequence =
+                get_state_sequence(api, key_cache, state_nonce_cache, &msg.from(), &cur_ts)?;
```
>>> Same; let's try not to bloat the signatures.
-- INLINE src/message_pool/msgpool/mod.rs:330
```diff
@@ -308,20 +327,19 @@ where
 /// This is a helper function for `head_change`. This method will remove a
 /// sequence for a from address from the messages selected by priority hash-map.
 /// It also removes the 'from' address and sequence from the `MessagePool`.
-pub(in crate::message_pool) fn remove_from_selected_msgs(
+pub(in crate::message_pool) fn remove_from_selected_msgs<T: Provider>(
```
>>> Sanity check: does the method description still reflect the actual code?


==================== PR #6793 by hanabi1224 [closed merged] fix: avoid validating forest car twice in AnyCar::new
https://github.com/ChainSafe/forest/pull/6793
review sequence: APPROVED


==================== PR #6796 by dependabot[bot] [closed merged] chore(deps-dev): bump @types/node from 25.4.0 to 25.5.0 in /docs
https://github.com/ChainSafe/forest/pull/6796
review sequence: APPROVED


==================== PR #6797 by hanabi1224 [closed merged] fix: unify logic of getting finalized tipset
https://github.com/ChainSafe/forest/pull/6797
review sequence: APPROVED


==================== PR #6798 by hanabi1224 [closed merged] fix: replace unnecessary Arc<dyn Fn> with Box<dyn Fn> where shared ownership is not needed
https://github.com/ChainSafe/forest/pull/6798
review sequence: APPROVED


==================== PR #6804 by dependabot[bot] [closed merged] chore(deps): bump undici from 7.22.0 to 7.24.4 in /docs
https://github.com/ChainSafe/forest/pull/6804
review sequence: APPROVED


==================== PR #6807 by leshyatwork[bot] [closed merged] [automated] Update Forest RPC reference docs
https://github.com/ChainSafe/forest/pull/6807
review sequence: APPROVED


==================== PR #6808 by leshyatwork[bot] [closed merged] [automated] Update Forest checkpoints
https://github.com/ChainSafe/forest/pull/6808
review sequence: APPROVED
-- CONVERSATION
https://beryx.io/fil/mainnet/block-cid/bafy2bzacedmzhjqp4njt4vfjqjelkwo76ytd2kmrfbuloadnhjul6zxuegk6c :ok_hand:


==================== PR #6811 by hanabi1224 [closed merged] feat(rpc): impl Filecoin.ChainGetTipSetFinalityStatus
https://github.com/ChainSafe/forest/pull/6811
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
no green checkmark, no review!
-- INLINE src/rpc/methods/chain/types.rs:34
```diff
+    #[serde(with = "crate::lotus_json")]
+    #[schemars(with = "LotusJson<Option<Tipset>>")]
+    pub ec_finalized_tip_set: Option<Tipset>,
+
+    /// The tipset finalized by F3 (Fast Finality), if F3
+    /// is running and has issued a certificate. Nil if F3 is not available.
```
>>> I understand this is taken from Lotus, but let's not use `nil` in Forest documentation.
-- INLINE src/rpc/methods/chain.rs:1200
```diff
+            DEFAULT_BLOCKS_PER_EPOCH, DEFAULT_BYZANTINE_FRACTION, DEFAULT_GUARANTEE,
+            find_threshold_depth,
+        };
+
+        let finality = ctx.chain_config().policy.chain_finality;
+        let chain_len = finality as usize + 5;
```
>>> what's this magic number?
-- INLINE src/rpc/methods/chain.rs:1215
```diff
+                    if chain.len() < chain_len {
+                        chain.push(0);
+                    } else {
+                        break;
+                    }
+                }
```
>>> Any reason to do it manually? Seems like this could be a `Vec::resize` (with guard) or [pad using](https://docs.rs/itertools/latest/itertools/trait.Itertools.html#method.pad_using). The former would do a single allocation, though, not sure about the latter.
-- INLINE src/rpc/methods/chain.rs:1199
```diff
+        use crate::chain::ec_finality::calculator::{
+            DEFAULT_BLOCKS_PER_EPOCH, DEFAULT_BYZANTINE_FRACTION, DEFAULT_GUARANTEE,
+            find_threshold_depth,
+        };
+
+        const FINALITY_CHAIN_EXTRA_EPOCHS: usize = 5;
```
>>> Let's add some comment around this; why is it five? Why not 10? Why do we need those extra epochs?


==================== PR #6812 by hanabi1224 [closed merged] refactor: simplify hasher usage
https://github.com/ChainSafe/forest/pull/6812
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/utils/proofs_api/parameters.rs:65
```diff
-            let mut reader = SyncBufReader::new(file);
+        move || -> io::Result<Hash> {
+            let mut reader = std::io::BufReader::new(file);
             let mut hasher = Blake2b::new();
-            sync_copy(&mut reader, &mut hasher)?;
+            std::io::copy(&mut reader, &mut hasher)?;
```
>>> any reason not to use the tokio copy?
-- INLINE (reply) src/utils/proofs_api/parameters.rs:65
```diff
-            let mut reader = SyncBufReader::new(file);
+        move || -> io::Result<Hash> {
+            let mut reader = std::io::BufReader::new(file);
             let mut hasher = Blake2b::new();
-            sync_copy(&mut reader, &mut hasher)?;
+            std::io::copy(&mut reader, &mut hasher)?;
```
>>> I guess it's not a huge deal, it was sync in the first place anyway


==================== PR #6816 by dependabot[bot] [closed merged] chore(deps): bump toml from 1.0.7+spec-1.1.0 to 1.1.0+spec-1.1.0
https://github.com/ChainSafe/forest/pull/6816
review sequence: APPROVED


==================== PR #6817 by hanabi1224 [closed merged] fix(rpc): update StateSearchMsg to return null instead of error when not found
https://github.com/ChainSafe/forest/pull/6817
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
Let's add a changelog entry, it's a user-facing change.


==================== PR #6818 by hanabi1224 [closed merged] fix(ci): migrate from buildjet to warpbuild
https://github.com/ChainSafe/forest/pull/6818
review sequence: COMMENTED, APPROVED
-- INLINE .github/workflows/coverage.yml:47
```diff
@@ -44,7 +44,7 @@ jobs:
   codecov:
     name: Coverage
     if: github.event.pull_request.draft == false && github.actor != 'dependabot[bot]'
-    runs-on: buildjet-4vcpu-ubuntu-2204
+    runs-on: warp-ubuntu-2404-arm64-4x
```
>>> let's avoid changing the CPU architecture in this PR


==================== PR #6819 by hanabi1224 [closed merged] refactor: relax generic constraint on ReadOpsTrackingStore
https://github.com/ChainSafe/forest/pull/6819
review sequence: APPROVED


==================== PR #6821 by hanabi1224 [closed merged] feat(tool): print message receipt size and event size in `forest-tool archive info`
https://github.com/ChainSafe/forest/pull/6821
review sequence: APPROVED


==================== PR #6822 by hanabi1224 [closed merged] fix(ci): update warpbuild images
https://github.com/ChainSafe/forest/pull/6822
review sequence: APPROVED


==================== PR #6827 by hanabi1224 [closed merged] tool: forest-dev export-tipset-lookup
https://github.com/ChainSafe/forest/pull/6827
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/dev/subcommands/export_tipset_lookup_cmd.rs:38
```diff
+    /// Start epoch (inclusive). Defaults to the current chain head
+    #[arg(long)]
+    from: Option<ChainEpoch>,
+    /// End epoch (inclusive). Defaults to 0 (genesis)
+    #[arg(long)]
+    to: Option<ChainEpoch>,
```
>>> Why `Option` if we have a sane default?
-- INLINE src/dev/subcommands/export_tipset_lookup_cmd.rs:1
```diff

```
>>> What's the mapping in case of null tipsets?
-- INLINE (reply) src/dev/subcommands/export_tipset_lookup_cmd.rs:1
```diff

```
>>> I guess it's a fair design choice; in the checkpoint generation logic: https://github.com/ChainSafe/forest/blob/feeb99859a02fcf7ede4803404578824dc96985a/src/dev/subcommands/update_checkpoints_cmd.rs#L211-L213

we use `ChainGetTipSetByHeight` which grabs the older epoch. This way we avoid bigger gaps. Not a big deal, so I'll leave this up to you.

Potentially, we could use some default Cid for null tipsets to be explicit.


==================== PR #6829 by akaladarshi [closed merged] refactor: use typed errors in eth trace
https://github.com/ChainSafe/forest/pull/6829
-- CONVERSATION
no green checkmark, no review!


==================== PR #6841 by hanabi1224 [closed merged] fix: no reboot after GC
https://github.com/ChainSafe/forest/pull/6841
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, DISMISSED, APPROVED
-- REVIEW COMMENTED
Not sure I'm sold on this approach. I'd love to see some numbers to back this up.
-- REVIEW COMMENTED
As discussed, it'd be great to have some numbers on a relatively large database on mainnet, e.g., 1 month worth of data with two weeks retention. I'd like to be able to inform users (better, point them to docs) with expected duration of the process.

I drop my concern around `RwLock` in hot path - given the underlying access is disk IO, any lock overhead is likely negligible.
-- INLINE src/db/parity_db/gc.rs:43
```diff
+                format!(
+                    "failed to reopen parity-db at {}",
+                    self.options.path.display()
+                )
+            })
+            .expect("infallible");
```
>>> I don't get it; you're adding an error message via `with_context` but then you claim it's infallible. If it's infallible you don't need an error message, no? That said, it seems totally fallible.
-- INLINE src/db/gc/snapshot.rs:245
```diff
+                    Ok((k, v)) => {
+                        map.insert(k, v);
+                    }
+                    Err(tokio::sync::broadcast::error::RecvError::Closed) => break,
+                    Err(tokio::sync::broadcast::error::RecvError::Lagged(skipped)) => {
+                        tracing::warn!("write ops channel lagged, skipped {skipped} ops");
```
>>> what are the implications of lost data?
-- INLINE src/db/parity_db/gc.rs:18
```diff
+}
+
+/// A wrapper around `ParityDb` that provides a method to reset the columns used for garbage collection.
+pub struct GarbageCollectableParityDb {
+    options: Options,
+    db: RwLock<ParityDb>,
```
>>> So the way I'm seeing this, every single blockstore operation now pays the locking price. Is this price steep? Not sure.
-- INLINE src/db/parity_db/gc.rs:65
```diff
+            const MAX_RETRIES: usize = 10;
+            for i in 1..=MAX_RETRIES {
+                match parity_db::Db::reset_column(&mut options, col, None) {
+                    Ok(_) => break,
+                    Err(_) if i < MAX_RETRIES => {
+                        std::thread::sleep(Duration::from_secs(1));
```
>>> Ultimately this is called from an async context down the line and would block the worker thread even for `MAX_RETRIES*GC_COLUMNS.len()` seconds (so 20s). I think we could avoid with async sleep (or putting it on a dedicated blocking thread)
-- INLINE src/db/car/many.rs:331
```diff
     }
 }
 
+impl<T: GarbageCollectableDb> GarbageCollectableDb for ManyCar<T> {
+    fn reset_gc_columns(&self) -> anyhow::Result<()> {
+        self.writer().reset_gc_columns()
```
>>> I'm not sold on this. You put a write lock during GC - during this period all read operations are blocked, no?
-- INLINE src/db/gc/snapshot.rs:206
```diff
 
+    async fn gc_once(&self) {
+        if self.running.load(Ordering::Relaxed) {
+            tracing::warn!("snap gc has already been running");
+        } else {
+            self.running.store(true, Ordering::Relaxed);
```
>>> That's a [TOCTOU](https://en.wikipedia.org/wiki/Time-of-check_to_time-of-use)! Extremely unlikely (one would have to pair scheduled GC with triggered GC and do it at exactly same moment)
-- INLINE (reply) src/db/gc/snapshot.rs:206
```diff
 
+    async fn gc_once(&self) {
+        if self.running.load(Ordering::Relaxed) {
+            tracing::warn!("snap gc has already been running");
+        } else {
+            self.running.store(true, Ordering::Relaxed);
```
>>> I think the way to avoid this is https://doc.rust-lang.org/std/sync/atomic/struct.AtomicBool.html#method.compare_exchange
-- INLINE (reply) src/db/parity_db/gc.rs:65
```diff
+            const MAX_RETRIES: usize = 10;
+            for i in 1..=MAX_RETRIES {
+                match parity_db::Db::reset_column(&mut options, col, None) {
+                    Ok(_) => break,
+                    Err(_) if i < MAX_RETRIES => {
+                        std::thread::sleep(Duration::from_secs(1));
```
>>> It's not fixed, not? You're still using sync sleep `std::thread::sleep`, whereas the _correct_ way is to use `tokio::time::sleep` so that the task can yield and something else can be done in the meantime. Am I missing anything?
-- INLINE (reply) src/db/parity_db/gc.rs:43
```diff
+                format!(
+                    "failed to reopen parity-db at {}",
+                    self.options.path.display()
+                )
+            })
+            .expect("infallible");
```
>>> Should we log some additional information in such a case?


==================== PR #6842 by hanabi1224 [closed merged] refactor: simplify trait impl with ambassador
https://github.com/ChainSafe/forest/pull/6842
review sequence: APPROVED


==================== PR #6847 by hanabi1224 [closed merged] fix(ci): set opt-level = 1 for ci codecov
https://github.com/ChainSafe/forest/pull/6847
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- INLINE .github/workflows/unit-tests.yml:1
```diff

```
>>> Let's just remove it; it'd still be available in git history.
-- INLINE Cargo.toml:300
```diff
+
+[profile.codecov.package."*"]
+# Avoid generating any debug information for dependencies
+debug = false
+# Speed up debug tests and codecov tests
+opt-level = 1
```
>>> Would it make sense to `opt-level = 2` for deps?
-- INLINE mise.toml:225
```diff
 [tasks.codecov]
 description = "Generate codecov report"
 run = '''
-cargo llvm-cov -p forest-filecoin --codecov --no-default-features --features "test cargo-test" --output-path lcov.info
+mise task run codecov:nextest
+mise task run codecov:cargo
```
>>> I think you can run those two in parallel, no? you'd need to mark them as dependencies
-- INLINE (reply) mise.toml:225
```diff
 [tasks.codecov]
 description = "Generate codecov report"
 run = '''
-cargo llvm-cov -p forest-filecoin --codecov --no-default-features --features "test cargo-test" --output-path lcov.info
+mise task run codecov:nextest
+mise task run codecov:cargo
```
>>> I mean, one would be locked (I guess) when the another runs, but then potentially it would start while the other is running the test. Not a huge deal if it doesn't bring noticeable improvements.
-- INLINE .github/workflows/forest.yml:69
```diff
-      - name: Cargo Check
-        run: cargo check
+      - uses: jdx/mise-action@v4
+      - run: cargo check
+      # nextest doesn't run doctests https://github.com/nextest-rs/nextest/issues/16
+      - run: mise test:docs
```
>>> So... there's no way to put doctests to coverage as well?


==================== PR #6848 by dependabot[bot] [closed merged] chore(deps): bump sha2 from 0.10.9 to 0.11.0
https://github.com/ChainSafe/forest/pull/6848
review sequence: APPROVED


==================== PR #6849 by akaladarshi [closed merged] fix: include strict bound in blocks included for calculating gas premium
https://github.com/ChainSafe/forest/pull/6849
review sequence: COMMENTED, APPROVED
-- INLINE src/rpc/methods/gas.rs:118
```diff
     if nblocksincl == 0 {
         nblocksincl = 1;
-    }
+    } else if nblocksincl > MAX_GAS_HISTORY {
+        nblocksincl = MAX_GAS_HISTORY
+    };
```
>>> Let's use some elegant Rust tools:
```rust
let nblocksincl = nblocksincl.clamp(1,MAX_GAS_HISTORY);
```


==================== PR #6867 by hanabi1224 [closed merged] doc: explain const FINALITY_CHAIN_EXTRA_EPOCHS
https://github.com/ChainSafe/forest/pull/6867
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/methods/chain.rs:1210
```diff
+        /// [`calc_validator_prob`] requires a lookback window of up to `chain_finality`
+        /// epochs of historical data — so the sample must be at least `chain_finality`
+        /// entries long. This matches the Lotus reference implementation, where
+        /// `chain/ecfinality/cache.go` documents this window size as
+        /// "finality + 5 (the lookback the calculator needs)".
+        ///
```
>>> ```suggestion
```

that's a bit of an overkill
-- INLINE src/rpc/methods/chain.rs:1202
```diff
+        /// epochs of historical data — so the sample must be at least `chain_finality`
+        /// entries long. This matches the Lotus reference implementation, where
+        /// `chain/ecfinality/cache.go` documents this window size as
+        /// "finality + 5 (the lookback the calculator needs)".
+        ///
+        /// The extra 5 epochs act as a tail buffer to prevent out-of-bounds access,
```
>>> So what happens if there are more than 5 null epochs in row? It's possible, no?
-- INLINE (reply) src/rpc/methods/chain.rs:1202
```diff
+        /// epochs of historical data — so the sample must be at least `chain_finality`
+        /// entries long. This matches the Lotus reference implementation, where
+        /// `chain/ecfinality/cache.go` documents this window size as
+        /// "finality + 5 (the lookback the calculator needs)".
+        ///
+        /// The extra 5 epochs act as a tail buffer to prevent out-of-bounds access,
```
>>> Especially on calibnet or a devnet


==================== PR #6871 by hanabi1224 [closed merged] fix: ensure the result state tree is loadable in ForestStateCompute
https://github.com/ChainSafe/forest/pull/6871
review sequence: DISMISSED


==================== PR #6872 by akaladarshi [closed merged] fix: use `i64` instead of `u64` in limit exceed error to avoid conversion
https://github.com/ChainSafe/forest/pull/6872
review sequence: APPROVED


==================== PR #6875 by hanabi1224 [closed merged] chore: force upgrading js deps to resolve all npm dependabot alerts
https://github.com/ChainSafe/forest/pull/6875
review sequence: APPROVED


==================== PR #6876 by dependabot[bot] [closed merged] chore(deps): bump similar from 2.7.0 to 3.0.0
https://github.com/ChainSafe/forest/pull/6876
review sequence: APPROVED


==================== PR #6880 by hanabi1224 [closed merged] fix: use smaller skip length for `tipset_by_height` cache
https://github.com/ChainSafe/forest/pull/6880
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/chain/store/index.rs:54
```diff
         let ts_height_cache: SizeTrackingLruCache<ChainEpoch, TipsetKey> =
             SizeTrackingLruCache::new_with_metrics(
                 "tipset_by_height".into(),
-                // 20480 * 900 = 18432000 which is sufficient for mainnet
-                nonzero!(20480_usize),
+                // 1048576 * 20 = 20971520 which is sufficient for mainnet
```
>>> clearly it was not sufficient 😆
-- INLINE src/chain/store/index.rs:56
```diff
             SizeTrackingLruCache::new_with_metrics(
                 "tipset_by_height".into(),
-                // 20480 * 900 = 18432000 which is sufficient for mainnet
-                nonzero!(20480_usize),
+                // 1048576 * 20 = 20971520 which is sufficient for mainnet
+                nonzero!(1048576_usize),
```
>>> What's the memory increase if cache is full?
-- INLINE (reply) src/chain/store/index.rs:54
```diff
         let ts_height_cache: SizeTrackingLruCache<ChainEpoch, TipsetKey> =
             SizeTrackingLruCache::new_with_metrics(
                 "tipset_by_height".into(),
-                // 20480 * 900 = 18432000 which is sufficient for mainnet
-                nonzero!(20480_usize),
+                // 1048576 * 20 = 20971520 which is sufficient for mainnet
```
>>> maybe worth creating an issue and putting it into the sprint 357!


==================== PR #6881 by hanabi1224 [closed merged] refactor: parallelize `collect_event` for tipset range
https://github.com/ChainSafe/forest/pull/6881
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/methods/eth/filter/mod.rs:415
```diff
                 for tipset in max_tipset
                     .chain(&ctx.store())
                     .take_while(|ts| ts.epoch() >= *range.start())
                 {
-                    Self::collect_events(ctx, &tipset, Some(pf), skip_event, &mut collected_events)
+                    tasks.push_back(async move {
```
>>> could we refactor this logic to its own method?
-- INLINE src/rpc/methods/eth/filter/mod.rs:273
```diff
+        tipsets: impl Iterator<Item = Tipset>,
+        spec: Option<&impl Matcher>,
+        skip_event: SkipEvent,
+        collected_events: &mut Vec<CollectedEvent>,
+    ) -> anyhow::Result<()> {
+        let mut tasks = FuturesOrdered::new();
```
>>> Hm, do they need to be ordered?
-- INLINE src/rpc/methods/eth/filter/mod.rs:374
```diff
-                        if collected_events.len() >= ctx.eth_event_handler.max_filter_results {
-                            bail!("filter matches too many events, try a more restricted filter");
+                        if collected_events.len() >= max_filter_results {
+                            bail!(
+                                "filter matches too many events (maximum {max_filter_results} allowed), try a more restricted filter"
+                            );
```
>>> 1. nit: `ensure!` is nicer
2. this doesn't seem right (an existing bug?) - shouldn't collected events be allowed to be up to `max_filter_results`? In other words, `if collected_events.len() > max_filter_results then fail`?


==================== PR #6882 by hanabi1224 [closed merged] chore: bump npm deps
https://github.com/ChainSafe/forest/pull/6882
review sequence: APPROVED


==================== PR #6885 by hanabi1224 [closed merged] feat: forest-dev export-state-tree
https://github.com/ChainSafe/forest/pull/6885
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/ipld/util.rs:454
```diff
+            if should_save_block_to_snapshot(cid) && this.seen.insert(cid) {
+                if let Some(data) = this.db.get(&cid)? {
+                    if cid.codec() == fvm_ipld_encoding::DAG_CBOR {
+                        let new_cids = extract_cids(&data)?;
+                        if !new_cids.is_empty() {
+                            this.cid_vec.reserve(new_cids.len());
```
>>> ```suggestion
```

Is this useful in any way? I would assume `extend` would do at most one allocation to accommodate for `new_cids`, no need to manually reserve it.
-- INLINE src/ipld/util.rs:455
```diff
+                if let Some(data) = this.db.get(&cid)? {
+                    if cid.codec() == fvm_ipld_encoding::DAG_CBOR {
+                        let new_cids = extract_cids(&data)?;
+                        if !new_cids.is_empty() {
+                            this.cid_vec.extend(new_cids);
+                        }
```
>>> ```suggestion
                        this.cid_vec.extend(new_cids);
```

I checked `extend` impl and it will do the right thing without us manually checking.

```rust
    #[cfg(not(no_global_oom_handling))]
    fn extend_desugared<I: Iterator<Item = T>>(&mut self, mut iterator: I) {
        // This is the case for a general iterator.
        //
        // This function should be the moral equivalent of:
        //
        //      for item in iterator {
        //          self.push(item);
        //      }
        while let Some(element) = iterator.next() {
            let len = self.len();
            if len == self.capacity() {
                let (lower, _) = iterator.size_hint();
                self.reserve(lower.saturating_add(1));
            }
            unsafe {
                ptr::write(self.as_mut_ptr().add(len), element);
                // Since next() executes user code which can panic we have to bump the length
                // after each step.
                // NB can't overflow since we would have had to alloc the address space
                self.set_len(len + 1);
            }
        }
    }
```
-- INLINE (reply) src/ipld/util.rs:455
```diff
+                if let Some(data) = this.db.get(&cid)? {
+                    if cid.codec() == fvm_ipld_encoding::DAG_CBOR {
+                        let new_cids = extract_cids(&data)?;
+                        if !new_cids.is_empty() {
+                            this.cid_vec.extend(new_cids);
+                        }
```
>>> @hanabi1224 Also, if you copy-pasted this code from somewhere, please update it there as well.
-- INLINE (reply) src/ipld/util.rs:455
```diff
+                if let Some(data) = this.db.get(&cid)? {
+                    if cid.codec() == fvm_ipld_encoding::DAG_CBOR {
+                        let new_cids = extract_cids(&data)?;
+                        if !new_cids.is_empty() {
+                            this.cid_vec.extend(new_cids);
+                        }
```
>>> Yeah, it's fine to do that in a subsequent PR. That said, I believe my suggestion to remove `is_empty` from the code you introduced is still sound, correct?


==================== PR #6889 by hanabi1224 [closed merged] refactor: no Arc wrapper around ChainIndex and ZstdFrameCache
https://github.com/ChainSafe/forest/pull/6889
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/db/car/mod.rs:55
```diff
@@ -52,11 +52,12 @@ pub static ZSTD_FRAME_CACHE_DEFAULT_MAX_SIZE: LazyLock<usize> = LazyLock::new(||
     256 * 1024 * 1024
 });
 
+#[derive(Clone)]
```
>>> This is somehow debatable. A `Clone` subjectively implies an _independent copy_; here it's kind of a weird hybrid.
```
a = cache
b = a.clone
a.current_size += 1
assert a.current_size == b.current_size // shared ownership
assert a == b // essentially same
a.max_size += 1
assert a.max_size == b.current_size // no longer a == b
```
And is `lru` also a deep or shallow clone? Can't say without going into the implementation.

My suggestion is to **not** have such ambiguity and make the code easily understandable.

For example, you could, e.g., rename it to `ZstdFrameCacheInner` (private) and make `ZstdFrameCAche { inner: Arc<ZstdFrameCacheInner> }`. It's immediately clear that it's a shallow copy and a clone just gives a handle. 

Does it make sense?
-- INLINE (reply) src/db/car/mod.rs:55
```diff
@@ -52,11 +52,12 @@ pub static ZSTD_FRAME_CACHE_DEFAULT_MAX_SIZE: LazyLock<usize> = LazyLock::new(||
     256 * 1024 * 1024
 });
 
+#[derive(Clone)]
```
>>> Yeah, either shallow clone or `Handle`. That was actually my first thought, but I don't think it's an established practice in the Rust ecosystem; I think we'd be more idiomatic with the `inner` approach, which we already have in some places in the code already.
-- INLINE (reply) src/db/car/mod.rs:55
```diff
@@ -52,11 +52,12 @@ pub static ZSTD_FRAME_CACHE_DEFAULT_MAX_SIZE: LazyLock<usize> = LazyLock::new(||
     256 * 1024 * 1024
 });
 
+#[derive(Clone)]
```
>>> Up to you; at the very least we need an explicit admonition.
-- INLINE src/utils/shallow_clone.rs:18
```diff
+
+use std::sync::Arc;
+
+pub trait ShallowClone {
+    fn shallow_clone(&self) -> Self;
+}
```
>>> Let's nicely document what this trait and its method are supposed to do (and why). It's obvious to us, might not be so obvious for a random contributor.


==================== PR #6897 by hanabi1224 [closed merged] feat(rpc): integrate ec finality calulator into Eth TipsetResolver
https://github.com/ChainSafe/forest/pull/6897
review sequence: APPROVED
-- CONVERSATION
no green checkmark!


==================== PR #6907 by hanabi1224 [closed merged] chore: deprecate `libsecp256k1` and fix RUSTSEC-2025-0161
https://github.com/ChainSafe/forest/pull/6907
review sequence: APPROVED


==================== PR #6915 by hanabi1224 [closed merged] refactor: replace `VecDequeue` with `Vec` in `stream_chain`  when applicable
https://github.com/ChainSafe/forest/pull/6915
review sequence: APPROVED
-- CONVERSATION
@hanabi1224 any performance boost/drop on mainnet?
-- CONVERSATION
> > any performance boost/drop on mainnet?
> 
> @LesnyRumcajs No difference observed on calibnet. And I won't expect a significant perf change on mainnet either. Do you need mainnet export time numbers?

I'd love a mainnet export timings (let's say, 3 on each branch) because calibnet often doesn't surface any issues.
-- CONVERSATION
> > I'd love a mainnet export timings (let's say, 3 on each branch) because calibnet often doesn't surface any issues.
> 
> @LesnyRumcajs test on DO droplet (), numbers show slight(~9% actually) performance improvement

That's amazing, worth putting in the changelog. ;)


==================== PR #6926 by sudo-shashank [closed merged] Deny unknown fields on RPC Request and Response
https://github.com/ChainSafe/forest/pull/6926
review sequence: APPROVED


==================== PR #6928 by hanabi1224 [closed merged] fix(perf): cache resolve_to_deterministic_address result
https://github.com/ChainSafe/forest/pull/6928
review sequence: APPROVED


==================== PR #6929 by hanabi1224 [closed merged] refactor: TipsetStateCache::get_or_else should not aquire write lock when cache hit
https://github.com/ChainSafe/forest/pull/6929
review sequence: APPROVED


==================== PR #6938 by hanabi1224 [closed merged] chore: bump Go deps
https://github.com/ChainSafe/forest/pull/6938
review sequence: APPROVED


==================== PR #6939 by hanabi1224 [closed merged] refactor: use file backed CID hashset to reduce memory usage in chain export
https://github.com/ChainSafe/forest/pull/6939
review sequence: COMMENTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW COMMENTED
Let's add a changelog entry for this; folks would like to know Forest potentially dropped its memory requirements by half (at the cost of slightly longer export times)
-- REVIEW DISMISSED
LGTM, anything from 🐰 to address?
-- CONVERSATION
What's the memory footprint reduction?
-- CONVERSATION
> > What's the memory footprint reduction?
> 
> @LesnyRumcajs 7-8GiB on mainnet and 1.8-2GiB on calibnet

Worth it!
-- INLINE src/cid_collections/hash_set.rs:107
```diff
+    fn default() -> Self {
+        const MAX_ATTEMPTS: usize = 10;
+        for _ in 0..MAX_ATTEMPTS {
+            // temp dir has limitation of 10GiB on some platforms, so we prefer current working directory
+            // and try multiple times to create one in case we hit that limit
+            if let Some(dir) = tempfile::tempdir_in(".")
```
>>> I'd rather we do it in the FOREST_DATA directory. Forest behavior mustn't depend on the CWD - I might be running Forest from `/tmp` or even from some HDD. It's also not nice to spam leftovers in case something fails.
-- INLINE src/cid_collections/hash_set.rs:143
```diff
+            }
+        }
+        panic!(
+            "failed to create parity db with a temporary directory after {MAX_ATTEMPTS} attempts"
+        );
+    }
```
>>> Let's just use Forest DATA_DIR we know we have proper access to. It would be really bad to crash Forest on GC just because it's running from some arbitrary directory.
-- INLINE (reply) src/cid_collections/hash_set.rs:107
```diff
+    fn default() -> Self {
+        const MAX_ATTEMPTS: usize = 10;
+        for _ in 0..MAX_ATTEMPTS {
+            // temp dir has limitation of 10GiB on some platforms, so we prefer current working directory
+            // and try multiple times to create one in case we hit that limit
+            if let Some(dir) = tempfile::tempdir_in(".")
```
>>> That's my point. Forest performance shouldn't depend on whether we run it from with the CWD of `/tmp` or `/mnt/some-hdd`.
-- INLINE src/chain/mod.rs:43
```diff
-    pub seen: CidHashSet,
+    pub seen: S,
 }
 
-pub async fn export_from_head<D: Digest>(
+impl<S: Default> Default for ExportOptions<S> {
```
>>> I assume this can't be easily derived?


==================== PR #6946 by sudo-shashank [closed merged] fix(mpool): fix flaky eth_newPendingTransactionFilter test
https://github.com/ChainSafe/forest/pull/6946
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/message_pool/msgpool/msg_pool.rs:589
```diff

```
>>> The method seems infallible now; any reason to keep `Result`?
-- INLINE src/message_pool/msgpool/msg_pool.rs:595
```diff
-                self.pending_for(&addr)
-                    .ok_or(Error::InvalidFromAddr)?
-                    .as_mut(),
-            )
+        for mset in pending.into_values() {
+            out.extend(
```
>>> We should know the total size to reserve upfront. This way, we avoid re-allocations.
-- INLINE src/message_pool/msgpool/msg_pool.rs:597
```diff
-            )
+        for mset in pending.into_values() {
+            out.extend(
+                mset.msgs
+                    .into_values()
+                    .sorted_by_key(|v| v.message().sequence),
```
>>> nit: this could be optimized a bit by using unstable sort; it's safe because we _know_ the nonce is unique in each group (right?). Unstable sort is better in such cases because it doesn't do any allocations https://doc.rust-lang.org/std/vec/struct.Vec.html#method.sort_unstable_by_key


==================== PR #6947 by hanabi1224 [closed merged] chore: reset butterflynet for nv28
https://github.com/ChainSafe/forest/pull/6947
review sequence: COMMENTED, COMMENTED, CHANGES_REQUESTED, COMMENTED, DISMISSED, DISMISSED, APPROVED
-- INLINE src/networks/actors_bundle.rs:88
```diff
@@ -85,9 +85,11 @@ pub static ACTOR_BUNDLES: LazyLock<Box<[ActorBundleInfo]>> = LazyLock::new(|| {
         "bafy2bzacebc7zpsrihpyd2jdcvmegbbk6yhzkifre3hxtoul5wdxxklbwitry" @ "v16.0.0-rc3" for "calibrationnet",
         "bafy2bzacecqtwq6hjhj2zy5gwjp76a4tpcg2lt7dps5ycenvynk2ijqqyo65e" @ "v16.0.1" for "calibrationnet",
         "bafy2bzacecn64rlb52rjsvgopnidz6w42z3zobmjxqek5s4xqjh3ly47rcurg" @ "v17.0.0" for "calibrationnet",
+        "bafy2bzaceaj5coptgsetafbr5hicqrlqror5662qbzuho24husf2bk4nol5xu" @ "v18.0.0-rc1" for "calibrationnet",
```
>>> any reason for adding the bundle for calibnet here?
-- INLINE src/networks/actors_bundle.rs:91
```diff
         "bafy2bzacecqtwq6hjhj2zy5gwjp76a4tpcg2lt7dps5ycenvynk2ijqqyo65e" @ "v16.0.1" for "calibrationnet",
         "bafy2bzacecn64rlb52rjsvgopnidz6w42z3zobmjxqek5s4xqjh3ly47rcurg" @ "v17.0.0" for "calibrationnet",
+        "bafy2bzaceaj5coptgsetafbr5hicqrlqror5662qbzuho24husf2bk4nol5xu" @ "v18.0.0-rc1" for "calibrationnet",
         "bafy2bzacearjal5rsmzloz3ny7aoju2rgw66wgxdrydgg27thcsazbmf5qihq" @ "v15.0.0-rc1" for "butterflynet",
         "bafy2bzaceda5lc7qrwp2hdm6s6erppwuydsfqrhbgld7juixalk342inqimbo" @ "v16.0.1" for "butterflynet",
         "bafy2bzacedzjwguwuihh4tptzfkkwaj3naamrnklbaixn2wfzqh67twwp56pi" @ "v17.0.0" for "butterflynet",
```
>>> you removed some bundles from butterflynet source code, so it doesn't make sense to keep them around here
-- INLINE (reply) src/networks/actors_bundle.rs:88
```diff
@@ -85,9 +85,11 @@ pub static ACTOR_BUNDLES: LazyLock<Box<[ActorBundleInfo]>> = LazyLock::new(|| {
         "bafy2bzacebc7zpsrihpyd2jdcvmegbbk6yhzkifre3hxtoul5wdxxklbwitry" @ "v16.0.0-rc3" for "calibrationnet",
         "bafy2bzacecqtwq6hjhj2zy5gwjp76a4tpcg2lt7dps5ycenvynk2ijqqyo65e" @ "v16.0.1" for "calibrationnet",
         "bafy2bzacecn64rlb52rjsvgopnidz6w42z3zobmjxqek5s4xqjh3ly47rcurg" @ "v17.0.0" for "calibrationnet",
+        "bafy2bzaceaj5coptgsetafbr5hicqrlqror5662qbzuho24husf2bk4nol5xu" @ "v18.0.0-rc1" for "calibrationnet",
```
>>> Yes, let's exclude this. This should be added with a PR that sets the calibnet height for NV28. Let's keep it focused on butterflynet.


==================== PR #6950 by akaladarshi [closed merged] feat(api): register missing miner actor v18 methods
https://github.com/ChainSafe/forest/pull/6950
review sequence: APPROVED


==================== PR #6956 by dependabot[bot] [closed merged] chore(deps): bump hashbrown from 0.16.1 to 0.17.0
https://github.com/ChainSafe/forest/pull/6956
review sequence: APPROVED


==================== PR #6961 by hanabi1224 [closed merged] refactor: optimize cache key type for `resolve_to_key`
https://github.com/ChainSafe/forest/pull/6961
review sequence: DISMISSED, COMMENTED, APPROVED
-- INLINE src/message_pool/msgpool/mod.rs:55
```diff
@@ -52,7 +52,7 @@ async fn republish_pending_messages<T>(
     cur_tipset: &SyncRwLock<Tipset>,
     republished: &SyncRwLock<HashSet<Cid>>,
     local_addrs: &SyncRwLock<Vec<Address>>,
-    key_cache: &SizeTrackingLruCache<Address, Address>,
+    key_cache: &SizeTrackingLruCache<u64, Address>,
```
>>> It'd be nicer to have some named type instead of just `u64`


==================== PR #6963 by hanabi1224 [closed merged] chore: unignore `RUSTSEC-2026-0097` in `deny.toml`
https://github.com/ChainSafe/forest/pull/6963
review sequence: APPROVED


==================== PR #6964 by hanabi1224 [closed merged] fix(ci): do not update custom Lotus image
https://github.com/ChainSafe/forest/pull/6964
review sequence: APPROVED


==================== PR #6965 by akaladarshi [closed merged] refactor: msg pool to make more structured
https://github.com/ChainSafe/forest/pull/6965
review sequence: COMMENTED, COMMENTED, COMMENTED, DISMISSED, COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
@akaladarshi Anything with :rabbit: comments to address? If not, mark them as resolved.
-- CONVERSATION
> Coverage check is failing dues to `TODO` lint error

The lint was removed in https://github.com/ChainSafe/forest/pull/6977.
-- INLINE src/message_pool/msgpool/events.rs:1
```diff

```
>>> Let's ease up on the comments; ideally documentation should explain things high-level, and not repeat the implementation.
-- INLINE src/message_pool/msgpool/events.rs:11
```diff
+use crate::message::SignedMessage;
+
+pub(in crate::message_pool) const MPOOL_UPDATE_CHANNEL_CAPACITY: usize = 256;
+
+/// A change to the pending pool.
+#[allow(dead_code)] // payloads consumed by external subscribers.
```
>>> ```suggestion
#[allow(dead_code)] // TODO: This will be used in https://github.com/ChainSafe/forest/pull/6941
```
-- INLINE src/message_pool/msgpool/pending_store.rs:9
```diff
+//! Pending message storage.
+//!
+//! [`PendingStore`] owns the per-actor [`MsgSet`] map and the
+//! [`MpoolUpdate`] broadcast channel. It is the single place in the code
+//! where the pending map is mutated, which lets observers subscribe to one
+//! event stream.
```
>>> This is a doc for `PendingStore`, not for the module itself.
-- INLINE src/message_pool/msgpool/pending_store.rs:21
```diff
+use crate::message_pool::msgpool::msg_set::{MsgSet, MsgSetLimits, StrictnessPolicy};
+use crate::shim::address::Address;
+
+/// Owns the per-actor [`MsgSet`] map and the [`MpoolUpdate`] broadcast
+/// channel. The single place where the pending map is mutated.
+#[derive(Clone)]
```
>>> This should be likely a `ShallowClone`, per @hanabi1224 's established convention.
-- INLINE src/message_pool/msgpool/pending_store.rs:123
```diff
+    pub fn subscribe(&self) -> broadcast::Receiver<MpoolUpdate> {
+        self.inner.events.subscribe()
+    }
+
+    /// `true` while at least one subscriber holds a live.
+    pub(in crate::message_pool) fn has_subscribers(&self) -> bool {
```
>>> There is already similar method in `utils/`. Not sure which one is more correct, but I don't see a point in having two implementations doing the same thing differently. Let's pick one.


==================== PR #6966 by 0xDevNinja [closed merged] fix(tool): surface MessagePool service task errors in api_cmd test ctx
https://github.com/ChainSafe/forest/pull/6966
-- CONVERSATION
Seems to timeout the tests. Please first assert locally that it works.


==================== PR #6967 by 0xDevNinja [closed] feat(wallet)!: apply FRC-0102 envelope to sign/verify by default
https://github.com/ChainSafe/forest/pull/6967
-- CONVERSATION
No activity, closing.


==================== PR #6970 by sudo-shashank [closed merged] Enable strict json check in api compare
https://github.com/ChainSafe/forest/pull/6970
review sequence: COMMENTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW CHANGES_REQUESTED
Why the do the snap files change?
-- INLINE docs/openrpc-specs/v1.json:10461
```diff
           "UpgradeTuktukHeight",
           "UpgradeTeepHeight",
           "UpgradeTockHeight",
-          "UpgradeGoldenWeekHeight"
+          "UpgradeGoldenWeekHeight",
+          "UpgradeXxHeight"
```
>>> this doesn't seem right
-- INLINE src/rpc/methods/state.rs:3234
```diff
@@ -3229,7 +3231,7 @@ pub struct ForkUpgradeParams {
     upgrade_teep_height: ChainEpoch,
     upgrade_tock_height: ChainEpoch,
     upgrade_golden_week_height: ChainEpoch,
-    //upgrade_xxx_height: ChainEpoch,
+    upgrade_xx_height: ChainEpoch,
```
>>> This is not correct.
-- INLINE (reply) docs/openrpc-specs/v1.json:10461
```diff
           "UpgradeTuktukHeight",
           "UpgradeTeepHeight",
           "UpgradeTockHeight",
-          "UpgradeGoldenWeekHeight"
+          "UpgradeGoldenWeekHeight",
+          "UpgradeXxHeight"
```
>>> I don't see XXX anywhere on master https://github.com/filecoin-project/lotus/blob/53f6fe609eea81c945bc832b201685bc96ca5ef1/api/types.go#L166-L200
-- INLINE (reply) docs/openrpc-specs/v1.json:10461
```diff
           "UpgradeTuktukHeight",
           "UpgradeTeepHeight",
           "UpgradeTockHeight",
-          "UpgradeGoldenWeekHeight"
+          "UpgradeGoldenWeekHeight",
+          "UpgradeXxHeight"
```
>>> Yeah, that's some bad release, best to wait for the new one.
-- INLINE src/rpc/methods/state/types.rs:210
```diff
@@ -149,6 +195,21 @@ pub struct ExecutionTrace {
     #[serde(with = "crate::lotus_json")]
     #[schemars(with = "LotusJson<Vec<ExecutionTrace>>")]
     pub subcalls: Vec<ExecutionTrace>,
+    #[serde(default, skip_serializing_if = "Vec::is_empty")]
```
>>> I'll let @akaladarshi pronounce on the correctness of this.
-- INLINE src/rpc/methods/common.rs:111
```diff
@@ -106,6 +107,8 @@ pub struct PublicVersion {
     #[serde(rename = "APIVersion")]
     pub api_version: ShiftingVersion,
     pub block_delay: u32,
+    #[serde(default, skip_serializing_if = "Option::is_none")]
+    pub agent: Option<String>,
```
>>> Why is this an `Option`? In Lotus this field doesn't seem optional.

https://github.com/filecoin-project/lotus/blob/732e604eb2bb505ac45016065533bdfd4b9c1b41/api/api_common.go#L77-L78

The goal here is not to suppress the errors coming from strict JSON checks, but to provide a meaningful fix.

@sudo-shashank for every structure in the RPC responses that you changed, please provide a link to the reference implementation (if it exists) so that it's easy to assert correctness.
-- INLINE src/rpc/methods/state/types.rs:209
```diff
@@ -149,6 +205,13 @@ pub struct ExecutionTrace {
     #[serde(with = "crate::lotus_json")]
     #[schemars(with = "LotusJson<Vec<ExecutionTrace>>")]
     pub subcalls: Vec<ExecutionTrace>,
+    /// FVM invocation logs (not EVM actor / `eth_getLogs` event logs).
+    /// See <https://github.com/filecoin-project/lotus/blob/a0ecb8687f1c60d5e66040b6de364dbc9cc4d253/chain/types/execresult.go#L115>
```
>>> The links to the Lotus code shouldn't be a exposed here (as doc comments); they're an internal detail for our reference. Most importantly, they shouldn't be a part of the OpenRPC spec.
-- INLINE src/lotus_json/message.rs:39
```diff
     #[serde(default)]
     method: u64,
-    #[schemars(with = "LotusJson<Option<RawBytes>>")]
+    #[schemars(with = "LotusJson<RawBytes>")]
+    #[serde(with = "crate::lotus_json", default)]
+    params: RawBytes,
```
>>> Why?


==================== PR #6983 by sudo-shashank [closed merged] Refactor calibnet wallet test
https://github.com/ChainSafe/forest/pull/6983
review sequence: CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, CHANGES_REQUESTED, APPROVED
-- REVIEW CHANGES_REQUESTED
The test themselves shouldn't use the `?` shorthand; in case of a failure the exact line which failed won't propagate and will make things more difficult to debug. `unwrap` should be used there and the tests shouldn't return `Result`.
-- INLINE tests/common/calibnet_wallet_helpers.rs:7
```diff
+// SPDX-License-Identifier: Apache-2.0, MIT
+
+//! Helpers for the calibnet wallet integration tests in
+//! [`tests/calibnet_wallet.rs`](../../calibnet_wallet.rs).
+
+#![allow(dead_code)]
```
>>> why?
-- INLINE tests/common/calibnet_wallet_helpers.rs:21
```diff
+use serde_json::{Value, json};
+use tempfile::NamedTempFile;
+
+/// Funded preloaded address from env `PRELOADED_ADDRESS` (see `forest_wallet_init`).
+pub static PRELOADED_ADDRESS: LazyLock<String> = LazyLock::new(|| {
+    std::env::var("PRELOADED_ADDRESS")
```
>>> What's the convention for environment variables in Forest?
-- INLINE tests/common/calibnet_wallet_helpers.rs:19
```diff
+use anyhow::{Context as _, bail};
+use parking_lot::Mutex;
+use serde_json::{Value, json};
+use tempfile::NamedTempFile;
+
+/// Funded preloaded address from env `PRELOADED_ADDRESS` (see `forest_wallet_init`).
```
>>> Links in the doc comments should point to actual code so that it's reported when a link is broken.
-- INLINE tests/common/calibnet_wallet_helpers.rs:25
```diff
+        .map(|s| s.trim().to_owned())
+        .filter(|s| !s.is_empty())
+        .expect("PRELOADED_ADDRESS must be set")
+});
+
+pub const FIL_AMT: &str = "500 atto FIL";
```
>>> What's this?
-- INLINE tests/calibnet_wallet.rs:172
```diff
+    );
+}
+
+/// Delegated signer: create once on local, fund locally, mirror to remote
+/// for tests that query or sign.
+async fn funded_delegated_addr() -> &'static str {
```
>>> shouldn't this be in the helpers?
-- INLINE mise.toml:219
```diff
+'''
+run = '''
+set -euo pipefail
+source ./scripts/tests/harness.sh
+forest_wallet_init "${usage_preloaded_key?}"
+cargo test --profile quick-test --test calibnet_wallet -- --ignored --nocapture
```
>>> I completely miss your usage of `ignored` in these tests. Please elaborate.
-- INLINE (reply) mise.toml:219
```diff
+'''
+run = '''
+set -euo pipefail
+source ./scripts/tests/harness.sh
+forest_wallet_init "${usage_preloaded_key?}"
+cargo test --profile quick-test --test calibnet_wallet -- --ignored --nocapture
```
>>> It's not a great use case for `ignored`. You should either use a feature flag or an environmental variable if feature flag introduce significantly longer compile times.
-- INLINE mise.toml:222
```diff
+usage = '''
+arg "<preloaded_key>" help="Hex-encoded preloaded calibnet wallet key (e.g. $CALIBNET_WALLET secret)"
+'''
+run = '''
+set -euo pipefail
+source ./scripts/tests/harness.sh
```
>>> Does the daemon run still after the test? If so, what if there are two consecutive tests?
-- INLINE tests/common/calibnet_wallet_helpers.rs:32
```diff
+pub const FIL_ZERO: &str = "0 FIL";
+/// Amount to seed a freshly-created delegated wallet.
+pub const DELEGATE_FUND_AMT: &str = "3 micro FIL";
+
+pub const POLL_RETRIES: usize = 20;
+pub const POLL_DELAY: Duration = Duration::from_secs(30);
```
>>> This is wasteful, making us wait for 29 seconds in worst case scenario where a condition ends up true just after the last retry. For a test that's supposed to be run often, that's long.

We should poll frequently (even sub-second) up to a specified timeout.
-- INLINE tests/common/calibnet_wallet_helpers.rs:32
```diff
+pub const FIL_ZERO: &str = "0 FIL";
+/// Amount to seed a freshly-created delegated wallet.
+pub const DELEGATE_FUND_AMT: &str = "3 micro FIL";
+
+/// Maximum time to wait for a polled condition before failing the test.
+pub const POLL_TIMEOUT: Duration = Duration::from_secs(60);
```
>>> are you sure 1 minute is enough and would not make the job flaky? the original bash script had it set to 10 minutes
-- INLINE tests/calibnet_wallet.rs:4
```diff
@@ -0,0 +1,157 @@
+// Copyright 2019-2026 ChainSafe Systems
+// SPDX-License-Identifier: Apache-2.0, MIT
+
+//! Calibnet wallet integration tests. Each `#[tokio::test]` is `#[ignore]`
```
>>> leftover
-- INLINE mise.toml:224
```diff
+'''
+run = '''
+set -euo pipefail
+source ./scripts/tests/harness.sh
+forest_wallet_init "${usage_preloaded_key?}"
+cargo test --profile quick-test --features calibnet-wallet-integration --test calibnet_wallet -- --nocapture
```
>>> The tests are run in parallel, no? Wouldn't this introduce some nonce races? It might be safer to run wallet tests on a single thread.


==================== PR #6998 by hanabi1224 [closed merged] chore(deps): bump `go-f3`
https://github.com/ChainSafe/forest/pull/6998
review sequence: APPROVED


==================== PR #7002 by hanabi1224 [closed merged] fix: flaky Kademlia test
https://github.com/ChainSafe/forest/pull/7002
review sequence: APPROVED


==================== PR #7003 by hanabi1224 [closed merged] chore: butterflynet reset
https://github.com/ChainSafe/forest/pull/7003
review sequence: APPROVED


==================== PR #7006 by akaladarshi [closed merged] refactor: msg pool to make more structured part 2
https://github.com/ChainSafe/forest/pull/7006
review sequence: DISMISSED


==================== PR #7019 by hanabi1224 [closed merged] fix: skip tipset if it's part of current chain in chain follower
https://github.com/ChainSafe/forest/pull/7019
review sequence: APPROVED


==================== PR #7023 by hanabi1224 [closed merged] refactor: use Bytes instead of Vec<u8> in CarBlock
https://github.com/ChainSafe/forest/pull/7023
review sequence: APPROVED


==================== PR #7028 by wjmelements [closed merged] feat: add `eth_baseFee` rpc method
https://github.com/ChainSafe/forest/pull/7028
review sequence: COMMENTED, CHANGES_REQUESTED, CHANGES_REQUESTED, APPROVED
-- REVIEW CHANGES_REQUESTED
Thanks for this! The CI is failing at RPC snapshot tests which is expected - you need to add an entry to https://github.com/ChainSafe/forest/blob/main/src/tool/subcommands/api_cmd/test_snapshots_ignored.txt.
-- REVIEW CHANGES_REQUESTED
@wjmelements You need to regenerate the insta snapshots - they contain the method description which you've added.
-- REVIEW APPROVED
LGTM, thanks!
-- INLINE src/rpc/methods/eth.rs:815
```diff
+impl RpcMethod<0> for EthBaseFee {
+    const NAME: &'static str = "Filecoin.EthBaseFee";
+    const NAME_ALIAS: Option<&'static str> = Some("eth_baseFee");
+    const PARAM_NAMES: [&'static str; 0] = [];
+    const API_PATHS: BitFlags<ApiPaths> = ApiPaths::all_with_v2();
+    const PERMISSION: Permission = Permission::Read;
```
>>> Could please also implement `DESCRIPTION` for this method?


==================== PR #7035 by leshyatwork[bot] [closed] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/7035
review sequence: CHANGES_REQUESTED
-- REVIEW CHANGES_REQUESTED
blocking
-- CONVERSATION
As discussed, it doesn't make sense to do manual changes unless needed. Let's wait for a proper release.
-- CONVERSATION
The latest Lotus release didn't include this fix for some reason, so still blocked. https://github.com/filecoin-project/lotus/releases/tag/v1.36.0
-- CONVERSATION
I disabled the workflow manually for now, since it will be failing indefinitely until Lotus makes a release with the aforementioned change.


==================== PR #7038 by hanabi1224 [closed merged] feat: persist tipset by height lookup table
https://github.com/ChainSafe/forest/pull/7038
review sequence: APPROVED


==================== PR #7048 by dependabot[bot] [closed merged] chore(deps): bump cloudflare/wrangler-action from 3 to 4
https://github.com/ChainSafe/forest/pull/7048
review sequence: COMMENTED, DISMISSED
-- INLINE docs/pnpm-workspace.yaml:8
```diff
@@ -5,6 +5,7 @@ allowBuilds:
   core-js-pure: false
   esbuild: false
   puppeteer: false
+
```
>>> ```suggestion
```


==================== PR #7051 by hanabi1224 [closed merged] refactor: replace generic db with enum
https://github.com/ChainSafe/forest/pull/7051
review sequence: DISMISSED, APPROVED


==================== PR #7056 by hanabi1224 [closed merged] test(rpc): eth_baseFee tests
https://github.com/ChainSafe/forest/pull/7056
review sequence: DISMISSED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1441
```diff
         )?));
+        tests.push(if offline {
+            RpcTest::basic(EthBaseFee::request_with_alias((), use_alias)?)
+        } else {
+            RpcTest::identity(EthBaseFee::request_with_alias((), use_alias)?)
+        });
```
>>> why is the distinction needed?
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1387
```diff
@@ -1384,7 +1384,7 @@ fn wallet_tests(worker_address: Option<Address>) -> Vec<RpcTest> {
     tests
 }
 
-fn eth_tests() -> anyhow::Result<Vec<RpcTest>> {
+fn eth_tests(offline: bool) -> anyhow::Result<Vec<RpcTest>> {
```
>>> bools as params are not very readable. What does `eth_tests(false)` mean? No way to guess without looking into the code. An enum is more self-documenting.
-- INLINE (reply) src/tool/subcommands/api_cmd/api_compare_tests.rs:1441
```diff
         )?));
+        tests.push(if offline {
+            RpcTest::basic(EthBaseFee::request_with_alias((), use_alias)?)
+        } else {
+            RpcTest::identity(EthBaseFee::request_with_alias((), use_alias)?)
+        });
```
>>> Isn't it fair to assume that all snapshots will be post-firehorse? I don't think anyone is running the tests against mainnet (we have a couple of hardcoded calibnet addresses anyway)


==================== PR #7057 by hanabi1224 [closed merged] test: add state computation test for FireHorse state migration
https://github.com/ChainSafe/forest/pull/7057
review sequence: APPROVED


==================== PR #7062 by hanabi1224 [closed merged] fix: optimize Block::from_filecoin_tipset
https://github.com/ChainSafe/forest/pull/7062
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
Looks good, let's have some numbers to confirm the optimization.
-- INLINE src/rpc/methods/eth.rs:484
```diff
-        static ETH_BLOCK_CACHE: LazyLock<SizeTrackingLruCache<CidWrapper, Block>> =
+    ) -> Result<Arc<Self>> {
+        static ETH_BLOCK_HASH_TX_CACHE: LazyLock<ForestLruCache<CidWrapper, Arc<Block>>> =
             LazyLock::new(|| {
-                const DEFAULT_CACHE_SIZE: NonZeroUsize = nonzero!(500usize);
-                let cache_size = std::env::var("FOREST_ETH_BLOCK_CACHE_SIZE")
```
>>> this must be removed from the docs, no?
-- INLINE (reply) src/rpc/methods/eth.rs:484
```diff
-        static ETH_BLOCK_CACHE: LazyLock<SizeTrackingLruCache<CidWrapper, Block>> =
+    ) -> Result<Arc<Self>> {
+        static ETH_BLOCK_HASH_TX_CACHE: LazyLock<ForestLruCache<CidWrapper, Arc<Block>>> =
             LazyLock::new(|| {
-                const DEFAULT_CACHE_SIZE: NonZeroUsize = nonzero!(500usize);
-                let cache_size = std::env::var("FOREST_ETH_BLOCK_CACHE_SIZE")
```
>>> Ah no, I see it's now used for full and hash.


==================== PR #7068 by hanabi1224 [closed merged] feat: prefill RPC caches for new validated tipsets
https://github.com/ChainSafe/forest/pull/7068
review sequence: COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
no green checkmark, no review
-- INLINE src/chain_sync/chain_follower.rs:797
```diff
@@ -783,10 +794,10 @@ impl SyncStateMachine {
         }
     }
 
-    fn mark_validated_tipset(&mut self, tipset: FullTipset, is_proposed_head: bool) {
+    fn mark_validated_tipset(&mut self, tipset: FullTipset, is_proposed_head: bool) -> bool {
```
>>> It's unclear to me what the return bool here denotes. Seems like it should be a `Result`.
-- INLINE src/daemon/mod.rs:352
```diff
+    let sync_status = chain_follower.sync_status.shallow_clone();
+    let state_manager = chain_follower.state_manager.shallow_clone();
+    let mut validated_tipset_rx = chain_follower.subscribe_validated_tipset();
     services.spawn(async move { chain_follower.run().await });
+    // Prefill RPC method caches for newly validated tipsets to speed up subsequent RPC calls.
+    if config.client.enable_rpc {
```
>>> does it make sense to run it in a stateless node?


==================== PR #7069 by hanabi1224 [closed] experiment: serialize rpc response with sonic
https://github.com/ChainSafe/forest/pull/7069
-- CONVERSATION
@hanabi1224 do we keep it, do we close it?


==================== PR #7070 by Patrick-Ehimen [closed] feat: add DESCRIPTION to all missing RPC methods
https://github.com/ChainSafe/forest/pull/7070
-- CONVERSATION
No activity and lots of conflicts now. Closing. Feel free to re-open with comments addressed.


==================== PR #7077 by hanabi1224 [closed merged] refactor: prefill RPC cache for `collect_events`
https://github.com/ChainSafe/forest/pull/7077
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/chain/store/chain_store.rs:637
```diff
 impl ShallowClone for MessagesInTipsetCache {
     fn shallow_clone(&self) -> Self {
-        Self {
-            cache: self.cache.shallow_clone(),
-        }
+        Self(self.0.shallow_clone())
```
>>> It'd be nice to improve ergonomics here. Consider implementing `Deref`/`DerefMut` to avoid always specifying `.0`.
-- INLINE src/utils/cache/size_tracking.rs:165
```diff
 
-    /// Get the value for `key`, computing it with `compute` on a miss.
-    ///
+    /// Gets or inserts an item in the cache with key.
     /// Concurrent callers for the same key are coalesced — only one runs
-    /// `compute`, the rest wait on the result.
```
>>> why did you cut this out?
-- INLINE src/chain/store/chain_store.rs:725
```diff
@@ -734,11 +722,11 @@ mod tests {
             DAG_CBOR,
             MultihashCode::Blake2b256.digest(&[1])
         )]);
-        assert!(cache.get(&key1).is_none());
+        assert!(cache.0.get_cloned(&key1).is_none());
```
>>> we no longer need to call with `.0`, no?
-- INLINE src/shim/state_tree.rs:361
```diff
                     .get_actor(&addr)?
                     .with_context(|| format!("failed to find actor: {addr}"))?;
-
-                // A workaround to implement `if state.Version() >= types.StateTreeVersion5`
-                // When state tree version is not available in rust APIs
-                if !matches!(self, Self::FvmV2(_) | Self::V0(_))
```
>>> why is it safe to remove?
-- INLINE docs/docs/users/guides/monitoring/metrics.md:14
```diff
-lru_cache_miss_total{kind="sm_tipset"} 181
-# HELP lru_cache_hit Stats of lru cache hit.
-# TYPE lru_cache_hit counter
-lru_cache_hit_total{kind="sm_tipset"} 913
-lru_cache_hit_total{kind="tipset"} 971846
+# HELP cache_tipset_size_bytes Size of cache tipset in bytes
```
>>> This is breaking change that invalidates the changelog. Please update accordingly. https://github.com/ChainSafe/forest/blob/main/CHANGELOG.md#breaking
-- INLINE src/utils/cache/size_tracking.rs:167
```diff
             cap.encode(cap_metric_encoder)?;
         }
+        {
+            let hits_metric_name = format!("cache_{}_hits", self.cache_name);
+            let hits_metric_help = format!("Cache hits of {}", self.cache_name);
+            let hits: Gauge = Default::default();
```
>>> It doesn't make sense for `hits` and `misses` to be `Gauge`; they're monotonically increasing. Those should be counters.
-- INLINE src/chain/store/chain_store.rs:623
```diff
-            Ok(self.insert(key.clone(), f()?))
-        }
-    }
-
-    pub fn insert(&self, key: TipsetKey, mut value: Vec<ChainMessage>) -> Arc<Vec<ChainMessage>> {
-        value.shrink_to_fit();
```
>>> We're not doing this `shrink_to_fit` anymore. Should we?
-- INLINE src/daemon/mod.rs:405
```diff
+                                            ) -> anyhow::Result<bool> {
+                                                Ok(false)
+                                            }
+                                        }
+                                        let mut collected_events = vec![];
+                                        if let Err(e) = EthEventHandler::collect_events(&state_manager,&ts,Some(&CollectEventsCachePrefillingMatcher), crate::rpc::eth::filter::SkipEvent::OnUnresolvedAddress,&mut collected_events).await {
```
>>> this line is completely unreadable


==================== PR #7078 by hanabi1224 [closed merged] chore(ci): speed up linking with mold
https://github.com/ChainSafe/forest/pull/7078
review sequence: APPROVED


==================== PR #7080 by 0xDevNinja [closed] feat: honor FOREST_PATH env var for data directory
https://github.com/ChainSafe/forest/pull/7080
-- CONVERSATION
Stale, closing.


==================== PR #7082 by hanabi1224 [closed merged] fix: log Rand errors
https://github.com/ChainSafe/forest/pull/7082
review sequence: APPROVED


==================== PR #7083 by hanabi1224 [closed merged] fix: inspect and log errors in fvm consensus trait methods
https://github.com/ChainSafe/forest/pull/7083
review sequence: APPROVED


==================== PR #7087 by sudo-shashank [closed merged] Fix `forest-wallet set-default` failing when the keystore has no `default` entry
https://github.com/ChainSafe/forest/pull/7087
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/key_management/wallet.rs:223
```diff
@@ -227,6 +219,16 @@ pub fn try_find_key(addr: &Address, keystore: &KeyStore) -> Result<Key, Error> {
     ki.try_into()
 }
 
+/// Set the default key to the key identified by `addr`.
+pub fn set_default(addr: &Address, keystore: &mut KeyStore) -> Result<(), Error> {
```
>>> why does this need to be a free function?
-- INLINE (reply) src/key_management/wallet.rs:223
```diff
@@ -227,6 +219,16 @@ pub fn try_find_key(addr: &Address, keystore: &KeyStore) -> Result<Key, Error> {
     ki.try_into()
 }
 
+/// Set the default key to the key identified by `addr`.
+pub fn set_default(addr: &Address, keystore: &mut KeyStore) -> Result<(), Error> {
```
>>> This isn't a compelling argument; this is essentially a method on the keystore that requires the keystore. I think what you should consider is moving the listed methods under the aegis of keystore. Rust is a multi-paradigm language, OOP (or parts of it) being on them. Entities like keystore would benefit well from proper encapsulation.


==================== PR #7090 by sudo-shashank [closed merged] Fix incorrect swap index in bubble-down loop in select_messages_to_republish
https://github.com/ChainSafe/forest/pull/7090
-- CONVERSATION
I'll let @akaladarshi review, given he identified it.


==================== PR #7093 by hanabi1224 [closed merged] fix: process batch RPC request in parallel
https://github.com/ChainSafe/forest/pull/7093
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/parallel_batch_layer.rs:13
```diff
+    core::middleware::{Batch, BatchEntry, Notification},
+    server::{BatchResponseBuilder, middleware::rpc::RpcServiceT},
+};
+use tower::Layer;
+
+// State-less jsonrpcsee layer for measuring RPC metrics
```
>>> ?
-- INLINE CHANGELOG.md:40
```diff
@@ -37,6 +37,8 @@
 
 - [`#7066`](https://github.com/ChainSafe/forest/pull/7066): Disable JSON-RPC HTTP response compression by default. Set `FOREST_RPC_COMPRESS_MIN_BODY_SIZE` to a non-negative value (e.g. `1024`) to re-enable gzip compression of responses above that size.
 
+- [`#7093`](https://github.com/ChainSafe/forest/pull/7093): Parallelize RPC batch request processing.
```
>>> that's internal change, no need for changelog entry


==================== PR #7095 by sudo-shashank [closed merged] Increase wallet test poll timeout
https://github.com/ChainSafe/forest/pull/7095
review sequence: APPROVED


==================== PR #7098 by akaladarshi [closed merged] fix trace is evm or eam check to return correct error code
https://github.com/ChainSafe/forest/pull/7098
review sequence: COMMENTED, APPROVED
-- INLINE CHANGELOG.md:38
```diff
@@ -35,6 +35,8 @@
 
 ### Fixed
 
+- [#6748](https://github.com/ChainSafe/forest/issues/6748): Fixed trace transaction api returns the correct error.
```
>>> That's not a really helpful changelog entry. What methods are affected? What was the gist of the issue? No need to copy-paste the entire issue here, but a single sentence would help.


==================== PR #7103 by hanabi1224 [closed merged] feat(rpc): internal method `Forest.BaseFeeByHeight` for troubleshooting
https://github.com/ChainSafe/forest/pull/7103
review sequence: APPROVED


==================== PR #7104 by hanabi1224 [closed merged] fix: tokio::spawn futures for FuturesOrdered
https://github.com/ChainSafe/forest/pull/7104
review sequence: COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- INLINE src/rpc/methods/state.rs:1615
```diff
@@ -1612,7 +1612,7 @@ impl RpcMethod<3> for ForestStateCompute {
         {
             let chain_store = ctx.chain_store().shallow_clone();
             let network_context = ctx.sync_network_context.shallow_clone();
-            futures.push_front(async move {
+            futures.push_front(tokio::spawn(async move {
```
>>> Any noticeable speedup?
-- INLINE (reply) src/rpc/methods/state.rs:1615
```diff
@@ -1612,7 +1612,7 @@ impl RpcMethod<3> for ForestStateCompute {
         {
             let chain_store = ctx.chain_store().shallow_clone();
             let network_context = ctx.sync_network_context.shallow_clone();
-            futures.push_front(async move {
+            futures.push_front(tokio::spawn(async move {
```
>>> I like it
-- INLINE src/utils/task/mod.rs:12
```diff
+#[derive(Default, derive_more::Deref, derive_more::DerefMut)]
+pub struct AbortHandles(Vec<AbortHandle>);
+
+impl Drop for AbortHandles {
+    fn drop(&mut self) {
+        for handle in self.0.iter() {
```
>>> ```suggestion
        for handle in self.iter() {
```

I assume you implemented `Deref` for a reason :)


==================== PR #7108 by hanabi1224 [closed merged] perf(rpc): cache block traces
https://github.com/ChainSafe/forest/pull/7108
review sequence: APPROVED


==================== PR #7110 by hanabi1224 [closed merged] patch tipset traces parallelization logic for #7107
https://github.com/ChainSafe/forest/pull/7110
review sequence: APPROVED


==================== PR #7112 by hanabi1224 [closed merged] fix: incorporate EC finality calculator into tipset lookup-by-height cache
https://github.com/ChainSafe/forest/pull/7112
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/chain/store/chain_store.rs:67
```diff
@@ -60,6 +63,9 @@ pub struct ChainStore {
     /// F3 finalized tipset cache
     f3_finalized_tipset: Arc<RwLock<Option<Tipset>>>,
 
+    /// EC calculator finalized epoch cache
+    ec_calculator_finalized_epoch: Arc<RwLock<ChainEpoch>>,
```
>>> Why do we need `Arc<RwLock>` for i64? Wouldn't it be more efficient to use AtomicI64 instead?
-- INLINE src/rpc/methods/chain.rs:1198
```diff
+    fn get_ec_finality_threshold_depth_with_cache(
+        chain_index: &ChainIndex,
+        chain_config: &ChainConfig,
+        head: &Tipset,
+    ) -> i64 {
+        static CACHE: parking_lot::Mutex<Option<(Tipset, i64)>> = parking_lot::Mutex::new(None);
```
>>> Any reason to do manual caching and not `quick_cache`?


==================== PR #7114 by dependabot[bot] [closed merged] chore(deps-dev): bump @types/react from 19.2.14 to 19.2.15 in /docs in the patch-versions group
https://github.com/ChainSafe/forest/pull/7114
review sequence: APPROVED


==================== PR #7116 by hanabi1224 [closed merged] fix: adjust state computation policy for Eth RPC methods
https://github.com/ChainSafe/forest/pull/7116
review sequence: COMMENTED, COMMENTED, CHANGES_REQUESTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
This is a functional breaking change, hence a CHANGELOG entry is required.
-- INLINE src/state_manager/state_computation.rs:107
```diff
         }) {
             Some((state_root, receipt_root, receipts)) => (state_root, receipt_root, receipts),
             None => {
+                if !allow_state_compute {
+                    anyhow::bail!(
+                        "failed to load tipset state output, epoch={}, key={}",
```
>>> Probably worth adding the error message that the state re-compute is disallowed. It's kind of obvious by grepping Forest code but let's save folks some hassle.
-- INLINE src/tool/subcommands/api_cmd/test_snapshot.rs:68
```diff
@@ -65,6 +65,7 @@ fn backfill_eth_mappings(db: &MemoryDB, index: Option<Index>) -> anyhow::Result<
 }
 
 pub async fn run_test_from_snapshot(path: &Path) -> anyhow::Result<()> {
+    unsafe { std::env::set_var("FOREST_ETH_RPC_COMPUTE_STATE_ON_INDEX_MISS", "1") };
```
>>> @hanabi1224 you must not do this, this is actually undefined behavior in concurrent environments. See https://github.com/ChainSafe/forest/pull/6986
-- INLINE CHANGELOG.md:32
```diff
@@ -29,6 +29,8 @@
 
 - [#7073](https://github.com/ChainSafe/forest/pull/7073) and [#7077](https://github.com/ChainSafe/forest/pull/7077): Replaced the underlying cache engine across the node. The eviction policy is no longer strict LRU — it is now CLOCK-PRO via [`quick_cache`](https://crates.io/crates/quick_cache), which is scan-resistant and typically gives higher hit rates on chain workloads. Refactored internal cache metrics to include `hits` and `misses` for all automatically. The old metrics `lru_cache_hit_total` and `lru_cache_miss_total` are deprecated in favor of `cache_{name}_hits` and `cache_{name}_misses`. Details can be found in https://forest-docs.pages.dev/reference/metrics
 
+- [#7116](https://github.com/ChainSafe/forest/pull/7116): Disable state computation for Ethereum RPC methods by default. It could be explictly enabled by setting environment variable `FOREST_ETH_RPC_COMPUTE_STATE_ON_INDEX_MISS=1`
```
>>> What about non-Ethereum methods? Do we have a follow-up on those?


==================== PR #7123 by hanabi1224 [closed merged] fix: save snapshot on mismatch in `forest-dev state compute`
https://github.com/ChainSafe/forest/pull/7123
review sequence: APPROVED


==================== PR #7124 by hanabi1224 [closed merged] fix: RPC cache prefilling may cause data loss in GC
https://github.com/ChainSafe/forest/pull/7124
review sequence: APPROVED


==================== PR #7126 by hanabi1224 [closed merged] fix: adjust default cache sizes to reduce RAM requirement
https://github.com/ChainSafe/forest/pull/7126
-- CONVERSATION
I don't think it's confirmed that was the culprit.


==================== PR #7129 by hanabi1224 [closed merged] fix: Inaccurate GetSize impl for a few types
https://github.com/ChainSafe/forest/pull/7129
review sequence: COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW DISMISSED
Worth a changelog entry, given metrics were incorrect.
-- CONVERSATION
Also, please provide numbers from before and after the change. It'd be good to have them for reference.
-- INLINE src/blocks/header.rs:227
```diff
-            + big_int_heap_size_helper(weight)
-            + bls_aggregate.get_heap_size()
-            + signature.get_heap_size()
-            + parent_base_fee.get_heap_size()
+        (
+            miner_address.get_heap_size_with_tracker(&mut tracker).0
```
>>> Are you confident this is the correct use of this API? Seems in examples the tracker gets passed around by move. Not sure if there's a practical difference.
-- INLINE (reply) src/blocks/header.rs:227
```diff
-            + big_int_heap_size_helper(weight)
-            + bls_aggregate.get_heap_size()
-            + signature.get_heap_size()
-            + parent_base_fee.get_heap_size()
+        (
+            miner_address.get_heap_size_with_tracker(&mut tracker).0
```
>>> https://github.com/bircni/get-size2/blob/6d0ae91743cfba0efa29c3e11507ed0583a670aa/crates/get-size2/src/impls/feature/dashmap.rs#L13
-- INLINE (reply) src/blocks/header.rs:227
```diff
-            + big_int_heap_size_helper(weight)
-            + bls_aggregate.get_heap_size()
-            + signature.get_heap_size()
-            + parent_base_fee.get_heap_size()
+        (
+            miner_address.get_heap_size_with_tracker(&mut tracker).0
```
>>> Can't say for sure, but I trust you checked it.


==================== PR #7131 by hanabi1224 [closed merged] feat: size metrics for chain follower internal hash maps
https://github.com/ChainSafe/forest/pull/7131
review sequence: APPROVED


==================== PR #7152 by hanabi1224 [closed merged] chore: adjust cache sizes for better RPC performance
https://github.com/ChainSafe/forest/pull/7152
review sequence: APPROVED


==================== PR #7153 by hanabi1224 [closed merged] feat: add mise task jemalloc:heap for memory leak profiling
https://github.com/ChainSafe/forest/pull/7153
review sequence: APPROVED


==================== PR #7154 by sudo-shashank [closed merged] chore: remove `FOREST_ETH_RPC_COMPUTE_STATE_ON_INDEX_MISS` workaround in snapshot tests and update affected snapshots
https://github.com/ChainSafe/forest/pull/7154
review sequence: APPROVED


==================== PR #7156 by akaladarshi [closed merged] fix: eth block returns correctly filled logs bloom field
https://github.com/ChainSafe/forest/pull/7156
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW DISMISSED
LGTM
-- CONVERSATION
no green checkmark, no review!
-- INLINE src/db/car/many.rs:332
```diff
+        bloom: &[u8; BLOCK_BLOOM_LEN],
+    ) -> anyhow::Result<()> {
+        EthBlockBloomStore::write_bloom(self.writer(), key, height, bloom)
+    }
+
+    fn delete_blooms_before_height(&self, height: i64) -> anyhow::Result<()> {
```
>>> ```suggestion
    fn delete_blooms_before_height(&self, height: ChainEpoch) -> anyhow::Result<()> {
```
Here and elsewhere. It's not exactly strict typing, but type aliases in Rust exist for areason.
-- INLINE src/db/gc/snapshot.rs:368
```diff
@@ -348,6 +348,14 @@ impl SnapshotGarbageCollector {
                     .unwrap_or_default()
             );
 
+            // Prune blooms whose events are no longer retained by the lite snapshot.
```
>>> I'll let @hanabi1224 chip in here.
-- INLINE src/db/migration/migration_map.rs:161
```diff
@@ -157,6 +158,7 @@ create_migrations!(
     "0.22.0" -> "0.22.1" @ Migration0_22_0_0_22_1,
     "0.25.3" -> "0.26.0" @ Migration0_25_3_0_26_0,
     "0.30.5" -> "0.31.0" @ Migration0_30_5_0_31_0,
+    "0.33.6" -> "0.33.7" @ Migration0_33_6_0_33_7,
```
>>> These need to be updated, no?
-- INLINE (reply) src/db/migration/v0_33_7.rs:54
```diff
+        std::fs::create_dir_all(&old_db).context("failed to create placeholder directory")?;
+
+        info!("Adding EthBlockBloom column to database");
+        let mut opts = paritydb_0_33_6::to_options(temp_db.clone());
+        parity_db::Db::add_column(&mut opts, paritydb_0_33_6::eth_block_bloom_column_options())
+            .context("failed to add EthBlockBloom column")?;
```
>>> Seems viable, no @akaladarshi ?


==================== PR #7158 by hanabi1224 [closed merged] fix: clear chain tmp folder on startup
https://github.com/ChainSafe/forest/pull/7158
review sequence: APPROVED


==================== PR #7159 by hanabi1224 [closed merged] fix: unify `HashSet` and `HashMap` usages
https://github.com/ChainSafe/forest/pull/7159
review sequence: APPROVED


==================== PR #7160 by hanabi1224 [closed merged] fix: more server logs for snapshot export
https://github.com/ChainSafe/forest/pull/7160
review sequence: APPROVED


==================== PR #7165 by hanabi1224 [closed merged] fix: RPC cache prefilling for eth pending tag
https://github.com/ChainSafe/forest/pull/7165
review sequence: APPROVED


==================== PR #7167 by hanabi1224 [closed merged] fix: increase tipset cache size
https://github.com/ChainSafe/forest/pull/7167
review sequence: APPROVED


==================== PR #7171 by hanabi1224 [closed merged] fix: integrate EC finality into chain follower
https://github.com/ChainSafe/forest/pull/7171
review sequence: APPROVED


==================== PR #7172 by akaladarshi [closed merged] fix: return reverted field of logs set to true if tipset was reverted
https://github.com/ChainSafe/forest/pull/7172
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/state_manager/state_computation.rs:48
```diff
@@ -47,7 +45,43 @@ impl StateManager {
             StateRecomputePolicy::Disallowed
         };
 
-        self.load_executed_tipset_with_cache(ts, policy).await
+        // https://github.com/ChainSafe/forest/issues/7118
```
>>> that issue is closed, no?
-- INLINE src/tool/subcommands/api_cmd/stateful_tests.rs:662
```diff
-                "address": [],
-                "topics": [tx.topic.to_string()],
-            });
-            let (mut ws_stream, subscription_id) =
-                open_eth_subscription(&client, SubscriptionKind::Logs, Some(filter)).await?;
+            let contract = EthAddress::from_filecoin_address(&tx.to)?;
```
>>> this test is huge and it's unclear to me what it does. Tests must be easy to follow, either with clear code, e.g., by extracting certain chunks to separate functions or at least with meaningful, commented blocks. I also see there are tons of magical timeouts, each with different value. Why? How long is this method supposed to run, realistically?


==================== PR #7173 by hanabi1224 [closed merged] fix: less locking in ExportStatus
https://github.com/ChainSafe/forest/pull/7173
review sequence: APPROVED


==================== PR #7176 by hanabi1224 [closed merged] fix(ci): install lint tools with --locked
https://github.com/ChainSafe/forest/pull/7176
review sequence: APPROVED, COMMENTED
-- INLINE mise.toml:28
```diff
@@ -25,7 +25,7 @@ alias = 'i'
 
 [tasks.install-lint-tools]
 description = "Installs linting tools for CI"
-run = "cargo binstall --no-confirm taplo-cli cargo-spellcheck@0.15.7 cargo-deny"
+run = "cargo binstall --no-confirm --locked taplo-cli cargo-spellcheck@0.15.7 cargo-deny"
```
>>> should we also pin taplo-cli and cargo-deny versions?


==================== PR #7179 by hanabi1224 [closed merged] fix: cleanup dangling forks in chain follower
https://github.com/ChainSafe/forest/pull/7179
-- CONVERSATION
As discussed, let's let it run for a bit.
-- CONVERSATION
merge? @hanabi1224


==================== PR #7180 by akaladarshi [closed merged] fix(mpool): removing the applied tipset from pending store should advance with correct nonce
https://github.com/ChainSafe/forest/pull/7180
-- CONVERSATION
no green checkmark, no review


==================== PR #7181 by hanabi1224 [closed merged] fix: less clones in `start_rpc`
https://github.com/ChainSafe/forest/pull/7181
review sequence: COMMENTED, APPROVED
-- INLINE src/rpc/mod.rs:665
```diff
-                    .layer(MetricsLayer::new(metrics_mode));
-                let mut jsonrpsee_svc = svc_builder
-                    .set_rpc_middleware(rpc_middleware)
-                    .build(methods, stop_handle);
-
-                if is_websocket {
```
>>> we don't need this logic?


==================== PR #7182 by akaladarshi [closed merged] fix(mpool): `run_head_change` in select message to only simulate the head change
https://github.com/ChainSafe/forest/pull/7182
review sequence: APPROVED


==================== PR #7184 by hanabi1224 [closed merged] fix: spawn_blocking for fvm calls
https://github.com/ChainSafe/forest/pull/7184
review sequence: APPROVED


==================== PR #7185 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group in /docs with 2 updates
https://github.com/ChainSafe/forest/pull/7185
review sequence: APPROVED


==================== PR #7190 by Copilot [closed merged] Normalize remaining non-Common RPC param names to camelCase in OpenRPC
https://github.com/ChainSafe/forest/pull/7190
review sequence: APPROVED
-- CONVERSATION
@copilot sign the CLA


==================== PR #7191 by hanabi1224 [closed merged] fix: use ArcSwap for ChainStore::heaviest_tipset
https://github.com/ChainSafe/forest/pull/7191
review sequence: APPROVED


==================== PR #7194 by sudo-shashank [closed merged] fix(rpc): add `UpgradeXxHeight` to `Filecoin.StateGetNetworkParams`
https://github.com/ChainSafe/forest/pull/7194
review sequence: DISMISSED, APPROVED


==================== PR #7197 by sudo-shashank [closed] Fix calibnet wallet test flakiness
https://github.com/ChainSafe/forest/pull/7197
review sequence: COMMENTED, COMMENTED
-- INLINE tests/common/calibnet_wallet_helpers.rs:6
```diff
 // Copyright 2019-2026 ChainSafe Systems
 // SPDX-License-Identifier: Apache-2.0, MIT
 
+//! Shared helpers for calibnet integration tests (`wallet`, `mpool_tools`).
+
+#![allow(dead_code)]
```
>>> why?
-- INLINE tests/common/calibnet_wallet_helpers.rs:228
```diff
@@ -220,12 +225,12 @@ pub async fn funded_delegated_addr() -> &'static str {
     addr.as_str()
 }
 
-static HTTP: LazyLock<reqwest::Client> = LazyLock::new(|| {
+fn build_http_client() -> reqwest::Client {
```
>>> why?


==================== PR #7199 by Copilot [closed] Add missing `DESCRIPTION` metadata across RPC methods
https://github.com/ChainSafe/forest/pull/7199
-- CONVERSATION
my expectations were low, yet I was still let down


==================== PR #7213 by hanabi1224 [closed merged] fix: use ArcSwap for SyncStatus
https://github.com/ChainSafe/forest/pull/7213
review sequence: COMMENTED, APPROVED
-- INLINE src/health/mod.rs:115
```diff
         };
 
         // instrument the state so that the ready requirements are met
-        sync_status.write().status = NodeSyncStatus::Synced;
-        sync_status.write().current_head_epoch = i64::MAX;
+        sync_status.store(
```
>>> it's quite a bit of boilerplate for each update. Can we refactor it a bit?


==================== PR #7214 by sudo-shashank [closed merged] fix(eth): serialize empty lists as `[]` instead of `null`
https://github.com/ChainSafe/forest/pull/7214
review sequence: COMMENTED, COMMENTED, COMMENTED
-- CONVERSATION
API compare tests failed in ` | Filecoin.EthGetTransactionByBlockNumberAndIndex (20) | 18/20   | 18/20   | ⚠️  Mixed Results (CustomCheckFailed) |`. This must be addressed.
-- INLINE src/rpc/methods/eth.rs:4212
```diff
@@ -4201,6 +4209,27 @@ mod test {
         }
     }
 
+    #[test]
```
>>> To reproduce `reth`/`geth` and ensure no regressions, we should test the legacy transactions have the field omitted.
-- INLINE src/rpc/methods/eth.rs:4225
```diff
@@ -4209,6 +4221,53 @@ mod test {
         }
     }
 
+    #[rstest]
```
>>> These tests are unreadable. I appreciate the effort of making them concise, but at the very least there should be a comment telling what's  happening and why.
-- INLINE scripts/tests/api_compare/filter-list:6
```diff
@@ -3,3 +3,8 @@
 
 !Filecoin.EthGetBlockByHash
 !Filecoin.EthGetBlockByNumber
+# https://github.com/filecoin-project/lotus/issues/12214
```
>>> I don't think ignoring entire methods until Lotus addresses the issue (might be never) is the way to go. We should serialize correctly but allow legacy (Lotus) deserialization.


==================== PR #7215 by 0xDevNinja [closed] feat: honor FOREST_PATH env var for data directory
https://github.com/ChainSafe/forest/pull/7215
-- CONVERSATION
@0xDevNinja this is already being resolved via https://github.com/ChainSafe/forest/pull/7200.


==================== PR #7216 by 0xDevNinja [closed merged] feat(wallet)!: apply FRC-0102 envelope to sign/verify by default
https://github.com/ChainSafe/forest/pull/7216
review sequence: COMMENTED, APPROVED
-- REVIEW APPROVED
LGTM, thanks!
-- CONVERSATION
LGTM, just please update the changelog.
-- INLINE CHANGELOG.md:52
```diff
@@ -49,6 +49,8 @@
 
 - [#7164](https://github.com/ChainSafe/forest/issues/7164): JSON-RPC authentication is now performed once per connection (e.g. at the WebSocket upgrade) instead of on every request, matching Lotus. Note that token expiry is no longer re-checked for the lifetime of an established connection.
 
+- [#6442](https://github.com/ChainSafe/forest/issues/6442): `forest-wallet sign` and `forest-wallet verify` now apply the FRC-0102 signing envelope to the message by default. Pass `--raw` on both sides to reproduce the previous raw-bytes behaviour.
```
>>> Let's move that to the current release train


==================== PR #7217 by hanabi1224 [closed merged] fix: datacap actor should be counted in get_state_circulating_supply
https://github.com/ChainSafe/forest/pull/7217
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
This should've been caught by RPC parity tests - why it was not the case? Can we add tests to ensure eternal compatibility?
-- REVIEW COMMENTED
It's a user-facing bug, let's add a changelog entry.
-- INLINE src/state_manager/circulating_supply.rs:126
```diff
+    pub fn get_state_circulating_supply_with_cache(
+        &self,
+        db: &(impl Blockstore + ShallowClone),
+        ts: &Tipset,
+    ) -> anyhow::Result<TokenAmount> {
+        static CACHE: LazyLock<quick_cache::sync::Cache<TipsetKey, Result<TokenAmount, String>>> =
```
>>> Why storing a Result in the cache? There should be a TokenAmount for every tipset, no?
-- INLINE src/state_manager/circulating_supply.rs:133
```diff
+
+        let height = ts.epoch() - 1;
+        let root = ts.parent_state();
+
+        CACHE
+            .get_or_insert_with(ts.key(), || {
```
>>> worth logging an error in case of fiasco on the daemon side?


==================== PR #7218 by hanabi1224 [closed merged] fix(ci): speed up wallet tests on CI
https://github.com/ChainSafe/forest/pull/7218
review sequence: APPROVED
-- REVIEW APPROVED
nice


==================== PR #7231 by hanabi1224 [closed merged] fix: avoid sorting height_infos on the fly
https://github.com/ChainSafe/forest/pull/7231
review sequence: APPROVED


==================== PR #7236 by hanabi1224 [closed merged] fix: wrap more expensive calls with `spawn_blocking`
https://github.com/ChainSafe/forest/pull/7236
review sequence: APPROVED


==================== PR #7237 by hanabi1224 [closed merged] fix: prefer `StateTree::for_each_cacheless` over `StateTree::for_each`
https://github.com/ChainSafe/forest/pull/7237
review sequence: APPROVED


==================== PR #7239 by hanabi1224 [closed merged] fix: check char boundary in String::truncate
https://github.com/ChainSafe/forest/pull/7239
review sequence: APPROVED
-- CONVERSATION
no green checkmark, no review!


==================== PR #7249 by hanabi1224 [closed merged] fix: refactor snapshot export locking / cancelling logic
https://github.com/ChainSafe/forest/pull/7249
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
@hanabi1224 Do you think `Ordering::Relaxed` is enough here?


==================== PR #7252 by hanabi1224 [closed merged] fix: refactor forest-cli snapshot command
https://github.com/ChainSafe/forest/pull/7252
review sequence: COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
> update snapshot checksum file extension from forest.car.sha256sum to forest.car.zst.sha256sum

That's a breaking change, no?
-- CONVERSATION
no green checkmark, no review!
-- INLINE src/db/car/forest.rs:515
```diff
@@ -508,6 +508,18 @@ pub fn new_forest_car_temp_path_in(
         .into_temp_path())
 }
 
+pub fn tmp_exporting_forest_car_path(output_path: &Path) -> PathBuf {
```
>>> nit: let's add a simple test here. The code is kind of obvious but obvious things managed to bite us in the past. Plus, we want coverage to be high.
-- INLINE src/ipld/util.rs:92
```diff
 impl Drop for ChainExportGuard {
     fn drop(&mut self) {
+        // In case some tasks are waiting on this token
+        if !self.cancellation_token.is_cancelled() {
+            self.cancellation_token.cancel();
+        }
```
>>> Do we need to check if it's cancelled? Might be a TOCTOU issue


==================== PR #7254 by hanabi1224 [closed merged] fix: yield to runtime on every 128 ChainStream polls
https://github.com/ChainSafe/forest/pull/7254
review sequence: APPROVED


==================== PR #7263 by sudo-shashank [closed merged] Run wallet and mpool tool test on devnet
https://github.com/ChainSafe/forest/pull/7263
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
What was the issue you mentioned earlier this week around those tests failing on devnet?
-- INLINE src/state_manager/message_search.rs:110
```diff
@@ -100,13 +100,12 @@ impl StateManager {
                 .get_actor(&message_from_id, *parent_tipset.parent_state())
                 .map_err(|e| Error::State(e.to_string()))?;
 
-            if parent_actor_state.is_none()
+            if (parent_actor_state.is_none()
```
>>> Please explain this fix. Can we have a test for this?
-- INLINE src/dev/subcommands/tests_cmd.rs:10
```diff
 /// Integration tests
 #[derive(Debug, clap::Subcommand)]
 pub enum TestsCommand {
     #[command(subcommand)]
-    Calibnet(calibnet::CalibnetTestsCommand),
+    Calibnet(shared::TestCommand),
```
>>> I don't understand. If those are the same, why bother with different subcommands?
-- INLINE (reply) src/state_manager/message_search.rs:110
```diff
@@ -100,13 +100,12 @@ impl StateManager {
                 .get_actor(&message_from_id, *parent_tipset.parent_state())
                 .map_err(|e| Error::State(e.to_string()))?;
 
-            if parent_actor_state.is_none()
+            if (parent_actor_state.is_none()
```
>>> So, how further back are we looking?
-- INLINE scripts/devnet/wallet_harness.sh:22
```diff
+  token=$(docker exec forest cat "${FOREST_DATA_DIR}/token.jwt")
+  export FULLNODE_API_INFO="${token}:/ip4/127.0.0.1/tcp/${FOREST_RPC_PORT}/http"
+
+  local tmp="${TMPDIR:-/tmp}"
+
+  # Derive the genesis address via a throwaway keystore (`XDG_DATA_HOME`) so it
```
>>> This logic is pretty convoluted. What are you trying to achieve?
-- INLINE scripts/devnet/wallet_harness.sh:20
```diff
+function devnet_wallet_env_init {
+  local token
+  token=$(docker exec forest cat "${FOREST_DATA_DIR}/token.jwt")
+  export FULLNODE_API_INFO="${token}:/ip4/127.0.0.1/tcp/${FOREST_RPC_PORT}/http"
+
+  local tmp="${TMPDIR:-/tmp}"
```
>>> This is not the way to deal with temporary directories.
-- INLINE (reply) src/state_manager/message_search.rs:110
```diff
@@ -100,13 +100,12 @@ impl StateManager {
                 .get_actor(&message_from_id, *parent_tipset.parent_state())
                 .map_err(|e| Error::State(e.to_string()))?;
 
-            if parent_actor_state.is_none()
+            if (parent_actor_state.is_none()
```
>>> and what's the `lookback_max_epoch` in this scenario?
-- INLINE (reply) src/state_manager/message_search.rs:110
```diff
@@ -100,13 +100,12 @@ impl StateManager {
                 .get_actor(&message_from_id, *parent_tipset.parent_state())
                 .map_err(|e| Error::State(e.to_string()))?;
 
-            if parent_actor_state.is_none()
+            if (parent_actor_state.is_none()
```
>>> What I want to ensure is that we don't lookback till genesis, executing all tipsets, which is relatively costly.


==================== PR #7269 by hanabi1224 [closed merged] feat: add --wait-confidence to forest-wallet send
https://github.com/ChainSafe/forest/pull/7269
review sequence: APPROVED


==================== PR #7274 by hanabi1224 [closed merged] fix: add cancellation to `tokio::spawn` in `par_buffer` in snapshot export
https://github.com/ChainSafe/forest/pull/7274
review sequence: APPROVED


==================== PR #7280 by 0xDevNinja [closed merged] refactor(rpc): make RpcMethod DESCRIPTION mandatory
https://github.com/ChainSafe/forest/pull/7280
review sequence: APPROVED
-- REVIEW APPROVED
LGTM, thanks!


==================== PR #7285 by hanabi1224 [closed merged] fix: enable StateSearchMsg RPC gateway test on CI
https://github.com/ChainSafe/forest/pull/7285
review sequence: APPROVED


==================== PR #7286 by hanabi1224 [closed] fix: expire bad block cache item after 5mins to allow re-validating the block
https://github.com/ChainSafe/forest/pull/7286
review sequence: DISMISSED


==================== PR #7288 by hanabi1224 [closed merged] fix: make tipset lookup-by-epoch table resilient on chain reorg
https://github.com/ChainSafe/forest/pull/7288
review sequence: COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
@hanabi1224 Can you summarize the root cause?
-- CONVERSATION
Hm, stateful tests are failing. Is it related to the change?
-- INLINE src/chain/store/chain_store.rs:202
```diff
+            }
+            // Fix stale lookups at null rounds which could be caused by chain reorg.
+            // This is a no-op in most of the cases so it's OK to always run.
+            // (caching parent of head is intended as it's heavily used in Eth APIs as `latest`, while head is `pending`)
+            if let Ok(Some(parent)) = self.chain_index.load_tipset(head.parents())
+                && let Err(e) = ChainIndex::cleanup_stale_tipset_lookup_at_null_rounds(
```
>>> Why would this fail? What's the severity of it? Can a node-operator do anything about it?
-- INLINE (reply) src/chain/store/chain_store.rs:202
```diff
+            }
+            // Fix stale lookups at null rounds which could be caused by chain reorg.
+            // This is a no-op in most of the cases so it's OK to always run.
+            // (caching parent of head is intended as it's heavily used in Eth APIs as `latest`, while head is `pending`)
+            if let Ok(Some(parent)) = self.chain_index.load_tipset(head.parents())
+                && let Err(e) = ChainIndex::cleanup_stale_tipset_lookup_at_null_rounds(
```
>>> Ok, let's mark this as `ERROR` then


==================== PR #7289 by hanabi1224 [closed merged] fix: make `load_required_tipset_by_height` non blocking
https://github.com/ChainSafe/forest/pull/7289
review sequence: APPROVED, COMMENTED
-- INLINE (reply) src/message_pool/msgpool/provider.rs:150
```diff
 }
+
+#[allow(dead_code)]
+pub trait ProviderExt {
+    /// Non-blocking version of [`Provider::resolve_to_deterministic_address_at_finality`]
+    async fn resolve_to_deterministic_address_at_finality_async(
```
>>> @hanabi1224 do we have a tracking issue? If not, let's create one to not forget about it.


==================== PR #7290 by hanabi1224 [closed merged] fix: optimize logic of looking up genesis from a tipset
https://github.com/ChainSafe/forest/pull/7290
review sequence: COMMENTED, APPROVED
-- CONVERSATION
```
FAIL [   0.007s] ( 104/2135) forest-filecoin chain::store::chain_store::tests::genesis_test
```
-- INLINE src/db/car/many.rs:313
```diff
         EthMappingsStore::tipset_key_by_epoch(self.writer(), epoch)
     }
 
-    fn set_tipset_key_at_epoch(&self, ts: &Tipset) -> anyhow::Result<()> {
-        EthMappingsStore::set_tipset_key_at_epoch(self.writer(), ts)
+    fn set_tipset_key_at_epoch_raw(&self, epoch: i64, tsk: &TipsetKey) -> anyhow::Result<()> {
```
>>> can we please not use `i64` for when `ChainEpoch` would be more expressive?


==================== PR #7306 by leshyatwork[bot] [closed] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/7306
-- CONVERSATION
Disabling until Lotus has recent fixes released.


==================== PR #7310 by hanabi1224 [closed merged] fix: cache eth transaction receipt for better RPC perf
https://github.com/ChainSafe/forest/pull/7310
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/methods/eth.rs:3006
```diff
+    limit: Option<ChainEpoch>,
+    cancellation_token: &CancellationToken,
+) -> Result<Option<EthTxReceipt>, ServerError> {
+    const CACHE_SIZE: NonZeroUsize = nonzero!(1024usize); // ~1.25MiB on mainnet
+    static CACHE: LazyLock<SizeTrackingCache<EthHash, EthTxReceipt>> =
+        LazyLock::new(|| SizeTrackingCache::new_with_metrics("eth_transaction", CACHE_SIZE));
```
>>> ```suggestion
        LazyLock::new(|| SizeTrackingCache::new_with_metrics("eth_transaction_receipt", CACHE_SIZE));
```
-- INLINE src/rpc/methods/eth.rs:3040
```diff
+            //     // Example: limit=2000 at epoch=1000 → min_epoch=0, searches [0,1000] = 1001 epochs (all available)
+            //     Some(limit) if limit > 0 => (current_epoch - limit + 1).max(0),
+            //     // Search all the way to genesis (epoch 0)
+            //     _ => 0,
+            // };
+            // ```
```
>>> what's this?


==================== PR #7312 by 0xDevNinja [closed merged] docs: let wide tables use full width on large screens
https://github.com/ChainSafe/forest/pull/7312
review sequence: APPROVED
-- REVIEW APPROVED
LGTM, thanks!


==================== PR #7315 by hanabi1224 [closed merged] feat: support exporting epoch-to-tipset lookup table
https://github.com/ChainSafe/forest/pull/7315
review sequence: COMMENTED, APPROVED
-- INLINE src/chain/mod.rs:160
```diff
         include_tipset_keys,
+        include_tipset_lookup,
         seen,
     }: ExportOptions<S>,
-) -> anyhow::Result<Option<digest::Output<D>>> {
+) -> anyhow::Result<(
```
>>> that's a pretty convoluted output. Let's have a dedicated struct?


==================== PR #7318 by 0xDevNinja [closed] docs: group JSON-RPC overview and JWT auth in one section
https://github.com/ChainSafe/forest/pull/7318
-- CONVERSATION
https://github.com/ChainSafe/forest/issues/5211#issuecomment-4967303280


==================== PR #7319 by 0xDevNinja [closed] refactor(tool): extract snapshot export into a shared module
https://github.com/ChainSafe/forest/pull/7319
-- CONVERSATION
https://github.com/ChainSafe/forest/issues/6096#issuecomment-4967324489


==================== PR #7322 by dependabot[bot] [closed merged] chore(deps-dev): bump @types/node from 25.9.4 to 26.1.0 in /docs
https://github.com/ChainSafe/forest/pull/7322
review sequence: APPROVED


==================== PR #7327 by hanabi1224 [closed merged] fix: more clear function names and error messages in ChainStore
https://github.com/ChainSafe/forest/pull/7327
review sequence: DISMISSED, COMMENTED, APPROVED
-- INLINE (reply) src/chain/store/chain_store.rs:434
```diff
         };
         let lbr = (round - lb).max(0);
 
-        // More null blocks than lookback
+        // More null blocks than our lookback
         if lbr >= heaviest_tipset.epoch() {
```
>>> @hanabi1224 what do we do with this?


==================== PR #7335 by sudo-shashank [closed merged] Strict raw JSON check in RPC snapshot tests
https://github.com/ChainSafe/forest/pull/7335
review sequence: COMMENTED, APPROVED
-- INLINE src/tool/subcommands/api_cmd/test_snapshot.rs:75
```diff
     Ok(())
 }
 
+/// JSON fields removed from both actual and expected responses before the
+/// strict raw-JSON snapshot comparison. See each group below for why.
+const SKIPPED_COMPARISON_FIELDS: &[&str] = &[
```
>>> It's not great to globally ignore fields with a given name. The ignores should be scoped to given methods.


==================== PR #7336 by leshyatwork[bot] [closed merged] [automated] Update Forest RPC reference docs
https://github.com/ChainSafe/forest/pull/7336
review sequence: APPROVED


==================== PR #7345 by hanabi1224 [closed merged] chore: rename `tipset_by_height*` to follow convention
https://github.com/ChainSafe/forest/pull/7345
review sequence: APPROVED


==================== PR #7346 by sudo-shashank [closed merged] Docs: Node comparison report
https://github.com/ChainSafe/forest/pull/7346
review sequence: CHANGES_REQUESTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW CHANGES_REQUESTED
Please re-read the task description. It's fine to shill Forest's performance but this was not the gap in documentation we want to close.

Let's:
- make it less marketing-aggressive. There are certain individuals in the ecosystem that do that already (especially with the help of their sycophant AI companions) and we shouldn't follow their example. Just showing the numbers and explaining them in boring, academic voice is fine.
- split to two reports - RPC and snapshot generation.
- this does not close the linked issue - a separate page must be made to address it - with actual feature objective comparison etc., good and bad points. All in all, a new user must be able to decide whether to use Forest or Lotus after reading it.
-- REVIEW COMMENTED
It makes sense to run any descriptions through some Grammarly to tweak up the language.
-- REVIEW DISMISSED
Some nits, otherwise LGTM
-- INLINE docs/docs/users/reports/node_comparison/index.md:25
```diff
+| Snapshot export                                            | Yes    | Yes   |
+| Built-in wallet                                            | Yes    | Yes   |
+| Bootstrap node                                             | Yes    | Yes   |
+| F3 (Fast Finality) participation                           | Yes    | Yes   |
+| Storage provider / sealing (`lotus-miner`, `lotus-worker`) | No     | Yes   |
+| Block production / mining                                  | No     | Yes   |
```
>>> ```suggestion
| Block production / mining                                  | No (untested)     | Yes   |
```
-- INLINE docs/docs/users/reports/node_comparison/index.md:18
```diff
+## Feature comparison
+
+| Capability                                                 | Forest | Lotus |
+| ---------------------------------------------------------- | ------ | ----- |
+| Chain synchronization and validation                       | Yes    | Yes   |
+| Filecoin JSON-RPC API                                      | Yes    | Yes   |
```
>>> This needs more details. Where are we compatible? All `/v0`? All `/v1`? All `/v2`?. Otherwise you can just say Filecoin JSON-RPC API with only one method implemented for `/v0`.
-- INLINE docs/docs/users/reports/node_comparison/index.md:11
```diff
+# Forest vs Lotus
+
+Forest and Lotus are both full-node implementations of the Filecoin protocol. Forest is written in Rust and maintained by [ChainSafe Systems](https://chainsafe.io); it focuses on chain validation, a high-performance RPC API, and snapshot generation, exporting a chain snapshot substantially faster than Lotus while using a fraction of the memory. Lotus is written in Go; it provides the complete Filecoin feature set, including the storage-provider stack.
+
+- Choose **Forest** to validate the chain, serve the Filecoin or Ethereum RPC API, generate snapshots, or run a bootstrap node, particularly where lower resource usage matters.
+- Choose **Lotus** when you need storage-provider functionality (sealing, proving, block production) or the completeness of the reference implementation, where new protocol features typically land first.
```
>>> ```suggestion
```

Let's not tell people what to do. Rather, we should facilitate making informed decisions.
-- INLINE docs/docs/users/reports/node_comparison/index.md:8
```diff
+title: Forest vs Lotus
+---
+
+# Forest vs Lotus
+
+Forest and Lotus are both full-node implementations of the Filecoin protocol. Forest is written in Rust and maintained by [ChainSafe Systems](https://chainsafe.io); it focuses on chain validation, a high-performance RPC API, and snapshot generation, exporting a chain snapshot substantially faster than Lotus while using a fraction of the memory. Lotus is written in Go; it provides the complete Filecoin feature set, including the storage-provider stack.
```
>>> ```suggestion
Forest and Lotus are both full-node implementations of the Filecoin protocol. Forest is written in Rust and maintained by [ChainSafe Systems](https://chainsafe.io); it focuses on chain validation, a high-performance RPC API, and snapshot generation with low hardware requirements. Lotus is the reference implementation written in Go; it provides the complete Filecoin feature set, including the storage-provider stack.
```
-- INLINE docs/docs/users/reports/node_comparison/index.md:3
```diff
@@ -0,0 +1,29 @@
+---
+sidebar_position: 1
+title: Forest vs Lotus
```
>>> This shouldn't really be a Forest vs Lotus, just Filecoin Node comparison. Venus should also be included there, at least an extended mention.
-- INLINE docs/docs/users/reports/node_comparison/index.md:29
```diff
+| Storage provider / sealing (`lotus-miner`, `lotus-worker`) | No     | Yes   |
+| Block production / mining                                  | No     | Yes   |
+
+## Performance
+
+For a comparable RPC workload, Forest served requests at lower latency while using less CPU and memory than Lotus, and it exported a snapshot in a fraction of the time and memory. See the [RPC Performance Comparison](./rpc_comparison.md) and [Snapshot Generation Comparison](./snapshot_comparison.md) for the full figures and methodology.
```
>>> ```suggestion
For a comparable RPC workload, Forest served requests at lower latency while using less CPU and memory than Lotus, and it exports a snapshot significantly faster and with lower hardware requirements. See the [RPC Performance Comparison](./rpc_comparison.md) and [Snapshot Generation Comparison](./snapshot_comparison.md) for the full figures and methodology.
```
-- INLINE docs/docs/users/reports/node_comparison/index.md:28
```diff
+| Bootstrap node                                             | Yes           | Yes   |
+| F3 (Fast Finality) participation                           | Yes           | Yes   |
+| Storage provider / sealing (`lotus-miner`, `lotus-worker`) | No            | Yes   |
+| Block production / mining                                  | No (untested) | Yes   |
+
+Forest and Lotus both expose a Lotus-compatible Filecoin JSON-RPC API (requests and responses use the same JSON format). Forest serves three API versions: `/rpc/v0` (deprecated, legacy Lotus-compatible methods), `/rpc/v1` (stable and recommended for production), and `/rpc/v2` (experimental, still being rolled out). Forest does not aim for 100% Lotus API parity: it implements the methods needed for chain validation, RPC serving, and snapshots, but not storage-provider or sealing methods. For the full, per-version list of supported methods, see the [JSON-RPC overview](../../reference/json-rpc/overview.md) and [methods reference](../../reference/json-rpc/methods.mdx).
```
>>> ```suggestion
Forest and Lotus both expose a compatible Filecoin JSON-RPC API (requests and responses use the same JSON format). Forest serves three API versions: `/rpc/v0` (deprecated, legacy Lotus-compatible methods), `/rpc/v1` (stable and recommended for production), and `/rpc/v2` (experimental, still being rolled out). For the full, per-version list of supported methods, see the [JSON-RPC overview](../../reference/json-rpc/overview.md) and [methods reference](../../reference/json-rpc/methods.mdx).
```

Also, maybe mention https://docs.forest.chainsafe.io/reports/api_conformance/report_2026-07-01
-- INLINE docs/docs/users/reports/node_comparison/index.md:17
```diff
+- **[Venus](https://github.com/filecoin-project/venus)** is a modular Go implementation. Rather than a single daemon, it is a set of independently deployable components (`venus`, `damocles`, `sophon-miner`, `venus-wallet`, `sophon-messager`, `sophon-auth`, `sophon-gateway`) that together pioneered Filecoin's distributed storage pool model, with a largely Lotus-compatible API. It aims to help small and medium-sized storage providers join the Filecoin network with a lower barrier to entry.
+
+## Feature comparison
+
+| Capability                                                 | Forest        | Lotus |
+| ---------------------------------------------------------- | ------------- | ----- |
```
>>> Let's add some features that Forest has that Lotus doesn't like eth tracing methods, archival lite/diff snapshots generation, auto-download snapshot on startup, single binary for all networks.
-- INLINE docs/docs/users/reports/node_comparison/index.md:26
```diff
+| Snapshot export                                            | Yes           | Yes   |
+| Built-in wallet                                            | Yes           | Yes   |
+| Bootstrap node                                             | Yes           | Yes   |
+| F3 (Fast Finality) participation                           | Yes           | Yes   |
+| Storage provider / sealing (`lotus-miner`, `lotus-worker`) | No            | Yes   |
+| Block production / mining                                  | No (untested) | Yes   |
```
>>> ```suggestion
| Block production / mining                                  | No (untested) | Yes   |
| Written in Rust 🦀  | Yes | No |
```
;)
-- INLINE (reply) docs/docs/users/reports/node_comparison/index.md:17
```diff
+- **[Venus](https://github.com/filecoin-project/venus)** is a modular Go implementation. Rather than a single daemon, it is a set of independently deployable components (`venus`, `damocles`, `sophon-miner`, `venus-wallet`, `sophon-messager`, `sophon-auth`, `sophon-gateway`) that together pioneered Filecoin's distributed storage pool model, with a largely Lotus-compatible API. It aims to help small and medium-sized storage providers join the Filecoin network with a lower barrier to entry.
+
+## Feature comparison
+
+| Capability                                                 | Forest        | Lotus |
+| ---------------------------------------------------------- | ------------- | ----- |
```
>>> I'm not sure if Lotus has healthcheck endpoint - something to check out
-- INLINE (reply) docs/docs/users/reports/node_comparison/index.md:17
```diff
+- **[Venus](https://github.com/filecoin-project/venus)** is a modular Go implementation. Rather than a single daemon, it is a set of independently deployable components (`venus`, `damocles`, `sophon-miner`, `venus-wallet`, `sophon-messager`, `sophon-auth`, `sophon-gateway`) that together pioneered Filecoin's distributed storage pool model, with a largely Lotus-compatible API. It aims to help small and medium-sized storage providers join the Filecoin network with a lower barrier to entry.
+
+## Feature comparison
+
+| Capability                                                 | Forest        | Lotus |
+| ---------------------------------------------------------- | ------------- | ----- |
```
>>> separate wallet
-- INLINE docs/static/img/reports/rpc_comparison/average-batch-size.png:1
```diff

```
>>> I don't think batch size is interesting for readers. The only thing that's important in that regard is the req/s and the fact that both Forest and Lotus received roughly same volume of traffic.
-- INLINE docs/static/img/reports/rpc_comparison/build-info.png:1
```diff

```
>>> No need for a screenshot of this, just write down the versions...
-- INLINE docs/docs/users/reports/node_comparison/snapshot_comparison.md:20
```diff
+| Lotus  | 450                       | 128       | 450                       |
+| Lotus  | 450                       | 256       | 232                       |
+
+Both implementations produce compatible snapshots and can consume snapshots produced by the other.
+
+:::note
```
>>> also worth noting that Forest snapshots contain an index that currently only Forest supports that allow it to use the snapshot directly as a blockstore.
-- INLINE docs/docs/users/reports/node_comparison/snapshot_comparison.md:24
```diff
+
+|        | Required disk space [GiB] | RAM [GiB] | Export duration [minutes] |
+| ------ | ------------------------- | --------- | ------------------------- |
+| Forest | 200                       | 32        | 34                        |
+| Lotus  | 450                       | 128       | 450                       |
+| Lotus  | 450                       | 256       | 232                       |
```
>>> ```suggestion
```
it's already in thescreenshot, why the repetition?
-- INLINE docs/docs/users/reports/node_comparison/index.md:8
```diff
+title: Filecoin Node Comparison
+---
+
+# Filecoin Node Comparison
+
+There are several full-node implementations of the Filecoin protocol.
```
>>> ```suggestion
There are three full-node implementations of the Filecoin protocol.
```
-- INLINE docs/docs/users/reports/node_comparison/rpc_comparison.md:97
```diff
+
+![In-flight requests](/img/reports/rpc_comparison/in-flight-requests.png)
+
+## Resource usage
+
+While serving this workload, Forest used approximately 10-15% CPU and Lotus used approximately 45-55%, briefly spiking to about 80% during the higher-load period. Forest's CPU trace was also less variable.
```
>>> Details of the hardware used must be added here to make this meaningful in any way. 10% CPU of what, 166 MHz single processor from 90s?
-- INLINE docs/docs/users/reports/node_comparison/rpc_comparison.md:19
```diff
@@ -16,6 +16,8 @@ The measurements cover the following node builds:
 | Forest | `0.34.1`     | `git.0ba362e` |
 | Lotus  | `1.36.1-rc1` | `git.45eade8` |
 
+Hardware: Intel / 8 vCPUs / 32 GB RAM / 640 GB disk
```
>>> @sudo-shashank What's the source of this information?
-- INLINE docs/docs/users/reports/node_comparison/index.md:12
```diff
+
+There are three full-node implementations of the Filecoin protocol.
+
+- **[Forest](https://github.com/ChainSafe/forest)** is written in Rust and maintained by [ChainSafe Systems](https://chainsafe.io). It focuses on chain validation, a high-performance RPC API, and snapshot generation, all with low hardware requirements.
+- **[Lotus](https://github.com/filecoin-project/lotus)** is the reference implementation, written in Go. It provides the complete Filecoin feature set, including the storage-provider stack, and is typically where new protocol features land first.
+- **[Venus](https://github.com/filecoin-project/venus)** is a modular Go implementation. Rather than a single daemon, it is a set of independently deployable components (`venus`, `damocles`, `sophon-miner`, `venus-wallet`, `sophon-messager`, `sophon-auth`, `sophon-gateway`) that together pioneered Filecoin's distributed storage pool model and offer a largely Lotus-compatible API. It aims to help small and medium-sized storage providers join the Filecoin network with a lower barrier to entry.
```
>>> ```suggestion
- **[Venus](https://github.com/filecoin-project/venus)** is a modular Go implementation. Rather than a single daemon, it is a set of independently deployable components that together pioneered Filecoin's distributed storage pool model and offer a largely Lotus-compatible API. It aims to help small and medium-sized storage providers join the Filecoin network with a lower barrier to entry.
```

We don't name Forest and Lotus binaries, no need to do that for Venus, no?
-- INLINE docs/docs/users/reports/node_comparison/index.md:36
```diff
+| Offline RPC server from snapshot (`forest-tool api serve`)        | Yes           | No    |
+| Stateless node mode (`--stateless`)                               | Yes           | No    |
+| Ethereum `trace_call` and `debug_traceTransaction`                | Yes           | No    |
+| Written in Rust 🦀                                                | Yes           | No    |
+
+Forest and Lotus both expose a compatible Filecoin JSON-RPC API (requests and responses use the same JSON format). Forest supports three API versions: `/rpc/v0` (deprecated; legacy Lotus-compatible methods), `/rpc/v1` (stable and recommended for production), and `/rpc/v2` (experimental; still being rolled out). See the [JSON-RPC overview](../../reference/json-rpc/overview.md), [methods reference](../../reference/json-rpc/methods.mdx), and [API conformance report](../api_conformance/report_2026-07-01.md) for supported methods and Lotus API parity.
```
>>> ```suggestion
Forest and Lotus both expose a compatible Filecoin JSON-RPC API. Forest supports three API versions: `/rpc/v0` (deprecated; legacy Lotus-compatible methods), `/rpc/v1` (stable and recommended for production), and `/rpc/v2` (experimental; still being rolled out). See the [JSON-RPC overview](../../reference/json-rpc/overview.md), [methods reference](../../reference/json-rpc/methods.mdx), and [API conformance report](../api_conformance/report_2026-07-01.md) for supported methods and Lotus API parity.
```

Let's assume the reader knows what `compatible` means, no need to explain basic technical nomenclature.
-- INLINE docs/docs/users/reports/node_comparison/rpc_comparison.md:8
```diff
+title: RPC Performance Comparison
+---
+
+# RPC Performance: Forest vs Lotus
+
+The following measurements were taken while a Forest node and a Lotus node handled a similar stream of real RPC provider traffic during the same time window. Because both nodes served an almost identical request stream, the differences below are attributable to the nodes themselves rather than to uneven load. The "Comparable load" section documents the checks used to support that assumption.
```
>>> Let's mention when exactly they were taken. It's not that important but adds some credibility to those charts.
-- INLINE docs/docs/users/reports/node_comparison/rpc_comparison.md:14
```diff
+
+## Environment
+
+Node builds and hardware used for these measurements:
+
+| Node   | Version      | Commit        | vCPUs | RAM   |
```
>>> let's squash version and commit, no need for an extra column


==================== PR #7363 by parkan [closed merged] add coreos + k3s deployment example
https://github.com/ChainSafe/forest/pull/7363
review sequence: APPROVED
-- REVIEW APPROVED
LGTM, thanks!


==================== PR #7365 by dependabot[bot] [closed merged] chore(deps): bump quick_cache from 0.6.24 to 0.7.0
https://github.com/ChainSafe/forest/pull/7365
review sequence: APPROVED


==================== PR #7371 by akaladarshi [closed merged] chore: update the contribution guidelines for external contributors
https://github.com/ChainSafe/forest/pull/7371
review sequence: APPROVED
-- REVIEW APPROVED
LGTM


==================== PR #7372 by akaladarshi [closed merged] chore(release): prepare release 0.34.1 "Ashadh"
https://github.com/ChainSafe/forest/pull/7372
review sequence: COMMENTED, APPROVED
-- INLINE (reply) CHANGELOG.md:40
```diff
 
 ### Fixed
 
+## Forest v0.34.1 "Ashadh"
+
+Non-mandatory release for all node operators. It includes fixes and RPC api improvements.
```
>>> @akaladarshi the bot is right.


==================== PR #7374 by hanabi1224 [closed merged] fix(rpc): bound nEpochs in StateCompute
https://github.com/ChainSafe/forest/pull/7374
review sequence: COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
This a breaking change in the unlikely case that someone was using this internal (but exposed) method.
-- INLINE CHANGELOG.md:30
```diff
@@ -27,6 +27,8 @@
 
 ### Breaking
 
+- [#7374](https://github.com/ChainSafe/forest/pull/7374): Bounded `nEpochs` in `Forest.StateCompute` RPC method.
```
>>> Let's mention the env variable that allows brining back whatever behavior the user might have depended on.


==================== PR #7376 by hanabi1224 [closed merged] feat: add `--augmented-snapshot` and `--tipset-lookup` to `forest-cli snapshot export`
https://github.com/ChainSafe/forest/pull/7376
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- CONVERSATION
no green checkmark, no review!
-- INLINE src/rpc/mod.rs:101
```diff
@@ -98,6 +98,7 @@ macro_rules! for_each_rpc_method {
         $callback!($crate::rpc::chain::ForestChainExportDiff);
         $callback!($crate::rpc::chain::ForestChainExportStatus);
         $callback!($crate::rpc::chain::ForestChainExportCancel);
+        // $callback!($crate::rpc::chain::ForestChainExportReceiptsEvents);
```
>>> ?
-- INLINE src/rpc/methods/chain.rs:1639
```diff
 
+#[allow(dead_code)]
+#[derive(Debug, Clone, Serialize, Deserialize, JsonSchema)]
+pub struct ForestChainExportReceiptsEventsParams {
+    pub epoch: ChainEpoch,
+    pub recent_roots: i64,
```
>>> unsigned? I guess it can't be negative?
-- INLINE src/tool/subcommands/snapshot_cmd.rs:226
```diff
+                tipset_lookup,
+            } => {
+                anyhow::ensure!(
+                    augmented.is_some() || tipset_lookup.is_some(),
+                    "either `--augmented` or `--tipset-lookup` needs to be specified"
+                );
```
>>> Not a huge deal, but AFAIK this could be specified via clap derive sorcery.
-- INLINE src/tool/subcommands/snapshot_cmd.rs:248
```diff
+                    println!("Verifying message receipts and events can be loaded...");
+                    let head = store.heaviest_tipset()?;
+                    let mut n_ts = 0;
+                    for ts in head.shallow_clone().chain(&store) {
+                        if !store.has(ts.parent_state())? {
+                            break;
```
>>> I don't get it. If it can't find the parent state, then it will break... but the command itself will succeed, no?
-- INLINE (reply) src/tool/subcommands/snapshot_cmd.rs:248
```diff
+                    println!("Verifying message receipts and events can be loaded...");
+                    let head = store.heaviest_tipset()?;
+                    let mut n_ts = 0;
+                    for ts in head.shallow_clone().chain(&store) {
+                        if !store.has(ts.parent_state())? {
+                            break;
```
>>> Perhaps worth adding a comment in the code.
-- INLINE (reply) src/rpc/mod.rs:101
```diff
@@ -98,6 +98,7 @@ macro_rules! for_each_rpc_method {
         $callback!($crate::rpc::chain::ForestChainExportDiff);
         $callback!($crate::rpc::chain::ForestChainExportStatus);
         $callback!($crate::rpc::chain::ForestChainExportCancel);
+        // $callback!($crate::rpc::chain::ForestChainExportReceiptsEvents);
```
>>> Commented out code is a pretty bad smell. The logic is trivial, so IMO it's fine to just remove it. If it later turns out to be needed, we can revive it and maybe gate it with some env variables.
-- INLINE scripts/tests/calibnet_export_check.sh:94
```diff
@@ -91,6 +91,15 @@ $FOREST_CLI_PATH snapshot export-status --wait
 
 $FOREST_CLI_PATH shutdown --force
 
+# Check file sizes
```
>>> @hanabi1224 I don't see any tests exercising the import of those snapshots. Do you intend to do it in a follow-up?
-- INLINE src/chain/mod.rs:283
```diff
+    let min_lookup_epoch_exclusive = tipset.epoch() - lookup_depth;
+    let ipld_roots = tokio::task::spawn_blocking({
+        let tipset = tipset.shallow_clone();
+        let db = db.shallow_clone();
+        move || {
+            let mut ipld_roots = vec![];
```
>>> can we guesstimate the capacity of this collection?


==================== PR #7379 by sudo-shashank [closed merged] Serialize wallet + mpool tests
https://github.com/ChainSafe/forest/pull/7379
review sequence: APPROVED


==================== PR #7382 by akaladarshi [closed merged] chore: update the chain follower logs to report validation
https://github.com/ChainSafe/forest/pull/7382
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW APPROVED
LGTM
-- INLINE src/chain_sync/chain_follower.rs:421
```diff
+                    .any(|fork| fork.stage == ForkSyncStage::ValidatingTipsets);
+
+                let status = if is_validating {
+                    ", validating tipsets".to_string()
+                } else if let Some(gap) = until_validation {
+                    format!(", validation starts in ~{gap} epochs")
```
>>> I don't think this communicates what the node is doing correctly. If I'd be reading this as a user and see "validation starts in ~10 epochs" I'd expect it to start in 5 minutes. It's more of a "fetching X headers", no?
-- INLINE src/chain_sync/chain_follower.rs:423
```diff
+                let status = if is_validating {
+                    ", validating tipsets".to_string()
+                } else if let Some(gap) = until_validation {
+                    format!(", validation starts in ~{gap} epochs")
+                } else {
+                    String::new()
```
>>> So, what is this situation?
-- INLINE (reply) src/chain_sync/chain_follower.rs:421
```diff
+                    .any(|fork| fork.stage == ForkSyncStage::ValidatingTipsets);
+
+                let status = if is_validating {
+                    ", validating tipsets".to_string()
+                } else if let Some(gap) = until_validation {
+                    format!(", validation starts in ~{gap} epochs")
```
>>> no, `validation starts in...` in my view implies clock time duration. Maybe `fetching headers (number)` would be the simplest option
-- INLINE (reply) src/chain_sync/chain_follower.rs:423
```diff
+                let status = if is_validating {
+                    ", validating tipsets".to_string()
+                } else if let Some(gap) = until_validation {
+                    format!(", validation starts in ~{gap} epochs")
+                } else {
+                    String::new()
```
>>> Yeah, but what is this state of Forest? It's not validating and there's no gap yet it's not synced?
-- INLINE src/chain_sync/chain_follower.rs:423
```diff
+                        let gap = fork.target_sync_epoch_start - fork.validated_chain_head_epoch;
+                        (gap > 0).then_some(gap)
+                    })
+                    .min();
+
+                let status = if is_validating {
```
>>> It's obviously not a big deal because these logs aren't emitted very often, but you might be interested in using a `Cow` to avoid allocations for static strings.
-- INLINE src/chain_sync/chain_follower.rs:426
```diff
+                    .min();
+
+                let status = if is_validating {
+                    ", validating tipsets".to_string()
+                } else if let Some(remaining) = remaining_headers {
+                    format!(", fetching headers (~{remaining})")
```
>>> why `~`? we know the exact value, no?
-- INLINE src/chain_sync/chain_follower.rs:409
```diff
+                let is_validating = forks
+                    .iter()
+                    .any(|fork| fork.stage == ForkSyncStage::ValidatingTipsets);
+                let is_fetching = forks
+                    .iter()
+                    .any(|fork| fork.stage == ForkSyncStage::FetchingHeaders);
```
>>> This seems kind of off or wasteful. Please revisit the logic. Imagine you have two forks, one doing validations, and another fetching headers. You kind of authoritatively decided only display only the first one (I'm not saying it's bad, it just need to be conscious) if it's present and skip the second one but you still do all the computation (however cheap) for the latter.


==================== PR #7388 by hanabi1224 [closed merged] chore: bump Go deps
https://github.com/ChainSafe/forest/pull/7388
review sequence: APPROVED


==================== PR #7392 by akaladarshi [closed merged] fix: `eth` new block filter stateful test
https://github.com/ChainSafe/forest/pull/7392
review sequence: COMMENTED, DISMISSED, APPROVED
-- INLINE src/tool/subcommands/api_cmd/stateful_tests.rs:202
```diff
-                // Ignore other message types
+async fn wait_next_epoch(client: &rpc::Client) -> anyhow::Result<()> {
+    let base = client.call(ChainHead::request(())?).await?.epoch();
+    tokio::time::timeout(Duration::from_secs(180), async {
+        loop {
+            tokio::time::sleep(Duration::from_secs(1)).await;
```
>>> Let's wait, i.e., 100 ms, this call should be cheap to invoke and it'll save some time.


==================== PR #7396 by leshyatwork[bot] [closed merged] [automated] Update Forest RPC reference docs
https://github.com/ChainSafe/forest/pull/7396
review sequence: APPROVED


==================== PR #7407 by akaladarshi [closed merged] chore(docs): include versioning note in the release checklist
https://github.com/ChainSafe/forest/pull/7407
review sequence: APPROVED


==================== PR #7435 by sudo-shashank [closed merged] fix(eth): allow eth_call and eth_estimateGas from contract and non-existent senders
https://github.com/ChainSafe/forest/pull/7435
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED
-- REVIEW COMMENTED
what's the coverage for the code you added? As in, are there any arms that are not exercised?
-- CONVERSATION
@sudo-shashank Did you run your changes against local CC review? I ran it on this PR and it surfaced some potential issues.

```
Correctness:
1. eth.rs:2064 — out-of-gas trace check is dead. Some(_expected_exit_code) is a fresh pattern binding, so it matches any exit code, not SYS_OUT_OF_GAS. A genuinely-reverting eth_estimateGas can be pushed into gas_search and return a bogus near-block-limit estimate instead of the revert error. This is the most important one — and it directly qualifies the parity agent's "point 5 correct" verdict.
2. message_simulation.rs:195 — skip-retry never fires for ID-mapped senders. The Enforce branch propagates an untyped resolve error, so eth_call from a non-existent 0xff..<id> sender errors out where Lotus (which checks GetActor first) succeeds. The sibling gas.rs path types it correctly, so the two are inconsistent.
3. gas.rs:281 — lost diagnostics. Direct Filecoin.GasEstimate* callers now get a bare "sender validation failed" instead of the exit-code + reason main surfaced.

Plausible:
4. gas.rs:225 — map_err(|_| …) swallows the real error. A transient blockstore/state read failure gets misclassified as a sender-validation problem and silently falls into the skip-sender path with a fabricated placeholder.

Cleanups: a redundant tipset+actor load on the hot EOA path (eth.rs:1875), a duplicated overestimation/cap formula that can drift (eth.rs:1974), and unconditional tipset+msg clones for a rarely-fired retry (eth.rs:1997).
```

Are those plausible?
-- CONVERSATION
> Yes

Yes what? Which issues were correctly flagged and fixed, and which did you discard?
-- CONVERSATION
> Still looking into transient read failure getting misclassified.

Ok, in this case if you're still looking into it, please put the PR to draft.
-- INLINE src/tool/subcommands/api_cmd/test_snapshots.txt:1
```diff

```
>>> 1. What's the grand total size of all new snapshots?
2. What are those snapshots doing? Is each one of them testing something distinct or is there some overlap?
-- INLINE (reply) src/tool/subcommands/api_cmd/test_snapshots.txt:1
```diff

```
>>> are v1/v2 paths any different in practice for these methods?
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1649
```diff
     tests
 }
 
+const SKIP_SENDER_CONTRACT: &str = "0x0c1d86d34e469770339b53613f3a2343accd62cb";
+const SKIP_SENDER_CALLDATA: &str =
+    "0xf8b2cb4f000000000000000000000000CbfF24DED1CE6B53712078759233Ac8f91ea71B6";
```
>>> Those needs some commenting.
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1659
```diff
+    tests.extend(eth_skip_sender_create_reject_tests(epoch)?);
+    tests.extend(eth_skip_sender_block_param_tests(epoch)?);
+    Ok(tests)
+}
+
+fn eth_skip_sender_cases(
```
>>> It's not exactly clear what it does. Please add some comments.
-- INLINE src/tool/subcommands/api_cmd/api_compare_tests.rs:1664
```diff
+fn eth_skip_sender_cases(
+    epoch: ChainEpoch,
+    policy: PolicyOnRejected,
+    messages: impl IntoIterator<Item = EthCallMessage>,
+) -> anyhow::Result<Vec<RpcTest>> {
+    let mut tests = Vec::new();
```
>>> nit: not a huge deal given perf is not crucial on this path, but you could avoid reallocating - you already know the capacity the vec should have.
```suggestion
    let mut tests = Vec::with_capacity(2 * messages.len());
```
-- INLINE src/state_manager/message_simulation.rs:383
```diff
+        assert_eq!(msg.gas_limit, IMPLICIT_MESSAGE_GAS_LIMIT as u64);
+        assert!(msg.value.is_zero());
+        assert!(msg.gas_fee_cap.is_zero());
+        assert!(msg.gas_premium.is_zero());
+    }
+}
```
>>> ```suggestion
```

I think it's fair to assume that field assignment in Rust works, and there's no need to test it.
-- INLINE src/state_manager/message_simulation.rs:1
```diff

```
>>> So, just to understand it, right now when we are simulating messages, we're writing some fake executions to the blockstore? If so, it's not great as it can spam the database (not a HUGE deal, we have GC, but I'd rather avoid garbage rather than deal with it when possible). If so, we'd need some mechanism to NOT include them.

It's likely a pre-existing issue so no need to do anything in this PR - this will be a bigger change. Just confirm I understand it correctly.
-- INLINE src/rpc/methods/gas.rs:630
```diff
@@ -578,6 +626,23 @@ mod tests {
         assert_eq!(msg.gas_premium(), TokenAmount::from_atto(100));
     }
 
+    #[test]
+    fn test_overestimate_applies_the_multiplier_and_truncates() {
```
>>> I don't see how valuable this test is really. I don't see a need to validate `(gas as f64 * multiplier) as u64` works as expected (unless it's just to win 1 line of coverage, but I'd assume the callers of this method already do it).
-- INLINE src/rpc/methods/eth.rs:1929
```diff
             Ok(expected_gas.into())
         }
     }
 }
 
+fn is_sender_validation_failure(err: &Error) -> bool {
```
>>> 1. You extracted this one but do the downcast thing with others; why? 
2. This (and other occurrences) can be done more neatly with `matches!(err.downcast_ref(), Some(StateManagerError::SenderValidationFailed(_)))`. Likely removing the need for a separate function.
-- INLINE src/state_manager/message_simulation.rs:208
```diff
+                            Error::SenderValidationFailed(format!(
+                                "sender {} not found on chain",
+                                msg.from
+                            ))
+                            .into()
+                        });
```
>>> Is this for some error parity with Lotus? This seems like a pretty expensive operation just to pick between two error messages.
-- INLINE (reply) src/rpc/methods/eth.rs:3904
```diff
-                msg.clone(),
+                Some(&ts),
+                &msg,
                 VMFlush::Flush,
                 VMTrace::Traced,
+                SenderValidation::Enforce,
```
>>> I think we do? I guess this could be tackled as a separate issue, though.


==================== PR #7450 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group in /docs with 2 updates
https://github.com/ChainSafe/forest/pull/7450
review sequence: APPROVED


==================== PR #7451 by dependabot[bot] [closed merged] chore(deps): bump hadolint/hadolint-action from 3.3.0 to 3.4.0
https://github.com/ChainSafe/forest/pull/7451
review sequence: APPROVED


==================== PR #7462 by hanabi1224 [closed] fix: disable overflow in head change broadcast channel
https://github.com/ChainSafe/forest/pull/7462
-- CONVERSATION
I don't think `async-broadcast` is the best way to resolve this issue; it just pushes the overflow to another place. I think we can get away with just a simple `flume` contraption; the memory footprint should be minimal even on unbounded channel (~20 MiB per DAY of lagging behind, if you're that far away you likely have bigger problems on your node). I'll make a separate PR to address the linked issue.


==================== PR #7463 by 0xDevNinja [closed merged] docs: fix incorrect CLI help and a garbled doc comment
https://github.com/ChainSafe/forest/pull/7463
-- CONVERSATION
@EclesioMeloJunior Please remember to merge PRs from external contributors once approved.


==================== PR #7475 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/7475
review sequence: APPROVED
-- CONVERSATION
@EclesioMeloJunior No need to check, it's a periodic update. It fails due to https://github.com/ChainSafe/forest/pull/7435 not yet merged. I'll keep this open for now, otherwise the bot will re-open it tomorrow anyway.


==================== PR #7480 by sudo-shashank [closed merged] fix index backfill start epoch
https://github.com/ChainSafe/forest/pull/7480
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
No existing scripts need to be updated?
-- CONVERSATION
> > No existing scripts need to be updated?
> 
> No

Why? Is this feature not used anywhere?
-- CONVERSATION
> We use --index-backfill-epochs 200 in api-compare. Previously it backfilled 200 tipsets but counted the head as one of them; now it backfills 200 epochs below the head.

So, it this wasteful? Do we need all 200 in practice?


==================== PR #7482 by dependabot[bot] [closed merged] chore(deps): bump mermaid from 11.16.0 to 11.16.1 in /docs in the patch-versions group
https://github.com/ChainSafe/forest/pull/7482
review sequence: APPROVED


==================== PR #7484 by leshyatwork[bot] [closed merged] [automated] Update Forest checkpoints
https://github.com/ChainSafe/forest/pull/7484
review sequence: APPROVED


==================== PR #7488 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/7488
review sequence: APPROVED


==================== PR #7493 by akaladarshi [closed merged] fix: filecoin chain notify API and tests
https://github.com/ChainSafe/forest/pull/7493
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/methods/chain.rs:2435
```diff
@@ -2430,4 +2431,339 @@ mod tests {
             "expected change (left) does not match actual change (right)"
         )
     }
+
+    const BATCH_TIMEOUT: Duration = Duration::from_secs(5);
```
>>> Why 5s? How long are those tests taking? In general, we should aim for all tests to be in milliseconds land.
-- INLINE (reply) src/rpc/methods/chain.rs:2435
```diff
@@ -2430,4 +2431,339 @@ mod tests {
             "expected change (left) does not match actual change (right)"
         )
     }
+
+    const BATCH_TIMEOUT: Duration = Duration::from_secs(5);
```
>>> So how long are the tests taking right now?
-- INLINE src/rpc/channel.rs:389
```diff

```
>>> should we `send_close` here as well?
-- INLINE src/rpc/channel.rs:728
```diff
+        // no value frames arrive, the client is told the channel is gone
+        assert_eq!(next_frame(&mut frames).await, close_frame(channel_id));
+
+        // the pump exited and dropped its receiver — the source has no
+        // subscribers left, and no stray frames follow the close (the frame
+        // stream stays open because the pump's registry entry is not yet
```
>>> So when's the entry cleaned from `channels`?


==================== PR #7502 by akaladarshi [open] fix(rpc): more fixes in filecoin chain notify API
https://github.com/ChainSafe/forest/pull/7502
review sequence: COMMENTED, COMMENTED
-- REVIEW COMMENTED
I'm not really an expert in the JSON-RPC subscription mechanism and WS, so I employed AI to check some things, especially compatibility with `go-jsonrpc` (which majority of the Filecoin ecosystem uses). Please check.


```
Headline: xrpc.cancel still does not work for any go-jsonrpc client, three independent reasons

(a) id-less form never dispatched. go-jsonrpc builds the cancel with ID unset (client.go:379-384, websocket.go:381-385) and request.ID is json:"id,omitempty" (handler.go:56), so the wire frame is a JSON-RPC notification. jsonrpsee RpcService::notification (jsonrpsee-server-0.26.0/src/middleware/rpc.rs:190-195) returns MethodResponse::notification() and never reaches a MethodCallback. No Forest layer converts notifications to calls (src/rpc/mod.rs:665-672). The module doc admits this ("the id-less notification form is currently ignored") while the CHANGELOG reads as if cancel now works. The filter exemption and the register-before-accept race fix are both unreachable for real clients: the dispatch gap, not the filter list, is what blocks cancels.

(b) the reply is the wrong frame kind. close_channel_response (channel.rs:311) emits {"jsonrpc":"2.0","id":null,"result":{"jsonrpc":"2.0","method":"xrpc.ch.close","params":[N]}}, a response with a notification-shaped object nested in result. go-jsonrpc's handleFrame switches on frame.Method; that frame has none, so it lands in handleResponse, never handleChanClose. The client's chanHandlers[N] entry and its Go channel are never released for the life of the websocket. Lotus always sends the bare notification, including after a cancel: cancelling the handler ctx closes the handler's channel, and the select loop then writes request{ID: nil, // notification, Method: chClose} (websocket.go:303-315). Forest's pump instead breaks on the sink.closed() arm and sends nothing. The PR's new "Close path (a)" diagram documents this divergence as if it were the protocol.

(c) id convention is inconsistent. Success uses Id::Null, the error path echoes the request id (channel.rs:379). A spec-compliant client keying pending calls by id never resolves the success case. Tracked as #4453, but this PR rewrote exactly that doc block and added assertions locking it in.

Fixing (b) is cheap and is the one that actually unbreaks Lotus/Curio teardown: send the bare xrpc.ch.close from the pump's unsubscribe arm. (a) needs a notification hook in a layer.

Medium

Reject list also bypassed. filter_layer.rs:50: method_name == CANCEL_METHOD_NAME || self.filter_list.authorize(..) short-circuits before the reject half. FilterList::authorize is "allow-empty-or-matched AND not rejected", with substring matching (filter_list.rs:22-26), so !xrpc.cancel (and even !xrpc) is silently ignored, no log. Keep the reject check in the condition; there is no is_rejected accessor yet, so one has to be added.

Serialization-failure skip contradicts the #5795 sibling entry. channel.rs:429: a batch that fails to serialize is now dropped and the channel stays open, 12 lines above a RecvError::Lagged arm that closes the channel specifically to avoid undetectable gaps, and one CHANGELOG bullet above "Filecoin.ChainNotify now closes the subscription channel ... instead of silently dropping head changes". go-jsonrpc's continue (websocket.go:320-323) is faithful parity, but for ChainNotify a lost apply/revert batch is exactly the failure #5795 set out to kill. A persistently unserializable payload also produces one tracing::error! per head change forever. Was skip-vs-close a deliberate choice for ChainNotify, or just parity copied from a different payload class? Also missing CHANGELOG entries for this flip, for 10 -> 16, and for the new slow-subscriber warning.

Cancel-after-server-close now errors. Previously the registry entry outlived the pump, so a client tearing down after xrpc.ch.close got a success. Now ChannelRegistration::drop removes it and the same cancel gets channel not found, codified by the new source_closed_sends_bare_close assertion. go-jsonrpc's cancelCtx (websocket.go:416-441) ignores an unknown id silently and never responds. Unconditional-cancel-on-teardown is a normal pattern, so this surfaces a new spurious error.

Low

- Displaced close races its own pump (channel.rs:512). insert drops the old unsubscribe receiver and a detached send_close runs concurrently with the old pump, whose tokio::select! picks randomly among ready arms. With queued broadcast messages, xrpc.ch.val for channel N can be written after xrpc.ch.close for N on the same sink; go-jsonrpc has already deleted the handler and logs handler N not found per stray frame. Send the close from the pump's unsubscribe arm instead.
- Double close window (channel.rs:504). Pump does send_close, break, debug!, then drops the registration. A same-id subscribe in that window displaces the stale entry and spawns a second send_close for the already-dead channel id.
- Forwarder task outlives the pump (chain.rs:1739). It parks on head_changes_rx.recv_async() and only notices the dead receiver on the next sender.send. On a stalled or header-syncing node, every connect/disconnect cycle accumulates a task plus a flume receiver the publisher must fan out to. Pre-existing, but the CHANGELOG says "no longer leak node resources".
- Duplicate mechanism (chain.rs:61). subscribe_head_changes_bounded exists for precisely "consumers driven by an untrusted RPC client's read rate" (chain_store.rs:349-355) and eth newHeads uses it (pubsub.rs:119). ChainNotify keeps the unbounded subscription and stacks a second bespoke scheme on top (16-slot broadcast, lag close, hand-rolled len() > 5 warn). Two things to keep in sync, and the bounded of the new warning.
- Permit fix untested (channel.rs:158). Methods::raw_json_request injects mock_subscription_permit() (jsonrpsee-core-0.26.0/src/server/rpc_module.rs:358), so nothing in the module asserts the max_subscriptions_per_connection claim. Only _permit plus a comment stand between a future cleanup and silently reverting it.
- PendingSubscriptionSink.id duplicates registration.request_id (lines 499 and 522 both clone the same Id). One source of truth would do.
- ChannelRegistration::drop does get then remove (two hashes; entry/Occupied does it in one) and drops the removed tuple while ke the subscribe path (504) and cancel handler (360) which both release first.
- v1.36.2 lotus link is likely dead (chain.rs:60). No such tag in the local clone (latest v1.36.1), and the repo pins v1.36.0 / release/v1.36.1 elsewhere. Content is right: at v1.36.1, store.go:295 is make(chan []*api.HeadChange, 16), warn at :329 with > 5 and byte-identical text.
- CHANGELOG: stray blank line inserted between the two ### Added bullets; five separate #5795 bullets could collapse.
- SLOW_SUBSCRIBER_BACKLOG doc: "forest" -> "Forest".

Style, your personal rules rather than repo rules, so raise only if you want to: the new comments include several 6-7 line narrative paragraphs that explain the alternative and the decision (lines 489-494, 505-511), the _permit rationale is duplicated verbatim at 156-158 and 232-234, and four added comment lines use em-dashes (78, 107, 739, 867).
```
-- INLINE src/rpc/channel.rs:160
```diff
-        let success = response.is_success();
+        if !response.is_success() {
+            panic!(
+                "The subscription response was too big; adjust the `max_response_size` or change Subscription ID generation"
+            );
+        }
```
>>> I understand this is an existing behavior, but crashing the node when the response is a bit too big is a bit dramatic, isn't it?


==================== PR #7513 by parkan [closed merged] coreos-k3s: pin hostname so k3s node name survives reboots
https://github.com/ChainSafe/forest/pull/7513
review sequence: APPROVED
-- REVIEW APPROVED
LGTM, thanks!


==================== PR #7517 by dependabot[bot] [closed merged] chore(deps-dev): bump tsx from 4.23.5 to 4.23.12 in /docs in the patch-versions group
https://github.com/ChainSafe/forest/pull/7517
review sequence: APPROVED


==================== PR #7519 by sudo-shashank [closed] Relax finalty-gated caches
https://github.com/ChainSafe/forest/pull/7519
review sequence: COMMENTED
-- REVIEW COMMENTED
Have you run it through some AI code review? I'm unsure this relaxation is safe and correct.
-- INLINE CHANGELOG.md:37
```diff
@@ -33,6 +33,8 @@
 
 ### Changed
 
+- [#7356](https://github.com/ChainSafe/forest/issues/7356): Ethereum transaction receipts and ID-to-address resolutions are now cached once the EC finality calculator considers them final, instead of waiting for the static 900-epoch chain finality.
+
```
>>> ```suggestion
```

This is not a user-facing change but an internal detail.
-- INLINE src/chain/store/index.rs:516
```diff
+                    timestamp: nonce,
+                    state_root,
+                    ..Default::default()
+                })
+            })
+            .collect();
```
>>> nit: `collect_vec`


==================== PR #7539 by 0xDevNinja [closed] feat(wallet): support calldata in forest-wallet send
https://github.com/ChainSafe/forest/pull/7539
review sequence: COMMENTED
-- REVIEW COMMENTED
It'd be good to exercise this logic in form of some integration test, i.e., through devnet tests.


==================== PR #7545 by 0xDevNinja [closed] test(rpc): cover StateMinerCreationDeposit initial-pledge calc
https://github.com/ChainSafe/forest/pull/7545
review sequence: CHANGES_REQUESTED, COMMENTED
-- REVIEW CHANGES_REQUESTED
Thanks. Let's move it to a separate file, e.g., `state_tests.rs` as to not bloat this massive file any further.

Let's also address all the CodeRabbit issues and make sure the CI is passing.
-- REVIEW COMMENTED
@0xDevNinja Thank you for you contribution, but after reviewing your PR I decided this needs to be handled internally. There are too many traps to do it correctly.
-- INLINE src/rpc/methods/state.rs:3600
```diff
+    // 1 FIL in atto.
+    const FIL: i128 = 1_000_000_000_000_000_000;
+
+    fn filter_estimate(
+        position: i128,
+    ) -> fil_actors_shared::v18::builtin::reward::smooth::FilterEstimate {
```
>>> Let's not use specific versions of actors/fvm directly. Ideally, only shims would would deal with those.
-- INLINE src/rpc/methods/state.rs:3645
```diff
+            StoragePower::from(1u64 << 50),
+            fvm_shared4::econ::TokenAmount::from_atto(0),
+            filter_estimate(smoothed),      // this_epoch_reward_smoothed
+            StoragePower::from(1u64 << 50), // this_epoch_baseline_power (> 0, avoids div-by-zero)
+            0,
+            fvm_shared4::econ::TokenAmount::from_atto(1_000_000 * FIL), // total_storage_power_reward -> fil_mined
```
>>> You can just use `from_whole`.
-- INLINE src/rpc/methods/state.rs:3435
```diff
+
+    let circ_supply = state_manager
         .genesis_info()
-        .get_vm_circulating_supply_detailed_with_state_tree(ts.epoch(), &state_tree)?;
+        .get_vm_circulating_supply_detailed_with_state_tree(ts.epoch(), &state_tree)
+        .context("computing circulating supply for initial pledge")?;
```
>>> The error messages mustn't be modified. Adding context makes this

https://github.com/ChainSafe/forest/blob/a80b3f8f6534c777909d2ef3f851e7ea029c0536/src/rpc/error.rs#L117-L118

completely override the error message. So now instead of a `Actor state doesn't exist in store` we get something else (arguably not as helpful).
-- INLINE src/rpc/methods/state_tests.rs:37
```diff
+use std::sync::Arc;
+
+// First calibnet epoch on network version 27 (the deposit calculation): one
+// past the GoldenWeek upgrade height. Testing here keeps the real upgrade
+// schedule rather than overriding the genesis network version.
+const CALIBNET_V27_EPOCH: i64 = 3_007_295;
```
>>> Is the activation boundary covered?
-- INLINE src/rpc/methods/state_tests.rs:96
```diff
+        (&TokenAmount::from_whole(0)).into(),
+    );
+
+    let meta = ACTOR_BUNDLES_METADATA
+        .values()
+        .find(|m| m.actor_major_version().ok() == Some(18))
```
>>> incorrect
-- INLINE src/rpc/methods/state_tests.rs:53
```diff
+
+// Builds a state tree with the power, reward, burnt-funds and reserve actors
+// that `compute_initial_pledge_for_power` and the circulating-supply
+// calculation read, and returns its root. `ramp_start_epoch` selects the
+// pledge-ramp branch under test.
+fn build_state_root<S: Blockstore + ShallowClone>(
```
>>> Incorrect actor version & bundle handling.


==================== PR #7557 by dependabot[bot] [closed merged] chore(deps-dev): bump typescript from 6.0.3 to 7.0.2 in /docs
https://github.com/ChainSafe/forest/pull/7557
review sequence: APPROVED
-- REVIEW APPROVED
I guess it's fine
-- CONVERSATION
@EclesioMeloJunior the checks are not passing but you approved it; this means you're volunteer to fix them!


==================== PR #7565 by sudo-shashank [closed merged] Add --as-default flag to forest-wallet import
https://github.com/ChainSafe/forest/pull/7565
review sequence: COMMENTED, APPROVED
-- INLINE src/dev/subcommands/tests_cmd/wallet.rs:113
```diff
@@ -82,13 +82,14 @@ async fn export_import_roundtrip(backend: Backend) {
     let deleted = wallet(backend, &["delete", &addr]).unwrap();
     eprintln!("delete output ({}): {deleted}", backend.label());
 
-    let imported = wallet(backend, &["import", path]).unwrap();
+    let imported = wallet(backend, &["import", "--as-default", path]).unwrap();
```
>>> Now the test is missing the _non-default_ path. It should be a separate one.


==================== PR #7584 by dependabot[bot] [closed merged] chore(deps-dev): bump @types/react-dom from 19.2.4 to 19.2.5 in /docs in the patch-versions group
https://github.com/ChainSafe/forest/pull/7584
review sequence: APPROVED


==================== PR #7586 by dependabot[bot] [closed merged] chore(deps): bump flate2 from 1.1.9 to 1.1.10 in the patch-versions group
https://github.com/ChainSafe/forest/pull/7586
review sequence: APPROVED


==================== PR #7587 by fmterrors [closed] docs: fix minimum hardware requirement wording
https://github.com/ChainSafe/forest/pull/7587
review sequence: APPROVED
-- CONVERSATION
Closing due to lack of response.


==================== PR #7593 by akaladarshi [closed merged] feat: integrate v19 actor
https://github.com/ChainSafe/forest/pull/7593
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
A couple of things, not necessarily to do in this PR but worth considering at least as a separate issue.

1. There's a lot of this `impl_evm_delegate_call_params_lotus_json` dance; if one misses a particular version, is there a compilation error? If not, can we make it so that it's somehow an exhaustive match? It's not a huge deal if it can't be done easily (let's not contrive a monster abstraction to do that), a simple human-based grep can find those. Just something to consider.
2. Are there places where actor version is "leaking"? Ideally, there wouldn't be, e.g., `fil_actor_XYZ_state::v19::` outside of shims. It might be idealistic, of coruse, but each place where it isn't need to have a solid justification why it's not. In some places, it might be sufficient to have a public `latest` export from the shim, so that modifications outside of it are minimal.
3. I see there is still some duplication, e.g., 
```rust
          State::V17(st) => Ok(DealStates::V17(V16AsActorError::context_code(
                V17DealMetaArray::load(&st.states, store),
                FVM4ExitCode::USR_ILLEGAL_STATE,
                "failed to load deal state array",
            )?)),
            State::V18(st) => Ok(DealStates::V18(V18AsActorError::context_code(
                V18DealMetaArray::load(&st.states, store),
                FVM4ExitCode::USR_ILLEGAL_STATE,
                "failed to load deal state array",
            )?)),
            State::V19(st) => Ok(DealStates::V19(V19AsActorError::context_code(
                V19DealMetaArray::load(&st.states, store),
                FVM4ExitCode::USR_ILLEGAL_STATE,
                "failed to load deal state array",
            )?)),
```
(which is also incorrect, see `DealStates::V17(V16AsActorErro`). This could be deduplicated with some macros or perhaps the delegate mechanism from `ambassador`.
4. I see things like `impl TryFrom<&fil_actor_market_state::v19::DealProposal> for DealProposal` that do a bunch of cloning. I'm not sure where we use it, but it might be worth considering having an owned version of it to avoid re-allocations and redundant ops. I might be missing something but I'd assume we mostly want to upgrade one struct to another and then throw the other one away.

Also, stating the obvious, but this PR mustn't be merged until the shape of the v19 actor state is known and a proper `fil-actor-states` is released; we cannot have git references in `Cargo.toml`, as it'd block any (potential emergency) releases
-- REVIEW COMMENTED
LGTM outside of one comment around potential RPC failure.
-- INLINE src/shim/actors/builtin/market/ext/state.rs:137
```diff
@@ -133,6 +133,9 @@ impl MarketStateExt for market::State {
                     Ok(())
                 })?;
             }
+            Self::V19(_) => {
+                anyhow::bail!("unsupported from actors v19");
```
>>> Is this the expected behavior? As in, should an RPC method that somewhere along the way invokes `get_allocations_for_pending_deals` always fail from NV29 onwards? What's the behaviour in Lotus?
-- INLINE src/shim/actors/builtin/market/mod.rs:731
```diff
@@ -703,6 +728,34 @@ impl TryFrom<&fil_actor_market_state::v18::DealProposal> for DealProposal {
     }
 }
 
+impl TryFrom<&fil_actor_market_state::v19::DealProposal> for DealProposal {
```
>>> There's lots of this `TryFrom<ref to new version> for Shim` that does a lot of cloning. My naive assumption is that normally we should just consume the original (compiler will tell).

It's an existing issue with the codebase based on my feeling how it's used, not a deep research. To be investigated. @coderabbitai create an issue.


==================== PR #7594 by sudo-shashank [closed merged] Drop `--recompute` flag on index backfill
https://github.com/ChainSafe/forest/pull/7594
review sequence: COMMENTED
-- REVIEW COMMENTED
1. We can avoid a breaking change by making the `--recompute` default to `true`. This way, we also allow the consumer to not do it, for any reason, e.g., mentioned sync starving.
2. Does the backfill now fail or not on a missing receipt? I think that was the main issue; it silently failed with return code zero, thus it flew under @EclesioMeloJunior radar.
-- INLINE CHANGELOG.md:34
```diff
@@ -31,6 +31,8 @@
 
 ### Changed
 
+- [#7594](https://github.com/ChainSafe/forest/pull/7594): Dropped `--recompute` from `forest-cli index backfill`; always recompute and backfill as much as possible, exiting non-zero on error.
```
>>> This is a breaking change; users that were applying the `--recompute` would find their command failing after this.


==================== PR #7595 by sudo-shashank [closed merged] Implement `forest-cli evm invoke`
https://github.com/ChainSafe/forest/pull/7595
review sequence: DISMISSED, COMMENTED, COMMENTED
-- REVIEW DISMISSED
Might be worth taking over (with proper attribution) https://github.com/ChainSafe/forest/pull/7539 in a follow-up, I think the changes are largely similar.
-- INLINE src/cli/subcommands/evm_cmd.rs:55
```diff
+        /// Optionally specify the account to use for sending the exec message
+        #[arg(long)]
+        from: Option<Address>,
+        /// Value to send with the invocation message, in attoFIL
+        #[arg(long, default_value_t = 0)]
+        value: i64,
```
>>> Can we just use `TokenAmount` or something similar, e.g., what we used with `forest-wallet send`? This way we'd have stronger typing AND one wouldn't have to deal monstrous attoFIL values, e.g., via `1milliFIL`
-- INLINE src/lotus_json/mod.rs:735
```diff
             ("0xF", vec![15]),
             ("0x2a42", vec![42, 66]),
             ("0x2A42", vec![42, 66]),
+            ("0X2a42", vec![42, 66]),
+            ("  0x2a42\n", vec![42, 66]),
+            ("\t0X2A42  ", vec![42, 66]),
```
>>> do we really want to deal with such inputs? Handling this costs resources for EVERY caller, but I'd say if a user puts tabs or whitespace in their input, well, they deserve errors.
-- INLINE (reply) src/lotus_json/mod.rs:735
```diff
             ("0xF", vec![15]),
             ("0x2a42", vec![42, 66]),
             ("0x2A42", vec![42, 66]),
+            ("0X2a42", vec![42, 66]),
+            ("  0x2a42\n", vec![42, 66]),
+            ("\t0X2A42  ", vec![42, 66]),
```
>>> Let's not do this; I'd rather we are fast and strict.
-- INLINE (reply) src/cli/subcommands/evm_cmd.rs:55
```diff
+        /// Optionally specify the account to use for sending the exec message
+        #[arg(long)]
+        from: Option<Address>,
+        /// Value to send with the invocation message, in attoFIL
+        #[arg(long, default_value_t = 0)]
+        value: i64,
```
>>> If we can offer a better approach than Lotus does then we should. While must maintain compatibility on the RPC layer, we're free to improve UX on the CLI level.


==================== PR #7598 by akaladarshi [closed merged] chore: nv29 skeleton
https://github.com/ChainSafe/forest/pull/7598
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, DISMISSED, APPROVED
-- CONVERSATION
@akaladarshi no green checkmark, no review!
-- INLINE src/networks/mod.rs:49
```diff
@@ -46,7 +46,13 @@ pub mod mainnet;
 pub mod metrics;
 
 /// Newest network version for all networks
-pub const NEWEST_NETWORK_VERSION: NetworkVersion = NetworkVersion::V27;
+pub const NEWEST_NETWORK_VERSION: NetworkVersion = NetworkVersion::V29;
```
>>> I don't remember in what scenarios this is used, but it might be incorrect to update it across the board like this; better to revisit its usage. We don't want, e.g., calibnet to report somehow its newest network version as nv29 (likely not the case here, but you get the point)
-- INLINE src/utils/misc/logo.rs:258
```diff
@@ -253,3 +254,18 @@ fn reveal_fire_horse_upgrade() {
         "###
     );
 }
+
+/// Reveals the sun standing still on the horizon.
```
>>> beautiful solstice! it's a pity it'll happen on equinox (unless it slips 3 months)
-- INLINE src/networks/butterflynet/mod.rs:111
```diff
@@ -108,6 +108,7 @@ pub static HEIGHT_INFOS: LazyLock<IndexMap<Height, HeightInfo>> = LazyLock::new(
         make_height!(Tock, -29),
         make_height!(GoldenWeek, -30),
         make_height!(FireHorse, 1440, get_bundle_cid("v18.0.0")),
+        make_height!(Solstice, UPGRADE_HEIGHT_UNSCHEDULED),
```
>>> the bundle is there already. https://github.com/filecoin-project/builtin-actors/releases/tag/v19.0.0-dev1
-- INLINE src/networks/mod.rs:54
```diff
+pub const NEWEST_NETWORK_VERSION: NetworkVersion = NetworkVersion::V29;
+
+/// Placeholder epoch for a network upgrade that has no scheduled height yet.
+///
+/// Same sentinel as Lotus `UpgradeHeightUnscheduled`, so `Filecoin.StateGetNetworkParams` matches:
+/// <https://github.com/filecoin-project/lotus/blob/1b0155685292f691babd930f1060562ecff645c3/build/buildconstants/params.go#L13>
```
>>> ```suggestion
```

Half of Forest code has stuff that's supposed to match Lotus. Should be pretty obvious.
-- INLINE (reply) src/utils/misc/logo.rs:258
```diff
@@ -253,3 +254,18 @@ fn reveal_fire_horse_upgrade() {
         "###
     );
 }
+
+/// Reveals the sun standing still on the horizon.
```
>>> it already slipped 2 weeks, we can get there


==================== PR #7599 by akaladarshi [closed merged] feat: nv29 base
https://github.com/ChainSafe/forest/pull/7599
review sequence: COMMENTED, CHANGES_REQUESTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW CHANGES_REQUESTED
This needs a passing migration on a 2k network so that we are confident that the stateroot produced by Forest is exactly the same as in Lotus. Right now nothing exercises it.
-- REVIEW APPROVED
Approving on the condition the outstanding comments are resolved in a an immediate follow-up.
-- INLINE src/networks/mod.rs:258
```diff
@@ -254,6 +255,56 @@ struct DrandPoint<'a> {
     pub config: &'a LazyLock<DrandConfig<'a>>,
 }
 
+/// A clamped linear stream weight in `DENOM` fixed point, without its slope and start epoch.
```
>>> I don't see why some Solstice stuff should be part of the general network module. If it must be a part of the network, let's at least kick it out to its own dedicated file.
-- INLINE src/networks/mod.rs:367
```diff
@@ -311,6 +363,17 @@ impl ChainConfig {
             // 1 year on mainnet
             fip0081_ramp_duration_epochs: 365 * EPOCHS_IN_DAY as u64,
             upgrade_teep_initial_fil_reserved: None,
+            // Taken from here <https://github.com/filecoin-project/lotus/blob/1b0155685292f691babd930f1060562ecff645c3/build/buildconstants/params_mainnet.go#L150>
+            solstice_reward_bootstrap: SolsticeRewardBootstrapParams {
```
>>> lots of shared boilerplate, a proper c-tor would help
-- INLINE src/state_migration/nv29/reward.rs:59
```diff
+impl RewardMigrator {
+    /// Derives the bootstrap streams starting at `activation_epoch`, the first epoch executed on
+    /// the migrated state, and vets them with the actor's own state validation.
+    ///
+    /// # Errors
+    /// Fails on the inputs Lotus and go-state-types reject: a missing or non-ID bootstrap
```
>>> It's kind of pointless to constantly (here and in other places) mention that it's the same behavior as Lotus and go-state-types. It's kind of expected of the consensus code to follow the reference implementation, no need to emphasize. On the other hand, it's definitely worth mentioning if we surprisingly don't follow it.
-- INLINE src/rpc/methods/state/tests.rs:450
```diff
 }
 
+/// From NV29 a pre-commit no longer describes a pledge, so the collateral RPC refuses.
+#[tokio::test]
+async fn initial_pledge_collateral_is_retired_from_nv29() {
+    let mut config = ChainConfig::calibnet();
```
>>> work parametrizing via `rstest` against different networks
-- INLINE (reply) src/state_migration/mod.rs:28
```diff
@@ -25,6 +25,7 @@ mod nv25;
 mod nv26fix;
 mod nv27;
 mod nv28;
+mod nv29;
```
>>> done higher up the stack
-- INLINE src/state_migration/nv29/market.rs:90
```diff
+            total_provider_locked_collateral: in_state.total_provider_locked_collateral.clone(),
+            total_client_storage_fee: in_state.total_client_storage_fee.clone(),
+            provider_sectors: in_state.provider_sectors,
+        };
+        // `State` has no `PartialEq`.
+        assert_eq!(format!("{out_state:?}"), format!("{expected:?}"));
```
>>> that's annoying - can we derive it? not a blocker but a general good practice for the APIs is to have those derived (even if `builtin-actors` don't do it - they're not a library themselves after all)

Refer to https://rust-lang.github.io/api-guidelines/interoperability.html
-- INLINE src/state_migration/nv29/migration.rs:48
```diff
+        self.add_migrator(
+            current_manifest.get_system(),
+            system::system_migrator(new_manifest),
+        );
+        // Streams start at the first epoch executed on the migrated state.
+        let activation_epoch = chain_config.epoch(Height::Solstice) + 1;
```
>>> what's the behaviour in case of a null round? is it fine and defined somewhere in the FIP?
-- INLINE src/state_migration/nv29/reward.rs:417
```diff
+        let output = RewardMigrator::new(&bootstrap_params(), activation_epoch, new_code_cid)
+            .unwrap()
+            .migrate_state(&store, ActorMigrationInput::for_head(head))
+            .unwrap()
+            .unwrap();
+        assert_eq!(output.new_code_cid, new_code_cid);
```
>>> Shouldn't you rather check new reward state? The current assertion is not really useful, you're essentially checking equality of the cid that you passed there.
-- INLINE src/rpc/methods/state/tests.rs:449
```diff
     );
 }
 
+/// Puts nv29 at [`FIXTURE_EPOCH`], because no network schedules Solstice yet.
+fn solstice_at_fixture_epoch(mut config: ChainConfig) -> ChainConfig {
+    config
```
>>> you might want to sort this collection, just in case - the ordering there is important.
-- INLINE src/rpc/methods/state.rs:1128
```diff
+        if ctx.state_manager.get_network_version(ts.epoch()) >= NetworkVersion::V29 {
+            return Err(anyhow::anyhow!(
+                "StateMinerInitialPledgeCollateral is unsupported from network version 29 (FIP-0118): use StateMinerInitialPledgeForSector"
+            )
+            .into());
+        }
```
>>> will this fail the RPC calibnet tests once NV29 activates on calibnet? Not sure what policy we put there, but should be that if both nodes fail with similar errors then it's okay.
-- INLINE src/state_migration/nv29/migration.rs:48
```diff
+        self.add_migrator(
+            current_manifest.get_system(),
+            system::system_migrator(new_manifest),
+        );
+        // Streams start at the first epoch executed on the migrated state.
+        let activation_epoch = chain_config.epoch(Height::Solstice) + 1;
```
>>> also, this is simply `let activation_epoch = epoch + 1;`, no? if you forward that from the `run_migration`
-- INLINE (reply) src/state_migration/nv29/migration.rs:48
```diff
+        self.add_migrator(
+            current_manifest.get_system(),
+            system::system_migrator(new_manifest),
+        );
+        // Streams start at the first epoch executed on the migrated state.
+        let activation_epoch = chain_config.epoch(Height::Solstice) + 1;
```
>>> obviously not a big deal


==================== PR #7608 by akaladarshi [open] refactor(events): restructure `filecoin` and `eth` events.
https://github.com/ChainSafe/forest/pull/7608
review sequence: COMMENTED
-- REVIEW COMMENTED
The direction seems sound, I also don't love our sprawl of free functions but they're often the most optimal ones (hello C). Couple of things to think about before going forward with this:

1. It doesn't seem to me lots of this logic should live under `rpc/methods/eth`; more like `src/eth/*`. Ideally, `rpc/*` would contain logic that is strictly there for RPC handling. As you mentioned, tipset events are a _chain-level artifact_.
2. Performance - it seems to me that there might be a lot of redundant work with building those structs. We'd need A LOT of care not to introduce a regression in affected methods.
3. Error handling - it's also unclear to me whether this design preserves error handling logic.

In general, it'd be worth having a broader discussion with multiple alternatives so that we can have a consensus why a given one is the best one.

Also, as a broader refactoring on user-critical logic, this would need first a good coverage net (not sure what's the % at the moment); refer to Working Effectively with Legacy Code from Michael Feathers. I really wouldn't like to have regressions for the sake of nicer (subjectively) code. At the same time, if refactoring could open vistas for improved performance, I'm convinced everyone would be on board with that.

A good first step in doing some refactoring would be just moving things around (without ANY logic changes); I already mentioned it elsewhere, but the chain logic should be minimal in `rpc/*`. Afterwards, with proper coverage on high-level methods we could tinker around if it makes sense.
-- INLINE src/rpc/methods/eth/events/logs.rs:18
```diff
+use crate::rpc::types::EventEntry;
+use crate::state_manager::{ExecutedTipset, StateManager};
+use fvm_ipld_encoding::IPLD_RAW;
+
+/// The Ethereum logs of one tipset, grouped by message, in tipset order.
+pub struct BlockLogs {
```
>>> ```suggestion
pub struct EthBlockLogs {
```

This is longer but arguably more descriptive.
-- INLINE src/rpc/methods/eth/events/logs.rs:30
```diff
+        state_manager: &StateManager,
+        tipset: &Tipset,
+        executed: &ExecutedTipset,
+    ) -> anyhow::Result<Self> {
+        Self::from_events(&TipsetEvents::collect(state_manager, tipset, executed))
+    }
```
>>> I think it's a bit awkward as an associated function - seems like there should be a method on `TipsetEvents` to convert to `BlockLogs` instead.
-- INLINE src/rpc/methods/eth/events/mod.rs:57
```diff
+    entries: Vec<EventEntry>,
+}
+
+impl TipsetEvents {
+    /// Collects the events of an executed tipset.
+    pub fn collect(
```
>>> ```suggestion
    pub fn from_tipset(
```
-- INLINE src/rpc/methods/eth/events/mod.rs:60
```diff
+impl TipsetEvents {
+    /// Collects the events of an executed tipset.
+    pub fn collect(
+        state_manager: &StateManager,
+        tipset: &Tipset,
+        executed: &ExecutedTipset,
```
>>> having both `tipset` and `executed to pass is a bit awkward; `ExecutedTipset` seems more like `TipsetExecutionResult`. I understand it's what we have at the moment, but it's not an ideal name.
-- INLINE src/rpc/methods/eth/events/mod.rs:143
```diff
+        resolved
+    }
+
+    /// Global cache first, then the finality-deep state (cached globally when reorg-stable),
+    /// then the post-execution state, the only one holding an actor created in this tipset.
+    fn resolve_uncached(&self, emitter: ActorID) -> Option<Address> {
```
>>> Don't we have this logic somewhere already?


==================== PR #7624 by Copilot [closed merged] docs: fix hardware requirements typo
https://github.com/ChainSafe/forest/pull/7624
review sequence: APPROVED
-- CONVERSATION
@sudo-shashank apparently you need to approve it, I'm somehow a co-author.


==================== PR #7625 by wjmelements [closed merged] fix: return eth hash from eth_sendRawTransaction
https://github.com/ChainSafe/forest/pull/7625
review sequence: CHANGES_REQUESTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW CHANGES_REQUESTED
Thanks for finding this. Real gap. Couple of things need addressing:

1. Test is not testing the fix.
2. (optional) refactor shared logic for deduplication and ease of testing the above
3. changelog entry (user-facing fix)
4. (optional here, if not we can address it on a follow-up) the gap comes from lack of parity checks via https://github.com/ChainSafe/forest/blob/main/src/tool/subcommands/api_cmd/api_compare_tests.rs
-- REVIEW COMMENTED
LGTM, as mentioned earlier, let's have a changelog entry for this fix. Thanks!
-- REVIEW APPROVED
LGTM, thanks!
-- INLINE src/rpc/methods/eth.rs:3238
```diff
+            ctx.chain_store()
+                .put_mapping(tx_hash, cid, chrono::Utc::now().timestamp() as u64)
+        {
+            tracing::error!("error inserting eth tx mapping: {e}");
+        }
+        Ok(tx_hash)
```
>>> Kind of unrelated to this PR, but those methods are almost exactly the same. Worth factoring out.
-- INLINE src/rpc/methods/eth.rs:4640
```diff
+        assert_eq!(
+            &format!("{tx_hash}"),
+            "0x9f2e70d5737c6b798eccea14895893fb48091ab3c59d0fe95508dc7efdae2e5f"
+        );
+
+        // What it returned before the fix.
```
>>> You fixed the `EthSendRawTransaction*::handle`; this test checks `get_signed_message`; not a regression test for `EthSendRawTransaction`. This will happily pass without the fix.

You might want to factor out 

```rust
       let tx_args = parse_eth_transaction(&raw_tx.0)?;
        // Eth tx hash != Filecoin message CID (see EthHash::to_cid's note).
        let tx_hash: EthHash = tx_args.eth_hash()?.into();
        let smsg = tx_args.get_signed_message(ctx.chain_config().eth_chain_id)?;
```
to a shared function and test against it (thus avoiding the dance with the context)


==================== PR #7628 by dependabot[bot] [closed merged] chore(deps): bump react from 19.2.8 to 19.3.0 in /docs in the react group across 1 directory
https://github.com/ChainSafe/forest/pull/7628
-- CONVERSATION
@dependabot recreate


==================== PR #7634 by sudo-shashank [closed merged] Remove redundant CBOR allocations
https://github.com/ChainSafe/forest/pull/7634
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
I think it's an improvement but you removed redundant CBOR allocations and not redundant CBOR encoding. Encoding cost, even without allocations, is still there. We can work on that in a follow-up, but the linked issue shouldn't be closed.
-- INLINE src/utils/cid/mod.rs:23
```diff
+        let mut hasher = multihash_codetable::Blake2b256::default();
+        fvm_ipld_encoding::to_writer(&mut hasher, obj)?;
+        let digest = MultihashCode::Blake2b256
+            .wrap(hasher.finalize())
+            .expect("BLAKE2b-256 digest is 32 bytes, within the multihash allocation");
+        Ok(Cid::new_v1(fvm_ipld_encoding::DAG_CBOR, digest))
```
>>> Is this faster than the previous implementation? Do you have a benchmark? I mean, it looks like it should be faster but I'd love some numbers.
-- INLINE src/utils/encoding/mod.rs:67
```diff
+}
+
+/// Byte length of the `DAG_CBOR` encoding of `value`, without allocating it.
+pub fn encoded_len<T: serde::Serialize>(value: &T) -> Result<usize, fvm_ipld_encoding::Error> {
+    let mut sink = CountingSink(0);
+    fvm_ipld_encoding::to_writer(&mut sink, value)?;
```
>>> > now constant-time

How is this constant time? It still has to go through the encoding process which depends on the object size; to my understanding you're just not allocating anything.
-- INLINE (reply) src/utils/cid/mod.rs:23
```diff
+        let mut hasher = multihash_codetable::Blake2b256::default();
+        fvm_ipld_encoding::to_writer(&mut hasher, obj)?;
+        let digest = MultihashCode::Blake2b256
+            .wrap(hasher.finalize())
+            .expect("BLAKE2b-256 digest is 32 bytes, within the multihash allocation");
+        Ok(Cid::new_v1(fvm_ipld_encoding::DAG_CBOR, digest))
```
>>> Ok, ping me once it's there. I don't get notifications on edits.
-- INLINE src/utils/encoding/mod.rs:58
```diff
+        Ok(())
+    }
+}
+
+/// Byte length of the `DAG_CBOR` encoding of `value`, without allocating it.
+pub fn encoded_len<T: serde::Serialize>(value: &T) -> Result<usize, fvm_ipld_encoding::Error> {
```
>>> `encoded_len` kind of suggests that's it's a no-op, as in, it was already calculated somewhere and you're just extracting the value.

I suggest a more prescriptive name, e.g., `calc_encoded_len`.
-- INLINE src/utils/encoding/mod.rs:62
```diff
+    }
+
+    fn flush(&mut self) -> std::io::Result<()> {
+        Ok(())
+    }
+}
```
>>> ```suggestion
/// `io::Write` that discards the bytes and only tracks how many were written.
#[derive(Default)]
struct CountingSink(usize);

impl std::io::Write for CountingSink {
    fn write(&mut self, buf: &[u8]) -> std::io::Result<usize> {
        self.0 += buf.len();
        Ok(buf.len())
    }

    fn write_vectored(&mut self, bufs: &[std::io::IoSlice<'_>]) -> std::io::Result<usize> {
        let n: usize = bufs.iter().map(|b| b.len()).sum();
        self.0 += n;
        Ok(n)
    }

    fn is_write_vectored(&self) -> bool {
        true
    }

    fn flush(&mut self) -> std::io::Result<()> {
        Ok(())
    }
}
```

If we're writing a sink, we might as well do it in full https://doc.rust-lang.org/src/std/io/util.rs.html#301-304
-- INLINE (reply) src/utils/encoding/mod.rs:62
```diff
+    }
+
+    fn flush(&mut self) -> std::io::Result<()> {
+        Ok(())
+    }
+}
```
>>> This _can_ potentially increase performance by avoiding some syscalls, if the input is scattered across different buffers


==================== PR #7635 by akaladarshi [closed merged] feat: nv29 support for devnet
https://github.com/ChainSafe/forest/pull/7635
review sequence: CHANGES_REQUESTED, COMMENTED, CHANGES_REQUESTED, APPROVED
-- REVIEW CHANGES_REQUESTED
Where can I see the migration succeeding? Also, seems you missed the  `devnet-forest-miner`.
-- INLINE scripts/devnet-forest-miner/.env:1
```diff

```
>>> @akaladarshi You need to update this as well.


==================== PR #7638 by akaladarshi [closed merged] chore: butterflynet reset
https://github.com/ChainSafe/forest/pull/7638
review sequence: DISMISSED, COMMENTED, COMMENTED, APPROVED, COMMENTED, CHANGES_REQUESTED, APPROVED
-- REVIEW CHANGES_REQUESTED
Upgraded epoch seems wrong
-- INLINE src/networks/butterflynet/mod.rs:111
```diff
         make_height!(Tock, -29),
         make_height!(GoldenWeek, -30),
-        make_height!(FireHorse, 1440, get_bundle_cid("v18.0.0")),
-        make_height!(Solstice, UPGRADE_HEIGHT_UNSCHEDULED),
+        make_height!(FireHorse, -31),
+        make_height!(Solstice, 90, get_bundle_cid("v19.0.0-dev1")),
```
>>> only 90 epochs after the reset? sounds short compared to previous runs but okay
-- INLINE src/rpc/methods/state/tests.rs:342
```diff
@@ -339,7 +339,7 @@ async fn creation_deposit_is_positive_from_goldenweek(#[case] config: ChainConfi
     assert!(creation_deposit(config, activation).await.is_positive());
 }
 
-/// Butterflynet is absent because its genesis is already nv27: it has no epoch to be inactive at.
+/// Butterflynet is absent because its genesis is already nv28: it has no epoch to be inactive at.
```
>>> ```suggestion
// Butterflynet is absent because its genesis is already past goldenweek: it has no epoch to be inactive at.
```

no point in maintaining this comment with every network upgrade
-- INLINE (reply) src/networks/butterflynet/mod.rs:111
```diff
         make_height!(Tock, -29),
         make_height!(GoldenWeek, -30),
-        make_height!(FireHorse, 1440, get_bundle_cid("v18.0.0")),
-        make_height!(Solstice, UPGRADE_HEIGHT_UNSCHEDULED),
+        make_height!(FireHorse, -31),
+        make_height!(Solstice, 90, get_bundle_cid("v19.0.0-dev1")),
```
>>> https://github.com/filecoin-project/solstice/pull/72


==================== PR #7644 by sudo-shashank [closed] Remove redundant cbor encoding
https://github.com/ChainSafe/forest/pull/7644
review sequence: COMMENTED, COMMENTED, COMMENTED, CHANGES_REQUESTED
-- REVIEW CHANGES_REQUESTED
I'm not sure this is the way to go. It introduces a bit of complexity and manual handling to avoid redundancy. IMO it should be done via memoization, reducing any mental overhead.

I noticed also a couple of more severe bugs (pre-existing) that should be handled separately.

Let's hold it off for a bit.
-- CONVERSATION
Superseded by https://github.com/ChainSafe/forest/pull/7692 - this required some refactoring along the way, but it does make using the messages worry-free.
-- INLINE src/chain_sync/tipset_syncer.rs:499
```diff
                 "Network version must be at least NV23 for legacy Ethereum transactions".to_owned(),
             ));
         }
-        check_msg(msg.message(), &mut account_sequences, &tree).map_err(|e| {
+        let (cid, encoded_len) =
+            cid_and_encoded_len(msg).expect("message serialization is infallible");
```
>>> I don't trust this. If it's infallible, then why does the function even returns a `Result`? Mind that messages are user-supplied so they can and will be malicious.
-- INLINE src/utils/cid/mod.rs:49
```diff
+        .expect("BLAKE2b-256 digest is 32 bytes, within the multihash allocation");
+    Ok((
+        Cid::new_v1(fvm_ipld_encoding::DAG_CBOR, digest),
+        writer.written,
+    ))
+}
```
>>> this seems to be almost an exact duplicate of the one you introduced earlier. Can we do some delegation instead?
-- INLINE src/chain_sync/tipset_syncer.rs:399
```diff
         let db = state_manager.db();
         for m in block.bls_msgs() {
             let pk = StateManager::get_bls_public_key(db, m.from, *base_tipset.parent_state())?;
             pub_keys.push(pk);
-            cids.push(m.cid().to_bytes());
+            bls_info.push(cid_and_encoded_len(m).expect("message serialization is infallible"));
```
>>> I don't feel comfortable with panicking on user-provided input.


==================== PR #7645 by akaladarshi [closed merged] chore: nv29 cleanup changes 
https://github.com/ChainSafe/forest/pull/7645
review sequence: COMMENTED, COMMENTED, APPROVED
-- INLINE src/rpc/methods/state.rs:1
```diff

```
>>> I don't see how these changes are NV29-related. Can you elaborate which comment are you addressing?
-- INLINE (reply) src/rpc/methods/state.rs:1
```diff

```
>>> If it's not related to NV29 it shouldn't live in `nv29 cleanup changes` PR, but as a separate one with its own explanation.


==================== PR #7647 by leshyatwork[bot] [closed merged] [automated] Update Forest RPC reference docs
https://github.com/ChainSafe/forest/pull/7647
review sequence: APPROVED


==================== PR #7654 by akaladarshi [closed merged] chore: nv29 latest bundles
https://github.com/ChainSafe/forest/pull/7654
review sequence: COMMENTED, APPROVED
-- REVIEW COMMENTED
LGTM, let's add a changelog for when the calibnet upgrade is scheduled.


==================== PR #7660 by akaladarshi [closed merged] chore(release): prepare release v0.37.0
https://github.com/ChainSafe/forest/pull/7660
review sequence: COMMENTED, DISMISSED, APPROVED
-- INLINE CHANGELOG.md:40
```diff
+
+### Fixed
+
+## Forest v0.37.0 "Sharad Sampat"
+
+Mandatory release for calibnet node operators. It includes support for the NV29 _Solstice_ network upgrade for calibnet, which is set to activate at epoch `4109133` (2026-09-28T12:59:30Z). It also includes important fixes around the ipfs gateway and proof parameters.
```
>>> ```suggestion
Mandatory release for calibnet node operators. It includes support for the NV29 _Solstice_ network upgrade for calibnet, which is set to activate at epoch `4109133` (2026-09-28T12:59:30Z). There are also additional (breaking) changes and improvements; see below.
```


==================== PR #7662 by dependabot[bot] [closed merged] chore(deps-dev): bump @types/node from 26.1.2 to 26.6.1 in /docs
https://github.com/ChainSafe/forest/pull/7662
review sequence: APPROVED


==================== PR #7663 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 4 updates
https://github.com/ChainSafe/forest/pull/7663
review sequence: APPROVED


==================== PR #7667 by akaladarshi [closed merged] chore: update the faster hex to 0.10.1
https://github.com/ChainSafe/forest/pull/7667
review sequence: APPROVED


==================== PR #7671 by akaladarshi [closed merged] docs: include discussion announcement for network upgrades in release checklist
https://github.com/ChainSafe/forest/pull/7671
review sequence: CHANGES_REQUESTED, DISMISSED, APPROVED
-- REVIEW CHANGES_REQUESTED
Network upgrade handling should be a separate document.
-- REVIEW DISMISSED
LGTM, some minor nits.
-- CONVERSATION
no green checkmark!
-- CONVERSATION
> Network upgrade handling should be a separate document.

@akaladarshi In case you missed.
-- INLINE docs/docs/developers/guides/network_upgrades.md:124
```diff
-- [Forest Discussions](https://github.com/ChainSafe/forest/discussions). See the [NV23 announcement](https://github.com/ChainSafe/forest/discussions/4488) for an example.
-- The `#fil-forest-announcements` channel in the Filecoin Slack.
+- [Forest Discussions](https://github.com/ChainSafe/forest/discussions/categories/announcements), in the Announcements category.
+- The `#fil-forest-announcements` channel in the Filecoin Slack, where the GitHub Slack app posts the announcements automatically.
+
+A network upgrade needs two Forest releases: one ahead of the calibnet upgrade and one ahead of the mainnet upgrade. Both are announced in a single discussion; see the [NV29 announcement](https://github.com/ChainSafe/forest/discussions/7670) for the latest example.
```
>>> ```suggestion
A network upgrade needs two Forest releases: one ahead of the calibnet upgrade and one ahead of the mainnet upgrade. Both are announced in a single discussion; see the [NV29 announcement](https://github.com/ChainSafe/forest/discussions/7670) for an example.
```
-- INLINE (reply) docs/docs/developers/guides/network_upgrades.md:124
```diff
-- [Forest Discussions](https://github.com/ChainSafe/forest/discussions). See the [NV23 announcement](https://github.com/ChainSafe/forest/discussions/4488) for an example.
-- The `#fil-forest-announcements` channel in the Filecoin Slack.
+- [Forest Discussions](https://github.com/ChainSafe/forest/discussions/categories/announcements), in the Announcements category.
+- The `#fil-forest-announcements` channel in the Filecoin Slack, where the GitHub Slack app posts the announcements automatically.
+
+A network upgrade needs two Forest releases: one ahead of the calibnet upgrade and one ahead of the mainnet upgrade. Both are announced in a single discussion; see the [NV29 announcement](https://github.com/ChainSafe/forest/discussions/7670) for the latest example.
```
>>> Let's phrase in a way that won't require amendments when NV31 is there. :)
-- INLINE docs/docs/developers/guides/network_upgrades.md:128
```diff
+
+A network upgrade needs two Forest releases: one ahead of the calibnet upgrade and one ahead of the mainnet upgrade. Both are announced in a single discussion; see the [NV29 announcement](https://github.com/ChainSafe/forest/discussions/7670) for the latest example.
+
+### Announcement discussion
+
+Create the discussion as soon as the upgrade schedule is known, before the calibnet release, titled `Forest NVXX support`, using the previous announcement as a template. It must contain:
```
>>> ```suggestion
Create the discussion as soon as the upgrade schedule is known, before the calibnet release, titled `Forest NVXX support; you can use the previous announcement as a template. It must contain:
```

As discussed, I don't see a need to enforce that as template given it was never supposed to be one. If someone later contrives something better, it's completely fine.
-- INLINE docs/docs/developers/guides/network_upgrades.md:130
```diff
+
+### Announcement discussion
+
+Create the discussion as soon as the upgrade schedule is known, before the calibnet release, titled `Forest NVXX support`, using the previous announcement as a template. It must contain:
+
+- A link to the upstream post with the upgrade scope and dates (the Core Devs planning discussion, or the final Filecoin community post it points to).
```
>>> Let' have a link, it's notoriously difficult for me to find which repo it's in.


==================== PR #7682 by akaladarshi [closed merged] chore: add solstice `calibnet`epoch in migration test
https://github.com/ChainSafe/forest/pull/7682
review sequence: APPROVED


==================== PR #7685 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/7685
review sequence: APPROVED


==================== PR #7686 by akaladarshi [closed merged] chore: update wasmtime to fix cargo advisory
https://github.com/ChainSafe/forest/pull/7686
review sequence: APPROVED


==================== PR #7691 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group in /docs with 3 updates
https://github.com/ChainSafe/forest/pull/7691
review sequence: APPROVED


==================== PR #7694 by dependabot[bot] [closed merged] chore(deps): bump the patch-versions group with 4 updates
https://github.com/ChainSafe/forest/pull/7694
review sequence: APPROVED


==================== PR #7696 by leshyatwork[bot] [closed merged] [automated] Add monthly RPC parity conformance report
https://github.com/ChainSafe/forest/pull/7696
review sequence: APPROVED


==================== PR #7709 by akaladarshi [closed merged] feat(api): state reward distribution v2 api
https://github.com/ChainSafe/forest/pull/7709
review sequence: COMMENTED, COMMENTED, COMMENTED, APPROVED
-- REVIEW COMMENTED
partial review
-- REVIEW COMMENTED
Looks correct, no additional blockers. Let's resolve the issues at hand, have a merge and make a release. Good job!
-- INLINE src/rpc/methods/state/tests.rs:667
```diff
+    assert_eq!(explicit.rounding_adjustment, TokenAmount::from_atto(-1));
+}
+
+/// Lotus refuses tipsets executed by an earlier reward actor with this text, before executing
+/// anything: <https://github.com/filecoin-project/lotus/blob/v1.37.0-rc2/node/impl/full/state_v2.go#L61-L63>
+const REWARD_DISTRIBUTION_REQUIRES_NV29: &str =
```
>>> If possible, I'd avoid asserting against error messages. Proper error types are for that.
-- INLINE src/rpc/methods/state/tests.rs:621
```diff
+    }
+  ]
+}"#;
+
+#[test]
+fn reward_distribution_json_is_byte_compatible_with_lotus() {
```
>>> this belongs as an insta snapshot test for ease of updating
-- INLINE src/shim/actors/builtin/reward/mod.rs:563
```diff
+
+    /// Returns the v19 state, the first actors version with reward streams.
+    ///
+    /// # Errors
+    /// Fails before actors v19.
+    pub fn as_v19(&self) -> anyhow::Result<&fil_actor_reward_state::v19::State> {
```
>>> it's not a great name - once we update to v20 we'd still use `as_v19` or have to update all occurrences to `as_v20`? in general, shims are supposed to completely mask a notion of versions.
-- INLINE src/state_manager/mod.rs:209
```diff
@@ -201,6 +203,8 @@ pub struct StateManager {
     trace_cache: ForestCache<TipsetKey, (CidWrapper, Vec<Arc<ApiInvocResult>>)>,
     /// This is a cache which indexes tipsets to their per-transaction Ethereum traces.
     eth_trace_cache: ForestCache<TipsetKey, Arc<Vec<EthTxTraces>>>,
+    /// This is a cache which indexes tipsets to their reward distributions.
+    reward_distribution_cache: ForestCache<TipsetKey, Arc<RewardDistribution>>,
```
>>> what's the expected maximum size of this cache?
-- INLINE src/state_manager/utils.rs:429
```diff
@@ -426,6 +426,145 @@ pub mod state_compute {
             assert_eq!(sm.trace_cache.misses(), misses);
         }
 
+        /// The first tipset executed by the v19 reward actor. Its four blocks are awarded under
```
>>> nit: follow-up - worth moving those (and other) tests to a dedicated file. It seems off to find tests for a specific chain and a specific epoch in utils.
-- INLINE src/state_manager/reward_distribution.rs:1108
```diff
+        DENOM / 100 * n
+    }
+
+    fn atto(amount: i64) -> TokenAmount {
+        TokenAmount::from_atto(amount)
+    }
```
>>> Is it really useful? If you're tried of `TokemAmount::from_atto` you just import it. Let's not write more code than we have to.
-- INLINE src/state_manager/reward_distribution.rs:1113
```diff
+    }
+
+    fn int(amount: &TokenAmount) -> i64 {
+        amount.atto().try_into().unwrap()
+    }
+
```
>>> maybe just add a `TokenAmount::expect_i64` under `#[cfg(test)]`
-- INLINE src/state_manager/reward_distribution.rs:1115
```diff
+    fn int(amount: &TokenAmount) -> i64 {
+        amount.atto().try_into().unwrap()
+    }
+
+    /// A weight record that gives `weight` at every epoch.
+    fn constant(weight: u64) -> WeightRecord {
```
>>> ```suggestion
    fn constant_weight_record(weight: u64) -> WeightRecord {
```

let's make it more readable, `constant(2)` doesn't tell me me anything
-- INLINE src/state_manager/reward_distribution.rs:293
```diff
+    }
+    (miner_paid, burn_paid)
+}
+
+#[cfg(test)]
+pub(in crate::state_manager) mod tests {
```
>>> let's move it to dedicated file
-- INLINE src/state_manager/reward_distribution.rs:800
```diff
+            total_explicit_minted: 447, // + 447
+            total_burn_minted: 54,      // + 54
+            accrued_pools: vec![
+                pool_of(SERVICE_STREAM, 290),        // + 290
+                pool_of(SECOND_SERVICE_STREAM, 157), // + 157
+            ],
```
>>> you repeated the numbers 3 times, once is enough, at most 2...
```suggestion
        // The award mints 1001: 500 for the miner, 290 and 157 into the pools, 54 burned.
        let after = Snapshot {
            total_minted_reward: 2_001, // + 1001
            total_explicit_minted: 447,
            total_burn_minted: 54,
            accrued_pools: vec![
                pool_of(SERVICE_STREAM, 290),
                pool_of(SECOND_SERVICE_STREAM, 157),
            ],
```
-- INLINE src/state_manager/mod.rs:73
```diff
 const DEFAULT_ID_TO_DETERMINISTIC_ADDRESS_CACHE_SIZE: NonZeroUsize = nonzero!(8192usize); // maximum ~0.7MiB on mainnet
 const DEFAULT_TRACE_CACHE_SIZE: NonZeroUsize = nonzero!(16usize); // 1.4 to 7.7 MiB per entry measured, so ~20 to 120 MiB
 const DEFAULT_ETH_TRACE_CACHE_SIZE: NonZeroUsize = nonzero!(64usize); // 0.03 to 0.12 MiB per entry measured, so ~2 to 8 MiB
+// 5 to 6.3 KiB per entry measured (4 to 5 blocks, 2 streams, 1 recipient), ~66 KiB per block at the
+// reward actor's limits (8 streams of 64 recipients), so ~1.5 MiB, ~83 MiB with 5 blocks per tipset
+const DEFAULT_REWARD_DISTRIBUTION_CACHE_SIZE: NonZeroUsize = nonzero!(256usize);
```
>>> IMO 83 MiB is quite a bit, I don't think it makes sense to do a lot of caching there as it's not and will likely not be a hot method. Let's limit it to like 8 to survive a burst but that's about it.


==================== PR #7712 by fmterrors [closed merged] docs: fix some typos in comment
https://github.com/ChainSafe/forest/pull/7712
review sequence: APPROVED
-- REVIEW APPROVED
Thanks!


==================== PR #7721 by akaladarshi [closed merged] chore: nv29 mainnet support
https://github.com/ChainSafe/forest/pull/7721
review sequence: COMMENTED, COMMENTED, DISMISSED, APPROVED
-- REVIEW DISMISSED
LGTM, please make fake run as a sanity check.
-- INLINE src/networks/mod.rs:52
```diff
@@ -48,9 +48,6 @@ pub mod metrics;
 /// Newest network version for all networks
 pub const NEWEST_NETWORK_VERSION: NetworkVersion = NetworkVersion::V29;
 
-/// Placeholder epoch for a network upgrade that has no scheduled height yet.
-pub const UPGRADE_HEIGHT_UNSCHEDULED: ChainEpoch = 999_999_999_999_999;
```
>>> I think we can leave it and mark as `maybe_unused` - we'd be constantly resurrecting it otherwise.
-- INLINE CHANGELOG.md:30
```diff
@@ -27,12 +27,14 @@
 
 ### Breaking
 
-- [#XXXX](https://github.com/ChainSafe/forest/pull/XXXX): JSON-RPC batch requests are now limited to 100 entries; larger batches are rejected with error `-32010`. The limit can be raised with `FOREST_RPC_MAX_BATCH_LEN`.
+- [#7697](https://github.com/ChainSafe/forest/pull/7697): JSON-RPC batch requests are now limited to 100 entries; larger batches are rejected with error `-32010`. The limit can be raised with `FOREST_RPC_MAX_BATCH_LEN`.
```
>>> ooops! thanks for catching this


==================== PR #7722 by sudo-shashank [closed merged] Allow overflow checks in CI on release builds
https://github.com/ChainSafe/forest/pull/7722
-- CONVERSATION
> This comment is actually for @LesnyRumcajs
> 
> The checks listed in #7560 all build with `quick`, which already inherits release (and inherited overflow-checks until #7559 dropped it), so adding the flag to quick restores CI detection for free, whereas release-checked takes the `forest.yml` build step from `~7` to `~25` min on every PR. Was an opt-level-3 build in CI the goal, or am I reading too much into "inherits from release"?

Yes, adding `overflow-checks = true` is a better and more robust way given we don't ship `quick` binaries anywhere.

Steps in issues are usually just a quick suggestion (and even then, what I meant but was too imprecise was to inherit from `quick`, so have a `quick-checked`, but there's no need for two profiles).


==================== PR #7724 by dependabot[bot] [closed merged] chore(deps-dev): bump @types/node from 26.6.2 to 26.6.3 in /docs in the patch-versions group across 1 directory
https://github.com/ChainSafe/forest/pull/7724
-- CONVERSATION
to be re-run in two days, https://github.com/ChainSafe/forest/pull/7728 will fix such mismatches


==================== PR #7725 by dependabot[bot] [closed merged] chore(deps): bump jdx/mise-action from 4 to 5
https://github.com/ChainSafe/forest/pull/7725
review sequence: APPROVED


==================== PR #7727 by leshyatwork[bot] [closed merged] [automated] Update Forest CLI docs
https://github.com/ChainSafe/forest/pull/7727
review sequence: APPROVED


==================== PR #7730 by akaladarshi [closed merged] chore: prepare release `v0.38.0`
https://github.com/ChainSafe/forest/pull/7730
review sequence: APPROVED


==================== PR #7735 by leshyatwork[bot] [closed merged] [automated] Update Forest RPC reference docs
https://github.com/ChainSafe/forest/pull/7735
review sequence: APPROVED


==================== PR #7759 by sudo-shashank [open] Devnet additional check for miner integration
https://github.com/ChainSafe/forest/pull/7759
review sequence: COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED, COMMENTED
-- INLINE scripts/devnet-forest-miner/README.md:27
```diff
+- `create_miner` — `lotus-miner init` through Forest; Forest and Lotus must agree on creation
+  deposit, VM circulating supply, and the new miner's info.
+- `pledge_sector` — real 2 KiB CC seal to `Proving`. The chain verifies the seal proof, so wrong
+  Forest randomness fails prove-commit. Sector info must then match on Forest and Lotus.
+
+Skips `../devnet` (Lotus Miner would talk to Lotus, not Forest).
```
>>> Let's not overbloat this file with all the things that are happening.
-- INLINE scripts/devnet/test_harness.sh:3
```diff
@@ -1,6 +1,6 @@
 #!/bin/bash
 # Sourced (not executed) helpers for the devnet test suites (wallet, mpool,
-# eth-gas, eth-skip-sender) on the docker devnet. Run after the devnet is up (`setup.sh`) and synced (`check.sh`).
+# eth-gas, eth-skip-sender, miner-onboarding) on the docker devnet. Run after the devnet is up (`setup.sh`) and synced (`check.sh`).
```
>>> Let's get rid of this line instead of adding every single tests we create there.
-- INLINE src/state_manager/state_computation.rs:565
```diff
         let parent_epoch = self
             .chain_index
             .load_required_tipset(self.tipset.parents())?
             .epoch();
         let epoch = self.tipset.epoch();
+        let block_delay_secs = u64::from(self.chain_config.block_delay_secs);
```
>>> nit: it'd be shorter with `as u64` and `u32` must fit.
-- INLINE src/dev/subcommands/devnet_cmd/miner_onboarding.rs:65
```diff
+        r#"{{index .Config.Labels "com.docker.compose.project"}}"#,
+        "lotus-miner",
+    ])
+    .context("could not inspect the `lotus-miner` container; is the devnet up?")?;
+    Ok(project == FOREST_MINER_PROJECT)
+}
```
>>> This is pretty weird coupling with Docker. I don't trust the system that can skip things on its own. The miner tests should simplfy not be invoked on the lotus devnet.
-- INLINE src/dev/subcommands/devnet_cmd/miner_onboarding.rs:273
```diff
+            .lines()
+            .find_map(|l| l.trim().strip_prefix("Status:"))
+            .map(str::trim)
+            .with_context(|| format!("no `Status:` in sector status output:\n{status}"))?;
+        eprintln!("sector {sector} state: {state}");
+        if state.contains("Fail") {
```
>>> My CC complains that this is might be flaky (it makes sense). Please investigate.

```
2. Wrong check on sector state (miner_onboarding.rs:273). state.contains("Fail") fails the test on states like PreCommitFailed, CommitFailed and FinalizeFailed, which Lotus retries on its own. So one brief RPC hiccup makes the test flaky. In the other direction, Removed, Terminating and Faulty don't contain "Fail", so the test waits out the full 600s timeout before reporting.
```

I also don't love this string matching approach, I wonder if there is some more formalized output (JSON) we could use and not play with human-readable dump.
-- INLINE scripts/devnet/run_integration_tests.sh:26
```diff
@@ -23,4 +23,5 @@ forest-dev tests wallet
 forest-dev devnet eth-gas
 forest-dev devnet eth-skip-sender
 forest-dev devnet state-call
+forest-dev devnet miner-onboarding
```
>>> As discussed, this might take A LONG time to execute (along with other miner tests). This should be really behind a flag in the CI that would run on
- dedicated label, `miner-tests` or something like that
- our general `Release` label
- nightly

Worth doing it as a separate PR (before merging this one)
-- INLINE src/networks/devnet/mod.rs:34
```diff
@@ -31,40 +31,40 @@ pub static GENESIS_NETWORK_VERSION: LazyLock<NetworkVersion> = LazyLock::new(||
 
 /// Height epochs.
 /// Environment variable names follow
-/// <https://github.com/filecoin-project/lotus/blob/8f73f157933435f5020d7b8f23bee9e4ab71cb1c/build/params_2k.go#L108>
+/// <https://github.com/filecoin-project/lotus/blob/v1.38.0/build/buildconstants/params_2k.go>.
```
>>> There are more mismatches, no?


==================== PR #7776 by leshyatwork[bot] [closed merged] [automated] Update Lotus version in API tests
https://github.com/ChainSafe/forest/pull/7776
review sequence: APPROVED


==================== PR #7777 by akaladarshi [closed merged] chore: cargo deny advisory issues
https://github.com/ChainSafe/forest/pull/7777
review sequence: APPROVED
-- CONVERSATION
@akaladarshi please add in the description what you updated and what exactly failed.


==================== PR #7782 by akaladarshi [closed merged] chore: prepare for release v0.39.0
https://github.com/ChainSafe/forest/pull/7782
review sequence: APPROVED