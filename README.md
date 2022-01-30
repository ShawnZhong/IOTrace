# IOTrace

A library for tracing function calls and I/O code path.

## Usage

1. Add `-finstrument-functions -Wl,--export-dynamic` to the compiler flags.

2. Run your program with `libtrace.so` loaded.

## Example

Sample program: [sample/basic.cpp](sample/basic.cpp)

Compile and run:

```shell
g++ sample/basic.cpp -O2 -finstrument-functions -Wl,--export-dynamic
make # compile libtrace.so
LD_PRELOAD=./build/libtrace.so ./a.out
```

Output:

```cpp
> main(...)
 > test3(char const*)
  > test2(char const*, int, int)
   > test(char const*)
    > open(/dev/null, 0) = 3 in 4838ns
     = backtraces:
     = [3] test(char const*)
     = [2] test2(char const*, int, int)
     = [1] test3(char const*)
     = [0] main(...)
    < open(...)
    > read(3, 0x0, 0) = 0 in 426ns
     = backtraces:
     = [3] test(char const*)
     = [2] test2(char const*, int, int)
     = [1] test3(char const*)
     = [0] main(...)
    < read(...)
    > close(3) = 0 in 710ns
     = backtraces:
     = [3] test(char const*)
     = [2] test2(char const*, int, int)
     = [1] test3(char const*)
     = [0] main(...)
    < close(...)
   < test(char const*)
  < test2(char const*, int, int)
 < test3(char const*)
< main(...)
```

## Sample Traces

- [sample/leveldb.cpp](sample/leveldb.cpp): [traces/leveldb-full.txt](https://raw.githubusercontent.com/ShawnZhong/FuncTrace/main/traces/leveldb-full.txt)
  , [traces/leveldb-io.txt](https://raw.githubusercontent.com/ShawnZhong/FuncTrace/main/traces/leveldb-io.txt)

  ```
  make leveldb_trace
  ./build-sample/leveldb_trace 2> traces/leveldb-full.txt
  TRACE_PRINT_FN_TRACE=0 ./build-sample/leveldb_trace 2> traces/leveldb-io.txt
  ```


