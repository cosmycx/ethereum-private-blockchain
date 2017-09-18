# How to Start an Ethereum Private Network Blockchain

This page contains description on how to start an own private network blockchain with [Go Ethereum](https://github.com/ethereum/go-ethereum). 
__One local machine__ running node and __one cloud__ based running node are connected. Similar additional nodes can be added to the private network.
>An Ethereum network is a private network if the nodes are not connected to the main network nodes.

More info: [go-ethereum/wiki/Private-network](https://github.com/ethereum/go-ethereum/wiki/Private-network)

## First Node - Local machine: 
This is done on macOS Sierra 10.12.6, however it should work in a similar way on other OS or maybe using docker and linux.

__First thing first:__
Install Go Ethereum, __geth__ which is the CLI Ethereum client, details are here: [go-ethereum/wiki/Building-Ethereum](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum)

For Mac, you can install using Homebrew, the brew installed version could be behind the actively developed Go Ethereum, but it should work or you can also install from develop branch.
[go-ethereum/wiki/Installation-Instructions-for-Mac](https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Mac)
```
$ brew tap ethereum/ethereum
$ brew install ethereum
```
Check geth is installed from the terminal:
```
$ geth version
```
Create a new directory somewhere on the local machine for all the the private blockchain data:
```
$ cd ~/Documents
$ mkdir private-blockchain
$ cd private-blockchain
$ mkdir chain-data
```
Next you will need a configuration file called __genesis block__, which is the start of every blockchain. Here is an example of a __genesis.json__ file:
```
{
    "config": {
        "chainId"         : 555,                                              
        "homesteadBlock"  : 0,                                                
        "eip155Block"     : 0,                                            
        "eip158Block"     : 0
    },
    "coinbase"    : "0x0000000000000000000000000000000000000001",                         
    "nonce"       : "0x0042",                                                             
    "mixhash"     : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "parentHash"  : "0x0000000000000000000000000000000000000000000000000000000000000000", 
    "difficulty"  : "0x2000000",                                                          
    "gasLimit"    : "0x999000000",                                                        
    "extraData"   : "",                                                                   
    "alloc"       : {                                                                     
    }
}
```
Some basic notes on the above genesis.json:
+ "chainId"           - must be different than 1 which is the main Ethereum net
+ "homesteadBlock"    - using the Ethereum Homestead release
+ "eip155Block"       - epi = Ethereum Improvement Proposal
+ "coinbase"          - collects mining rewards for this block, not required
+ "nonce"             - proof of work nonce
+ "mixhash"           - proof of work hash
+ "parentHash"        - pointer to the parent block, 0 for genesis
+ "difficulty"        - mining dificulty
+ "gasLimit"          - limit of gas cost per block, high for testing
+ "extraData"         - (_to eternity and beyond_ note)[https://en.bitcoin.it/wiki/Genesis_block]
+ "alloc"             - could pre-fund wallet accounts, it does not create the accounts

Use an text editor to make the file then save it in the __private-blockchain__ directory. The directory tree to look something like this:


![directory tree](/images/dirtree.png)

Now from private-blockchain directory run geth to create a database that uses this genesis block:
```
$ geth --datadir ./chain-data init ./genesis.json
```

Start the node on the blockchain:
```
$ geth --datadir ./chain-data
```
Things to note from the terminal output, that you will need later on:

__IPC endpoint opened:__ /Users/..../private-blockchain/chain-data/geth.ipc

__Enode:__ self=enode://a3d1....bf3@EXT:IP:ADD:RESS:30303 
(note at the end your external IP address and port)


__Local Home Network Port Forwarding:__ Depending on your local/home network configuration you may have to get into your router box advance settings and forward external IP address port 30303 to the machine your are using local IP address and same port.

## Second Node - Cloud: 

This is using [DigitalOcean](https://www.digitalocean.com/), however other cloud services such as Amazon, etc.. would work the same.

Create Ubuntu 16.04.3 x64 Droplet, here is the time to add a SSH key if you previously created one. [DigitalOcean has tutorials](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets) how to do that. The SSH key is not needed but nice to have. 

Once you have the droplet created, you will need to copy the droplet's IP address.


![digidroplet](/images/digidroplet.png)
Log into the new droplet via terminal, or if you do it from the droplet’s page it is the access console.

The following is done with a SSH key to easy communicate with the cloud droplet. This is just playing around so root user would be fine for now.


Next you need the __genesis.json__ file copied on the cloud droplet. The easier way to do that is using the __sftp__ net protocol:


```
$ sftp root@Droplet:IP:ADD:RESS 
```
Then asumming you are in the private-blockchain directory run:
```
$ put ./genesis.json genesis.json
```
and you shuld see something like _Uploading ./genesis.json to /root/genesis.json_

Next you need to access the Droplet terminal:

```
ssh root@Droplet:IP:ADD:RESS 
```

Installing Ethereum Go on Linux Ubuntu from terminal:
[go-ethereum/wiki/Installation-Instructions-for-Ubuntu](https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Ubuntu)

```
$ add-apt-repository -y ppa:ethereum/ethereum
$ apt-get update
$ apt-get install ethereum
```

You can do the same thing as on the local machine and create a directory chain-data
```
$ mkdir chain-data
```
then run geth to create a database that uses this genesis block:
```
$ geth --datadir ./chain-data init ./genesis.json
```
Start the node on the blockchain:
```
geth --datadir ./chain-data console
```
console - to have cloud console access after the node is started, otherwise you will have to start another terminal and attach to the running geth

In the geth JS console run:
```
admin.addPeer("enode://a3d1....bf3@EXT:IP:ADD:RESS:30303")
```
and you shuld get a console answer: __true__ !!

list peers
```
admin.peers
```

### Transfer Ether from local account to cloud account

__Cloud in the geth JS console:__
```
eth.accounts
```
No accounts are present, then create an account
```
personal.newAccount("passphrase")
```

__Local node attach a geth JS console from a terminal:__
```
geth attach /Users/..../private-blockchain/chain-data/geth.ipc
```
Then in the JS console
```
personal.newAccount("passphrase")
personal.unlockAccount(address, "passphrase")
```
Start mining with one thread
```
miner.start(1)
```
Transfer ether bewteen accounts, must have account unlocked and miner going on for the transaction to work
```
eth.sendTransaction({from: '0x126.....c3c', to: '0xd9d.....d19', value: web3.toWei(1, "ether")})
```
to check balance
```
eth.getBalance("0xd9d.....d19") 
```


### Connect Mist Ethereum Wallet to the Private Net:
Install Mist Ethereum Wallet: [/ethereum/mist/releases](https://github.com/ethereum/mist/releases)
##### Connect Mist to your private net blockchain and use Mist functionality
On macOS start Mist like this from a terminal to connect to the private network, the ipc end point is the above noted when the local node was started:
```
/Applications/Mist.app/Contents/MacOS/Mist --rpc /Users/...../private-blockchain/chain-data/geth.ipc
```
Mist starts on Private-Net, and connects to 2 peers


![digidroplet](/images/miststart.png)

Interesting read from 2015: [Vitalik on public and private blockchains](https://blog.ethereum.org/2015/08/07/on-public-and-private-blockchains/) 


