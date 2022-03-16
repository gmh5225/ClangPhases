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

## Lexer
```batch
clang -fmodules -fsyntax-only -Xclang -dump-tokens main.cpp
```
->>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
```C++
Program arguments:clang.exe -fmodules -fsyntax-only -Xclang -dump-tokens main.cpp
int 'int'        [StartOfLine]  Loc=<main.cpp:2:1>
identifier 'add'         [LeadingSpace] Loc=<main.cpp:2:5>
l_paren '('             Loc=<main.cpp:2:8>
int 'int'               Loc=<main.cpp:2:9>
identifier 'a'   [LeadingSpace] Loc=<main.cpp:2:13>
comma ','               Loc=<main.cpp:2:14>
int 'int'        [LeadingSpace] Loc=<main.cpp:2:16>
identifier 'b'   [LeadingSpace] Loc=<main.cpp:2:20>
r_paren ')'             Loc=<main.cpp:2:21>
l_brace '{'      [LeadingSpace] Loc=<main.cpp:2:23>
return 'return'  [StartOfLine] [LeadingSpace]   Loc=<main.cpp:3:2>
identifier 'a'   [LeadingSpace] Loc=<main.cpp:3:9>
plus '+'         [LeadingSpace] Loc=<main.cpp:3:11>
identifier 'b'   [LeadingSpace] Loc=<main.cpp:3:13>
plus '+'         [LeadingSpace] Loc=<main.cpp:3:15>
numeric_constant '3389'  [LeadingSpace] Loc=<main.cpp:3:17 <Spelling=main.cpp:1:15>>
semi ';'                Loc=<main.cpp:3:22>
r_brace '}'      [StartOfLine]  Loc=<main.cpp:4:1>
int 'int'        [StartOfLine]  Loc=<main.cpp:5:1>
identifier 'main'        [LeadingSpace] Loc=<main.cpp:5:5>
l_paren '('             Loc=<main.cpp:5:9>
int 'int'               Loc=<main.cpp:5:10>
identifier 'argc'        [LeadingSpace] Loc=<main.cpp:5:14>
comma ','               Loc=<main.cpp:5:18>
const 'const'    [LeadingSpace] Loc=<main.cpp:5:20>
char 'char'      [LeadingSpace] Loc=<main.cpp:5:26>
star '*'                Loc=<main.cpp:5:30>
identifier 'argv'        [LeadingSpace] Loc=<main.cpp:5:32>
l_square '['            Loc=<main.cpp:5:36>
r_square ']'            Loc=<main.cpp:5:37>
r_paren ')'             Loc=<main.cpp:5:38>
l_brace '{'      [LeadingSpace] Loc=<main.cpp:5:40>
int 'int'        [StartOfLine] [LeadingSpace]   Loc=<main.cpp:6:2>
identifier 'a'   [LeadingSpace] Loc=<main.cpp:6:6>
equal '='        [LeadingSpace] Loc=<main.cpp:6:8>
identifier 'add'         [LeadingSpace] Loc=<main.cpp:6:10>
l_paren '('             Loc=<main.cpp:6:13>
numeric_constant '1'            Loc=<main.cpp:6:14>
comma ','               Loc=<main.cpp:6:15>
numeric_constant '2'     [LeadingSpace] Loc=<main.cpp:6:17>
r_paren ')'             Loc=<main.cpp:6:18>
semi ';'                Loc=<main.cpp:6:19>
return 'return'  [StartOfLine] [LeadingSpace]   Loc=<main.cpp:7:2>
numeric_constant '0'     [LeadingSpace] Loc=<main.cpp:7:9>
semi ';'                Loc=<main.cpp:7:10>
r_brace '}'      [StartOfLine]  Loc=<main.cpp:8:1>
eof ''          Loc=<main.cpp:9:1>
```













