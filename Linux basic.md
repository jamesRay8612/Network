Linux basic個人心得筆記
#
ch1 linux intro
ch2 basic command
ch3 file and vim
ch4 linux file 權限
ch5 file display, 管理
ch6 file system intro
ch7 bash system
ch8 bash command
ch9 shell script
#

### ch1 linux intro

kernel: OS管理底層硬體的介面

system call: access kernel for priviliage instruction execute 向OS呼叫更高權限的服務 

physical console: 實體控制台，提供鍵盤滑鼠等實體功能的硬體環境， eg 電腦
Virtual console : 虛擬控制台 eg.tty1~tty6
提供六個terminal(一個文字命令介面供用戶呼叫系統服務) 切換用Ctrl+Alt+F1~F6 命名為tty1~tty6的操作介面

Terminal: 提供用戶與OS互動的軟體介面，登入後取得shell來進行系統互動 eg. GUI圖形用戶介面
Shell (program): 一種只直譯(line by line to object code)的software，可稱shell program

linux分絕對位址與相對位址
/home/etc 以/起頭的為絕對位址，需得清楚完整
相對位址 若目前在home 可直接type .etc 意旨 在目前目錄(.的含意)找etc 


/根目錄
.本目錄
..為上一層
~為user家目錄
-上一次工作目錄

:::warning
基礎指令:
exit 登出 (關閉終端常見方法 exit/logout/Ctrl+D)
--help  系統內建函數使用說明 
man (mannul簡寫) 外部函數使用手冊 (常見指令g第一行;G最後行;q離開;pageUp向前一頁)
/keyword: 命令找keyword關鍵字; n向下方繼續找關鍵字/N
ls (list列出檔名)
ll 為ls -l (list long縮寫)
history (叫出歷史命令)
cd 切換目錄

traceroutes：檢查從你的電腦到網路另一端的主機是走的什麼路徑
ping：網路檢測工具 檢測目的地址網路可達姓
nslookup：查詢 DNS 回應是否正常
find：查詢檔案
:::


### ch2 basic command

指令下達格式: command [-options] [ para1...]
#### 注意linux 會把-後面的文字只看一個 eg -help -> -h-e-l-p  長句要 --help
help輸出分為Usage(執行語法部分)/Mandatory argument(主要選項說明)/ Formate controls(詳細格式說明) 要根據需求查詢與使用 例如想查date %Y%M%D輸出就去format controls，大多使用情況是看語法跟主要選項說明

:::danger
date (取得時間輸出的指令)
LANG = zh_TW.uft8/zh_TW.big5/en_US.utf8 設定語言變數 改變顯示語言
echo $var/${var} 呼叫變數var，變數前要記得加$符號
設定變數則是直接 var = "xxxx"

管線命令 | (pipe) 將前一個指令輸出資料交由後指令處理
echo "scale = 10; 4*a(1)" | bc -l 先echo純文字後，就在計算機BC環境執行scale = 10; 4*a(1)運算


grep (關鍵字擷取) 
eg : ll /home/xxhile | grep 'passwd' 

檔案放置位置:
![pos](https://hackmd.io/_uploads/BkdyuBeVyl.png)
:::


#### 所有目錄都從根目錄(/)衍生，從根目錄開始撰寫的檔名就是"絕對路徑"
可用df(display filesystem)查詢了解磁碟與目錄樹

:::success
mkdir 建目錄(不是建檔案 建檔要用touch)
cp [option] 來源 目的 (copy指令的簡寫 要注意-r recurrsive 才可複製目錄)
並且cp要注意檔案權限問題，看檔案權限rwx可用 ll -d  (單純ll xxx是看到目錄xxx內檔名列表(許多目錄內的檔名資料) 要ll -d xxx 才顯示xxx的狀態  )
cp 常用-a/-r選項 差異在-a連權限時間等詳細資訊一併複製
:::




### ch3 file and vim

mkdir 見目錄
rmdir 刪除空目錄 
rm 預設刪除空檔案 (--help 看怎麼刪除非空檔案)

萬用字元(常用在關鍵字查詢)
*(表示0-inf任意字元)
?(表示一定有一個字元)
[](代表一定有一括號內字元) eg[abcd] 表訂有一字元 可能a or b or c or d
[-]代表編碼順序內所有字元
[^]若中括號第一個是^代表反向選擇 eg [^abd] 代表有一非abc就接受
空白字元可用"\ "來處理 eg mkdir class\ one ，若不加\那dir名稱會是classone
而加減號開頭的黨名，可用絕對路徑處理

ll -a 觀察隱藏與非隱藏檔
ll -d .*址顯示隱藏檔 (因為隱藏檔是開頭為小數點的黨名)

mv 移動檔案
touch 建立檔案

輸出檔案內容
cat : 將檔案內容全列出
head: 預設檔案最前十行
tail: 預設檔案最後十行

查詢大量資訊 可用more/less軟體處理 
more軟體內常用指令
/關鍵字:查詢關鍵字
空白建:向下翻頁
q:離開
less軟體內常用址令
/關鍵
[pageup]/[pagetdown]
g/G/q

vim編輯器 4模式(Command按下esc可回此mode 可複製貼上刪除與移動游標) (edit 按下i) ( vitual選取 v字元選 shift+v行選 ctrl+v區塊選 按y複製d刪除p貼上) (extended command mode 儲存離開搜尋取代等動作) 

![mode](https://hackmd.io/_uploads/Skl5hBgV1e.png)

vim編輯器常用址令:

![ins](https://hackmd.io/_uploads/S1cn2HlNkg.png)


ch4 linux file 權限

三種身分 : user(owner)/group/others

觀察檔案權限 ls - l or ll

[-(nornal file)/d(目錄檔)][9 char (rwx)][owner]

檔案類型 用 file 指令查詢

屬性與權限修改:
chown修改擁有者
chgrp修改擁有群組
chmod 搭配數字改權限: chmod 777 filename (owner 7 ie rwx = 111, group,other also rwx = 111)
也可搭配符號(+/-/=) eg chmod u=rwx,g=rw,o=r filename

帳號管理 useradd username
passwd username(改密碼)
userdel -r 刪除帳號

groupadd groupname
grep groupname /etc/group 確定groupname有在設定檔中
useradd -G groupname user1
id user1可查詢此user資訊包刮在地group

管理員可以透過 passwd --help 找到 --stdin 的選項來操作密碼的給予

群組共用(共享彼此資料)
mkdir /srv/project1
chgrp groupname /srv/project1
chmod 770 /srv/project1
ls -ld /srv/project1

ch5 權限應用，程序管理

![wow](https://hackmd.io/_uploads/HkmeycINyx.png)

程序觀察常用指令 : ps,pstree,top等

ps -l 觀察bash自己相關程序 
F(flag 4表root特權0為一般1為fork但未exec) S(stat R(runing)/S(slepp)/D(uninterrupt sleep eg wait I/O) UID PID PPID C(CPU use rate)

psterr -A (以ASCII輸出 觀察程序關聯樹

ch6 file系統基礎管理
分割磁區後才可access，MSDOS磁碟分割(小系統8T內)，GPT磁碟分割

Linux 使用EXT2 file system
![fs](https://hackmd.io/_uploads/BkFKgqIVJx.png)

superblock為整的檔案系統總結資料處，讀取必從superblock讀起 主要紀錄block與inode總量/未用/已用/size/掛載(使硬體連接電腦檔案系統令硬體可供訪問的過程)時間

inode table紀錄rwx, owner/group, capacity, ctime, atime, mtime, pointer
每個檔案固定占用一個inode，系統讀取檔案先找到inode並分析權限符合才開始讀取block內容


建立分割 GPT use gdisk , if msdos use fdisk
gdisk /dev/vda 常用d(deleta分割)/n(add)/p(print)/q(quit)/w(write儲存分割操作後離開)指令

檔案掛載 注意:
單一檔案系統不應該被重複掛載在不同的掛載點(目錄)中；
單一目錄不應該重複掛載多個檔案系統；
要作為掛載點的目錄，理論上應該都是空目錄才是。

mount -a
mount [-t 檔案系統] UUID=''   掛載點  


ch7 bash基礎

echo $0 列出目前執行檔

變數="變數內容"
echo $變數
echo ${變數}
unset 變數名稱
$(command)

規則:
變數與變數內容以一個等號『=』來連結
等號兩邊不能直接接空白字元
變數名稱只能是英文字母與數字，但是開頭字元不能是數字
變數內容若有空白字元可使用雙引號『"』或單引號『'』將變數內容結合起來

***在一串指令的執行中，還需要藉由其他額外的指令所提供的資訊時，可以使用反單引號『`指令`』或 『$(指令)』。
**若該變數需要在其他子程序執行，則需要以 export 來使變數變成環境變數

過子指令 $() 來處理資料的流程
$(command) 


![langVar](https://hackmd.io/_uploads/HyHnsfuEJx.png)

PATH 路徑搜尋變數，他會影響到使用者操作的行為

由於 PATH 設定錯誤時，可能會導致系統的 crash 狀態，尤其是當 PATH 並未含有 /bin 這個搜尋路徑時


kill 並不是『刪除』程序，而是給予程序一個『訊號 (signal) 』來管理，預設的訊號為 15 號，該訊號的功能為『正常關閉程序』的意思。 而想要強制關閉該程式，就得要使用 -9

 login shell 讀取設定檔的流程是：

/etc/profile：這是系統整體的設定，你最好不要修改這個檔案；
~/.bash_profile 或 ~/.bash_login 或 ~/.profile (只會讀 1 個，依據優先順序決定)：屬於使用者個人設定，你要改自己的資料，就寫入這裡！

non-login shell
~/.bashrc

ch8 bash command

連續指令的下達

var=${   變數  }
var=$(  指令   )
var=$((   數學計算式  ))
var="  保持 $ 功能   "
var='  純文字   '
var=`     `

 ifconfig (interface configuration) 位址指派給網路介面，以及配置或顯示現行網路介面配置資訊
 
 chfn命令提供使用者更改个人资讯，用于finger and mail username
 
 Linux 環境下預設的指令正常結束回傳值為 0 ，呼叫的方式為使用『 echo $? 』
 
 command1 && command2  0 時(成功)，command2 才會執行
 command1 || command2 非 0 時(失敗)，command2 才會執行
 
 test 的指令可以確認許多檔名參數
[ 判斷式 ] 

![testCom1](https://hackmd.io/_uploads/ByMyJmuEJg.png)

![testCom2](https://hackmd.io/_uploads/B1YkkXOE1l.png)

![testcom3](https://hackmd.io/_uploads/HJxgkXON1x.png)


假設我們需要使用『 checkfile filename 』來處理，此時可以撰寫一隻小腳本來進行此任務。 若該指令可以讓所有用戶執行，則可將指令寫入 /usr/local/bin 目錄內

[root@localhost ~]# vim /usr/local/bin/checkfile
#!/bin/bash
ls -d ${1} &> /dev/null && echo "${1} exist" || echo "${1} non-exist"

[root@localhost ~]# chmod a+x /usr/local/bin/checkfile
[root@localhost ~]# checkfile /etc
/etc exist
[root@localhost ~]# checkfile /vbird
/vbird non-exist

#!/bin/bash代表使用 bash 來執行底下的語法
 ${1} 代表在本檔案後面所接的第一個參數
 
 [ -e "${1}" ] && echo "${1} exist" || echo "${1} non-exist"
 中括號代表的是『具有一個指定的任意字元』
使用中括號替代 test 指令時， 中括號的內部需要留白一個以上的空白字元！
 
 hell script 創立一個指令，也能夠自己設定回傳值的意義。
 #!/bin/bash
ls ${@} && exit 100 || exit 10

管線 (pipe) | 的意義
將前一個指令的標準輸出作為下一個指令的標準輸入來處理

管線命令必須要能夠接受來自前一個指令的資料成為 standard input 繼續處理才行。
cut ：裁切資料，包括透過固定符號或者是固定字元位置
grep ：擷取特殊關鍵字的功能
sort ：進行資料排序
split ：將資料依據行數或容量數切割成數份

ch9 shell script:
正規表示法:
^word 待搜尋的字串(word)在行首！
word$ 待搜尋的字串(word)在行尾

. 代表『一定有一個任意字元』的字符！
\ 脫字符，將特殊符號的特殊意義去除！
* 重複零個到無窮多個的前一個 RE 字符
\{n,m\} 連續 n 到 m 個的『前一個 RE 字符』

$[:alnum:] 0-9, A-Z, a-z

shell script  (直譯程式)
指令的執行是從上而下、從左而右的分析與執行
多個空白都會被忽略掉
至於如果一行的內容太多，則可以使用『 \[Enter] 』來延伸至下一行；
『 # 』可做為註解！任何加在 # 後面的資料將全部被視為註解文字而被忽略！

![shellCom1](https://hackmd.io/_uploads/Skj9-7d41e.png)    


 source 或 . 來執行腳本
直接產生一個新的程序 (process) 來執行，例如 bash script.sh、 ./script.sh 都屬於這一種；
ource ~/bin/gototmp.sh

使用 read 讓用戶輸入參數：
read -option "show str" varName
echo varName

![if](https://hackmd.io/_uploads/BJM-fXOEyx.png)

![if2](https://hackmd.io/_uploads/rkbXzQuE1l.png)

![if3](https://hackmd.io/_uploads/Bk5uf7_Nkg.png)


![case](https://hackmd.io/_uploads/HJXer7ONJe.png)



































