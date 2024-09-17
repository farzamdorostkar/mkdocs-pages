
## llvm::StoreInst Class

The public member function `Value* getPointerOperand ()` is a wrapper function for `getOperand(1)`.

```cpp title="llvm/include/llvm/IR/Instructions.h" linenums="0"
Value *getPointerOperand() { return getOperand(1); }
```
