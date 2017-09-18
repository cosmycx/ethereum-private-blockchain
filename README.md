# How to Start an Ethereum Private Network Blockchain

This page contains description on how to start an own private network blockchain with [Go Ethereum](https://github.com/ethereum/go-ethereum). 
__One local machine__ running node and __one cloud__ based running node are connected. Similar additional nodes can be added to the private network.
>An Ethereum network is a private network if the nodes are not connected to the main network nodes.

More info: [https://github.com/ethereum/go-ethereum/wiki/Private-network](https://github.com/ethereum/go-ethereum/wiki/Private-network)

### First Node - Local machine: 
This is done on macOS Sierra 10.12.6, however it should work in a similar way on other OS or maybe using docker and linux.

__First thing first:__
Install Go Ethereum, __geth__ which is the CLI Ethereum client, details are here: [https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum)

For Mac, you can install using Homebrew, the brew installed version could be behind the actively developed Go Ethereum, but it should work or you can also install from develop branch.
[https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Mac](https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Mac)
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
