<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/deku.jpg"/>

<h1 align="center">Deku, Shared DA as a messaging channel</h1>

<h3 align="center">Deku is a new idea of sovereign rollup architecture design that adds messaging layer for cross-sovereign rollups that share the same Data Availability layer</h1>

- Eventually, what rollup guarantee, is hard finality when data is posted to the DA-layer. `Deku` approached directly to the DA-layer.
- Make multiple cross-chain transactions at only one request to the DA-layer.
- Let's think differently. Lower the dimension where a user is sending transactions. We can first post on DA and later guarantee finality through rollup sequencing.

---

🖥️ Rollup: <https://github.com/Deku-DA-project/rollup>

🖥️ Arabica (Celestia testnet): <https://github.com/Deku-DA-project/stack>

🖥️ FrontEnd: <https://github.com/Deku-DA-project/frontend>

---

## Project Description

### What is Deku?

In this project, Deku, we introduce a new approach to accessing cross-chain data if they are sovereign rollups that use the same DA layer.

Deku aims to provide an `seamless cross-chain transaction experience` that offers users `batch multiple cross-chain transactions into one request`. By creating a membership payment system that utilizes blockchain technology --using Account Abstraction for customization, users can potentially receive discounts on gas fees and even pay gas with native tokens -- can be anything! Additionally, the ability to customize benefits based on membership tier could provide a `personalized experience for users while also allowing communities to set specific conditions for each tier`.

### Usecase

For example, let's say Gitcoin is running its own rollup. And using Deku, other rollups that use the same DA layer as Gitcoin can actually do `cross-chain public goods voting` and batch these `multiple voting transactions into one DA request`.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/example.png" width=400/>

---

## Architecture Deep Dive

Let's see what happens when the user wants to create a cross-chain bridging transaction about Soverign Rollup A and B using Celestia as DA-layer.

### (1) User-created cross-chain transactions to Deku

- Let's assume rollup A and B use Celestia as a DA-layer.
- I generate multiple cross-chain bridge transactions in Deku, and Deku will send it as 1 DA RPC request ( /submit_pbf ).

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step0.png" width=400/>

### Deku processes transactions and posts to DA-layer using `Deku` namespace

- Deku will divide each cross-chain transaction into transactions per chain.
- For example, migrating 10 eth from A to B will burn 10 eth from A and mint 10 eth from B.
- Each transaction data will be encoded and stored in `Deku`'s namespace

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step1.png" width=400/>

### Sequencer fetch state of storage from `Deku` namespace in Rollup

- Originally, rollup A sequencer only gets updated by `A` namespace, which stores their state update.
- To make Deku design accessible from A to B, each rollup's sequencer should accept not only their own state update but also accept `Deku` namespace update for data accessibility with another chain that they want.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step2.png" width=400/>

### Decode data and validate signature and drop transactions that are not compatible with certain execution

- Decode the transaction data, containing transactions that cannot execute on this rollup. We need to drop this transaction if not compatible. And executes the batch of transactions.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step3.png" width=800/>

### Now it's the same as how other rollup works. Post the latest state as a block to DA

- After you execute it, you can get the final state of A which, in this case, can include an updated account balance. And sequencer will make the block as usual and post the latest state to the DA layer. In this case, rollup's own DA layer is used to update the state.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step4.png" width=400/>

### Now your two rollups have updated state in DA

- Same for other rollups who want to access other chains' data through this architecture.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step4.png" width=400/>

---

## Implementation and Blockers

### Implementation for the hackathon

- Need at least 2 rollup deployed by rollkit. ( using Celestia as DA in common )
- Each rollup needs to modify the sequencer Golang code to accept `Deku` namespace from celestia
- Each rollup needs to deploy the account and fund balance on one chain
- Using `Deku` frontend, make two transactions 1) send 10 eth from A to B, 2) send 5 eth from B to A
- after submitting transactions, `transaction processor` needs to process ( divide one cross-chain transaction into per chain and encode it and sign the message ), and Celestia light client node that is running locally will send transactions to DA-layer using `Deku` namespace.
- Check out whether sequencer on A and B gets transaction data from `Deku` namespace, and if they get it, they need to decode it, validate the signature, and execute the transactions that are compatible with their execution layer.
- Check out the sequencer on A and B will generate a block of the latest state and post it in DA-layer again
- If we check the balance on accounts a and b has changed on DA through a light node, then this cross-chain transaction is finalized. (Success)

### What blockers we faced while implementing

- We failed to integrate with a Celestia local devnet in different versions.
  There were different reasons why we failed for different Celestia versions.
- We tried integrating with a public devnet Arabica based on a new rc of Celestia. This means we need to use the newest version of rollkit to be compatible, but we could not start the first node because we can only start it if we have a trusted peer.
- We got CORS errors when calling the Celestia endpoints from our front end. We tried to fix it, but the Celestia node cannot add CORS headers. We tried to figure out if we could change the code of the Celestia node, but we could not find the correct place.
- Celestia has two endpoints, gateway and openRPC. Celestia plans to have gateway read-only and openRPC for writing and reading.
  We tried using openRPC first, but in the older versions, it does not return anything, and in the newest version, it is failing with a claim that the JSON is misconfigured. Then we tried gateway nevertheless and could read and write in the versions we tested.
  But due to the other blockers, we could not continue with that.

---

## Resources

🖥️ Celestia: <https://docs.celestia.org/>

🖥️ Rollkit: <https://rollkit.dev/docs/intro/>

💡 Data Availability <https://ethereum.org/en/developers/docs/data-availability/>
