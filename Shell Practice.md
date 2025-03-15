### Shell Script 實作練習

面試時談到自己正研讀Linux kernel networking，而被問到shell script有無甚麼經驗或成果，發現在讀完IPv6, 802.11, The C Programing lauguage後，還須在Linux有更多實戰經驗為好，尤其netfiter, iptables, nftables(較新且RED HAT建議)，開始實作練習。

:::warning
我目標: 面試時，能講出成果，例如我用shell 看netstat 然後作甚麼自動化，防火牆iptables之類?

:::
### Introduction:

Intro to shell and advantage:
基礎概念
Shell: 是一個command line提供用戶與kernel溝通  
好處像是: 1.建立腳本自動執行重複任  2.它可以存取系統程式與功能  

shell script 的執行，至少需要有 r 的權限，若需要直接指令下達，則需要擁有 r 與 x 的權限  

回憶Linux指令 mkdir建立目錄, cd變更至其他目錄(/絕對位址, ..上層, 直接打相對位址), cp複製檔案, touch建檔, ls列出目錄, ll列出文件訊息, cat串接(配>(從stdin輸入進後面檔案etc) or >> or |)或顯示(直接cat filename), find查詢目錄或檔案, rm刪除, type查命令, --help顯示內建命令簡要訊息, man查系統手冊關於此(可顯示外建)命令  

vim test.sh用vim編輯器寫script(一般指令mode 打i進編輯mode, ESC回一班mode, 一班指令mode打:wq寫並退出等等)  

~用戶主目錄, /根目錄, ..回上頁, ./當前目錄
su切換超級帳戶  (都會開一個新shell環境 要離開用exit)

$ ' ' 不解析變數 可打出$var  
$ "" 會解析變數的文字 保持$功能    
${var} 變數  
$(cmd) 執行指令 並把結果作替換 ，命令間用;隔開(bash指令可連續下達 只要透過;隔開)    
$ `` 指令  
$(()) 數學計算式  

comd1 && comd2 若comd1回傳成功時，comd2執行  
comd1 || comd2 若comd1失敗才執行comd2  

[]中括號可取代test，內部要留空白 eg \[ \"\$var \" == \" \$var2\" \] 




```shell!
seq [OPTION]... LAST
seq [OPTION]... FIRST LAST
seq [OPTION]... FIRST INCREMENT LAST
```


netstat指定 可以查詢目前主機開啟的網路埠口(service port)
(eg use netstat -tuln 取得目前主機有啟動的服務 以 封包格式 本地ip:port  遠端ip:port  是否監聽  四個類型的格式顯示)

double dash (--) is used in most Bash built-in commands and many other commands to signify the end of command options, after which only positional ("non-option") arguments are accepted.

再來是shell script注意事項:  
1. 指令由上而下，從左至右，若讀取到Enter符號就執行指令，可用\[Enter]作換行，#是註解  
2. 直接指令執行(sh檔要有rx權限 可用chmod改)  
    絕對路徑 /home/shell_practice/hello.sh  
    相對路徑 若已再/home/shell_practice/，則使用./hell.sh  
    
3.以bash程式來執行，透過bash helloworld<span/>.sh 或是 sh shell.sh來執行  

#### hello shell
接下來開始寫 hello<span/>.sh   
:::spoiler 
使用<span/>避免helloworld.sh在hackmd自動變成超連結... 
:::

1. 注意第一行為#!/bin/bash 宣告這個script使用的shell名稱  
2. 然後寫script建議養成習慣用#說明 1.內容與功能 2.版本 3.建檔日與歷史紀錄 來幫助程式維護  
3. 重要環境變數宣告 PATH(可直接下達外部指令，而不用寫絕對路徑)與LANG(輸出資訊)。  
4. 告知執行結果，使用exit 0告知正常結束

```shell!
#!/bin/bash
# Program: Says Hell World
# 
# History: 2025/03/11
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "Hello World! \a \n"
exit 0
```
-e: 激活轉譯字符，若出現\b刪前一字\n換行\t插tab等會加以處理，而不當一班文字輸出。  
export聲明的變數 就是環境變數，可用unset 刪除環境變數，env 查看環境變數 (env | grep PATH)  

為了維護性，要再script 檔頭紀錄運作需要的環境變數宣告與設定  

#### 寫個簡單腳本:變數由使用者決定，以及四則運算  
使用declare定義變數的類型，或是用$( 計算式 )進行數字運算，另外bash shell預設僅支援到整數資料。
```shell!
#!/bin/bash
#Program: use 2 input int to evaluate multiply
#History 2025/03/15 
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "input 2 num to do multiply\n"
read -p "first num: " firstNum
read -p "second num: " secondNum
total = $(( ${firstNum} * ${secondNum} ))
#delcare -i total = ${firstNum} * ${secondNum}
echo -e "\n result: ${total}"

```

-p 后面跟提示信息，即在输入前打印提示信息  
回憶 $()與 `</span>` 都是作命令替換用的  

$[] 與 $(()) 支持數學運算  

#### Script的執行差異:   
  
直接指令下達(絕對路徑/相對路徑 或是${PATH}內)，以及用bash或sh下達腳本時  
該Script都使用一個新的bash環境來執行腳本內的指令，也就是script在子程序bash內執行  

![script-diff](https://hackmd.io/_uploads/SJr-j4MnJe.png)  

利用source來執行腳本: 可以在父程序中執行      

使用test指令測試 某物是否存在  
test -e filename && echo "exist" || echo "not exist" 測該檔名是否存在  (&&前成功 就&&後 而||前失敗 就作||後指令)      
-f 檔名是否存在且為file?
-d 檔名是否存在且為directory?
-S 檔名是否存在且為Socket檔
-r -w -x 檔名是否存在且有 讀 寫 執行權限?
-z string 判定字串是否為0
-eq 判斷兩數值是否相等
多重條件判斷-a 兩條件同時成立回傳true,  -o 兩條件任一成立傳true, ! 反向條件判斷  

在來我們寫 script自動幫我們測 輸入的檔名是否存在與判斷類型屬性
```shell!
#!/bin/bash
#Program: input file name, let program check 1.exist? 2.file/dir? 3. file permission
#History: 2025/03/16 
#step1.
echo -e "input filename, check exist or not?\n"
read -p "input a filename: " filename
test -z ${filename} && echo "input name!" && exit 0
#step.2
test ! -e ${filename} && echo "not exist" && exit 0
#step.3
test -f ${filename} && filetype = "regular file"
test -d ${filename} && filetype = "directory"
test -r ${filename} && perm = "readable"
test -w ${filename} && perm = "${perm} writable"
test -x ${filename} && perm = "${perm} executable"
#step4. 
echo "filename: ${filename} is a ${filetype}"
echo "And permission are: ${perm}"

```
除了test外，還可以用判斷符號 [] 來進行資料判斷





### Shell script 預設變數, 條件式if-else, 迴圈for/while
/dir/filename opt1 opt2 opt3 opt4
$0 執行的腳本檔名 
$1 是第一個參數 etc

還有特殊變數 可再script內呼叫
$# 代表後接個參數個數
$@ 代表 $1" "$2" "$3" "$4"   (whole parameters)
```shell!
#!/bin/bash
#Program: parameter pratice, show script name, para
#History: 2025/03/15
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo "script name: ${0} "
echo "total parp num $#"
[ "$#" lt 2 ] && echo "para less than 2 , exit" && exit 0
echo "whole para: $@"
echo "first para: $1 "
echo "second para: $2"


```

條件判斷式 
if[條件]; then
... 
fi
多重條件判斷
if[condition]; then
elif[condition]; then
else
fi

```shell!
#!/bin/bash
#Program: test first para = hello?
#History: 2025/03/15
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
if [ "$1" == "hello" ]; then
    echo "true hello"
elif [ "$1" == "" ]; then
    echo "no para, exit program" && exit 0
fi

```


配和netstat 寫寫看抓某port是否再跑 (80WWW, 22ssh, 21ftp, 25mail)
```shell!
#!/bin/bash
#Program: netstat and grep detect protocol
#History: 2025/03/15
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
testfile = /dev/netstat_check.txt
netstat -tuln > ${testfile}
test = $(grep ":80 " $testfile$)
if [ test != ""]; then 
    echo "WWW service is on"
fi
test = $(grep ":22" $testfile )
if[ test != "" ]; then
    echo "SSH is on"
fi

```
case $變數in "第一個變數內容");; *)esac
```shell!
case  $變數名稱 in   <==關鍵字為 case ，還有變數前有錢字號
  "第一個變數內容")   <==每個變數內容建議用雙引號括起來，關鍵字則為小括號 )
	程式段
	;;            <==每個類別結尾使用兩個連續的分號來處理！
  "第二個變數內容")
	程式段
	;;
  *)                  <==最後一個變數內容都會用 * 來代表所有其他值(萬用字元)
	不包含第一個變數內容與第二個變數內容的其他程式執行段
	exit 1
	;;
esac 
```
變數獲取方法 1.執行預先給 參數$1 etc , 2.互動 read讀用戶輸入


### function, array, 字符擷取, 正則表達式
function語法: 要注意一定要定最前面 (shell笨由上而下，無強編譯器幫你找)
```shell!
function fname(){
    ...
}

```
使用就 fname (opt1, etc) or fname; 

```shell!
while[condition]
do
    ...
done

```
or
```shell!
until[condition]
do
    ...
done

```

for要知道進行幾次迴圈
```shell!
for var in con1 con2 con3 ...
do 
    ...
done


```
這個 $var 的變數內容在迴圈工作時：

第一次迴圈時， $var 的內容為 con1 ；
第二次迴圈時， $var 的內容為 con2 ；
第三次迴圈時， $var 的內容為 con3 ；
...

寫個ping ip 自動化例子
```shell!
#!/bin/bash
#Program: ping to check network state
#History: 2025/03/15
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
network="192.168.1"
for seqn in $(seq 1 100 )
do
    ping -c 1 -w 1 ${network}.$seqn 

done


```
&>file意味著“將 stdout (1)和 stderr (對應2)重定向至file (隱含1>file 2>file 但這打開file兩次故障 而&>file打開一次順利)

)

陣列 (很麻煩 自己要一個個訂 還要訂好size，跟自己設好index)
var_name[1] = "..."
var_name[2] = "..."
...
var_name[9] = "..."
var_size = 9 
var_index = 0;




### stdout, stderr, pipe

標準輸入　　(stdin) ：代碼為 0 ，使用 < (覆蓋)或 << (累加)；
標準輸出　　(stdout)：代碼為 1 ，使用 > 或 >> ；
標準錯誤輸出(stderr)：代碼為 2 ，使用 2> 或 2>> ；

![輸出重定向](https://hackmd.io/_uploads/Skjajk73yl.png)

注意，檔案無法同時讀寫的，所以要將正確與錯誤資料同步寫入一個檔案內:
這樣思考
1. 錯誤資料轉到正確資料的管線上，然後同步輸出
2. 正確資料轉到錯誤資料的管線上，然後同步輸出
3. 所有的資料通通依序同步輸出

```shell!

find /etc -name '*passwd*' >   ~/find_passwd2.txt 2>&1
find /etc -name '*passwd*' 2>  ~/find_passwd2.txt 1>&2
find /etc -name '*passwd*' &>  ~/find_passwd2.txt

```

pipe | 將前指令輸出作下指令輸入
![pipe](https://hackmd.io/_uploads/Hk5wDu7hye.png)



### shell 追蹤與debug
sh [-nvx] scripts</span>.sh
-n: 不執行，只檢查語法
-v: 執行前先將scripts內容輸出至螢幕
-x: 將使用到的script內容顯示到螢幕上 (執行過程會列出來，很有用)




  
  