<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/deku.jpg"/>

<h1 align="center">Deku</h1>

<h3 align="center">Deku, messaging layer for cross-chains sharing same Data Availability layer</h1>

<p align="center">
Make multiple cross-chain transactions at one request. How? If rollups are sharing same DA layer, just post cross-chain transaction to DA directly.

---

🖥️ Rollup: <https://github.com/Deku-DA-project/rollup>

🖥️ Arabica (Celestia testnet): <https://github.com/Deku-DA-project/stack>

🖥️ FrontEnd: <https://github.com/Deku-DA-project/frontend>

💡 Live Site: <>

---

## Project Description

### What is Deku?

Deku aims to provide an `seamless cross-chain transaction experience` that offers users `batch multiple cross-chain transactions into one request`. By creating a membership payment system that utilizes blockchain technology --using Account Abstraction for customization, users can potentially receive discounts on gas fees and even pay gas with native tokens -- can be anything! Additionally, the ability to customize benefits based on membership tier could provide a `personalized experience for users while also allowing communities to set specific conditions for each tier`.

### Usecase

For example, lets say Gitcoin is running their own rollup. And using Deku, other rollups that uses same DA layer as Gitcoin can actually do `cross-chain public goods voting` and batch this `multiple voting transactions into one DA request`.

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/example.png" width=400/>

---

## Architecture

### User created cross-chain transactions to Deku

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step0.png" width=400/>

### Deku process transactions and post to DA layer using `Deku` namespace

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step1.png" width=400/>

### Sequencer fetch state of storage from `Deku` namespace in Rollup

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step2.png" width=400/>

### Decode data and validate signature and drop transactions that are not compatible in certain execution

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step3.png" width=400/>

### Now its same as how other rollup works. Post latest state as block to DA

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step4.png" width=400/>

### Now your two rollups have updated state in DA

<p align="center">
<img src="https://github.com/Deku-DA-project/.github/blob/main/profile/step4.png" width=400/>
