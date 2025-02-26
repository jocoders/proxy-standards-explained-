# Proxy Contract Questions and Answers

## ✅ If a proxy calls an implementation, and the implementation self-destructs in the function that gets called, what happens?
- **Short Answer:** The proxy continues to work, as `selfdestruct` no longer removes contract code after Cancun (EIP-6780).
- **Explanation:**
  1. `selfdestruct` **does not remove** code if the contract was deployed in a previous transaction.
  2. Only **storage is cleared**, but `delegatecall` does not use implementation storage.
  3. The proxy still works, even if the implementation self-destructs.

## ✅ If a proxy calls an empty address or an implementation that was previously self-destructed, what happens?
### 1️⃣ If the proxy calls `address(0)` or an address without code:
- `delegatecall` returns `false`.
- If `require(success)` is used, the call **reverts**.
- Otherwise, the call returns empty `bytes` (`0x`).

### 2️⃣ If the proxy calls a contract that previously self-destructed:
- After Cancun (EIP-6780), the contract **code remains**, but storage is cleared.
- `delegatecall` still works, but **storage-dependent data is reset to `0`**.

📌 **Conclusion:**
✅ `delegatecall` to an empty address **fails**.  
✅ `delegatecall` to a self-destructed contract **works**, but storage is reset.

## ✅ If a user calls a proxy, which makes a delegatecall to A, and A makes a regular call to B, who is `msg.sender`?
- **A’s `msg.sender`** = Proxy  
- **B’s `msg.sender`** = Proxy  
- **Proxy’s `msg.sender`** = User  

📌 **Reason:** `delegatecall` keeps `msg.sender` unchanged, while a regular `call` sets `msg.sender` to the caller.

## ✅ If a proxy makes a `delegatecall` to A, and A does `address(this).balance`, whose balance is returned?
- **Short Answer:** The proxy’s balance.
- **Explanation:** `delegatecall` executes in the proxy's context, so `address(this)` refers to the proxy.

## ✅ If a proxy makes a `delegatecall` to A, and A calls `codesize`, what size is returned?
- **Short Answer:** The proxy’s code size.
- **Explanation:** Since `delegatecall` executes in the proxy's context, `codesize` checks the proxy’s bytecode.

## ✅ If a `delegatecall` is made to a function that reverts, what happens?
- **Short Answer:** `delegatecall` returns `false`, and the transaction can revert if `require(success)` is used.
- **Explanation:**
  - `success = false`
  - `calldata = 0x`
  - Transaction **reverts** if not handled properly.

## ✅ Under what conditions does the OpenZeppelin Proxy.sol overwrite the free memory pointer? Why is it safe to do this?

- **Short Answer:** The proxy overwrites the free memory pointer when copying calldata and returndata in `_delegate()`. It is safe because Solidity execution does not continue after inline assembly.
- **Explanation:**
  1. The proxy uses `calldatacopy(0, 0, calldatasize())` and `returndatacopy(0, 0, returndatasize())`, overwriting memory from `0x00`.
  2. This does not affect Solidity's free memory pointer (`0x40`), as the execution ends in `return(0, returndatasize())` or `revert(0, returndatasize())`.
  3. Each transaction starts with a clean memory, so overwriting is safe.

## ✅ If a delegatecall is made to a function that reads from an immutable variable, what will the value be?

- **Short Answer:** The value returned will be from the implementation contract, not the proxy.
- **Explanation:**
  1. Immutable variables are stored **in the contract’s bytecode**, not in storage.
  2. `delegatecall` executes the logic of the implementation but in the proxy’s context.
  3. Since the bytecode is from the implementation, the immutable variable holds its original deployment value.

## ✅ If a delegatecall is made to a contract that makes a delegatecall to another contract, who is msg.sender in the proxy, the first contract, and the second contract?

- **Short Answer:**
  - **Proxy:** `msg.sender = user`
  - **Implementation A (first contract):** `msg.sender = user`
  - **Implementation B (second contract):** `msg.sender = user`
- **Explanation:**
  1. `delegatecall` preserves `msg.sender`, meaning each contract executes as if the original caller made the request.
  2. Since both `A` and `B` use `delegatecall`, `msg.sender` remains the same across all calls.
  3. The proxy, implementation A, and implementation B all see `msg.sender` as the original user who initiated the transaction.


