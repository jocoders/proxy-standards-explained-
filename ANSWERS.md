### WEEK_1:
### **Question 1: The OZ upgrade tool for hardhat defends against 6 kinds of mistakes. What are they and why do they matter?**:  

1. **No Constructors** – Constructors only run in the implementation contract, not the proxy, causing lost values. Use an **initializer function** instead.  
2. **No Initial Values in Variable Declarations** – Setting values during declaration stores them in the implementation, not the proxy. **Initialize inside a function** instead.  
3. **Initializer Can Only Run Once** – Without protection, the contract can be **reinitialized** by attackers. Use the **initializer modifier** to prevent this.  
4. **No Changing Variable Types or Order** – Changing types or reordering variables **corrupts storage**. Always add new variables **at the end** of the contract.  
5. **No `selfdestruct` or `delegatecall` in Implementation** – Attackers can **destroy** the implementation or exploit **delegatecall**. Avoid both in upgradeable contracts.  
6. **No Removing Inherited Contracts** – Removing parents **shifts storage layout**, breaking the contract. Always **keep the inheritance structure stable**.
---   

### **Question 2: What is a beacon proxy used for?**:  
A **Beacon Proxy** lets many proxies share the same implementation contract. Instead of upgrading each proxy separately, you update the **Beacon contract**, and all proxies instantly use the new logic.  

**Why use it?**  
**Cheaper upgrades** – No need to upgrade each proxy individually.  
**Instant updates** – Change one address in the Beacon, and all proxies update.  

**How does it work?**  
- Each **proxy** stores the Beacon’s address, not the implementation.  
- When called, the proxy **asks the Beacon** for the latest implementation.  
- It then **delegatecalls** to that implementation while keeping its own storage.  
- To upgrade, just **update the Beacon’s implementation address**.  

**Downsides?**  
**Higher gas cost** – Each proxy must fetch the implementation from the Beacon before calling it.  
**More complexity** – Requires three contracts: **Proxy, Beacon, and Implementation**.  

**When to use?**
Best for **many proxies sharing the same logic** (e.g., user accounts in a dApp).  
Not needed for a **single proxy** (UUPS or Transparent Proxy is better).

---

### **Question 3: Why does the openzeppelin upgradeable tool insert something like uint256[50] private __gap; inside the contracts? To see it, create an upgradeable smart contract that has a parent contract and look in the parent.**
The **OpenZeppelin upgradeable tool** adds `uint256[50] private __gap;` to **reserve storage slots** for future upgrades.  

**Why is it needed?**  
**Storage layout must stay the same** – Adding a new variable in the middle **shifts storage** and **corrupts data**.  

**How does it work?**  
- `uint256[50]` **reserves 50 empty slots** in storage.  
- When upgrading, **new variables** can use these slots **without breaking the contract**.  

**Benefit?**  
**Safe upgrades** – No risk of overwriting important data.  
**Flexibility** – Allows adding new variables **without changing storage order**.

---

### **Question 4: What is the difference between initializing the proxy and initializing the implementation? Do you need to do both? When do they need to be done?**
The **proxy** must be **initialized**, but the **implementation** must **not**.  

**Proxy (Storage + Logic Calls)**  
**Must be initialized** after deployment to set values like `owner`.  
Stores all data and calls the implementation for logic.  

**Implementation (Logic Only)**  
**Should NOT be initialized** – it does not store data.  
Must use `_disableInitializers()` to prevent accidental initialization.  

**When to do it?**  
- **After deploying the proxy**, call `initialize()`.  
- **Implementation should block initialization** from the start.  

**Rule: Only initialize the proxy!** 

---

### **Question 5: What is the use for the reinitializer? Provide a minimal example of proper use in Solidity**
**Reinitializer** is used in upgradeable contracts to **initialize new features** when upgrading.  

**Why is it needed?**  
- It **only runs once per version**, so old initialization logic is not repeated.  
- It **allows safe upgrades**, adding new setup logic without overwriting old data.  
- It **prevents accidental re-initialization**, making contracts more secure.  

**How to use it?**  
When upgrading a contract, use **reinitializer(version)** to set up new features. Each version can be used **only once**, keeping upgrades safe and organized.

---

**Ethernaut 16: [solution](https://github.com/jocoders/ethernaut/blob/main/test/Preservation.t.sol)** 

**Ethernaut 24: [solution](https://github.com/jocoders/ethernaut/blob/main/test/PuzzleWallet.t.sol)** 




