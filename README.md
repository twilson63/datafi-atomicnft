# Arweave DataFi: Atomic NFTs ⚛️

DataFi is the concept of combining permanent Data + NFT (Non-Fungible Token) + Permaweb + SmartWeave Contracts + PST (Profit Sharing Token) in ways that provide decentralized finalization features for immutable storage in the Arweave community. So that is a mouth full of concepts. 😀 For us to get a deeper understanding of the bundled concept of DataFi, we will need to dive down the rabbit hole of each of these technologies. We will not go deep, but hopefully deep enough for you to understand what DataFi can be. We will also discuss some use cases that apply this concept in practice and some potential use cases for this concept.

> 🧪 NOTE: This is under heavy discovery and will evolve. Many of the technologies mentioned are in motion and subject to affect this bundled concept called DataFi. So be warned, there will be dragons here! 🐉

## About Workshop

### What do I need to know to get the most out of this workshop?

* Understanding the concept of Blockchains - https://youtu.be/yubzJw0uiE4
* Understanding the concept of NFTs - https://youtu.be/NNQLJcJEzv0
* Fullstack Developer skills (HTML, CSS, and Javascript)
* Arweave Concepts (Data Storage and Smartweave Contracts) - https://arweave.org

### What tools do I need to follow along with this workshop?

* Code Editor https://code.visualstudio.com
* NodeJS (v16.15.1) https://nodejs.org
* ArLocal (v1.1.41) https://github.com/textury/arlocal

> IF you want to use mainnet: You will also need a couple of Arweave Wallets and a little AR to make some transactions, you can get a wallet at https://arweave.app or https://arconnect.io and you can find the best exchanges to acquire AR via this article - https://arweave.news/how-to-buy-arweave-token/


To better understand the capabilities of DataFi, I think it will be best to unpack each of the underlining concepts with references to explore them deeper. Then walk through a proof of concept via code that can demonstrate the combination of these concepts in a simple project. Finally, share some use cases that demonstrate DataFi in the wild. Buckle up this should be a fun ride. 🚀

* Trustless
* Permissionless
* Composable

DataFi Workshop is a multipart series of concepts grouped under the idea of dataFi, where permanent **data** meets decentralized **fi**nalization. Arweave contains many core concepts that can bundle together to create exciting features and functionality unique to the Arweave ecosystem. 

Permanent **Data** + Decentralized **Fi**nalization = **DataFi**

![DataFi](https://fedzrgwlefkoztvkbdve3hkqeimokiowci6gyf63ojd3tfxcpi.arweave.net/KQeYmsshVOzOqgjqTZ1QIhjlIdYSPGwX23J_HuZbiek)

## Atomic NFTs Workshop

The NFT concept is about showing and transfering ownership of an item, to show ownership you have a token that shows the owner of a specific key pair (public/private) keys owns a specific (Digital) asset. 

> It is possible for an NFT to point to a physical asset as well, but for the purposes of this workshop we will be dealing with NFTs that show ownership of a digital asset.

That digital asset needs to be available for reference at all times. Arweave is a great storage choice for digital assets, because you pay once to store and it is available through a decentrailized network forever. Many NFT developers may store the NFT or Token on blockchain then store the data on a storage layer, which separates the token from the digital asset into two platforms. With Arweave Smart Contracts you can store both the NFT (Token) and the Digital Asset on the same blockchain(blockweave). This allows you to keep both the token and the digital asset combined together.

### What is an Atomic NFT?

> NOTE: As of June 10, 2022 we are using the Atomic NFT standard defined at https://atomicnft.com - This standard may change in the future! 

Atomic NFT is a standard where the media assets and the contract metadata are stored together immutably under a single unique identifier. -- https://atomicnft.com/en/General-definition-of-an-atomic-NFT/

In order to provide a single identifier as the point of entry for both the NFT and asset, we will use the process we demoed in the last workshop. The ability to add a NFT SmartWeave Contract and Digital Asset as the data in a single transaction provides us the ability to have a single transaction id point to both the NFT and the digital asset.

![Atomic NFT](https://arweave.net/_3iet3wjKfJPsM4QTRZnLYGrfe77jRJKraLzfJVCLSs)

Initial State Attributes

| Name | Description | Type |
| ------ | ----------- | ----- |
| owner | creator of NFT | wallet address |
| title | title of digital asset | string |
| description | description of the asset | string |
| name | name of PST associated with NFT | string |
| ticker | ticker symbol of associated PST | uppercase string |
| balances | collection of wallet addresses and their quantity of ownership | string |
| locked | array of locked data | array |
| contentType | mime type of digital asset (html, jpg, png) | string
| createdAt | A Number representing the milliseconds elapsed since the UNIX epoch. | number |
| tags | array of strings | array |


Metadata Tags

| Name | Description | 
| ------ | ----------- |
| Content-Type | mime type of the digital asset (text/html, image/png, etc) |
| Network | always 'Koi' if planning to use Koi for attention tracking |
| Action | always 'marketplace/Create' for Arweave NFT compatibility |
| App-Name | always 'SmartWeaveContract' for Arweave SmartWeave Contract identification |
| App-Version | always '0.3.0' for Arweave SmartWeave Contract idenitification |
| Contract-Src | Arweave TxID references the source code of the Smart Contract |
| Init-State | the above initial state stringified |

Core Contract Methods:

* Balance - returns the current Token Balance for a given wallet address
* Transfer - allows a NFT owner to transfer all or some of their balance to another wallet address

There are two additional concepts with Atomic NFTs:

* Dynamic - (data "Digital Asset" can be wrapped with an App to provide dynamic interaction) 
* Bridging - The ability to delegate ownership to another chain.

## Create an Atomic NFT (Basic)

### Install and start arlocal

In a separate terminal window start arlocal (devnet)

``` sh
npm install -g arlocal@1.1.41
arlocal
```

### Create new project

``` sh
mkdir atomicnft-demo
cd atomicnft-demo
npm init -y
```

Lets use a cool image of `Winston` that `DMac` has made public domain for Arweave Demo purposes - 🙏🏻 Thanks DMac!

> NOTE: Check out DMac's workshop on uploading NFTs https://www.youtube.com/watch?v=TMzKoxpf_GU

![Winston](https://arweave.net/je2PgRDv0XRbqQiUrCovofwlZHeYOS7WJrFJGigqNoY)


### Get our NFT Digital Asset

``` sh
curl -L -O https://arweave.net/je2PgRDv0XRbqQiUrCovofwlZHeYOS7WJrFJGigqNoY
mv je2PgRDv0XRbqQiUrCovofwlZHeYOS7WJrFJGigqNoY winston.svg
```

### Create our devnet wallet

``` 
npm install arweave@1.11.4
```

create-wallet.js

``` js
import Arweave from 'arweave'
import fs from 'fs'

const arweave = Arweave.init({
  host: 'localhost',
  port: 1984,
  protocol: 'http'
})

const jwk = await arweave.wallets.generate()
const addr = await arweave.wallets.jwkToAddress(jwk)
fs.writeFileSync('wallet.json', JSON.stringify(jwk))

await arweave.api.get(`mint/${addr}/${arweave.ar.arToWinston('100')}`)
```

Lets create our devnet wallet

``` sh
node create-wallet.js
```


### Create our NFT Contract

Let's create our contract source code. 

``` sh
touch contract.js
```

contract.js

This is our basic NFT contract, it contains two functions, balance and transfer.

``` js
const functions = { balance, transfer }

export function handle(state, action) {
  if (Object.keys(functions).includes(action.input.function)) {
    return functions[action.input.function](state, action)
  }
  return ContractError('function not defined!')
}

function balance(state, action) {
  const { input, caller } = action
  let target = input.target ? input.target : caller;
  const { ticker, balances } = state;
  ContractAssert(
    typeof target === 'string', 'Must specify target to retrieve balance for'
  )
  return {
    result: {
      target,
      ticker,
      balance: target in balances ? balances[target] : 0
    }
  }
}

function transfer(state, action) {
  const { input, caller } = action
  const { target, qty } = input
  ContractAssert(target, 'No target specified')
  ContractAssert(caller !== target, 'Invalid Token Transfer. ')
  ContractAssert(qty, 'No quantity specified')
  const { balances } = state
  ContractAssert(
    caller in balances && balances[caller] >= qty,
    'Caller has insufficient funds'
  )
  balances[caller] -= qty
  if (!(target in balances)) {
    balances[target] = 0
  }
  balances[target] += qty
  state.balances = balances
  return { state }
}


```

### Mint a NFT

`npm install arweave`

mint.js

In this script we are minting the NFT, we do this manually to go through all of the steps of the NFT process.

``` js
import Arweave from 'arweave'
import fs from 'fs'

const arweave = Arweave.init({
  host: 'localhost',
  port: 1984,
  protocol: 'http'
})

const jwk = JSON.parse(fs.readFileSync('wallet.json', 'utf-8'))
const addr = await arweave.wallets.jwkToAddress(jwk)
const src = fs.readFileSync('contract.js', 'utf-8')
const winston = fs.readFileSync('winston.svg', 'utf-8')
// create contract Src
const cSrc = await arweave.createTransaction({ data: src })
cSrc.addTag('Content-Type', 'application/javascript')
cSrc.addTag('App-Name', 'SmartWeaveContractSource')
cSrc.addTag('App-Version', '0.3.0')
await arweave.transactions.sign(cSrc, jwk)
await arweave.transactions.post(cSrc)

// create contract
const contract = await arweave.createTransaction({ data: winston })
contract.addTag('Content-Type', 'image/svg+xml')
contract.addTag('Network', 'Koii') // Exchange to list NFT on Koii
contract.addTag('Action', 'marketplace/Create')
contract.addTag('App-Name', 'SmartWeaveContract')
contract.addTag('App-Version', '0.3.1')
contract.addTag('Contract-Src', cSrc.id)
contract.addTag('NSFW', 'false')
contract.addTag('Init-State', JSON.stringify({
  owner: addr,
  title: 'Winston',
  description: 'Winston the mascot of Arweave who never forgets!',
  name: 'Koii', // PST Name
  ticker: 'KOINFT', // PST you want to associate with NFT
  balances: {
    [addr]: 1
  },
  locked: [],
  contentType: 'text/svg+xml',
  createdAt: Date.now(),
  tags: [],
	isPrivate: false
}))

await arweave.transactions.sign(contract, jwk)
await arweave.transactions.post(contract)
console.log('ContractId: ', contract.id)

```

### Read our balance on an NFT

read-contract.js

`npm install redstone-smartweave@0.5.7`

In this script, we read the balance of the NFT, for a wallet address 

``` js
import Arweave from 'arweave'
import { SmartWeaveNodeFactory, LoggerFactory } from 'redstone-smartweave'
LoggerFactory.INST.logLevel('error');

const arweave = Arweave.init({
  host: 'localhost',
  port: 1984,
  protocol: 'http'
})

const warp = SmartWeaveNodeFactory.memCachedBased(arweave).useArweaveGateway().build()
// MAINNET const warp = SmartWeaveNodeFactory.memCached(arweave)
const contract = warp.contract('zBe-smHxyQYkr9GZL817Vn2IgSUMd2SRdV0MHWvDRTA')
const { result } = (await contract.viewState({
  function: 'balance',
  target: 'vh-NTHVvlKZqRxc8LyyTNok65yQ55a_PJ1zWLb9G2JI'
}))
console.log(result)

```

### Transfer ownership of our NFT

create-wallet2.js

``` js
import Arweave from 'arweave'
import fs from 'fs'

const arweave = Arweave.init({
  host: 'localhost',
  port: 1984,
  protocol: 'http'
})

const jwk = await arweave.wallets.generate()
const addr = await arweave.wallets.jwkToAddress(jwk)
fs.writeFileSync('wallet2.json', JSON.stringify(jwk))

console.log('addr: ', addr)

await arweave.api.get(`mint/${addr}/${arweave.ar.arToWinston('100')}`)
```

transfer-ownership.js

In this script, we demonstrate how to transfer ownership from one wallet to another wallet, the transfer can only occur by the owner of the NFT. Using bundleInteraction, we should not have to pay any AR, because our data is under 100k

Arlocal Version

``` js
import Arweave from 'arweave'
import { SmartWeaveNodeFactory, LoggerFactory } from 'redstone-smartweave'
import fs from 'fs'

LoggerFactory.INST.logLevel('error');

const jwk = JSON.parse(fs.readFileSync('wallet.json', 'utf-8'))
const jwk2 = JSON.parse(fs.readFileSync('wallet2.json', 'utf-8'))

const arweave = Arweave.init({
  host: 'localhost',
  port: 1984,
  protocol: 'http'
})

const target = await arweave.wallets.jwkToAddress(jwk2)

const warp = SmartWeaveNodeFactory.memCachedBased(arweave).useArweaveGateway().build()
// MAINNET const warp = SmartWeaveNodeFactory.memCached(arweave)

const contract = warp.contract('CONTRACT_HERE').connect(jwk)
await contract.writeInteraction({ function: 'transfer', target, qty: 1 })

// MAINNET await contract.bundleInteraction({ function: 'transfer', target, qty: 1 })

console.log('Transfered Ownership!')

```

## Summary

AtomicNFTs are a powerful concept and have a lot of capabilities that are just not possible with other blockchain networks. In the next workshop, we will introduce the concept of dynamic Atomic NFTs, using the Koii Attention L2 network to show how to capture and access attention/view metrics for your NFT.



