**Question 1: The OZ upgrade tool for hardhat defends against 6 kinds of mistakes. What are they and why do they matter?**:  

1. **No Constructors** 🚫🏗️ – Constructors only run in the implementation contract, not the proxy, causing lost values. Use an **initializer function** instead.  
2. **No Initial Values in Variable Declarations** ❌🔢 – Setting values during declaration stores them in the implementation, not the proxy. **Initialize inside a function** instead.  
3. **Initializer Can Only Run Once** 🔒✅ – Without protection, the contract can be **reinitialized** by attackers. Use the **initializer modifier** to prevent this.  
4. **No Changing Variable Types or Order** 🚨📦 – Changing types or reordering variables **corrupts storage**. Always add new variables **at the end** of the contract.  
5. **No `selfdestruct` or `delegatecall` in Implementation** 🚀🔥 – Attackers can **destroy** the implementation or exploit **delegatecall**. Avoid both in upgradeable contracts.  
6. **No Removing Inherited Contracts** ⛔📜 – Removing parents **shifts storage layout**, breaking the contract. Always **keep the inheritance structure stable**.  
