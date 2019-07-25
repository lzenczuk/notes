# Test network setup
[libra quick start](https://developers.libra.org/) - description provided in documentation section doesn't contain testnet chackout
- `git clone https://github.com/libra/libra.git && cd libra`
- `git checkout testnet`
- `./scripts/dev_setup.sh`
- `./scripts/cli/start_cli_testnet.sh`

## Cli client run
Last script (`./scripts/cli/start_cli_testnet.sh`) runs client using cargo. Client points to main testnet and use `trusted_peers.config.toml` to configure trusted node.
```
cargo run -p client --release --host ac.testnet.libra.org --port 8000 -s $SCRIPT_PATH/trusted_peers.config.toml
```
## Local node
Test network have some limitations. To have full functionalities it is required to run local node.
#### Help
```
cargo run -p libra_swarm -- -h
```
#### Run one node
```
cargo run -p libra_swarm
```
#### Run one node and client (cli)
```
cargo run -p libra_swarm -- -s
```
