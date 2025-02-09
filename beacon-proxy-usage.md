### **🛡️ What is a Beacon Proxy used for?**  

A **Beacon Proxy** allows **many proxies** to share the **same implementation contract**. Instead of upgrading each proxy **one by one**, you can **upgrade all at once** by changing the implementation address in a **Beacon contract**.  

🚨 **Why is this useful?**  
- If you have **many proxies** (e.g., for user accounts in a game), upgrading them **individually is expensive**.  
- With a Beacon Proxy, you **change one address** in the Beacon, and **all proxies start using the new logic** instantly.  

✅ **How does it work?**  
1. Each proxy **stores the address of the Beacon**, not the implementation.  
2. When a proxy gets a transaction, it **asks the Beacon for the latest implementation address**.  
3. The proxy then **delegatecalls** to that implementation, using its own storage.  
4. To upgrade, the **owner updates the Beacon’s implementation address**—all proxies are instantly upgraded.  

⚠️ **Downside?**  
- Slightly **higher gas costs** because each proxy **must read the implementation from the Beacon** before delegatecall.  
- **More complex setup** since you need **three contracts**: **Proxy, Beacon, and Implementation.**  

**🛠️ When to use?**  
✅ If you have **many proxies that need the same logic** and want **easy upgrades**.  
❌ Not useful for **a single proxy** (UUPS or Transparent Proxy is better).
