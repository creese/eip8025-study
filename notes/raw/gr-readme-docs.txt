# EIP-8025 Phase 3a harvest artifact
# Artifact: gr-readme-docs.txt
# Cluster: n/a
# Repo: https://github.com/grandinetech/grandine @ 6ae713fca1fe6620ef7e45b864c3e136a767b1c1
# Date (UTC): 2026-07-16T18:41:06Z

## Command 1
$ ls
exit code: 0
stderr: empty
--- output ---
COPYRIGHT
Cargo.lock
Cargo.toml
Cross.toml
Dockerfile
Dockerfile.cross
LICENSE.md
Makefile
README.md
ad_hoc_bench
allocator
arithmetic
attestation_verifier
audit
benches
binary_utils
bindings
block_producer
bls
book
builder_api
clippy.toml
clock
data_dumper
database
debug_info
dedicated_executor
deposit_tree
directories
doppelganger_protection
eip_2335
eip_7594
eth1
eth1_api
eth2_cache_utils
eth2_libp2p
execution_engine
factory
features
fork_choice_control
fork_choice_store
funding.json
genesis
grandine
grandine-snapshot-tests
grandine_version
hashing
helper_functions
hive
http_api
http_api_utils
interop
keymanager
kzg_utils
liveness_tracker
logging
metrics
operation_pools
p2p
panics
predefined_chains
prometheus_metrics
pubkey_cache
runtime
rust-toolchain.toml
rustfmt.toml
scripts
serde_utils
shuffling
signer
slasher
slashing-protection-interchange-tests
slashing_protection
snapshot_test_utils
spec_test_utils
ssz
ssz_derive
state_cache
std_ext
transition_functions
try_from_iterator
types
validator
validator_key_cache
validator_statistics
zkvm
--- end output ---

## Command 2
$ cat README.md
exit code: 0
stderr: empty
--- output ---
# Grandine: A fast and lightweight Ethereum consensus client

## Documentation

The documentation is available [here](https://docs.grandine.io/). Feel free to reach us if you would like more details in some documentation chapters or have any questions.

## Performance

Grandine is a optimised and parallelised client. There aren't many published performance comparisons, but a previous [research](https://arxiv.org/abs/2311.05252) by MigaLabs may give some insight. We run 50,000 Holesky validators on one of our developer's machine.

## Memory Usage

Grandine is a lightweight client and needs only ~2.5GB of memory on the mainnet. In order to measure the amount of memory that Grandine actually needs one should stress the operating system to release the shared memory. `stress-ng` is an easy way to do it:

```
stress-ng --vm-bytes $(awk '/MemAvailable/{printf "%d\n", $2 * 0.9;}' < /proc/meminfo)k --vm-keep -m 1
```
## Build

Rust is needed in order to build Grandine. We recommend to use [rustup](https://rustup.rs/).

Some system dependencies are needed, the command below should install it on Ubuntu:

```
apt-get install ca-certificates libssl-dev clang cmake unzip protobuf-compiler libprotobuf-dev libz-dev
```

Then the build may take a few minutes:

```shell
git clone https://github.com/grandinetech/grandine
cd grandine
git submodule update --init dedicated_executor eth2_libp2p
make release
```

The compiled binary is available at `./target/compact/grandine`.

For faster building (larger binary size) use `--release` instead of `--profile compact`.

### Docker builds

Docker image can build with a simple command:

```shell
docker build .
```

### Cross-compilation

[Cross](https://github.com/cross-rs/cross) can be used for Grandine cross-compilation.

Cross-compilation command for `amd64` architecture:

```shell
make release TARGET=x86_64-unknown-linux-gnu
```

Cross-compilation command for `arm64` architecture:

```shell
make release TARGET=aarch64-unknown-linux-gnu
```

### Docker Cross builds

Cross-compiled binaries can be used for Docker images.

Docker build command for `amd64` architecture:

```shell
docker buildx build \
    --file Dockerfile.cross \
    --platform linux/amd64 \
    target/x86_64-unknown-linux-gnu/compact/
```

Docker build command for `arm64` architecture:

```shell
docker buildx build \
    --file Dockerfile.cross \
    --platform linux/arm64 \
    target/aarch64-unknown-linux-gnu/compact/
```

## Team

Grandine is built by [Grandine core team](https://grandine.io/) led by [Saulius Grigaitis](https://twitter.com/sauliuseth).
We also involve academia in the early stages of new ideas, however, the optimized production code is delivered by the core team.

## Audits

Grandine is used in production and the [audits](/audit) have been completed by [Matter Labs](https://matter-labs.io/) and [X41 D-Sec](https://x41-dsec.de/). The client was also tested by [Antithesis](https://antithesis.com/) team. No serious issues were found. However, always secure your keys with the approach you trust ([Web3Signer](https://docs.web3signer.consensys.io), [Vouch](https://github.com/attestantio/vouch), etc.). Use it at your risk.

## Contact

It's best to reach us via [Grandine Discord](https://discord.gg/H9XCdUSyZd) or [Grandine Telegram](https://t.me/+yMHjrJanClozYzQ0). Feel free to join!

## Donate

Funding public goods is hard. We appreciate your donations via Ethereum address [0x93b10cc89A3D3b5bb6bBB04EfFe104873EF002A9](https://etherscan.io/address/0x93b10cc89A3D3b5bb6bBB04EfFe104873EF002A9).

## Credits

Grandine focuses on original consensus core implementations, however it uses [a lot of crates](Cargo.lock) developed by the community. For example, Grandine uses `rust-libp2p` based networking libraries developed by the Lighthouse team since the beginning. Lighthouse's `eth2_libp2p` library was generic back then and we still use a fork of it now. We also used `libmdbx-rs` bindings library by Akula maintainer and now we use a fork of it maintained by Reth team. So we focus on the original consensus core because it's the unique value Grandine offers for the community, but we also love to use some great crates developed by other client teams and the community. Grandine would not be where it is now without the efforts of the other client teams and the community! Huge thanks to everyone!
--- end output ---

## Command 3
$ find . -maxdepth 1 -type d -iname 'doc*' | sort
exit code: 0
stderr: empty
--- output ---
--- end output ---
stdout: empty (0 bytes)

# END OF ARTIFACT
