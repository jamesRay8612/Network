C How to program 9/e 
個人心得筆記 by James Ray


#
ch1. Introduction to computer and C: Linking,Loading,Exe
ch2. Intro to C Programing: memory concepts, operator
ch3. Sturctured Progarm Development: if-else, while, Formulating algorithm
ch4. Program Control: for, switch, do-while
ch5. Functions: modularizing program, function prototypes, function call stack and stack frames
ch6. Arrays: char array to store and manipulate string, automatic local array, passing array to func, sort array, search array, variable-length array
ch7. Pointers: pass by reference, pass by value, constant pointer, arrays of pointers, function pointers
ch8. Char and strings: char handling library, string-conversion function
ch9. Formatted Input/Output: streams 
ch10. Structures, Union, bit manipulation
ch11. File processing: pointer to  a FILE, fopen,feof,fprintf,fclose
ch12. Data structures: Dynamic memory management, link-list, stack, queue, trees
ch14. Preprocessor: #include, #define, Macro, # and ## operator



#

#### 程式撰寫標準 重要材料 要溫故知新: https://wiki.sei.cmu.edu/confluence/display/c/SEI+CERT+C+Coding+Standard

### ch1. Introduction to computer and C: Linking,Loading,Exe
:::success
C programs 6 phases: edit, preprocess, compile, link, load, execute
phase 1. edit: edit program on IDE, and then store program on hard disk

preprocessing: preprocessor obeys preprocessor directive(前置編譯程式指引) perform text manipulations(eg #define 做text replacement)

compile: translate C program into machine code and store on disk

linking: linker link the object code with libraries, creates exe file and store on disk (on  linux system, command to compile  and link program is "gcc"(the GNU compiler)  )

loading: load program into memory.

execution: CPU takes each instruction and execute it.
:::



### ch2. Intro to C Programing: memory concepts, operator

\ is an escape character

![escape seq](https://hackmd.io/_uploads/HJxbWX951l.png)

%d conversion specification specifies that data shoud be integer.
& is address operator which tells the memory location of variable.


:::warning
defining: 表示變數已被配置好所需的記憶體空間並可以設定初值

declartion: introduce variable and describe it's type, object, or function. (宣告變數的型別和名稱)


operator precedence table:(useful to understand pointer with increment *p++ vs ++*p vs *++p, etc )
![operator precedence](https://hackmd.io/_uploads/BJFHm7qq1l.png)
(總結: 不同優先級 就依照優先級 若同優先級就依照結合率(左至右 or 右至左))

Secure C Programing: avoid single-argument printfs(使用"%s","str-text") 避免用戶自己爽輸入搞鬼 或是用puts("str-text") 

Function puts simply displays its string argument's content, so a conversion specification would be displayed as its individual characterts
printf("Welcome to C!\n"); 改用 puts("Welcome to C!"); (但游標自動下一行)

若要游標仍同行 則printf("Welcome "); 改用 printf("%s", "Welcome ");

scanf_s and printf_s, 後續ch3會提到 能提升安全性 把warning 去除掉

The compiler usually ignores white-space characters such as tabs, newlines and spaces.
:::



### ch3. Sturctured Progarm Development: if-else, while, Formulating algorithm
pseudocode虛擬碼 用來幫助程序員思考程式執行流程的自然語言

:::warning
conditional operator ?: 是C唯一的三元運算子 related to if...else statement. 舉例 (expression)? A : B 若運算式true 就運行A part code, False 則運行B part code. (很多 細節與用法技巧在中階書 C the programing language 提到 )
:::

:::success
#### Tips: 常見ERROR要注意 
避免無限迴圈 使用loop時要確保終止條件可達成
使用變數前要先define

Conditional operators can be used in places where if…else statements cannot,
including expressions and arguments to functions (such as printf). Use expressions
of the same type for the second and third operands of the conditional operator (?:)
to avoid subtle errors. 三元運算子的 第二與第三運算原要同類 eg (expression)? "True": "False" 同為字串 避免使用上可能的錯誤

A syntax error (such as misspelling “else”) is caught by the compiler. A logic error
has its effect at execution time. A fatal logic error causes a program to fail and terminate
prematurely. A nonfatal logic error allows a program to continue executing but
to produce incorrect results. 

Empty Statement if (grade >= 60); 要避免

An uninitialized variable
contains a “garbage” value—the value last stored in the memory location reserved for
that variable. 沒初始化的變數存放 垃圾值使程式邏輯錯誤

division by zero—a fatal error
that, if undetected, would cause the program to fail (often called “crashing”).除以0會使系統崩潰

因內存限制使floating point只是近似真實值 故儘量不要比較 floating point 可能造成邏輯錯誤
Floating-
point numbers are represented only approximately by most computers. For this
reason, you also should not compare floating-point values for equality.

Only a simple variable name may be used as a ++ or -- operator’s operand.
Attempting to use the increment or decrement operator on an expression other than
a simple variable name is a syntax error—  e.g., ++(x + 1). 只有簡單變數而非複雜運算式可用increment operator
:::


控制loop iteration的截止條件 常見兩法 counter-controll(根據程式設定跑固定次數迴圈) or sentinel(哨兵)-controll (也就是flag 等到某條件觸發)

:::warning
結構化程式設計 幫你程式精簡 結構明確:  
step1. initialize variables (粗略蓋化 之後要second refinement(具體化 那些變數初始化為0 等等))
step2. input, sum , and count
step3. calculate and processing
在對各步驟做second refinement(具體化 期望這步驟做完就是完整虛擬碼).

phases in basic program: divided logically into 3 phases, initialization phase, processing phase, termination phase.
(這些phase 結合function program 把程式切成多塊 function要明確用處 (讀取的函數 就read 不要多放printf) 還有合格的寫法要 function parameter 不該太多(超過4) 太多代表此function可再細分化)
:::


顯示轉換 用(cast) 隱性轉換則是兩運算元運算時 編譯器自動幫你把小size擴展成大size (eg int數字 + double類數字 會把int 擴展成double) 

(另外筆記 C the programing language書提到 有些(ctype.h)函數回傳值寫成unsignd char 即使是(isalpha 這類return 0 or 1 的) 我理解是，因為 unsigned char 對應無號數1 byte, 即使我要的是整數 但透過unsigned char 可限制回傳值是在 0~255 轉乘int 值也在0~255 不影響)



:::danger
![increment](https://hackmd.io/_uploads/SJxLO79cyl.png)
前置運算子 會在變數access前就做運算 使程式access 新值
後置運算子 會在變數access後做運算  使同行程式access 時使用的是舊值

在配合前面的operator 優先權表  可知 *p++ = *(p++) 但若printf("%d",*p++); 因後置關係 會output的是*p value 因其access是舊值，access完後做 *(p++) pointer移到下一位在做dereference(*取值運算元)

Secure C Programing: overlofw occur when result is too large to store in the variable. 可用INT_MIN, INT_MAX等 constants 來注意overflow是否發生 這些constant defined in <limiths.h> 

It’s good practice to ensure that before you perform arithmetic calculations like the one above, they will not overflow. https://wiki.sei.cmu.edu/confluence/display/c/

Many organizations have coding standards that require code to compile without warning messages.
There are two ways to eliminate Visual C++’s scanf warnings—use scanf_s instead of scanf  很多機構會要code沒有warning 因此可用scanf_s 取代scanf處理
:::



### ch4. Program Control: for, switch, do-while

:::warning
Tips
常見error: 
for loop裡定義的control variable離開for loop後就消失，不可在外部使用此control variable 這是compilation error

Off-By-One error: 迴圈終止條件的不謹慎設定 使的與預期迴圈次數差一 ，使用control variable 用<= 關係運算子 而非 < 來避免此類錯誤

使用switch 時 應放default case 來將一切未預期的狀況放入此處處理，還有任一case裡要放break 確保只執行某一case而不是繼續往下做

#### short-circuit evalution提升效能: 短路求值，是一種邏輯運算符的求值策略。只有當第一個運算數的值無法確定邏輯運算的結果時，才對第二個運算數進行求值。例如，當AND的第一個運算數的值為false時

讓較易發生 or 較不易發生的 放在第一個運算式來提升效能In && expressions, make the condition that’s most likely to be false the leftmost
condition. In expressions using operator ||, make the condition that’s most likely to
be true the leftmost condition

#### 注意== 與=誤用的錯誤  可能導致程式邏輯錯誤
eg x=1; vs x==1; 這不會有語法錯誤(編譯器不會告訴你) 但若你要指定值而非比較 卻寫成x==1 會使程式邏輯錯誤probably causing an execution-time logic error.

#### 總是做boundary check, overflow check, scanf's return value check(function ret value check)可提升程式維護性

:::

general format of for statment:
for(initialization; loop condition; increment statement){

}

break: immediate exit from current iteration loop.
continue: skip remaining statement in that control statement's body and perform next iteration of loop.

:::success
formatting numeric output 格式化輸出, 在conversion specification %21.2f 中間的21對應欄位寬度要21 字元長度 小數點後 .2代表精準度到小數點後第二位

Assignments Have Values 連續的assignment有意義 因其右至左順序 a=b=c=0 對應到 a = (b = ( c=0 ))
EOF(a symbol whose acronym stands for "end of file") which normally has value -1 as sentinel value. Linux/Unix 系統用Ctrl+d, Windows use Ctrl+z as EOF
:::



Floating-Point Numbers Are Approximations
C’s floating-point types suffer from what is referred to as representational
error. Assuming that floating-point numbers are represented exactly (e.g.,using them in comparisons for equality) can lead to incorrect results.

:::warning
Secure C Programing 

checking function scanf's return value: 
檢查return 值和做boundary, range check提升維護性
To make your input processing more robust, check scanf’s return value to ensure
that the number of inputs read matches the number of inputs expected. Otherwise,
your program will use the values of the variables as if scanf had completed successfully.
This could lead to logic errors, program crashes or even attacks

Range checking: 
you should validate each grade to ensure that it’s in the range 0–100 by using range
checking.
:::



### ch5. Functions: modularizing program, function prototypes, function call stack and stack frames

best way to develop and maintain program is using divide and conquer:  construct program from smaller pieces, each of which is more manageable than the original program.

avoid reinventing the wheel: 使用C 標準函式庫 這些函數都已最佳化 避免自己重複研發



![math library](https://hackmd.io/_uploads/BkbBY4c5yl.png)


software reusability is a key concept in object-oriented programming languages derived from C, such as C++,

:::warning
Tips:

#### Each function should be limited to performing a single, well-defined task, and the function name should express that task 函數要限制只做一個定義清楚的工作 且函數名要清晰表明其工作 若其引用的parameter太多(超過4) 可能就是做太多工作

If you cannot choose a concise name to describe what the function
does, it may be performing too many diverse tasks. It’s usually best to break such a
function into smaller functions—a process called decomposition 若無法為函數取確切名字 可能就是做太多工作 要切更小份 called decomposition 函數分解


Choosing meaningful function names and meaningful parameter names makes programs more readable and helps avoid excessive comments.有意義且明確的函數名與有意義的參數名幫助可讀性與管理

A function requiring a large number of parameters may be performing too many tasks.需要過多參數的函數可能做太多工作

function prototype原型 宣告函數參數型態與傳回類別 
The compiler uses function prototypes to validate function calls 編譯器用函數原型來驗證函數呼叫是否正確

#### 要注意least privilege原則 ，在寫程式時 要避免不相關的function call (eg. 我可在sorting function {} 在body裡面去declare compare function 使其他不相關函式不會知道compare function存在 但function 不可定義在function內  )

The statements within braces form the function body, which also is a block. Local
variables can be declared in any block, and blocks can be nested. 
#### Functions cannot be nested—defining a function inside another function is a syntax error.

local variable區域變數 此類變數只在其宣告區域{}內存活與取用 離開其範圍就會自動消滅 (also called automatic)


You should include function prototypes for all functions to take advantage of C’s
type-checking capabilities. 提前寫函數原型的declarition對C編譯器做type checking有幫助 更容易抓錯 避免誤用

We include parameter names in function prototypes for
documentation purposes. The compiler ignores these names, so the following prototype
also is valid:
int maximum(int, int, int);

傳參數給函數要避免用 隱性轉換 因若是編譯器無轉換規則用則會出現 錯誤結果Such conversions can lead to incorrect
results if C’s usual arithmetic conversion rules are not followed. These rules specify
how values can be converted to other types without losing data.

If there’s no function prototype for a function, the compiler forms one from the first
occurrence of the function—either the function definition or a call to the function.
This typically leads to warnings or errors, depending on the compiler若沒先寫好函數原型 則編譯器從第一個function出現的地方找她資訊 但這會造成warning 

Always include function prototypes for the functions you define or use in your
program to help prevent compilation errors and warnings.
A function prototype placed outside any function definition applies to all calls to
the function appearing after the function prototype. A function prototype placed in
a function body applies only to calls in that function made after that prototype
#### 總是要寫明函數原型來避免編譯錯誤或警告 並且若函數原型A宣告在B其他函數body外部 則所有函數可CALL此 函數原型A 但若A宣告在B內部 則只有B可以CALL此函數A 這也是least privilage 原則的顯現

列舉enumeration: enum是一系列變數名存一系列常數整數 Enumeration constants help make programs more readable and easier
to maintain. Values in an enum start with 0 and are incremented by 1

寫遞迴函數時要注意終止條件 要注意base caes來避免無限迴圈
Divide large program into function promotes good software engineering.
stack: LIFO的資料結構 舉例像函數呼叫就用stack儲存變數 return addr等

extern and static declare identifiers for variables and functions of static storage
duration. Static storage duration (p. 207) variables are allocated and initialized once, before
the program begins execution
#### (Static用來定義此變數在目前檔案(program)永久存活且存取區間為此檔案內皆可)  (Extern 告訴變數在多個檔案間存在 宣告此變數存在 但在其他檔案定義)

:::

![header table](https://hackmd.io/_uploads/r1YdiEq9ke.png)
#include "" 通常是在程式碼所在目錄找對應標頭檔
#include <> 則是根據預先訂好的目錄去找標準函式庫

C只有pass by value.
pass by value就是copy argument被引用數 的值傳給參數
pass by reference則是傳遞argument的參考給function parameter 也就是改動parameter value會影響原始argument value


pass by reference 可以藉由pass by value用其值去傳指標來達成

rand()%6 可以產生偽隨機數 (因每次執行程式 他值都依樣所以偽隨機)
要達成真隨機要用 srand(time(NULL)) 後面在用rand()%6

enum, is a set of integer constants represented
by variable name.


:::success
Storage classes: auto, register, extern, static

Storage duration is the period during which an identifier exists in memory.
Scope determines where a program can reference an identifier

auto keyword declares that a variable has automatic storage
duration. Such variables are created when program control enters the block in which
they’re defined. They exist while the block is active, and they’re destroyed when program
control exits the block.

static variables, storage is allocated
and initialized only once, before the program begins execution

:::

### ch6. Arrays: char array to store and manipulate string, automatic local array, passing array to func, sort array, search array, variable-length array

:::warning
Tips:

常見error要注意:
int n[5] = {0}; 這會顯性初始化index 0 的element as 0, 其他元素則是隱性初始化為0 
因此n[5]={1}不會全為1而是index 0的元素值為1其他隱性初始成0  然後初始化的初值量超過array大小會有編譯錯誤
This explicitly initializes n[0] to 0 and implicitly initializes the remaining elements to
0. It’s a compilation error if you provide more initializers in an array initializer list
than elements in the array

#define name replacetext 這個巨集define 只是做文字的替換 
因此在替換時要注意運算式優先順序等問題 要用括號包好確保替換後程式邏輯正確
還有不要在macro後面加; 會導致compile error, 例如define後加這個會使 "所有" name都換成replace text (eg xxnamexx -> xxrep-textxx)

不要誤用assign在符號常數(上行不能用name = replace text )會編譯錯誤
好的習慣是把name 全用大寫 (eg SIZE) 表明是預定義好的符號常數 且也更容易觀察到 不易誤用


因為C本身不提供boundary check 故程式員總要去驗證input, return value, validate index(array subscript)等 避免意外狀況

C裡面所有字串 "text"其實存的是 text + '\0' 這叫null character 最後一位都是'\0'
因此你宣告一個char a[20] 只能存19個字元 一定要留最後一位的空間給\0
為避免輸入產生buffer overflow 我們會在scanf裡面用%19s 告訴編譯器只讀19個 確保最後一個留給\0

A static local variable exists for the program’s duration but is visible only in the function body
使用static 區域變數確保只在此函數區間取用 但又不會離開就消失 下次進要重建的問題 此舉提升效能

傳陣列 與傳函數 C默認都是pass by reference 會傳記憶體位址過去給使用的函數
An array’s name evaluates to the address in memory of the array’s first element

void modifyArray(int b[], size_t size) 第二項參數size_t 是本機器所能容納建立物件的最大尺寸 (eg size_t of int = 4 byte, size_t of char = 1 byte depend on machine) 簡單看可以當成unsigned int 在參數裡用size_t 為了不access 錯誤(負)index 也為了protable可攜性 
使此程式帶到另一機器也能跑

#### 若是函數不打算改動參數值 就該用const (short for “constant”) can prevent a function from modifying an argument. 這也是least privilege 原則的顯現  加強維護性 除錯 

建立動態二維陣列 scanf a,b , int arr[a][b] 時 因動態建立 要知道其大小要用sizeof處理 element個數則是 sizeof(arr)/sizeof(arr[0])

Secure C Programing:
總是做Bounds Checking for Array Subscripts

使用更安全scanf_s performs checks to ensure that it does not write beyond the
end of the array.   舉例 scanf_s("%19s", myString, 20); Function scanf_s requires two arguments for each %s in
the format string: 需要兩引數 數據本體 與 數據數量

:::







### ch7. Pointers: pass by reference, pass by value, constant pointer, arrays of pointers, function pointers
:::success

*用在宣告時 代表式指標變數 指向記憶體位置 
*用在其他時候 是取值運算子(dereference operator) 使取出該指標指向位址存放的數值

在讀指標是 我們是由右向左 eg int *countPtr; 會說countPtr是一個指標指向int類物件 
在命名時 好習慣也重要 namePtr or pName or p_name 都可明確表示是指標

Pointers should be initialized when they’re defined 使用前要定義好 (分配空間分配值) 
&是取址運算子 取出變數的記憶體位址 eg int a=5; int *aPtr = &a; 初始化就賦值是好習慣  *aPtr = NULL;等等(NULL at <stddef.h>)

%p 是格式化字串 用來顯示記憶體位址的字串

Use pass-by-value unless the caller explicitly requires the called function
to modify the argument variable’s value in the caller. This prevents accidental modification
of the caller’s arguments and is another example of the principle of least privilege.

:::


:::warning
const qualifier enables you to inform the compiler that a particular variable’s
value should not be modified, thus enforcing the principle of least privilege. This can
reduce debugging time and prevent unintentional side effects, making a program
more robust, and easier to modify and maintain.

four ways to pass to a function a pointer to data以下四種方法傳指標給函數 依照 存取權高至低 來分
• a non-constant pointer to non-constant data. 指標可亂指到任一變數
• a constant pointer to non-constant data. 常數指標 只能指像一固定位址 但那位址的變數可變 非常數
• a non-constant pointer to constant data. 指標 能指向不同位址 但該位址只能放常數變數
• a constant pointer to constant data. 指向固定位址且存常數

Placing function prototypes in the definitions of
other functions enforces the principle of least privilege by restricting proper function
calls to the functions in which the prototypes appear.

指標的運算: 合法有
1.++,--
2.+,+= 配上整數值
3.-,-= 配上整數值
4.subtracting one pointer from another—meaningful only when both pointers
point into the same array. 指標相減 只在兩指標指向同個陣列時有意義  要注意指標不能相加
所以quick sort 裡 若用mid,low,high為指標  mid = (low+high)/2 錯誤 但可以 mid = low + (high-low)/2 (C the programing language書有提到)

寫程式常遇到 指標++到新位址  eg vPtr to 3016(arr[4]), 此時可方便的用vPtr -= 4; reset vPtr to 3000(arr[0]) 這當然也要注意boundary check 避免邏輯錯誤

Dereferencing a void * pointer is a syntax error.

只在兩指標指向同一陣列時 指標間比較才有意義 否則是邏輯錯誤 還有在指標間比較有一用處 是判斷指標是否是NULL pointer
You can compare pointers using equality and relational operators, but such comparisons
are meaningful only if the pointers point to elements of the same array; otherwise,
such comparisons are logic errors.
A common use of pointer
comparison is determining whether a pointer is NULL.

pointer/offset notation 會自動依據data type大小 做offset eg *(aPtr+3) 跟 a[3]一樣  然後括號必要有 因為前面提的運算子優先表

指標陣列
A common use of an array of pointers is to form an
array of strings, referred to simply as a string array.
舉例: const char *suit[4] = {"Hearts", "Diamonds", "Clubs", "Spades"};
![arr-pointer](https://hackmd.io/_uploads/r1ve1Li9Jg.png)
even though a char * array is fixed in size, it can point to character strings of any length 可彈性變字串長度 


:::


:::danger
A pointer to a function contains the
address of the function in memory. Pointers to functions can be passed to functions,
returned from functions, stored in arrays, assigned to other function pointers of the
same type and compared with one another for equality or inequality.
int (*compare)(int a, int b);  括號必要有 才會是function pointer 這東西好處就是 不必重寫過多函數 例如sorting比較可分 小至大acesding 或大致小desending  我可以傳遞寫好的acesding function 給function pointer in soring paramter 就可改變排序做法



:::


### ch8. Char and strings: char handling library, string-conversion function
:::success
Character handling library <ctype.h> function receives an unsigned char as argument
![char-lib](https://hackmd.io/_uploads/rJG-kFsqye.png)
最常用就 isdigit, isalpha, islower, tolower, isspace,這些
這些函數收字元為參數 回傳0代表此字元不為lower,alpha等等 回傳1代表此字元是lower,alpha 


String-Conversion Functions:
![str-to-long](https://hackmd.io/_uploads/rJ031Ko51x.png)
The function receives two arguments—a string (char *) and a
pointer to a string (char **).
舉例就是 : return值為 字串中變成數字的部分  第一個參數為字串原值 第二參數變成字串去掉數字的剩餘部分
The string "51.2% are admitted" is converted to the
double value 51.20 and the string "% are admitted"


Standard Input/Output Library Functions (in <stdio.h>)
![stdio](https://hackmd.io/_uploads/SJbDZtsc1l.png)
Each function receives an unsigned char (represented as an int)
or EOF as an argument. As we discussed in Chapter 4, characters are often manipulated
as integers because a character in C is a one-byte integer. EOF’s value is typically
–1.

sprintf to print formatted data into char array s.
sscanf, which works like scanf but reads formatted
data from a string

String manipulation: (copy, contaenting, comparing, searching, etc)
![str=m1](https://hackmd.io/_uploads/BJK-EYjqye.png)
![str-m2](https://hackmd.io/_uploads/HkJfEKj9Jl.png)
![str-m3](https://hackmd.io/_uploads/SyQENti9Jx.png)

常見錯誤: It’s a logic error if you do not append
a terminating null character to strncpy’s first argument when the third argument is
less than or equal to the second argument’s string length.


字串搜尋:
char *strchr(const char *s, int c);
Locates the first occurrence of character c in string s. If c is found, strchr returns a pointer
to c in s. Otherwise, a NULL pointer is returned.

char *strstr(const char *s1, const char *s2);
Locates the first occurrence in string s1 of string s2. If the string is found, strstr returns a
pointer to the string in s1. Otherwise, it returns NULL.

Memory Functions of the String-Handling Library:
![mem-1](https://hackmd.io/_uploads/SkDR4Yscyg.png)
![mem-2](https://hackmd.io/_uploads/SkhR4Fockx.png)

初始化更有效率 可用memset
Function memset copies the value of the byte in its second argument into the first n
bytes of the object pointed to by its first argument, where n is specified by the third
argument.  
舉例: char string1[15] = "BBBBBBBBBBBBBB";
printf("string1 after memset = %s\n", (char *) memset(string1, 'b', 7)); //結果為bbbbbbbBBBBBBB


size_t strlen(const char *s);
Returns the length of string s—that is, the number of characters preceding
the string’s terminating null character.


:::

### ch9 forrmatted I/O


:::warning

![print-int](https://hackmd.io/_uploads/Sk4KDFjq1x.png)



%e,%E 以指數化形式表示 eg 1200.53 = 1.20053 * 10^3 表示成1.20053E+03  (E+03對應10^3)
%f,%F 正常浮點數表示
%p 顯示記憶體位址
uses %% to display the % character

Function printf also provides flags to supplement its output formatting capabilities.
![printf-option](https://hackmd.io/_uploads/rkPt8Kickg.png)

常見錯誤: 要注意對齊問題 eg %4f 代表1~1234正常向右對齊 但12345會多出5超出四個字元寬度使對齊錯誤
%10f 右對齊 字元十格寬  ,  %-10f 左對齊 字元十格寬

prefixes a space to the positive number with the space flag. This is useful
for aligning positive and negative numbers with the same number of digits
eg: printf("% d\n% d\n", 547, -547);
printf("%+09d\n", 452); // 結果 +00000452


Scan Sets: a set of characters enclosed
in square brackets, [], and preceded by a percent sign in the format control string. A
scan set scans the characters in the input stream, looking only for those characters that
match characters contained in the scan set.
scanf("%8[aeiou]", z); // search for set of characters
scanf("%8[^aeiou]", z); // inverted scan set can scan for characters not contained in the scan set.

Skipping Characters in an Input Stream:  11-10-1999 to discard the dashes in the input, use the statement
scanf("%d-%d-%d", &month, &day, &year);

:::


### ch10. Structures, Union, bit manipulation
Struct: 一些具相關性的變數集合 (aggregate)，成員享有不同記憶體空間
typedefs: 建立 資料型別的 別稱

union: 變數集合但是所有成員共享一個記憶體空間，故一次只能用其中一種變數

bit fields: 用unsigned int or int規定你結構(struct or union)的成員儲存空間的bits數 用來幫助 pack information tightly.

enumerations: 一串由辨識子表示的整數

pointers and structures facilitate forming data structures such as linked lists, queues, stacks and trees

:::warning
A struct type may not contain a variable of its own struct type (which is a compilation error), but it may contain a pointer to that struct type 結構不能有成員是自己結構 但可有指標 來指向自己類型的結構 這類稱為self-referential struct
eg: struct employee {
char Name[20];
struct employee *managerPtr; // pointer
} example1, example2,*examplePtr;
上例的struct 後面接的是struct tag 結構標籤表明此結構名 而中括號後可直接創立結構example1, 也可不創立 寫成{...};

結構可執行的運算: 
assigning one struct variable to another of the same type
或使用&取指運算子到struct
  取用結構成員
  sizeof算結構byte數
  zero initializing a struct variable in its definition. eg struct employee ex1 = {};
  
結構不可比較 (==, !=) because structure members
may not be stored in consecutive bytes of memory

access 結構成員使用:
structure member operator (.),或是  (via structure variable) eg. ex1.name
structure pointer operator (->)   (via pointer to structure) eg. exPtr->name

對結構來說 pass by reference 用指標傳物件記憶體位址 效能更好 (因不用複製)

Programmers often use typedef to define a structure type, so a structure tag is not require.

eg: typedef struct {
const char *face;
const char *suit;
} Card;

Using typedefs can help make a program more readable and maintainable. Often, typedef is used to create synonyms for built-in types
使用tepedefs 幫助程式提升可讀性與管理姓 常用於建同義詞 

for union, you can reference only one member—and thus only one type—at a time (存取錯誤成員在union中有邏輯錯誤 eg int mem.a有值 但access mem.b)




聯合union允許的運算與struct類似 assigning a union to another union of the same type, 用&取址, 取用其成員via . or, -> ,還有 zero-initializing the union

As with a struct definition, a union definition simply creates
a new type. It does not reserve any memory until you use the type to create variables

putchar(value & displayMask ? : ); 神奇點是 他不須說明三元運算子中 另兩part 而是你不寫 就根據 做and mask的結果為0 or 1 去做output 算是程式特點?

As a C programmer, you’ll tend to work across many hardware architectures, and sometimes unsigned ints will be stored in smaller or larger numbers of bits. C程式員 常需要protability code (32位元系統 vs 64位元系統)

因此 下面原code 要增加可攜性(protable and generic) 可把for loop的31, 32 迴圈條件 改成
CHAR_BIT * sizeof(unsigned int) - 1  (對應32位元系統的 31  或64位元系統的63)
The symbolic constant CHAR_BIT (defined in <limits.h>) represents the number of bits in a byte (normally 8).
```cpp
1 // fig10_04.c
2 // Displaying an unsigned int in bits
3 #include <stdio.h>
4
5 void displayBits(unsigned int value); // prototype
6
7 int main(void) {
8 unsigned int x = 0; // variable to hold user input
9
10 printf("%s", "Enter a nonnegative int: ");
11 scanf("%u", &x);
12 displayBits(x);
13 }
14
15 // display bits of an unsigned int value
16 void displayBits(unsigned int value) {
17 // define displayMask and left shift 31 bits
18 unsigned int displayMask = 1 << 31;
19
20 printf("%10u = ", value);
21
22 // loop through bits
23 for (unsigned int c = 1; c <= 32; ++c) {
24 putchar(value & displayMask ? : );
25 value <<= 1; // shift value left by 1
26
27 if (c % 8 == 0) { // output space after 8 bits
28 putchar();
29 }
30 }
31
32 putchar();
33 }

```

You can specify the number of bits in which to store an unsigned or signed integral member of a struct or union. Known as bit fields
eg
struct bitCard {
    unsigned int face : 4;
    unsigned int suit : 2;
    unsigned int color : 1;
};

列舉enumeration:
Assigning a value to an enumeration constant after it’s been defined is a syntax error. 在列舉定義後 改值不合法
You should use only uppercase letters in enumeration constant names to make them stand out in a program and as a reminder that enumeration constants are not variables 列舉名字應該全大寫 來突出他 就像有些constant stacic 或是define的名字全大寫

Anonymous structs and unions can be nested in named structs and unions.
They can be accessed directly through an object of the enclosing
type
eg:
struct myStruct{
int test;
    struct {#anonymous struct
        int test2;
    };
}object;
access object.test1; object.test2;

Secure C Programing:
Because of boundary alignment requirements, a struct variable’s
size is not necessarily the sum of its members’ sizes. Always use sizeof to determine a struct variable’s number of bytes.

You must not compare struct, but you can compare thier indivudal member.

Complex type declarations, such as those for function pointers,
can be difficult to read. You should use typedef to create self-documenting
type names that make your programs more readable

:::






### ch11. File processing: pointer to  a FILE, fopen,feof,fprintf,fclose

#### some interesting concept: everything in UNIX tpye system is a FILE, such as stdin, stdout, stderr as file pointer ( chap7). Watch C the programing language for deeper look! 

fgetc, like getchar, reads one character from the file specified by
its FILE pointer argument.

fputc, like putchar, writes the character in its first argument to the file specified
by the FILE pointer in its second argument. fputc('a', stdout)

fopen takes two arguments: filename, file open mode.
w indicates open the file for writing.

open file with a separate FILE pointer, file pointer cfPtr is NULL. If it’s NULL, the file could not be opene 

Using feof to Check for the End-of-File Indicator, You should close each file as soon as it’s no longer needed.
This frees resources for which other users or programs may be waiting

Using fprintf to Write to a File, Using fclose to Close a File 

![common-err-file](https://hackmd.io/_uploads/BJghg0aqJl.png)

```cpp=
1 // fig11_02.c
2 // Reading and printing a sequential file
3 #include <stdio.h>
45
int main(void) {
6 FILE *cfPtr = NULL; // cfPtr = clients.txt file pointer Fig. 11.2
7
8// fopen opens file; exits program if file cannot be opened
9 if ((cfPtr = fopen("clients.txt", "r")) == NULL) {
10 puts("File could not be opened");
11 }
12 else { // read account, name and balance from file
13 int account = 0; // account number
14 char name[30] = ""; // account name
15 double balance = 0.0; // account balance
16
17 printf("%-10s%-13s%s\n", "Account", "Name", "Balance");
18 fscanf(cfPtr, "%d%29s%lf", &account, name, &balance);
19
20 // while not end of file
21 while (!feof(cfPtr)) {
22 printf("%-10d%-13s%7.2f\n", account, name, balance);
23 fscanf(cfPtr, "%d%29s%lf", &account, name, &balance);
24 }
25
26 fclose(cfPtr); // fclose closes the file
27 }
28 }


```
Resetting the File Position Pointer: rewind(cfPtr); 
repositions the file position pointer to the beginning (byte 0) of the file

Random-Access File: 
fwrite(&number, sizeof(int), 1, fPtr);

always writes four bytes (on a system with four-byte integers) from the int variable
number to the file represented by fPtr

fwrite and fread Can Write and Read Arrays

Positioning the File Position Pointer with fseek
file position pointer for the file referenced by cfPtr to the
byte location calculated by
(client.account - 1) * sizeof(struct clientData)



```cpp
1 // fig11_05.c
2 // Writing data randomly to a random-access file
3 #include <stdio.h>
4
5 // clientData structure definition
6 struct clientData {
7 int account;
8 char lastName[15];
9 char firstName[10];
10 double balance;
11 }; // end structure clientData
12
13 int main(void) {
14 FILE *cfPtr = NULL; // accounts.dat file pointer
15
16 // fopen opens the file; exits if file cannot be opened
17 if ((cfPtr = fopen("accounts.dat", "rb+")) == NULL) {
18 puts("File could not be opened.");
19 }
20 else {
21 // create clientData with default information
22 struct clientData client = {0, "", "", 0.0};
23
24 // require user to specify account number
25 printf("%s", "Enter account number (1 to 100, 0 to end input): ");
26 scanf("%d", &client.account);
27
28 // user enters information, which is copied into file
29 while (client.account != 0) {
30 // user enters last name, first name and balance
31 printf("%s", "Enter lastname, firstname, balance: ");
32
33 // set record lastName, firstName and balance value
34 fscanf(stdin, "%14s%9s%lf", client.lastName,
35 client.firstName, &client.balance);
36
37 // seek position in file to user-specified record
38 fseek(cfPtr, (client.account - 1) *
39 sizeof(struct clientData), SEEK_SET);
40
41 // write user-specified information in file
42 fwrite(&client, sizeof(struct clientData), 1, cfPtr);
43
44 // enable user to input another account number
45 printf("%s", "\nEnter account number: ");
46 scanf("%d", &client.account);
47 }
48
49 fclose(cfPtr); // fclose closes the file
50 }
51 }


```


fseek(cfPtr, (client.account - 1) *
39 sizeof(struct clientData), SEEK_SET);

The symbolic constant SEEK_SET indicates that fseek
should move the file position pointer relative to the beginning of the file

![redirction](https://hackmd.io/_uploads/H1LrZtJi1x.png)



Secure C Programming:
used functions fprintf and fscanf to write text
to and read text from files


### ch12. Data structures: Dynamic memory management, link-list, stack, queue, trees

動態記憶體管理 : 使用malloc, free, sizeof等來動態記憶體管理
malloc成功時 會回傳 void* pointer to the allocated memory. 失敗則回傳NULL  

Tips:
總是做檢查 malloc是否回傳NULL 還有 當不在用此動態記憶體時 要free此記憶體空間來避免 "memory leak" 

callc and realloc for creating and modifying the size of dynamic arrays.

link list:
pass the pointer by reference. Passing a pointer’s address creates a pointer to a pointer, this is sometimes called double indirection. This is a complex
notion that requires careful programming.

```cpp!
insert(ListNodePtr *sPtr, char value){
    ListNodePtr newPtr = malloc(sizeof(ListNode));

    while(curPtr != NULL && val > curPtr->data){
        prePtr = curPtr;
        curPtr = curPtr->next;
    }
}
```

Stack: LIFO data structure
```cpp
70 void push(StackNodePtr *topPtr, int info) {
71     StackNodePtr newPtr = malloc(sizeof(StackNode));
73     // insert the node at stack top
74     if (newPtr != NULL) {
75         newPtr->data = info;
76         newPtr->nextPtr = *topPtr;
77         *topPtr = newPtr;
78     }
79     else { // no space available
80         printf("%d not inserted. No memory available.\n", info);
81     }
82 }

85 int pop(StackNodePtr *topPtr) {
86     StackNodePtr tempPtr = *topPtr;
87     int popValue = (*topPtr)->data;
88     *topPtr = (*topPtr)->nextPtr;
89     free(tempPtr);
90     return popValue;
91 }

```

Queue: FIFO data structure, push at tail, pop at head
```cpp!
72 void enqueue(QueueNodePtr *headPtr, QueueNodePtr *tailPtr, char value) {
73 QueueNodePtr newPtr = malloc(sizeof(QueueNode));
74
75 if (newPtr != NULL) { // is space available?
76 newPtr->data = value;
77 newPtr->nextPtr = NULL;
78
79 // if empty, insert node at head
80 if (isEmpty(*headPtr)) {
81 *headPtr = newPtr;
82 }
83 else {
84 (*tailPtr)->nextPtr = newPtr;
85 }
86
87 *tailPtr = newPtr;
88 }
89 else {
90 printf("%c not inserted. No memory available.\n", value);
91 }
92 }
93
94 // remove node from queue head
95 char dequeue(QueueNodePtr *headPtr, QueueNodePtr *tailPtr) {
96 char value = (*headPtr)->data;
97 QueueNodePtr tempPtr = *headPtr;
98 *headPtr = (*headPtr)->nextPtr;
99
100 // if queue is empty
101 if (*headPtr == NULL) {
102 *tailPtr = NULL;
103 }
104
105 free(tempPtr);
106 return value;
107 }


```

:::danger
Secure C Programing: 

Pointers should always be initialized with NULL or
the address of a valid item in memory

When you free dynamically allocated
memory, immediately assign the pointer either NULL or a valid address.

To ensure that you don’t attempt to deallocate
the same memory more than once, immediately set a pointer to NULL after the
call to free. Freeing a NULL pointer has no effect.

You should always ensure that malloc did not return NULL before
attempting to use the pointer that stores malloc’s return value
:::

### ch14. Preprocessor: #include, #define, Macro, # and ## operator

The C preprocessor executes before each program compiles. It:
• includes other files into the file being compiled,
• defines symbolic constants and macros,
• conditionally compiles program code and
• conditionally executes preprocessor directives.

Preprocessor directives begin with #.

The #define directive creates:
• symbolic constants—constants represented as identifiers, and
• macros—operations defined as symbols.

:::warning
macro: 根據一系列預定義的規則 批量處理 替換文字
因為只做字串替換，要避免替換後邏輯錯誤 就需用括號包起來確保運算正確You should always enclose
macro arguments in parentheses in the replacement-text to prevent logic errors.
相比用macro做運算  最好用function來做更安全
:::


#undef preprocessor directive
A macro’s or symbolic constant’s scope is from its definition
until it’s undefined with #undef, or until the end of the source file.

:::success
Do Not Place Expressions with Side Effects in Macros
會改動值的運算(eg x++)要避免放在macro 因可能造成重複運算

Conditional Compilation條件編譯:
Every #if construct ends with #endif.
常用在避免重複載入標頭檔Conditional preprocessor directives are frequently used to prevent header files from
being included multiple times in the same source file

#if !defined(MY_CONSTANT)
    #define MY_CONSTANT 0
#elif
#endif

Conditional compilation is sometimes used as a debugging aid 條件編譯也常用來除錯 當你要除錯 就在#ifdef前面加 #define DEBUG 若不除錯就去掉 則編譯器自動無視#ifdef DEBUG這段

#ifdef DEBUG
printf("Variable x = %d\n", x);
#endif
:::



#error tokens
prints an implementation-dependent message, including the tokens specified in the
directive.

'#' operator converts a replacement-text token to a string surrounded by quotes.
eg. #define HELLO(x) puts("Hello, " #x); 只要用HELLO(John) 就會字串替換成 puts("Hello, John"); 

"##" operator concatenates two tokens.

The #line preprocessor directive causes the subsequent source-code lines to be
renumbered, starting with the specified constant integer value.


Predefined Symbolic Constants:
![Predef-symb-const](https://hackmd.io/_uploads/HJiOoWCcyx.png)

Assertions (用來幫助除錯的工具 測試變數是否正確值)
The assert macro—defined in <assert.h>—tests an expression’s value at execution
time. If the value is false (0), assert prints an error message and terminates the program
by calling function abort of the general utilities library (<stdlib.h>).

If the symbolic constant NDEBUG is defined, subsequent assertions in the source file
are ignored.  eg. #define NDEBUG

:::success
Secure C Programing:
#define CIRCLE_AREA(x) ((PI) * (x) * (x))
is an unsafe macro, because it evaluates its argument x more than once. This can cause
subtle errors. If the macro argument contains side effects—such as incrementing a
variable or calling a function that modifies a variable’s value—those side effects would
be performed multiple times.
For example, if we call CIRCLE_AREA as follows:
double result = CIRCLE_AREA(++radius);

:::

