# Interacting with eosio endpoints

## General
EOS dublin api endpoint [api1.eosdublin.io](api1.eosdublin.io)

## Accounts related
### Get info about account with known account name
`curl --request POST   --url http://api1.eosdublin.io/v1/chain/get_account   --data '{"account_name":"gezdmmrrgage"}'`
```json
{"account_name":"gezdmmrrgage","head_block_num":2916594,"head_block_time":"2018-06-27T13:20:16.000","privileged":false,"last_code_update":"1970-01-01T00:00:00.000","created":"2018-06-09T13:08:34.000","ram_quota":8145,"net_weight":35590734,"cpu_weight":35590734,"net_limit":{"used":303,"available":1726927858,"max":1726928161},"cpu_limit":{"used":6396,"available":330688698,"max":330695094},"ram_usage":3622,"permissions":[{"perm_name":"active","parent":"owner","required_auth":{"threshold":1,"keys":[{"key":"EOS88Yq9KWpbv22JDgiQ2wRvUw4cAFPM8Ap15sxydvEgVMcMuy7kK","weight":1}],"accounts":[],"waits":[]}},{"perm_name":"owner","parent":"","required_auth":{"threshold":1,"keys":[{"key":"EOS88Yq9KWpbv22JDgiQ2wRvUw4cAFPM8Ap15sxydvEgVMcMuy7kK","weight":1}],"accounts":[],"waits":[]}}],"total_resources":{"owner":"gezdmmrrgage","net_weight":"3559.0734 EOS","cpu_weight":"3559.0734 EOS","ram_bytes":8145},"self_delegated_bandwidth":{"from":"gezdmmrrgage","to":"gezdmmrrgage","net_weight":"3559.0734 EOS","cpu_weight":"3559.0734 EOS"},"refund_request":null,"voter_info":{"owner":"gezdmmrrgage","proxy":"","producers":["eosauthority","eoscanadacom","eosdacserver","eosliquideos","eosnewyorkio","eosswedenorg"],"staked":71181468,"last_vote_weight":"26388934802860.00390625000000000","proxied_vote_weight":"0.00000000000000000","is_proxy":0}}
```
### Get accounts connected with key
`curl --request POST   --url http://api1.eosdublin.io/v1/history/get_key_accounts   --data '{"public_key":"EOS88Yq9KWpbv22JDgiQ2wRvUw4cAFPM8Ap15sxydvEgVMcMuy7kK"}`
```json
{"account_names":["gezdmmrrgage"]}
```
### Controlled accounts(?)
`curl --request POST --url http://api1.eosdublin.io/v1/history/get_controlled_accounts --data '{"controlling_account":"gezdmmrrgage"}'`
 ```json
 {"controlled_accounts":[]}
 ```
## Hackaton

set console output to true: /home/lukaz/.local/share/eosio/nodeos/config/config.ini
contracts-console = true

run test network: nodeos -e -p eosio --plugin eosio::chain_api_plugin --plugin eosio::history_api_plugin 
(keosd will start automatically)

get correct private key: cat /home/lukaz/.local/share/eosio/nodeos/config/config.ini
```
signature-provider = EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV=KEY:5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
```

open wallet: cleos wallet open
unlock wallet: cleos wallet unlock --password PW5Kg7ic7PfcXcnfwzvJJLb3moGeFTJtj2W4JdLcEdcXKApvKSiqm
import key to wallet: cleos wallet import 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
check keys: cleos wallet keys

import bios contract: cleos set contract eosio Documents/eos/build/contracts/eosio.bios -p eosio

## acounts
creater keys: cleos create key
Private key: 5Jmsawgsp1tQ3GD6JyGCwy1dcvqKZgX6ugMVMdjirx85iv5VyPR
Public key: EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4

cleos create key
Private key: 5Jm4C6HPAU5tZ9dbpqjGan3hUCG6MdUna44orVxgyAmjeQinBqD
Public key: EOS8AT1m2gPYSMo9RWGqgk4V9QT3PWaA2VG4ZUArSGp8bnWH5n671

import key to wallet: cleos wallet import 5Jmsawgsp1tQ3GD6JyGCwy1dcvqKZgX6ugMVMdjirx85iv5VyPR
import key to wallet: cleos wallet import 5Jm4C6HPAU5tZ9dbpqjGan3hUCG6MdUna44orVxgyAmjeQinBqD

create user account: cleos create account eosio user EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4

additional, create tester account: cleos create account eosio tester EOS8AT1m2gPYSMo9RWGqgk4V9QT3PWaA2VG4ZUArSGp8bnWH5n671 EOS8AT1m2gPYSMo9RWGqgk4V9QT3PWaA2VG4ZUArSGp8bnWH5n671

check accounts: cleos get accounts EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4

## building contract
generate abi file: eosiocpp -g eossportbook.abi eossportbook.cpp
rebuild contract: cd build; make

## contract
create account for contract: cleos create account eosio eossportbook EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4

deploy esb contract: cleos set contract eossportbook Documents/eos/build/contracts/eossportbook -p eossportbook

check is abi correct: cleos get abi eossportbook

## actions
```
cleos push action eossportbook updrunners '{ "runners": [{"runner_id": 1, "event_id": 1, "runner_name": "test1"}] }' -p eossportbook
```

```
cleos push action eossportbook updevents '{ "events_to_update": [{"event_id": 1, "event_name": "test 1"}, {"event_id":2, "event_name":"test 2"}] }' -p eossportbook
```
