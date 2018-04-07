# BlockEx
Simple cryptocurrency block explorer system.

__Under active development and subject to rapid sudden change.__

## Required
This repo assumes `git`, `mongodb`, `node`, `yarn`, and are installed with configuration done.  Please adjust commands to your local environment. 

It is also required to have the Bulwark daemon running in the background. It is recommended to set this up before beginning to set up the explorer so that it syncs by the time you need it.

```
cd ~ 
wget https://github.com/bulwark-crypto/Bulwark/releases/download/1.2.3/bulwark-1.2.3.0-linux64.tar.gz 
tar xzvf bulwark-1.2.3.0-linux64.tar.gz 
sudo mv ./bin/* /usr/local/bin  
mkdir ~/.bulwark  
cat >~/.bulwark/bulwark.conf <<EOL  
rpcuser=bulwarkrpc  
rpcpassword=someverysafepassword  
EOL 
bulwarkd -daemon```

This will install the 1.2.3 Bulwark wallet and create a rpc username/password before starting the daemon.

## Install
`git clone https://github.com/dustinengle/blockex.git` - copy repo to local folder.

`cd blockex` - change into project directory.

`yarn install` - install packages used by the system.

## Configure
#### BlockEx API Configuration
`cp config.template.js config.js` - setup configuration using template.

`vim config.js` - replace with local values.

#### Database Configuration
`mongo` - connect using mongo client.

`use blockex` - switch to database.

`db.createUser( { user: "blockexuser", pwd: "Explorer!1", roles: [ "readWrite" ] } )` - create a user with the values stored in the `config.js` file from above, meaning they should match.

`exit` - exit the mongo client.

#### Crontab
The following automated tasks are currently needed for BlockEx to update but before running the tasks please update the cron script `/path/to/blockex/script/cron_block.sh` for the block with the local `/path/to/node`.

`yarn run cron:coin` - will fetch coin related information like price and supply from coinmarketcap.com.

`yarn run cron:masternode` - updates the masternodes list in the database with the most recent information clearing old information before.

`yarn run cron:peer` - gather the list of peers and fetch geographical IP information.

`yarn run cron:block` - will sync blocks and transactions by storing them in the database.

`yarn run cron:rich` - generate the rich list. __*Under Development*__

__Note:__ is is recommended to run all the crons before editing the crontab to have the information right away.  Follow the order above, start with `cron:coin` and end with `cron:rich`.

To setup the crontab please see run `crontab -e` to edit the crontab and paste the following lines (edit with your local information):
```
*/1 * * * * cd /path/to/blockex && ./script/cron_block.sh >> ./tmp/block.log 2>&1
*/5 * * * * cd /path/to/blockex && /path/to/node ./cron/coin.js >> ./tmp/coin.log 2>&1
0 * * * * cd /path/to/blockex && /path/to/node ./cron/masternode.js >> ./tmp/masternode.log 2>&1
0 * * * * cd /path/to/blockex && /path/to/node ./cron/peer.js >> ./tmp/peer.log 2>&1
0 * * * * cd /path/to/blockex && /path/to/node ./cron/rich.js >> ./tmp/rich.log 2>&1
```

## Build
At this time only the client web interface needs to be built using webpack and this can be done by running `yarn run build:web`.  This will bundle the application and put it in the `/public` folder for delivery.

## Run
`yarn run start:api` - will start the api.

`yarn run start:web` - will start the web, open browser [http://localhost:8081](http://localhost:8081).

## Test
`yarn run test:client` - will run the client side tests.

`yarn run test:server` - will test the rpc connection, database connection, and api endpoints.

## To-Do
- Write more tests
- Cluster support for api
