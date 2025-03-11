The **OpenZeppelin (OZ) upgrade tool for Hardhat** helps prevent **six common mistakes** when upgrading smart contracts:  

1. **Changing Storage Layout** – Avoids breaking data storage by ensuring variables stay in the correct order.  
2. **Removing Inherited Contracts** – Prevents removing parent contracts, which could change storage and break functionality.  
3. **Changing Function Signatures** – Ensures function names, parameters, and return types remain consistent to avoid unexpected behavior.  
4. **Adding New State Variables Before Existing Ones** – Stops reordering variables, which could corrupt stored data.  
5. **Using `selfdestruct`** – Blocks contract destruction, as it could make upgrades impossible.  
6. **Changing Constants and Immutables** – Prevents modifying values that should never change after deployment.  

These checks matter because they **protect contract data, ensure smooth upgrades, and prevent accidental loss of funds or functionality**.
