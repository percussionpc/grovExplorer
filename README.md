Altcoin Explorer 1.0
[Fork From Iquidus Explorer]
================

An open source block explorer written in node.js.

### ตัวอย่าง

*  [Litecoingreen](http://explorer.hi.in.th)

### Requires

*  node.js >= 8.17.0 (12.14.0 แนะนำ)
*  mongodb 4.2.x (4.2.8 แนะนำ)
*  *coind
*  Ubuntu 18.04 (e.g.)

### ติดตั้ง Node JS
    apt-get update
    apt install npm
    sudo npm install -g n
    sudo n stable
    sudo n 12.14.0
    sudo npm install forever -g
    node -v

### ติดตั้ง Mongo:
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 4B7C549A058F8B6B
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb.list
    sudo apt-get update
    sudo apt install mongodb-org=4.2.8 mongodb-org-server=4.2.8 mongodb-org-shell=4.2.8 mongodb-org-mongos=4.2.8 mongodb-org-tools=4.2.8
    sudo systemctl start mongod
    mongodb

### Create databse:

    mongo
    use explorer
    db.createUser( { user: "hq1024", pwd: "7U@Q5mX!$PBgXyMQMhf!", roles: [ "readWrite" ]} )
    exit

### Install:

    git clone https://github.com/1024HQ/Altcoin-Explorer.git
    cd explorer
    npm install --production

### ตั้งค่าไฟล์ settings.json
    https://github.com/1024HQ/explorer/blob/master/setting.json

### เปิด Screen สำหรับรัน

    sudo screen -S explorer
    cd explorer
    npm start
    ctrl+a & d #เพื่อออกจาก screen หรือ จะเปิดสองจอด้วย Duplicate Session ของ putty ก็ได้สะดวกดี

### Update ข้อมูล coind เข้าฐานข้อมูล

    node scripts/sync.js index reindex
    node scripts/sync.js index update
    
### ใช้งาน crontab เพื่อสั่งอัพเดตข้อมูลอัตโนมัติ
    // เลือก nano นะ
    sudo crontab -e
    // ใส่ข้อมูลด้านล่างนี้เข้าไป
    */1 * * * * cd /root/explorer && node scripts/sync.js index update > /dev/null 2>&1
    */2 * * * * cd /root/explorer && node scripts/sync.js market > /dev/null 2>&1
    */5 * * * * cd /root/explorer && node scripts/peers.js > /dev/null 2>&1
    // แล้วจากนั้นบันทึกไฟล์ ctrl+x y และ Enter
    sudo service cron restart

### เจอปัญหาข้อมูลไม่อัพเดต **script is already running.**

    cd explorer/tmp/
    rm index.pid
    rm db_index.pid
// จากนั้นลองใหม่อีกครั้ง



*Note: mongod must be running to start the explorer*

As of version 1.4.0 the explorer defaults to cluster mode, forking an instance of its process to each cpu core. This results in increased performance and stability. Load balancing gets automatically taken care of and any instances that for some reason die, will be restarted automatically. For testing/development (or if you just wish to) a single instance can be launched with

    node --stack-size=10000 bin/instance

To หยุดการทำงาน cluster โดยใช้คำสั่งนี้

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


### Wallet ต้องเปิด daemon=1 และ txindex=1 ใน coin.conf หรือรันด้วยคำสั่งนี้ต่อท้าย coind
    -daemon -txindex
    
### Security

Ensure mongodb is not exposed to the outside world via your mongo config or a firewall to prevent outside tampering of the indexed chain data. 

**exceeding stack size**

    RangeError: Maximum call stack size exceeded

Nodes default stack size may be too small to index addresses with many tx's. If you experience the above error while running sync.js the stack size needs to be increased.

To determine the default setting run

    node --v8-options | grep -B0 -A1 stack_size

To run sync.js with a larger stack size launch with

    node --stack-size=[SIZE] scripts/sync.js index update

Where [SIZE] is an integer higher than the default.

*note: SIZE will depend on which blockchain you are using, you may need to play around a bit to find an optimal setting*

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
