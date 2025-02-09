### **1️⃣ No Constructors in Upgradeable Contracts**  
💡 **What is it?**  
In Solidity, constructors run **only once** when the contract is deployed. But in **proxy contracts**, the constructor **never runs**.  

🚨 **Why is it bad?**  
If you use a constructor in an upgradeable contract, it will **not execute**. Important setup (like setting an `owner`) will be **missing**.  

✅ **Solution:**  
Use an **initializer function** instead of a constructor.  

```solidity
function initialize() public initializer {
    owner = msg.sender;
}
```

---

### **2️⃣ No Initial Values for Variables**  
💡 **What is it?**  
In Solidity, you can set **default values** when declaring variables:  

```solidity
uint256 public number = 10;
```

🚨 **Why is it bad?**  
These values **only set once in the constructor**, which does **not run** in a proxy. This means your contract **will not have these values** when using a proxy.  

✅ **Solution:**  
Set all **initial values** inside the `initialize` function.  

```solidity
function initialize() public initializer {
    number = 10;
}
```

---

### **3️⃣ Always Lock the `initialize()` Function**  
💡 **What is it?**  
If `initialize()` is not locked, anyone can call it **again** and change important values.  

🚨 **Why is it bad?**  
A hacker can call `initialize()` again and **change the owner** or reset the contract.  

✅ **Solution:**  
Use OpenZeppelin’s `initializer` modifier. It **makes sure** `initialize()` can be called **only once**.  

```solidity
function initialize() public initializer {
    owner = msg.sender;
}
```

To **fully lock** the contract, disable initializers in the implementation:  

```solidity
constructor() {
    _disableInitializers();
}
```

---

### **4️⃣ Do Not Change Variable Types**  
💡 **What is it?**  
If you change a variable type in a new version, **old stored data will become invalid**.  

🚨 **Why is it bad?**  
For example, if `uint256 number;` was stored in slot 1, but in the new contract you change it to `string number;`, the old data **becomes garbage** and the contract breaks.  

✅ **Solution:**  
**Never change** the type of existing storage variables.  

❌ Wrong:  

```solidity
// Old contract
uint256 number;

// New contract (wrong!)
string number;
```

✅ Correct:  

```solidity
// Old contract
uint256 number;

// New contract (correct! just add new variable)
uint256 number;
uint256 newValue;
```

---

### **5️⃣ Do Not Change the Order of Variables**  
💡 **What is it?**  
Solidity stores variables **in order**. If you add or remove a variable **in the middle** of the contract, the storage will **shift** and break.  

🚨 **Why is it bad?**  
The contract will read the **wrong values** from storage.  

✅ **Solution:**  
- **Do not reorder** variables.  
- **Only add new variables at the end.**  

❌ Wrong:  

```solidity
// Old contract
uint256 balance;
address owner;

// New contract (wrong! added new variable in the middle)
uint256 balance;
uint256 newVariable;  // ❌ Breaks storage!
address owner;
```

✅ Correct:  

```solidity
// Old contract
uint256 balance;
address owner;

// New contract (correct! added at the end)
uint256 balance;
address owner;
uint256 newVariable;  // ✅ Safe
```

---

### **6️⃣ Do Not Use `selfdestruct` or `delegatecall` in Implementation**  
💡 **What is it?**  
- `selfdestruct` deletes a contract.  
- `delegatecall` runs another contract’s code **inside the caller’s context**.  

🚨 **Why is it bad?**  
- If the **implementation contract** is destroyed with `selfdestruct`, the proxy will **break** because it points to an **empty contract**.  
- If the implementation has a bad `delegatecall`, it might call a **malicious contract**, which could **selfdestruct the proxy**.  

✅ **Solution:**  
**Never use `selfdestruct` or `delegatecall` in the implementation contract.**  

❌ Wrong:  

```solidity
selfdestruct(payable(msg.sender));  // ❌ Never use this in implementation!
```

✅ Correct:  

```solidity
// Do nothing! Just remove selfdestruct.
```

---

### **🔥 Summary: Why Do These Mistakes Matter?**  
- **Broken contracts**: Some changes make the contract unusable.  
- **Lost data**: Wrong storage changes can **corrupt old data**.  
- **Security risks**: Hackers can **reinitialize** or **destroy** your contract.  

### **✅ How to Avoid These Mistakes?**  
- Use **`initialize()` instead of constructors**.  
- **Set all values in `initialize()`**, not in the variable declaration.  
- **Lock the initializer** to prevent re-initialization.  
- **Never change storage types**.  
- **Only add variables at the end**.  
- **Never use `selfdestruct` or `delegatecall`**.  

This is how **OpenZeppelin's upgrade tool** keeps upgradeable contracts **safe and stable**! 🚀🔥
