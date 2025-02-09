💡 **What is it?**  
There are **two** contracts in an upgradeable system:  
1️⃣ **Proxy contract** – stores data and delegates calls to the logic.  
2️⃣ **Implementation contract** – contains the actual logic (functions).  

🚨 **What's the difference?**  
- **Initializing the proxy**: This is required! The proxy stores all data, so you must call `initialize()` on the **proxy** to set important values (e.g., owner, initial state).  
- **Initializing the implementation**: **DO NOT DO THIS!** The implementation is only logic. It does not store data. If you initialize it, the data will be stored in the implementation, not the proxy, and it will be **useless**.  

✅ **What to do?**  
- Always **initialize the proxy**.  
- Never initialize the implementation. Instead, **disable** its initializer using `_disableInitializers()` to prevent mistakes.  

📌 **When to initialize?**  
- Right **after deploying the proxy**, call `initialize()` to set up its state.  
- Do **not** call `initialize()` on the implementation contract.
