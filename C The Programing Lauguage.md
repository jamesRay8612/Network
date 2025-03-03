C The Programing Language:

# 
ch1. Introuction to C -- symblic constant, call by value, external varibale, scope
ch2. Types, Operator, Expression -- data tpye and size, declaration, relational logic bitwise operation
ch3. control flow -- if/else, for/while
ch4. function and program structure -- basic function, external variable, scope rule, recursion, preprocessor
ch5. pointer and arrays -- pointer and adresses, char pointer and function pointer, command-line arguments(argc, argv[]) 
ch6. structure -- structure and function, array of structure, pointer to structure, Typedef, union
ch7. Input and Output -- standart I/O, Formmated I/O, variable-length argument list( func(arg1, ...) ), error handling (stderr, exit)
ch8. UNIX system interface -- file descriptors(things in unix is file), low level I/O (Read/Write), Random access(Lseek) 

![redirction](https://hackmd.io/_uploads/H1LrZtJi1x.png)
#

### ch1. Introuction to C -- symblic constant, call by value, external varibale, scope


在轉換說明 conversion specifier '%' 跟 type (eg d, f)中間 可放入欄位要求(寬度 小數點後 左對齊 右對齊等)
eg:   %10.2d, %-10d


![operator precedence](https://hackmd.io/_uploads/SkgCp_gokx.png)

#define command define a symbolic constant to be a paritular string or character 依照預先定義的規則做文字替換 (要注意 括號確保運算順序 和 避免值的變動 eg x++ 可能造成重複做多次)
#define name replace-text
Notice that there is no semicolon at the end of a #define line.

此書提供copy input to output的例子中 一種是while(c!=EOF){c=getchar();} vs 另一種 while((c=getchar()) != EOF ){} 各有優劣，後者壓縮(集中 輸入條件) 
但瘋狂用會使程式難讀? 前者則是瑣碎，此書以後種寫法為主，個人理解是 重複做 且是 迴圈條件的 可寫進expression (做集中化) 也有點function那味道

++variable; more efficient than variable += 1;

long integers at least 32 bits.

連續assignment has value -- a=b=0 同議於 a= (b=0)
最好事先做declarition at begining (int a,b;) 然後definition 在要使用之前 (a=b=0; while(a ... && b...) )

parameter: function 使用的參數
argument: 傳給function使用的參數(被引數)

function prototype函數原型: 宣告function參數與回傳值
(prototype 可宣告在函數內，但只在此函數內使用(least privilage principle)，定義只能在函數外面)
總是做函數原型宣告，可讓編譯器檢測是否有誤用，幫助偵錯


In C, all function arguments are passed by value.(傳被引數複製值給函數使用，改動函數裡值不影響外面被引數)
pass by reference(傳遞被引數的地址給函數用，改動函數值會同步到被引數)

The purpose of supplying the size of an array in a declaration is to set aside storage.
在function parameter 宣告 array size 避免access 超過boundary

string end with '\0'

local variable (也叫automatic variable) existence only when the function is called, and disappears when the function is exited.


extern ( globally accessible  ,existence permanently): variables that are external toall functions, that is, variables that can be accessed by name by any function.
extern也可以file間傳 eg. file1 definition variable a; file2 use extern variable a 以及include 對應檔案

external definitions are just like definitions of local variables, but since they occur outside of
functions, the variables are external.

Definition refers to the place where the variable is created or assigned storage; declaration refers to places where the nature of the variable is
stated but no storage is allocated.
定義: 變數已建立並配好所需的記憶體空間，可以設定初值
宣告: 讓編譯器知道變數型態和語法



### ch2. Types, Operator, Expression -- data tpye and size, declaration, relational logic bitwise operation


We tend to use short names for local variables,
especially loop indices, and longer names for external variables.


strlen(s) returns the length of its character string
argument s, excluding the terminal '\0'.


a single character: 'x' is not the same as "x".
The latter is an array of characters that contains one character (the letter x) and a '\0'.

Since an argument of a function call is an expression, type conversion also takes place when
arguments are passed to functions.


But the expression ++n increments n before its value is used, while n++ increments n after its value has been used.

logic operator: & for masking(專注某特定bit值)， | for including all bits (  某一區間 全一值  ) ， ^ (  xor 專注於相異bit  ) ， ~ 取補數， 

Right shifting a signed quantity will fill with bit signs (arithmetic shift) on some machines and with 0-bits (logical shift) on others.

conditional expression, written with the ternary
operator ?:  三元運算子 類似IF-ELSE 
expr1 ? expr2 : expr3 (expr1 true then execute expr2, false execute expr3)

-> and . operator are used to access member of structure (-> 跟pointer一起用  .跟結構變數一起用)
從ch1. 運算子優先權表 要注意 &, && 這些優先權低於 == and != 
因此if(  (a&b) == 0   ) 要正確括號起來


Function calls, nested assignment statements, and increment and decrement operators cause side effects ( eg. printf("%d%d", ++n, pow(2,n)); 還有 a[i] = i++; 都要小心出邏輯錯誤)

程式精神上是寫code會depend在運算的order順序是不好的 : The moral is that writing code that depends on order of evaluation is a bad programming practice in any language


### ch3. control flow -- if/else, for/while

if-else, switch, for/while/do-while不贅述 

As a matter of good form, put a break after the last case (the default here) even though it's
logically unnecessary. Some day when another case gets added at the end, this bit of
defensive programming will save you.

The advantages of keeping loop control centralized are even more obvious when there are
several nested loops

Comma operators should be used sparingly. The most suitable uses are for constructs strongly
related to each other, as in the for loop in reverse, and in macros where a multistep
computation has to be a single expression
逗號 可以用在 一連串相關變數的assignment
eg:
```cpp
for (i = 0, j = strlen(s)-1; i < j; i++, j--)
    c = s[i], s[i] = s[j], s[j] = c;
```


### ch4. function and program structure -- basic function, external variable, scope rule, recursion, preprocessor

此書ch1~ch3 跟C how to program差不多
但ch4~ch7 難很多 也很多細節和有趣點 

return-type function-name(argument declarations)
{
    declarations and statements
}

The calling function is free to ignore the returned value. Furthermore, there need to be no
expression after return; in that case, no value is returned to the caller. Control also returns to
the caller with no value when execution falls off the end of the function by reaching the
closing right brace. It is not illegal, but probably a sign of trouble, if a function returns a value
from one place and no value from another. In any case, if a function fails to return a value, its
value is certain to be garbage.

在UNIX系統 若function放main.c , getline.c ,  strindex.c
可用cc main.c getline.c strindex.c指令
compiles the three files, placing the resulting object code in files main.o, getline.o, and
strindex.o, then loads them all into an executable file called a.out.

為確保function return type正確 除了宣告時就要宣告資料型態 還有接收的變數 也要是對應資料型態
declarations must match definitions


External Variables:(lifetime from declarition to end of file, scope is global)

External variables are defined outside of any function, and are thus potentionally available to many functions. Functions themselves are always external,
because C does not allow functions to be defined inside other functions.

If a large number of variables must be shared among functions, external variables are more
convenient and efficient than long argument lists.如果大量變數要共享於函數間，用external variable比傳argument好

stack: LIFO data structure
注意因pop 順序are not defined. 所以向 - , / 運算有差的要區別
push(pop() - pop()); // WRONG 
```cpp
case '-':
    op2 = pop();
    push(pop() - op2);
    break;
case '/':
    op2 = pop();
    if (op2 != 0.0)
        push(pop() / op2);
    else
        printf("error: zero divisor\n");
    break;
```
小總結: 這兩本書 教了許多細節 包括總是做 1. return value check, 2. boundary check, 3. range check, 4.evaluation order check 上例屬於第四個


Scopre Rules:

if an external variable is to be referred to before it is defined, or if it is
defined in a different source file from the one where it is being used, then an extern
declaration is mandatory.

There must be only one definition of an external variable among all the files that make up the
source program; other files may contain extern declarations to access it

Static Variables: 靜態變數 lifetime 靜態 scope 內部

Register Variables: 
advises the compiler that the variable in question will be heavily used. The idea is that register variables are to be placed in machine registers



ch4.9 Initialization: 

We have generally used explicit
assignments, because initializers in declarations are harder to see and further away from the point of use.

If there are fewer initializers for an array than the specified size, the others will be zero for
external, static and automatic variables. It is an error to have too many initializers. There is no
way to specify repetition of an initializer, nor to initialize an element in the middle of an array
without supplying all the preceding values as well.

preprocessor:
#define used to replace a token by anarbitrary sequence of characters.

If the filename is quoted, searching for the file typically begins where the source program was found  (include "" 就在程式所在資料夾找   <> 則在預先定義好的資料夾位置找)

The expressions areevaluated twice; this is bad if they involve side effects like increment operators or input and
output. For instance
max(i++, j++) /* WRONG */
(要注意 因define做文字替換 所以有side effect 對值做變動 可能做多次 需要避免邏輯錯誤)

Names may be undefined with #undef, usually to ensure that a routine is really a function, not a macro: eg #undef func_A  int func_A(...){...}   用undef來確保一個執行序 不適macro 而是function

#### Conditional Inclusion:
可以藉由控制語句控制 前處理本身 使其在前處理時期就做運算 This provides a way to include code selectively, depending on the value
of conditions evaluated during compilation

#if line evaluates a constant integer expression
例如下例 確保hdr只include一次
```cpp
#if !defined(HDR)
#define HDR
// contents of hdr.h go here 
#endif

```
The #ifdef and #ifndef lines are specialized forms that test whether a name is defined.


### ch5. pointer and arrays -- pointer and adresses, char pointer and function pointer, command-line arguments(argc, argv[]) 
:::success
指標是 指向記憶體位址 的變數，同時陣列名 對應到指向第一個陣列中元素的記憶體位址 
so its possble to set arrayPtr = array or arrayPtr = &array[0];

& 是取址運算子 取出對應物件的記憶體位址

Pointer cannot be applied to expressions, constants, or register variables.

* 在宣告時 代表是指標變數
* 在其他時候 是dereferencing operator 取值運算子

藉由在function parameter use pass by pointer 可以同步改動被引入數(argument)的值

指標 可接受的運算有 1. 指標++,-- 2.指標+常數整數(對應指向下一個元素(要配合alignment do offset)) 3.assign address 給指標 4. 不可相加 但指向同一陣列的指標可相減(代表元素個數) 

指標還可以assigning or comparing to 0

指標 不可比較(只在指向同一陣列時可比p < q若 ture表示p是指向陣列中更前面的元素) 不可相加 

There is one difference between an array name and a pointer that must be kept in mind.
A pointer is a variable, so pa=a and pa++ are legal. But an array name is not a variable;
constructions like a=pa and a++ are illegal.


char s[]; and char *s; are equivalent. we prefer the latter because it says more explicitly that the variable is a
pointer.
:::

:::warning
5.4 Address Arithmetic:
alloc(n), returns a pointer to n consecutive character positions(n個連續1 byte空間 因char correspond to 1 byte)

afree(p), releases the storage thus acquired so it can be reused
later.

C guarantees that zero is never a valid address for data, so a return value of zero can be used to signal an
abnormal event. eg char function(){} return 0就可拿來代表有例外狀況



size_t is the unsigned integer type returned by the sizeof operator
機器理論上所能容納 最大物件的bytes數(usually unsigned int)， 常用於 確定物件大小  陣列offset避免boundary 出錯和增加portability (32 machine to 64 machine still work)

All the pointer manipulations automatically take into account the size of the objects pointed to. (eg. charPtr vs intPtr do ++ 會自動offset 做對應alignment)

5.5 char pointer and function:

```cpp!
char amessage[] = "now is the time"; /* an array */
char *pmessage = "now is the time"; /* a pointer */

```
amessage只是個容量夠那字串的陣列，可改字元 但空間就限制在這範圍
pmesage是指向這字串的指標 可指向別位址 但無法改動字元值
On the other hand, pmessage is a pointer, initialized to point to a
string constant; the pointer may subsequently be modified to point elsewhere, but the result is
undefined if you try to modify the string contents.
```cpp!
version1.
void strcpy(char *s, char *t)
{
while ((*s = *t) != '\0') {
s++;
t++;
}
}
version2.
while ((*s++ = *t++) != '\0')
    ;
version3.
while (*s++ = *t++)
    ;
```
version3. 就是一個合格有經驗的C程式員會寫的 (集中化輸入 輸出 條件判斷) (運算不依賴order evaluation) (總是做range, return, boundary value check等等)

:::

Pointer vs multi-dimensional array:
pointer array more flexiable access, more stroage flexiable (指向size 3 指標陣列 each size 10 vs 固定連續 30 size陣列) 



:::danger
5.10 Command-line Argument
to pass command-line arguments or parameters to a program when it begins executing.

first (argc for argument count). 
second (argv for argument vector) is a pointer to an array of character strings that contain the arguments, one per string

By convention, argv[0] is the name by which the program was invoked, so argc is at least 1
![argv](https://hackmd.io/_uploads/r1h8tMzj1g.png)


5.11 pointer to functions:
it is possible to define pointers to functions, which can be assigned, placed in arrays, passed to functions, returned by functions.
增加靈活性用途  透過pass pointer function 改變要使用的運算 (eg +,-,*,/ 或是 sorting用遞增 or遞減)
eg
```cpp!
void qsort(void *lineptr[], int left, int right,int (*comp)(void *, void *));
int numcmp(char *, char *);


```

int (*comp)(void *, void *)
which says that comp is a pointer to a function that has two void * arguments and returns an int.

:::


### ch6. structure -- structure and function, array of structure, pointer to structure, Typedef, union


structer: collection of variables, grouped in a single name. each member has its own stroage space

union: collection of variables, but only 1 member has stroage space at a time.

struct typename {...} (variable(for declarition)) ;

uses . with structer variable to access member 
uses -> with pointer to structer to access member

允許的運算 類似pointer: 1. copying it or assigning to it as a unit, taking & operator, accessing its member

不允許做compare，而操作結構可pass memeber separatly, pass entire structure, pass pointer to its.

跟陣列一樣當要傳遞一個大物件，用pass by pointer better than pass by value

. and -> 與 postfix++ (還有(), [] ))has highest precedence and they are left to right.

完全由運算子優先權 可看出
pp = &structure;
(*pp).x 必要括弧 (.要配variable not pointer 所以要先dereference)
pp->x


eg.
struct {
int len;
char *str;
} *p;

thus ++p->len increments len, not p 

*p->str fetches whatever str points to.
*p->str++ increments str after
accessing whatever it points to (just like *s++); (*p->str)++ increments whatever str points to; and *p++->str increments p after accessing whatever str points to.


#### ch6.3 array of structure
結構陣列好處包刮 把多個陣列元素 封裝成一個結構處理 就可只需一個結構陣列
eg repalce  char *key[100]; int value[100]; by
struct struct_name {
    char *key;
    int value;
} struct_variable [100]= {
"auto", 0,
"break", 0,
"case", 0,
"char", 0,
"const", 0,
"continue", 0,
"default", 0,
/* ... */
"unsigned", 0,
"void", 0,
"volatile", 0,
"while", 0
};

It would be more
precise to enclose the initializers for each "row" or structure in braces eg ({ "auto", 0 },)

sizeof  可用來算物件的大小 
算element數量可用 sizeof(struct_variable)/ sizoof(struct_name)

#### ch6.4 pointer to structure:

用結構改寫binary search 使用結構的member 做search比較時 給例子有提到

mid = (low+high) / 2 // WRONG 
because the addition of pointers is illegal. Subtraction is legal, however, so high-low is the number of elements, and thus
mid = low + (high-low) / 2  //correct
sets mid to the element halfway between low and high
where low, high ,mid are structure pointer 

同樣pointer to structure 做++ 也會自動alignemnt offset to next element of structure (而且別假設是offset成員size合 因若char + int = 5byte 但結構會alignemnt 8 byte  not 5 byte 所以一定適用sizeof算)

自我指向(不可指向自己 而是要指向同類型的結構)的結構 (常用於tree, graph等)
Occasionally, one needs a variation of self-referential structures: two structures that refer to each other. The way to handle this is:

```cpp
struct t {
...
struct s *p; /* p points to an s */
};
struct s {
...
struct t *q; /* q points to a t */
};


```
malloc returns NULL if no space is available.
Storage obtained by calling malloc may be freed for re-use by calling free; (好的程式習慣 總是坐free memory space when no longer needed)


Typedef: 幫資料型別取別名  it merely adds a new name for some existing type.

two main reasons for using typedefs.  (為了portability 跟better documentation)

The first is to parameterize a program against portability problems

second purpose of typedefs is to provide better documentation for a program - a type called Treeptr may be easier to understand than one declared only as a pointer to a
complicated structure


### ch7. Input and Output -- standart I/O, Formmated I/O, variable-length argument list( func(arg1, ...) ), error handling (stderr, exit)

In many environments, a file may be substituted for the keyboard by using the < convention for input redirection: eg prog < infile 

otherprog | prog
runs the two programs otherprog and prog, and pipes the standard output of otherprog into
the standard input for prog

<是用來input >是用來output |是做pipe把前者輸出給後者輸入
Again,output can usually be directed to a file with >filename: if prog uses putchar,
prog >outfile
will write the standard output to outfile instead. If pipes are supported,
prog | anotherprog
puts the standard output of prog into the standard input of anotherprog.

![argument type](https://hackmd.io/_uploads/Skhgj4Xiyx.png)

sprintf does the same conversions as printf does, but stores the output in a string

int sprintf(char *string, char *format, arg1, arg2, ...);

fprintf does the same conversions as printf does, but stores the output in a file

ch7.3 Variable-length argument list(變動參數列表)

int printf(char *fmt, ...);
where the declaration ... means that the number and types of these arguments may vary. The
declaration ... can only appear at the end of an argument list.

<stdarg.h> contains a set of macro definitions that define how to step through an argument list.(使用va_start 初始化 參數指標ap 到第一個unnamed arguemnt)





### ch8. UNIX system interface -- file descriptors(things in unix is file), low level I/O (Read/Write), Random access(Lseek) 

















