
## llvm::StoreInst Class

The public member function `Value* getPointerOperand ()` is a wrapper function for `getOperand(1)`.

```cpp title="llvm/include/llvm/IR/Instructions.h" linenums="0"
Value *getPointerOperand() { return getOperand(1); }
```


## Static Value-Flow Analysis Framework (SVF)

### Build

**Note:** SVF uses **CMake** to generate build files for compiling the project. Starting with SVF version 2.9, the framework leverages the `FILE_SET` feature introduced in **CMake version 3.23**, which allows grouping related files within a target. Therefore, to successfully generate build files for SVF version 3, you must use CMake version 3.23 or higher.