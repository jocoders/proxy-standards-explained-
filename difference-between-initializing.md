💡 **What is the difference between initializing the proxy and initializing the implementation?**  

🔹 **Proxy contract (Storage + Logic Calls)**  
- The proxy **stores all data** and calls the logic contract.  
- It **MUST be initialized** using `initialize()` to set important values like `owner`.  
- Initialization happens **after deploying the proxy**.  

🔹 **Implementation contract (Logic Only)**  
- The implementation contract **only has logic**, it does **not store data**.  
- **DO NOT initialize it!** If you call `initialize()`, it will store data in the wrong place (inside itself instead of the proxy).  
- It should **block initialization** using `_disableInitializers()`.  

🚨 **Do you need to do both?**  
✅ **Initialize the proxy** – Yes, this is required.  
❌ **Initialize the implementation** – No, it should be disabled.  

🕐 **When should it be done?**  
- After **deploying the proxy**, call `initialize()`.  
- The implementation contract should **disable its own initialization** in its constructor.  

👉 **Rule:** **Only the proxy gets initialized!** 🚀
