# EOS development snippets
## Contract definition
```cpp
class eossportbook : public eosio::contract {
public:
    eossportbook(account_name self) : eosio::contract(self) {}
```

## ABI macro
Extended macro allow to support eosio.token transfer actions.
```cpp
// Code from monstereos contract
// https://github.com/leordev/monstereos/blob/904f8609a52287bd9c6e975be193ee1aeb11378e/contracts/pet/pet.cpp#L264

#define EOSIO_ABI_EX(TYPE, MEMBERS) \
extern "C" { \
   void apply( uint64_t receiver, uint64_t code, uint64_t action ) { \
      if( action == N(onerror)) { \
         /* onerror is only valid if it is for the "eosio" code account and authorized by "eosio"'s "active permission */ \
         eosio_assert(code == N(eosio), "onerror action's are only valid from the \"eosio\" system account"); \
      } \
      auto self = receiver; \
      if( code == self || code == N(eosio.token) || action == N(onerror) ) { \
         TYPE thiscontract( self ); \
         switch( action ) { \
            EOSIO_API( TYPE, MEMBERS ) \
         } \
         /* does not allow destructor of thiscontract to run: eosio_exit(0); */ \
      } \
   } \
}

EOSIO_ABI_EX(eossportbook, (transfer)(payout))
```

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
## Table
[General description](https://developers.eos.io/eosio-cpp/docs/db-api)

[C++ API](https://developers.eos.io/eosio-cpp/reference)
### Table definition
```cpp
// @abi table bets i64
struct bet {
    uint64_t bet_id;
    uint64_t offer_id;
    uint64_t mb_offer_id;
    uint64_t runner_id;
    account_name originator;
    account_name acceptor;
    double price;
    int64_t originator_amount;
    int64_t acceptor_amount;
    uint8_t status;

    uint64_t primary_key() const { return bet_id; }
    uint64_t mb_offer_index() const { return mb_offer_id; }

    EOSLIB_SERIALIZE(bet, (bet_id)(offer_id)(mb_offer_id)(runner_id)(originator)(acceptor)(price)(originator_amount)(acceptor_amount)(status))
};

typedef eosio::multi_index<N(bets), bet,
    eosio::indexed_by<N(betsbymbo), eosio::const_mem_fun<bet, uint64_t , &bet::mb_offer_index> >
> bet_index;
```
### Table instance. Primary key.
```cpp
bet_index global_bets_db(_self, _self);
```
### Table instance. Index.
```cpp
bet_index global_bets_db(_self, _self);

auto mbo_bets_db = global_bets_db.get_index<N(betsbymbo)>();
```
### Insert
It is possible to getting available id from table using [available_primary_key](https://developers.eos.io/eosio-cpp/reference#available_primary_key) method.
```cpp
bet_index global_bets_db(_self, _self);

global_bets_db.emplace(_self, [&](bet& nb) {
    nb.bet_id = new_bet_id;
    nb.offer_id = of.offer_id;
    nb.mb_offer_id = mb_offer_id;
    nb.runner_id = of.runner_id;
    nb.price = of.price;
    nb.originator = of.originator;
    nb.originator_amount = of.amount;
    nb.acceptor = acceptor;
    nb.acceptor_amount = amount;
    nb.status = matched;
});
```
### Find and update
```cpp
bet_index global_bets_db(_self, _self);

auto gbitr = global_bets_db.find(b.bet_id);

if(gbitr != global_bets_db.end()){
    global_bets_db.modify(gbitr, 0, [&](bet &b2u) {
        b2u.status = payed;
    });
}
```
