# Studionet deployment and live evidence

The active Provenance Engine deployment runs on Studionet (chain ID `61999`) at [0xA77018a83C4d353eF31E07B59A2Ff50153e1264a](https://explorer-studio.genlayer.com/address/0xA77018a83C4d353eF31E07B59A2Ff50153e1264a). Its deployment transaction [0x6e2689f9e87aa273366766dbc255bf9046623143fb7657b7d88e8fe4a7786f07](https://explorer-studio.genlayer.com/tx/0x6e2689f9e87aa273366766dbc255bf9046623143fb7657b7d88e8fe4a7786f07) finalized with `MAJORITY_AGREE`. The deployed source is `contracts/provenance_engine.py`, SHA-256 `81B81B6E5CB57875236719D1A762791D94FA5C30C8ED0E24B92CC51C27AFBCF6`.

## Live claim and evidence lifecycle

The finalized transaction chain registered claim `live-plan-v2-20260926` (transaction [0x97039aa56f4213b162312df1395339847b6ffaa69c972ab4cc137f9d8ed60275](https://explorer-studio.genlayer.com/tx/0x97039aa56f4213b162312df1395339847b6ffaa69c972ab4cc137f9d8ed60275)), appended evidence `live-evidence-v2-20260926` for `https://example.com/` with relationship `SUPPORTS` (transaction [0x3b7ad51d0647f10b46dfdf7bb4c3f1ca6086e972b7ee22681b0bca26578e38f4](https://explorer-studio.genlayer.com/tx/0x3b7ad51d0647f10b46dfdf7bb4c3f1ca6086e972b7ee22681b0bca26578e38f4)), then ran consensus verification (transaction [0x36942f77e2737927002f2cbecb02f78ba30566f16caca137f8a2c2b8fa473445](https://explorer-studio.genlayer.com/tx/0x36942f77e2737927002f2cbecb02f78ba30566f16caca137f8a2c2b8fa473445)). All three transactions finalized. Verification reached `MAJORITY_AGREE` with three validator votes agreeing and two disagreeing. The consensus output was `decision=SUPPORTS`, `visual=INSUFFICIENT`; no visual artifact was supplied. Live views returned claim status `CONFIRMED`, evidence verification `VERIFIED`, freshness `FRESH`, evidence count `1`, version `3`, and a provenance passport containing the same state.

## Live bounty payout and replay protection

A 1 GEN bounty for the confirmed claim was deposited in transaction [0x69c86ae417940079084cf1ab8374f0024508b678778ff252f1c17a08f182aafb](https://explorer-studio.genlayer.com/tx/0x69c86ae417940079084cf1ab8374f0024508b678778ff252f1c17a08f182aafb). Settlement finalized in transaction [0xc6e6e1c43eb0088c2706979844c0d3413c10a5b3faed44893f356be7eb1b5307](https://explorer-studio.genlayer.com/tx/0xc6e6e1c43eb0088c2706979844c0d3413c10a5b3faed44893f356be7eb1b5307), which triggered a finalized 1 GEN transfer to the caller at [0x2bc801cb1848c6cdc52d0867e27cf2c5aa75d3191982c7310050e060197fb470](https://explorer-studio.genlayer.com/tx/0x2bc801cb1848c6cdc52d0867e27cf2c5aa75d3191982c7310050e060197fb470). A second settlement attempt finalized as a rollback with `bounty already settled` (transaction [0x1903ae77b28568d4f46716a191d2ab0e1dec4462fa51b02675d7067786fe99a0](https://explorer-studio.genlayer.com/tx/0x1903ae77b28568d4f46716a191d2ab0e1dec4462fa51b02675d7067786fe99a0)); no second payout was created.

## Live contradiction and refund

A separate claim, `live-refund-v2-20260926`, stated that Example Domain advertises cars. Its evidence record pointed to `https://example.com/` and declared `CONTRADICTS`. Claim creation [0xaf90e3597ea4a3b8546f816c732b6aa5c63fb306b0cb73de67844a0c9eed3aa3](https://explorer-studio.genlayer.com/tx/0xaf90e3597ea4a3b8546f816c732b6aa5c63fb306b0cb73de67844a0c9eed3aa3) and evidence submission [0xef806c19fc6daa55847705c0938767ac9fcb3af2cb4043cb746d51769c0dfb98](https://explorer-studio.genlayer.com/tx/0xef806c19fc6daa55847705c0938767ac9fcb3af2cb4043cb746d51769c0dfb98) finalized. Consensus verification [0x7784b1b234ee6d65f06374f916b82b049854ff109fd7aff58ae1f367a6e6f663](https://explorer-studio.genlayer.com/tx/0x7784b1b234ee6d65f06374f916b82b049854ff109fd7aff58ae1f367a6e6f663) finalized after two rounds, with three agreeing and two disagreeing votes in the final round. Live state reads returned claim status `CONTRADICTED` and evidence verification `CONTRADICTED`.

A separate 1 GEN bounty was deposited in [0xcacf92cb7261bf8a68b52dbe83e54a972fe6c60171e77e6e8f6d03a9bdbef450](https://explorer-studio.genlayer.com/tx/0xcacf92cb7261bf8a68b52dbe83e54a972fe6c60171e77e6e8f6d03a9bdbef450). Refund settlement finalized in [0x5d873621702dc25c017cf0aba32416c04af6f6fa0186ab355486d29f8ebf8bd6](https://explorer-studio.genlayer.com/tx/0x5d873621702dc25c017cf0aba32416c04af6f6fa0186ab355486d29f8ebf8bd6), triggering a finalized 1 GEN transfer back to the sponsor at [0xf92b7080c46412d7b3496d9200be8f44cc61f0e21cbc23dbf2105d555b4daae1](https://explorer-studio.genlayer.com/tx/0xf92b7080c46412d7b3496d9200be8f44cc61f0e21cbc23dbf2105d555b4daae1). Claim history records `BOUNTY_REFUNDED`.

## Local verification

The current source passed:

- `python -m pytest -q` — 10 passed.
- `genvm-lint check contracts/provenance_engine.py` — 3 lint checks passed; validation passed.
- `genvm-lint schema contracts/provenance_engine.py` — 12 methods found (6 views, 6 writes).
- `gltest tests -v --network localnet` — 10 passed, including prompt injection, malformed validator output, challenge history, escrow structure, and duplicate identity checks.

## Superseded deployment

The earlier address `0xF28617DeFdB0E3683440c8Fa7a82c54E52DA2536` at transaction `0x3012c3008010c5eb42e8fb36ef90e9048fe60519fefde1f1192f6a5138a7fdbf` used source SHA-256 `A75B16DA0ED3449D1036D36D5E6B4A9581B50BC7B49787B066E41B476FEF1C8E`. A live verification attempt exposed that the deployed version could not parse fenced JSON returned by consensus. The parser was corrected and redeployed; use the active address above.
