# Merge changes from remote repo
* Fetch interesting tag `git fetch https://github.com/EOSIO/eos.git "refs/tags/v1.2.1:refs/tags/v1.2.1"`
* Merge tag to your current branch `git merge tags/v1.1.6`
* Resolve conflicts like in any other merge
# Build
* Fork repository
* Create brnach from tag: `git checkout tags/v1.1.4 -b lz_plugin_dev`
* Update submodules: `git submodule update --init --recursive`
* Build `./eosio_build.sh`
* Install `sudo ./eosio_install.sh`
# IDE
* Open project in Clion
* Go to File -> Settings and search from CMake
* In CMake options inster `-D BOOST_ROOT=/home/lzenczuk/opt/boost`
* Serach for Toolchains
* Set C Compiler: `/usr/bin/clang-4.0`
* Set C++ Compiler: `/usr/bin/clang++-4.0`
# Plugins
## Add watcher plugin
* Clone watcher plugin from [eos authority github](https://github.com/eosauthority/eosio-watcher-plugin) 
* Copy plugin code to eos project `cp -r watcher_plugin ../eos-monitor/plugins/`
* Go to plugins folder `cd ../eos-monitor/plugins/`
* Open CMakeList file `vim CMakeLists.txt`
* Add entry `add_subdirectory(watcher_plugin)`
* Go to nodeos code `cd ../programs/nodeos/`
* Open CMakeList file `vim CMakeLists.txt`
* To *target_link_libraries* add line `PRIVATE -Wl,${whole_archive_flag} watcher_plugin -Wl,${no_whole_archive_flag}`
* Go to build `cd ../../build/`
* Build `make`
## Edit configuration
* Go to configuration folder `cd ~/.local/share/eosio/nodeos/config`
* Open config.ini `vim config.ini`
* Enable plugin adding `plugin = eosio::watcher_plugin`
* Add link to http server listening for POST requests `watch-receiver-url = http://127.0.0.1:3000/bc-action`
* Add test network peers addresses 
```
p2p-peer-address = jungle.cryptolions.io:19876
p2p-peer-address = dev.cryptolions.io:39876
```
* Download jungle [genesis file](https://github.com/CryptoLions/EOS-Jungle-Testnet/blob/master/genesis.json)
* Run `./nodeos --delete-all-blocks  --genesis-json ~/.local/share/eosio/nodeos/config/genesis.json`
