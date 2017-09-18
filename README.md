# How to Start an Ethereum Private Network Blockchain

This page contains description on how to start an own private network blockchain with [Go Ethereum](https://github.com/ethereum/go-ethereum). 
__One local machine__ node and __one cloud__ based node are used to start and are connected on the private blockchain network. Similar additional nodes can be added to join.
>An Ethereum network is a private network if the nodes are not connected to the main network nodes.

More info: [go-ethereum/wiki/Private-network](https://github.com/ethereum/go-ethereum/wiki/Private-network)

Also an interesting read from 2015: [Vitalik on public and private blockchains](https://blog.ethereum.org/2015/08/07/on-public-and-private-blockchains/) 

But let's __get started!__

## First Node - Local machine
This is done on macOS Sierra 10.12.6, however it should work in a similar way on other OS or maybe using docker and linux.

__First thing first:__
Install Go Ethereum, __geth__ which is the CLI Ethereum client, details are here: [go-ethereum/wiki/Building-Ethereum](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum)

For Mac, you can install using Homebrew, the brew installed version could be behind the actively developed Go Ethereum, but it should work or you can also use brew to install from develop branch.
[go-ethereum/wiki/Installation-Instructions-for-Mac](https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Mac)
```
$ brew tap ethereum/ethereum
$ brew install ethereum
```
Check geth is installed and ready to go from the terminal:
```
$ geth version
```
Create a new directory somewhere on the local machine (e.g. Documents) for all the the __private blockchain data__:
```
$ cd ~/Documents
$ mkdir private-blockchain
$ cd private-blockchain
$ mkdir chain-data
```
Next you will need a configuration file called __genesis block__, which is the start of every blockchain. Here is an example of a __genesis.json__:
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
+ _"chainId"_           - must be different than 1 which is the main Ethereum net
+ _"homesteadBlock"_    - using the Ethereum Homestead release
+ _"eip155Block"_       - eip = Ethereum Improvement Proposal
+ _"coinbase"_          - collects mining rewards for this block, not required
+ _"nonce"_             - proof of work nonce
+ _"mixhash"_           - proof of work hash
+ _"parentHash"_        - pointer to the parent block, 0 for genesis
+ _"difficulty"_        - mining dificulty
+ _"gasLimit"_          - limit of gas cost per block, high for testing
+ _"extraData"_         - (_to eternity and beyond_ note)[https://en.bitcoin.it/wiki/Genesis_block]
+ _"alloc"_             - could pre-fund wallet accounts, it does not create the accounts

Use a text editor to write the genesis.json file and then save it in the __private-blockchain__ directory. The directory tree would look something like this:


![directory tree](/images/dirtree.png)

Now from inside the private-blockchain directory run geth to create a database that uses this genesis block:
```
$ geth --datadir ./chain-data init ./genesis.json
```

Start the node on the blockchain using the same chain-data directory:
```
$ geth --datadir ./chain-data
```
Things to note from the terminal output, that you will need later on:

+ __IPC endpoint opened:__ /Users/..../private-blockchain/chain-data/geth.ipc

+ __Enode:__ self=enode://a3d1....bf3@EXT:IP:ADD:RESS:30303 
(note at the end your external IP address and port)


Local Home Network __Port Forwarding:__ Depending on your local/home network configuration you may have to get into your router box advance settings and forward external IP address port 30303 to the machine your are using local IP address and same port. This is done to expose the local machine IP and port to the cloud IP and port for net communication.



## Second Node - Cloud

This is using [DigitalOcean](https://www.digitalocean.com/), however other cloud services such as Amazon, etc.. would work the same.

Create an Ubuntu 16.04.3 x64 Droplet, here is the time to add the SSH key if you previously created one. [DigitalOcean has tutorials](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets) how to create one. The SSH key is not needed but nice to have to easy connect to the cloud terminal. 

Once you have the droplet created, you will need to copy the droplet's IP address. On DigitalOcean it appears as below:


![digicloud](/images/digicloud.png)

Log into the new droplet via terminal, or if you do log from the droplet’s page it is called the access console.

The following is done with a SSH key to easy communicate with the cloud droplet. This is just playing around so __root__ user would be fine for now.


Next you need to copy the __genesis.json__ file to the cloud droplet. The easier way to do that is using the __sftp__ net protocol, run on the local machine terminal in the private-blockchain directory:

```
$ sftp root@DROP:IP:ADD:RESS 
```
Then asumming you are in the private-blockchain directory run:
```
sftp> put ./genesis.json genesis.json
```
and you should see something like _Uploading ./genesis.json to /root/genesis.json_

Next you need to access the Droplet terminal and install geth:

```
ssh root@DROP:IP:ADD:RESS 
```

Installing Ethereum Go on Linux Ubuntu from terminal:
[go-ethereum/wiki/Installation-Instructions-for-Ubuntu](https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Ubuntu)

```
$ add-apt-repository -y ppa:ethereum/ethereum
$ apt-get update
$ apt-get install ethereum
```

You can do the same thing as on the local machine and create a directory chain-data for the blockchain data:
```
$ mkdir chain-data
```
then run geth to create a database that uses the genesis block:
```
$ geth --datadir ./chain-data init ./genesis.json
```
Start the node on the blockchain using the same chain-data directory:
```
$ geth --datadir ./chain-data console
```
console is added to have cloud same console access (to the __JS console__) after the node is started, otherwise you will have to start another terminal connect to the droplet and then attach to the running geth.

In the geth JS console run admin.addPeer with the the saved enode info from the local machine:
```
> admin.addPeer("enode://a3d1....bf3@EXT:IP:ADD:RESS:30303")
```
and you should see a console answer: __true__ !!

Listing peers should confirm the other peer node:
```
> admin.peers
```

## Transfer Ether from local account to cloud account

__Cloud Node__ in the geth __JS console__:
```
> eth.accounts
```
No accounts are present, to create an account:
```
> personal.newAccount("passphrase")
```

__Local node__ attach a geth __JS console__ from a terminal:
```
$ geth attach /Users/..../private-blockchain/chain-data/geth.ipc
```
Then in the JS console make a new account and unlock it for transfer:
```
> personal.newAccount("passphrase")
> personal.unlockAccount(address, "passphrase")
```
Start mining with one thread, this would produce ether to the account:
```
> miner.start(1)
```
Transfer ether bewteen accounts, must have the _from_ account unlocked and miner mining for the transaction to go through:
```
> eth.sendTransaction({from: '0x126.....c3c', to: '0xd9d.....d19', value: web3.toWei(1, "ether")})
```
To check an account balance:
```
> eth.getBalance("0xd9d.....d19") 
```
To stop mining:
```
> miner.stop()
```

## Connect Mist Ethereum Wallet to the Private Net:
Install Mist Ethereum Wallet: [/ethereum/mist/releases](https://github.com/ethereum/mist/releases)
### Connect Mist to your private net blockchain and use Mist functionality
On macOS start Mist like this from a terminal to connect to the private network, the geth.ipc path end point is the above noted when the local node was started:
```
$ /Applications/Mist.app/Contents/MacOS/Mist --rpc /Users/...../private-blockchain/chain-data/geth.ipc
```
__Mist__ starts on Private-Net, and connects to the 2 peers:


![digidroplet](/images/miststart.png)


