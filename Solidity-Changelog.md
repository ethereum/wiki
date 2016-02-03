 * Index access for types `bytes8`, ..., `bytes32` (only read access for now).

### 0.2.1 (2016-01-30)

 * Inline arrays, i.e. `var y = [1,x,f()];` if there is a common type for `1`, `x` and `f()`. Note that the result is always a fixed-length memory array and conversion to dynamic-length memory arrays is not yet possible.
 * Import similar to ECMAScript6 import (`import "abc.sol" as d` and `import {x, y} from "abc.sol"`).
 * Commandline compiler solc automatically resolves missing imports and allows for "include directories".
 * Conditional: `x ? y : z`
 * Bugfix: Fixed several bugs where the optimizer generated invalid code.
 * Bugfix: Enums and structs were not accessible to other contracts.
 * Bugfix: Fixed segfault connected to function paramater types, appeared during gas estimation.
 * Bugfix: Type checker crash for wrong number of base constructor parameters.
 * Bugfix: Allow function overloads with different array types.
 * Bugfix: Allow assignments of type `(x) = 7`.
 * Bugfix: Type `uint176` was not available.
 * Bugfix: Fixed crash during type checking concerning constructor calls.
 * Bugfix: Fixed crash during code generation concerning invalid accessors for struct types.
 * Bugfix: Fixed crash during code generating concerning computing a hash of a struct type.

### 0.2.0 (2015-12-02)

 * **Breaking Change**: `new ContractName.value(10)()` has to be written as `(new ContractName).value(10)()`
 * Added `selfdestruct` as an alias for `suicide`.
 * Allocation of memory arrays using `new`.
 * Binding library functions to types via `using x for y`
 * `addmod` and `mulmod` (modular addition and modular multiplication with arbitrary intermediate precision)
 * Bugfix: Constructor arguments of fixed array type were not read correctly.
 * Bugfix: Memory allocation of structs containing arrays or strings.
 * Bugfix: Data location for explicit memory parameters in libraries was set to storage.

### 0.1.7 (2015-11-17)

 * Improved error messages for unexpected tokens.
 * Proof-of-concept transcompilation to why3 for formal verification of contracts.
 * Bugfix: Arrays (also strings) as indexed parameters of events.
 * Bugfix: Writing to elements of `bytes` or `string` overwrite others.
 * Bugfix: "Successor block not found" on Windows.
 * Bugfix: Using string literals in tuples.
 * Bugfix: Cope with invalid commit hash in version for libraries.
 * Bugfix: Some test framework fixes on windows.

### 0.1.6 (2015-10-16)

 * `.push()` for dynamic storage arrays.
 * Tuple expressions (`(1,2,3)` or `return (1,2,3);`)
 * Declaration and assignment of multiple variables (`var (x,y,) = (1,2,3,4,5);` or `var (x,y) = f();`)
 * Destructuring assignment (`(x,y,) = (1,2,3)`)
 * Bugfix: Internal error about usage of library function with invalid types.
 * Bugfix: Correctly parse `Library.structType a` at statement level.
 * Bugfix: Correctly report source locations of parenthesized expressions (as part of "tuple" story).

### 0.1.5 (2015-10-07)

 * Breaking change in storage encoding: Encode short byte arrays and strings together with their length in storage.
 * Report warnings
 * Allow storage reference types for public library functions.
 * Access to types declared in other contracts and libraries via `.`.
 * Version stamp at beginning of runtime bytecode of libraries.
 * Bugfix: Problem with initialized string state variables and dynamic data in constructor.
 * Bugfix: Resolve dependencies concerning `new` automatically.
 * Bugfix: Allow four indexed arguments for anonymous events.
 * Bugfix: Detect too large integer constants in functions that accept arbitrary parameters.

### 0.1.4 (2015-09-30)

 * Bugfix: Returning fixed-size arrays.
 * Bugfix: combined-json output of solc.
 * Bugfix: Accessing fixed-size array return values.
 * Bugfix: Disallow assignment from literal strings to storage pointers.
 * Refactoring: Move type checking into its own module.

### 0.1.3 (2015-09-25)

 * `throw` statement.
 * Libraries that contain functions which are called via CALLCODE.
 * Linker stage for compiler to insert other contract's addresses (used for libraries).
 * Compiler option to output runtime part of contracts.
 * Compile-time out of bounds check for access to fixed-size arrays by integer constants.
 * Version string includes libevmasm/libethereum's version (contains the optimizer).
 * Bugfix: Accessors for constant public state variables.
 * Bugfix: Propagate exceptions in clone contracts.
 * Bugfix: Empty single-line comments are now treated properly.
 * Bugfix: Properly check the number of indexed arguments for events.
 * Bugfix: Strings in struct constructors.

### 0.1.2 (2015-08-20)

 * Improved commandline interface.
 * Explicit conversion between `bytes` and `string`.
 * Bugfix: Value transfer used in clone contracts.
 * Bugfix: Problem with strings as mapping keys.
 * Bugfix: Prevent usage of some operators.

### 0.1.1 (2015-08-04)

 * Strings can be used as mapping keys.
 * Clone contracts.
 * Mapping members are skipped for structs in memory.
 * Use only a single stack slot for storage references.
 * Improved error message for wrong argument count. (#2456)
 * Bugfix: Fix comparison between `bytesXX` types. (#2087)
 * Bugfix: Do not allow floats for integer literals. (#2078)
 * Bugfix: Some problem with many local variables. (#2478)
 * Bugfix: Correctly initialise `string` and `bytes` state variables.
 * Bugfix: Correctly compute gas requirements for callcode.

### 0.1.0 (2015-07-10)