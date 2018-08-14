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
