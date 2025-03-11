**Question 1: The OZ upgrade tool for hardhat defends against 6 kinds of mistakes. What are they and why do they matter?**:  

1. **No Constructors** 🚫🏗️ – Constructors only run in the implementation contract, not the proxy, causing lost values. Use an **initializer function** instead.  
2. **No Initial Values in Variable Declarations** ❌🔢 – Setting values during declaration stores them in the implementation, not the proxy. **Initialize inside a function** instead.  
3. **Initializer Can Only Run Once** 🔒✅ – Without protection, the contract can be **reinitialized** by attackers. Use the **initializer modifier** to prevent this.  
4. **No Changing Variable Types or Order** 🚨📦 – Changing types or reordering variables **corrupts storage**. Always add new variables **at the end** of the contract.  
5. **No `selfdestruct` or `delegatecall` in Implementation** 🚀🔥 – Attackers can **destroy** the implementation or exploit **delegatecall**. Avoid both in upgradeable contracts.  
6. **No Removing Inherited Contracts** ⛔📜 – Removing parents **shifts storage layout**, breaking the contract. Always **keep the inheritance structure stable**.
---   

**Question 2: What is a beacon proxy used for?**:  
A **Beacon Proxy** lets many proxies share the same implementation contract. Instead of upgrading each proxy separately, you update the **Beacon contract**, and all proxies instantly use the new logic.  

### **Why use it?**  
✅ **Cheaper upgrades** – No need to upgrade each proxy individually.  
✅ **Instant updates** – Change one address in the Beacon, and all proxies update.  

### **How does it work?**  
- Each **proxy** stores the Beacon’s address, not the implementation.  
- When called, the proxy **asks the Beacon** for the latest implementation.  
- It then **delegatecalls** to that implementation while keeping its own storage.  
- To upgrade, just **update the Beacon’s implementation address**.  

### **Downsides?**  
⚠️ **Higher gas cost** – Each proxy must fetch the implementation from the Beacon before calling it.  
⚠️ **More complexity** – Requires three contracts: **Proxy, Beacon, and Implementation**.  

### **When to use?**  
✅ Best for **many proxies sharing the same logic** (e.g., user accounts in a dApp).  
❌ Not needed for a **single proxy** (UUPS or Transparent Proxy is better).
---
   
