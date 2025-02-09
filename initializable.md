### **📌 What is `reinitializer` used for?**  

🔹 **`reinitializer(uint64 version)`** is used in upgradeable contracts to **initialize new features** when upgrading the contract.  
🔹 It **allows re-initialization** **ONLY IF** the contract has not been initialized to a higher version before.  
🔹 This **prevents overwriting previous initialization** but **lets new upgrades add setup logic**.  

---

### **🚨 Why is it important?**  
✅ **Safe upgrades** – You can initialize new features without re-running old initialization logic.  
✅ **Prevents accidental re-initialization** – You cannot re-run an already used version.  
✅ **Keeps upgrades flexible** – You can introduce new features safely.  

---

### **🔧 Example of `reinitializer` in Solidity**  

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

contract MyContract is Initializable {
    uint256 public value;
    string public message;

    // First initialization (only called once)
    function initialize(uint256 _value) public initializer {
        value = _value;
    }

    // Second initialization (only called in version 2)
    function initializeV2(string memory _message) public reinitializer(2) {
        message = _message;
    }
}
```

### **🛠 How does it work?**  
1️⃣ **First deployment:** Call `initialize(100)`. This sets `value = 100`.  
2️⃣ **Upgrade contract:** Add `initializeV2(string _message)`.  
3️⃣ **After upgrade:** Call `initializeV2("Hello!")`. This sets `message = "Hello!"`.  
4️⃣ **Cannot call `initializeV2` again**, because version `2` is already used!  

---

### **💡 Summary**  
🔹 **Use `reinitializer(version)`** when adding **new features in an upgrade**.  
🔹 **Each version is used only once**, so old logic is not reset.  
🔹 **Keeps upgradeable contracts safe and organized!** 🚀
