<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/deku.jpg"/>

<h1 align="center">Deku, Shared DA as a messaging channel</h1>

<h3 align="center">Deku is a new idea of soverign roll-up architecture design, that adding messaging layer for cross- sovergin rollups that share same Data Availability layer</h1>

- Eventually what rollup garentee hard finality is DA layer. `Deku` approached directly to DA layer.
- Make multiple cross-chain transactions at only one request to DA layer.
- Let's think different. Lower the demention where user is sending transaction. We can first post on DA, and later can garentee finality through rollup sequencing.

---

🖥️ Rollup: <https://github.com/Deku-DA-project/rollup>

🖥️ Arabica (Celestia testnet): <https://github.com/Deku-DA-project/stack>

🖥️ FrontEnd: <https://github.com/Deku-DA-project/frontend>

---

## Project Description

### What is Deku?

In this project Deku, we introducing new approach to access crosschain data if they are soveriegn rollups that using same DA layer. This project is more like

Deku aims to provide an `seamless cross-chain transaction experience` that offers users `batch multiple cross-chain transactions into one request`. By creating a membership payment system that utilizes blockchain technology --using Account Abstraction for customization, users can potentially receive discounts on gas fees and even pay gas with native tokens -- can be anything! Additionally, the ability to customize benefits based on membership tier could provide a `personalized experience for users while also allowing communities to set specific conditions for each tier`.

### Usecase

For example, lets say Gitcoin is running their own rollup. And using Deku, other rollups that uses same DA layer as Gitcoin can actually do `cross-chain public goods voting` and batch this `multiple voting transactions into one DA request`.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/example.png" width=400/>

---

## Architecture Deep Dive

Lets see what happens when user wants to create cross-chain bridging transaction about Soverign Rollup A and B using Celestia as DA layer.

### (1) User created cross-chain transactions to Deku

- Let's assume there is rollup A and B that using Celestia as DA layer.
- What I do is generate multiple cross-chain bridge transactions in Deku, and Deku will send it as 1 DA rpc request ( /submit_pbf ).

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step0.png" width=400/>

### Deku process transactions and post to DA layer using `Deku` namespace

- Deku will devide each cross-chain transaction into transactions per each chain.
- For example, migrating 10 eth from A to B will be burn 10 eth from A and mint 10 eth from B.
- Each transaction data will be encoded and store in `Deku`'s namespace

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step1.png" width=400/>

### Sequencer fetch state of storage from `Deku` namespace in Rollup

- Originially rollup A sequencer is only get update by `A` namespace, which stores their state update.
- To make Deku design accessacble from A to B, each rollup's sequencer should accept not only their own state update, but also accept `Deku` namespace update for data accessability with other chain that they want

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step2.png" width=400/>

### Decode data and validate signature and drop transactions that are not compatible in certain execution

- Decode the transaction data and this will contain transactions that cannot execute on this rollup. We need to drop this transactions if not compatible. And executes the batch of transactions.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step3.png" width=800/>

### Now its same as how other rollup works. Post latest state as block to DA

- After you execute it, you can get final state of A which in this case can include updated account balance. And Sequencer will make block as usual and post latest state to the DA layer. This case, using rollup's own DA layer that used to update state.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step4.png" width=400/>

### Now your two rollups have updated state in DA

- Same for other rollups who want to access other chain's data through this architecture.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step4.png" width=400/>

---

## Implementation and Blockers

### Implementation for hackathon

- Need at least 2 rollup, deployed by rollkit. ( using Celestia as DA in common )
- Each rollup, need to modify sequencer Golang code to accept `Deku` namespace from celestia
- Each rollup, need to deploy account and fund balance on one chain
- using `Deku` frontend, make two transaction 1) send 10 eth from A to B, 2) send 5 eth from B to A
- after submit transactions, `transaction processor` need to process ( divide one cross-chain transaction into per chain and encode it and sign the message ), and Celestia light client node that running on local will send transactions to DA layer using `Deku` namespace.
- Check out does sequencer on A and B gets transaction data from `Deku` namespace, and if they get it then need to decode it and validate signature and execute the transactions that compatible to their execution layer.
- Check out sequencer on A and B will generate block of latest state and post it in DA layer again
- if we check balance on account a and b has changed on DA through light client, then can say this cross chain transaction is finalized. ( Succeess )

### What blockers we faced while implementing

- We tried integrating with a Celestia local devnet in different versions where we failed.
  There were different reasons why we failed for different Celestia versions.
- We tried integrating with a public devnet Arabica, which is based on a new rc of Celestia. This means we need to use the newest version of rollkit to be compatible, but we could not start the first node, because we can only start it if we have a trusted peer.
- When calling the Celestia endpoints from our frontend, we got CORS errors. We tried to fix it, but the Celestia node cannot add CORS headers. We tried to figure out if we can change the code of the Celestia node, but we could not find the correct place.
- Celestia has two endpoints, gateway and openRPC. Celestia plans to have gateway read-only and openRPC for writing and reading.
  We tried using openRPC first but in the older versions it does not return anything and in the newest version it is failing with a claim that the JSON is misconfigured. Then we tried gateway nevertheless and we were able to read and write in the versions we tested.
  But due to the other blockers, we were not able to continue with that.

---

## Resources

🖥️ Celestia: <https://docs.celestia.org/>

🖥️ Rollkit: <https://rollkit.dev/docs/intro/>

💡 Data Availability <https://ethereum.org/en/developers/docs/data-availability/>
