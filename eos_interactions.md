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
