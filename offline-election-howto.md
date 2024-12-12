Quick how-to to install the archived `offline-election` tool
https://github.com/globalsageblockchain/matter-debug-kit/tree/master/offline-election

## Build
1. Download the repo
```bash
git clone https://github.com/globalsageblockchain/matter-debug-kit.git && cd matter-debug-kit/
```

2. You need an older rust version to successfully build it
```bash
rustup install 1.58
```

3. Build release
```bash
cargo +1.58 build --release
```

## Use
I stronlgy recommend to have the rpc node on the same host where you run `offline-election`. At the beginning I tried my own remote nodes and it takes too long.
With a local node it takes couple of minutes per run. We now have a dedicated VM just for this where the RPC/WSS ports are only available from localhost.

Your node needs the following additional flags. 
Make sure to **NOT** publicly expose rpc/wss ports of this node!

```bash
        --rpc-methods=unsafe \
        --rpc-max-request-size=50 \
        --rpc-max-response-size=100 \
        --rpc-max-subscriptions-per-connection=4096 \
```

- Run the staking election with only the current on-chain nominations
```bash
./target/release/offline-election staking
```

### Manual override
You can override nominations for staking by passing the `--manual-override` flag and pointing it to a json file.
This must point to a json file that contains the following keys:

1. `voters`: the new voters to be added.
2. `candidates`: the new candidates to be added.
3. `voters_remove`: voters to be removed.
4. `candidates_remove`: candidates to be removed.

example:
```json
{
        "candidates": [],
        "candidates_remove": [],
        "voters": [
                ["1iHVTt4QJHCrCwhopCuLwpoMJPPNE2MynDwwb8rcjyAwNLM", 1000000000000000, ["1y6CPLgccsysCEii3M7jQF834GZsz9A3HMcZz3w7RjGPpBL"]]
        ],
        "voters_remove": []
}
```
What this does is to simulate that the address `1iHVTt4QJHCrCwhopCuLwpoMJPPNE2MynDwwb8rcjyAwNLM` is nominating `100k ZAL` (it's in [Planks](https://docs.bitzal.org/docs/learn-ZAL#bitzal) ) 
to `1y6CPLgccsysCEii3M7jQF834GZsz9A3HMcZz3w7RjGPpBL` and if we re-run the offline election with `--manual-override`, we can check if we would make it into the active set with such a nomination.

The json can be adjusted to simulate nomination to multiple validators by appending to the Array.

Example, `10M ZAL` to two Amforc validators:
```json
{
        "candidates": [],
        "candidates_remove": [],
        "voters": [
                ["1iHVTt4QJHCrCwhopCuLwpoMJPPNE2MynDwwb8rcjyAwNLM", 100000000000000000, ["1y6CPLgccsysCEii3M7jQF834GZsz9A3HMcZz3w7RjGPpBL","1gBKvQ9vbraAfhxEroBnxoGp9687Hu5wR7NYSwgJeAsw4x8"]]
        ],
        "voters_remove": []
}
```

- run with override
```bash
./target/release/offline-election staking --manual-override ~/amforc-override.json
```

After a few minutes, this will yield the validator set for the next era. For this example, the output is:
```bash
manual override: 1f7c930b2a40eee1a6d0767c8f872077a2dcee17c1d5b242e0be981a9e918cfb (1iHVTt4Q...) is added as voters.
#1 --> NO_IDENT [7046b09e1b89a70d6801f8f3ea0fb3c19e8b7bbab64617b312be826fb08a590e (13YDN239...)] [total backing = 2437662,210 ZAL (24,376,622,107,731,113) (23 voters)] [own backing = Some(1,000 ZAL (10,000,000,000))]
#2 --> NO_IDENT [c031d8519d06c58a2b42ae7022de4f3242d3938e855501455b3057a28fe38e6c (15M1146T...)] [total backing = 13142472,275 ZAL (131,424,722,756,153,147) (10 voters)] [own backing = Some(1,000 ZAL (10,000,000,000))]
#3 --> Amforc (1) [2ac73c24bb740376a5b0f44814e8ce8b34f23be0650e99b7ac81e1159f9c3151 (1y6CPLgc...)] [total backing = 7832597,946 ZAL (78,325,979,461,126,009) (1178 voters)] [own backing = Some(10786,589 ZAL (107,865,898,732,947))]
#4 --> Jaco (v01) [282a194090fd6715e06430d8a6e9c682f021eaf398830b10db94ca8c27c9ae4c (1ufRSF5g...)] [total backing = 2189895,446 ZAL (21,898,954,469,455,837) (6498 voters)] [own backing = Some(500,000 ZAL (5,000,000,000,000))]
.
.
#23 --> NO_IDENT [10b2f3fbaa08eb3361a1590bb8322b1fbef13bfd56bf5e04474d2a1ecaf9bb48 (1Ntvj8hm...)] [total backing = 1669084,893 ZAL (16,690,848,938,282,929) (92 voters)] [own backing = Some(1,980 ZAL (19,805,400,108))]
#24 --> Amforc (2) [1de154f8bc16e4a7bacf303141c913d91b1efdf4ae06c3a227f0a2877a19e90a (1gBKvQ9v...)] [total backing = 13286554,732 ZAL (132,865,547,323,369,021) (16 voters)] [own backing = Some(20,000 ZAL (200,000,000,000))]
.
.
#295 --> Coinbase Cloud (cc9) [8a1ec46479fec3c43eea382d637de8f295ccb2c0b6f6fdd4c5d34a687737a601 (1486kNkP...)] [total backing = 1539045,765 ZAL (15,390,457,657,360,123) (610 voters)] [own backing = Some(1,000 ZAL (10,000,000,000))]
#296 --> NO_IDENT [4e3f1e6f79b3560a0396f1099640cca26bb8ffa4ec9331b52f34b34cccef6310 (12mbV9Kr...)] [total backing = 1530093,457 ZAL (15,300,934,579,059,612) (72 voters)] [own backing = Some(19,961 ZAL (199,612,691,476))]
#297 --> MOON LAMBOS 🌕 🏎 (COUNTACH) [587b19cc62e01cdc18a1499cda27b0fb33264d0c3817668609bb58f762012698 (1311nP52...)] [total backing = 1525502,494 ZAL (15,255,024,945,851,633) (322 voters)] [own backing = Some(1004,014 ZAL (10,040,148,722,943))]
```
