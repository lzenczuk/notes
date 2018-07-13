# EOS development snippets

## Send action from contract
### Deferred
Call eosio.token transfer action: self (from), orginator (to), 120 EOS, "Bet" (memo) 
```cpp
eosio::transaction out;
out.actions.emplace_back(
       eosio::permission_level{_self, N(active)},
       N(eosio.token),
       N(transfer),
       std::make_tuple(_self, b.originator, eosio::asset(120, S(4, EOS)), std::string("Bet")));
out.send(get_id(), _self);
```
### Inline
Call eosio.token transfer action: self (from), orginator (to), 120 EOS, "Bet" (memo) 
```cpp
eosio::action(
       eosio::permission_level{ _self, N(active) },
       N(eosio.token), N(transfer),
       std::make_tuple(_self, b.originator, eosio::asset(120, S(4, EOS)), std::string("Bet"))
).send();
```
