# ClangPhases
Recording clang phases

## Test code:
```C++
#define MAGIC 3389
int add(int a, int b) {
	return a + b + MAGIC;
}
int main(int argc, const char* argv[]) {
	int a = add(1, 2);
	return 0;
}
```

## Print phases
```batch
clang -ccc-print-phases main.cpp
```
->>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
```C++
Program arguments:clang.exe -ccc-print-phases main.cpp
            +- 0: input, "main.cpp", c++
         +- 1: preprocessor, {0}, c++-cpp-output
      +- 2: compiler, {1}, ir
   +- 3: backend, {2}, assembler
+- 4: assembler, {3}, object
5: linker, {4}, image
```

## Preprocessor
```batch
clang -E main.cpp
```
->>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
```C++
Program arguments:clang.exe -E main.cpp
# 1 "main.cpp"
# 1 "<built-in>" 1
# 1 "<built-in>" 3
# 385 "<built-in>" 3
# 1 "<command line>" 1
# 1 "<built-in>" 2
# 1 "main.cpp" 2

int add(int a, int b) {
 return a + b + 3389;  // -> macro has been replaced by preprocessor
}
int main(int argc, const char* argv[]) {
 int a = add(1, 2);
 return 0;
}
```
