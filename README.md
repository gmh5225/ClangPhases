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

## Parser
```batch
clang -fmodules -fsyntax-only -Xclang -ast-dump main.cpp
```
->>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
```C++
Program arguments:clang.exe -fmodules -fsyntax-only -Xclang -ast-dump main.cpp
TranslationUnitDecl 0x2088ca7afa8 <<invalid sloc>> <invalid sloc>
|-CXXRecordDecl 0x2088ca7b7f8 <<invalid sloc>> <invalid sloc> implicit struct _GUID
| `-TypeVisibilityAttr 0x2088ca7b8b0 <<invalid sloc>> Implicit Default
|-TypedefDecl 0x2088ca7b928 <<invalid sloc>> <invalid sloc> implicit __int128_t '__int128'
| `-BuiltinType 0x2088ca7b570 '__int128'
|-TypedefDecl 0x2088ca7b998 <<invalid sloc>> <invalid sloc> implicit __uint128_t 'unsigned __int128'
| `-BuiltinType 0x2088ca7b590 'unsigned __int128'
|-TypedefDecl 0x2088ca7bd58 <<invalid sloc>> <invalid sloc> implicit __NSConstantString '__NSConstantString_tag'
| `-RecordType 0x2088ca7baa0 '__NSConstantString_tag'
|   `-CXXRecord 0x2088ca7b9f0 '__NSConstantString_tag'
|-CXXRecordDecl 0x2088ca7bdb0 <<invalid sloc>> <invalid sloc> implicit class type_info
| `-TypeVisibilityAttr 0x2088ca7be80 <<invalid sloc>> Implicit Default
|-TypedefDecl 0x2088ca7bef8 <<invalid sloc>> <invalid sloc> implicit size_t 'unsigned long long'
| `-BuiltinType 0x2088ca7b190 'unsigned long long'
|-TypedefDecl 0x2088e50c070 <<invalid sloc>> <invalid sloc> implicit __builtin_ms_va_list 'char *'
| `-PointerType 0x2088ca7bf50 'char *'
|   `-BuiltinType 0x2088ca7b050 'char'
|-TypedefDecl 0x2088e50c0e0 <<invalid sloc>> <invalid sloc> implicit __builtin_va_list 'char *'
| `-PointerType 0x2088ca7bf50 'char *'
|   `-BuiltinType 0x2088ca7b050 'char'
|-FunctionDecl 0x2088e50c2e0 <main.cpp:2:1, line:4:1> line:2:5 used add 'int (int, int)'
| |-ParmVarDecl 0x2088e50c150 <col:9, col:13> col:13 used a 'int'
| |-ParmVarDecl 0x2088e50c1d0 <col:16, col:20> col:20 used b 'int'
| `-CompoundStmt 0x2088e50c4d8 <col:23, line:4:1>
|   `-ReturnStmt 0x2088e50c4c8 <line:3:2, line:1:15>
|     `-BinaryOperator 0x2088e50c4a8 <line:3:9, line:1:15> 'int' '+'
|       |-BinaryOperator 0x2088e50c460 <line:3:9, col:13> 'int' '+'
|       | |-ImplicitCastExpr 0x2088e50c430 <col:9> 'int' <LValueToRValue>
|       | | `-DeclRefExpr 0x2088e50c3f0 <col:9> 'int' lvalue ParmVar 0x2088e50c150 'a' 'int'
|       | `-ImplicitCastExpr 0x2088e50c448 <col:13> 'int' <LValueToRValue>
|       |   `-DeclRefExpr 0x2088e50c410 <col:13> 'int' lvalue ParmVar 0x2088e50c1d0 'b' 'int'
|       `-IntegerLiteral 0x2088e50c480 <line:1:15> 'int' 3389
`-FunctionDecl 0x2088e50c770 <line:5:1, line:8:1> line:5:5 main 'int (int, const char **)'
  |-ParmVarDecl 0x2088e50c508 <col:10, col:14> col:14 argc 'int'
  |-ParmVarDecl 0x2088e50c620 <col:20, col:37> col:32 argv 'const char **':'const char **'
  `-CompoundStmt 0x2088e50ca78 <col:40, line:8:1>
    |-DeclStmt 0x2088e50ca28 <line:6:2, col:19>
    | `-VarDecl 0x2088e50c878 <col:2, col:18> col:6 a 'int' cinit
    |   `-CallExpr 0x2088e50c9e0 <col:10, col:18> 'int'
    |     |-ImplicitCastExpr 0x2088e50c9c8 <col:10> 'int (*)(int, int)' <FunctionToPointerDecay>
    |     | `-DeclRefExpr 0x2088e50c978 <col:10> 'int (int, int)' lvalue Function 0x2088e50c2e0 'add' 'int (int, int)'
    |     |-IntegerLiteral 0x2088e50c928 <col:14> 'int' 1
    |     `-IntegerLiteral 0x2088e50c950 <col:17> 'int' 2
    `-ReturnStmt 0x2088e50ca68 <line:7:2, col:9>
      `-IntegerLiteral 0x2088e50ca40 <col:9> 'int' 0
```

## IRCodenGen
```batch
clang -S -emit-llvm main.cpp
```
->>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
```LLVM
; ModuleID = 'main.cpp'
source_filename = "main.cpp"
target datalayout = "e-m:w-p270:32:32-p271:32:32-p272:64:64-i64:64-f80:128-n8:16:32:64-S128"
target triple = "x86_64-pc-windows-msvc19.31.31104"

; Function Attrs: mustprogress noinline nounwind null_pointer_is_valid optnone uwtable
define dso_local noundef i32 @"?add@@YAHHH@Z"(i32 noundef %0, i32 noundef %1) #0 {
  %3 = alloca i32, align 4
  %4 = alloca i32, align 4
  store i32 %1, i32* %3, align 4
  store i32 %0, i32* %4, align 4
  %5 = load i32, i32* %4, align 4
  %6 = load i32, i32* %3, align 4
  %7 = add nsw i32 %5, %6
  %8 = add nsw i32 %7, 3389
  ret i32 %8
}

; Function Attrs: mustprogress noinline norecurse nounwind null_pointer_is_valid optnone uwtable
define dso_local noundef i32 @main(i32 noundef %0, i8** noundef %1) #1 {
  %3 = alloca i32, align 4
  %4 = alloca i8**, align 8
  %5 = alloca i32, align 4
  %6 = alloca i32, align 4
  store i32 0, i32* %3, align 4
  store i8** %1, i8*** %4, align 8
  store i32 %0, i32* %5, align 4
  %7 = call noundef i32 @"?add@@YAHHH@Z"(i32 noundef 1, i32 noundef 2)
  store i32 %7, i32* %6, align 4
  ret i32 0
}
```










