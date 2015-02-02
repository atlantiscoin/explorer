Iquidus Explorer - 1.4.1
================

An open source block explorer written in node.js.

### Links

*  [Demo](http://explorer.iquidus.co.nz/) - Demo site running with Darkcoin; Market pages (bittrex,poloniex), Top 100 (received, balance)

### Requires

*  node.js >= 0.10.28
*  mongodb >= 2.6.0
*  *coind

### Create datbase

Enter MongoDB cli:

    $ mongo

Create databse:

    > use explorerdb

Create user with read/write access:

    > db.createUser( { user: "iquidus", pwd: "3xp!0reR", roles: [ "readWrite" ] } )

### Get the source

    git clone https://github.com/iquidus/explorer explorer

### Install node modules

    cd explorer && npm install --production

*note:If you plan to edit the codebase ignore the --production flag as it ignores dev dependecies required to run tests*

### Configure

    cp ./settings.json.template ./settings.json

*Make required changes in settings.json*

### Start Explorer

    npm start

*note: mongod must be running to start the explorer*

As of version 1.4.0 the explorer defaults to cluster mode, forking an instance of its process to each cpu core. This results in increased performance and stability. Load balancing gets automatically taken care of and any instances that for some reason die, will be restarted automatically. For testing/development (or if you just wish to) a single instance can be launched with

    node --stack-size=10000 bin/instance

To stop the cluster you can use

    npm stop

### Syncing databases with the blockchain

sync.js (located in scripts/) is used for updating the local databases. This script must be called from the explorers root directory.

    Usage: node scripts/sync.js [database] [mode]
    
    database: (required)
    index [mode] Main index: coin info/stats, transactions & addresses
    market       Market data: summaries, orderbooks, trade history & chartdata
    
    mode: (required for index database only)
    update       Updates index from last sync to current block
    check        checks index for (and adds) any missing transactions/addresses
    reindex      Clears index then resyncs from genesis to current block
    
    notes:
    * 'current block' is the latest created block when script is executed.
    * The market database only supports (& defaults to) reindex mode.
    * If check mode finds missing data(ignoring new data since last sync),
      index_timeout in settings.json is set too low.


*It is recommended to have this script launched via a cronjob at 1+ min intervals.*

**crontab**

*Example crontab; update index every minute and market data every 2 minutes*

    */1 * * * * cd /path/to/explorer && /usr/bin/node scripts/sync.js index update > /dev/null 2>&1
    */2 * * * * cd /path/to/explorer && /usr/bin/node scripts/sync.js market > /dev/null 2>&1

### Wallet

Iquidus Explorer is intended to be generic so it can be used with any wallet following the usual standards(PoW). The wallet must be running with atleast the following flags

    -daemon -txindex

### Donate

    BTC: 168hdKA3fkccPtkxnX8hBrsxNubvk4udJi

### Development

Current version: 1.4.1   
Next planned: 1.4.2

* proof of stake improvements

### Tests

Jasmine is used for unit/integration tests. The tests can be executed simply with

    npm test

### Known Issues

**exceeding stack size**

    RangeError: Maximum call stack size exceeded

Nodes default stack size may be too small to index addresses with many tx's. If you experience the above error while running sync.js the stack size needs to be increased.

To determine the default setting run

    node --v8-options | grep -B0 -A1 stack_size

To run sync.js with a larger stack size launch with

    node --stack-size=[SIZE] scripts/sync.js index update

Where [SIZE] is an integer higher than the default. 

*note: SIZE will depend on which blockchain you are using, you may need to play around a bit to find an optimal setting*

**Incorrect total sent/received on address page (PoS)**

Due to the nature of Proof of Stake the total sent and received values on address pages will appear higher than expected. This is due to how PoS is managed on the blockchain. When an address generates PoS it appears as a tx with matching input & output addresses in the form sent: 10.00000000, received: 10.04000000 (address gained 0.04 from PoS)

I'm on the fence whether this is a bug or not, as technically the current values are representing the blockchain accurately. However as it makes more sense to users to have it behave as (sent: 0.00, received: 0.04) a new option will be introduced in a future release allowing it to be set one way or the other.

For now if you don't wish to display the current values you can disable them by setting 'show_sent_received' to false in settings.json

### License

Copyright (c) 2015, Iquidus Technology  
Copyright (c) 2015, Luke Williams  
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of Iquidus Technology nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

