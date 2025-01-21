IPv6技術精要 --Rick Graziani
心得筆記:


:::success
重點包括:  拓展標頭/ ipv6 address type(unicast,anycast,multicast)/ ICMPv6(RA, RS, NA, NS, 多播偵聽查詢/完成/報告)/ EUI-64/ SLAAC/ routing(RIPng, EIGRP, OSPFv6)
:::

ch2 IPv6 protocol(協議與標頭分段處理)
ch3 IPv6 addressing(地址與壓縮規則與子網劃分半字節邊界/內部)
ch4 IPv6 address type(單播配置，如何用SLACC和DHCPv6等配置在路由接口啟用IPv6)
ch5 ICMPv6 and neighbor discover protocol(差錯訊息，目的不可能等問題與通知封包，路由宣告與鄰居請求發現重定向等解析，ARP SLACC UDN)
ch6 IPv6 allocation(編址配置，全局單播與link本地地址修改路由案例)
ch7 IPv6 routing(路由表與配置變化及Ipv6靜態路由與CEF)
ch8 IPv6 IGP routing protocl(RIPng IPv6 EIGRP與OSPFv3的三種路由協議解析與ipv4對等協議間區別)
ch9 DHCPv6(狀態化 無狀態化 通訊與服務)
ch10 channel(討論IPv4 to IPv6遷移與過存階段的機制)
ch11 NAT64(三個通訊組件 轉換addr圖)



### ch2 IPv6 protocol(協議與標頭分段處理)
:::success
優點: 大量地址128bits/ 無狀態自動配置/ 消除NAT減少運算加快速度/ 消除廣播引入被請求的多播處理/ 提供遷移工具包括channel將v6包道v4可透過v4 link傳輸，NAT供v4轉v6 or v6轉v4的機制
IPv6還擁有自動配置與鄰居發現機制

回憶IPv4標頭包含: version/ToS/identifier/fragment offset/TTL/protocols(TCP6/UDP6/ICMP1)/checksum/SA/DA/options 使用分段是因為link有MTU限制故允許路由切分封包送到目的主機重組

IPv6標頭包含:vesion/Traffic Class/Flow label/Payload length/Next header(TCP6/UDP17/ICMPv6 58)

traffic class 8bits: 類似ToS用於識別不同類或優先級的ipv6 package
Flow label 20bits: 標記一串ipv6 package，source可用此來請求路由特殊處理，可以標示同一flow中的數據包
Payload length 16:表示基本標頭後的payload(數據本體)長度
Next Header 8 bits:有兩座用，若只有ipv6基本標頭則夏禕標題指v6 package數據用的協議
Hop limit 8bits:與TTL相同，跳到0就丟棄發送ICMPv6超時通知送端已丟棄
SA/DA 128bits
基本標頭固定長度40bytes  相比下ipv4因option的存在使其標頭是變動長度 

Next Header 指定緊接在IP header後頭的header類型，即IPv6 Extension Header或上層PDU（如 TCP、UDP 或 ICMPv6）。
:::

:::danger
拓展標頭:
作用1.標示data乘載的協議(對應v4 protocol)/ 2.指示拓展標頭的存在(對header的補充)
拓展標頭是payload的一部分 用來指示ipv4 options對應功能

在IPv6中，屬於選項的internet-layer訊息依據其功能分別定義在不同的表頭。這些表頭被稱為IPv6 擴展表頭必須支援:
![sup](https://hackmd.io/_uploads/r1lHe90NJl.png)
:::

:::success
why taking ipv4 options into ipv6 extension header?(place inside payload)
Ans: 
IPv4 Option
需要在IPv4標頭的最後面再加上各種 OPTION，這個動作會造成IPv4標頭長度不固定。
(IPv4標頭最小是20 bytes，最大是60 bytes)
IPv4不常用Option功能，因為處理Option可能會消耗到寶貴的CPU資源
:::

:::warning
IPv6不只把Option移除，也移除了封包分割(Fragment)，IPv6把這些功能都放到延伸標頭內，這樣就可以保持IPv6標頭大小固定(40bytes)，提升處理速度，而想要增加功能時再使用延伸標頭，因此 Next Header欄位就變得很重要

通常只有 Destination Address 這台機器 (目的地主機) 需要處裡延伸標頭，但是延伸標頭有Hop-by-Hop時，沿路的每一台路由器都要處理延伸標頭

Destination Address 是Multicast時，每一個收到封包的主機都需要處理延伸標頭

使用延伸標頭除了可以提升處理速度外，在開發新的標頭時，可以不用修改IPv6標頭，像模組化的方式，使用延伸標頭就好，MIPv6 (Mobility) 就是一個例子
:::


![extHeader](https://hackmd.io/_uploads/S1MYgqR4yx.png)
#### 去想v4少的 v6多的 以及最重要自然就六種 -> hop,route,frag,AH,ESP,目的拓展
Hop by Hop option header: 記錄選項訊息，每台路由都要處理這些訊息，可對v6 package補充定義 TLV(type,length,value)
![hop by hop](https://hackmd.io/_uploads/B1CU4qCN1e.png)

Routing header:路由拓展標頭指定去目的的路徑，包含多台中間路由器，類似ipv4的松散路由選項

Frament header:分段拓展標頭類似ipv4用於Fragment的字段，生成唯一辨識值且每個切分的包都包含此值，確保收端能重組

Authenication header:
IPv6用AH與ESP(encapsulation security payload)兩種拓展標頭來示線IPsec的安全協議，AH提供認證與完整性ESP還多提供加密

目的拓展標頭: 紀錄需要DA處理的選項訊息

:::danger
ipv4 VS ipv6: 
package length -> payload length
TTL -> hop limit
protocol -> next header
identifier, fragment offset -> entension header
checksum 可選是否操作 -> 強制操作
option -> extension header
更大address and  data空間

與v4不同v6路由不對package分段，除非是原端才執行分段，中間路由若收到要分段的包就丟掉並向送端發icmpv6 11錯誤消息
:::


### ch3 IPv6 addressing(地址與壓縮規則與子網劃分半字節邊界/內部)
prefix label:
ipv4的prefix or subnet masking以十進制處理，而ipv6因32個16進制的字元，prefix使用/bits  eg 2001:0DB8:0:0:0:0:0:0/64 就是前綴為2001:0DB8:0:0 or 2001:DB8::/64

Ipv6地址類型: Unicast/ Anycast/ Multicast
:::warning
Unicast: 唯一地標示設備上某一port
Anycast: 分配給多台設備的單播地址，發送給anycast addr package只傳送給配置該地址的其中一台
Multicast: 標示一組port，且通常屬於不同設備，封包會發給multicast address標示的所有設備 ，v6沒有廣播 取代的是用全部節點多播(all nodes multicast address)
:::


![uni](https://hackmd.io/_uploads/rksMq5C4ke.png)
:::warning
v4與v6地址最大區別是子網配置，v4從主機位址借位創子網，v6子網id是獨立部分不屬於地址主機(即v6地址port接口id)

subnet:  is a logical subdivision of an IP network(網路位址的邏輯細分 可從網路地址借位(ipv4) or 固定位址(ipv6))

v4主機全0被保留給網路地址 全1保留給廣播 而v6無此要求 故v6全0全1的接口id合法
:::

:::warning
EUI-64
![EUI64](https://hackmd.io/_uploads/HkjQyu5BJx.png)


:::


### ch4 IPv6 address type:
詳細討論ch3提到的三種地址unicast/anycast/multicast
如何用不同方式配置全局單播地址

![type](https://hackmd.io/_uploads/Sywij5R4yg.png)
IANA 以分配的ipv6地址空間:
前導bit:     地址         分配情況
0000 0000    0000::/8    未指定地址/環回地址/內嵌IPv4地址
:::success
#### 001x        2000::/3        全局單播地址
1111 110x    FC00::/7        唯一本地單播地址
#### 1111 1110 10    FE80::/10        鏈路本地單播地址
1111 1111        FF00::/8     多播
:::


#### 單播地址分為: 全局單播(網路上可路由地址)/ 鏈路本地(本地link上設備通訊)/ 環回地址(不可分給任何物理接口 用此向自己送ipv6包)/ 唯一本地/ 未指定/ 內嵌IPv4地址(在低32bits中遷入ipv4地址)

note: 64bit接口id是SLAAC(無狀態位址自動組態)前提
![golb_uni](https://hackmd.io/_uploads/SJc6wnAEJl.png)

配置全局單播地址: 分靜態(似ipv4)/EUI-64(允許指定前綴長度而以動態方式建接口ID)
以Cisco路由器為例:
Router(config)# interface interface-type interface-number 指定接口類型與接口號碼
Rounter(config-if)# ipv6-address ipv6-address/refix-length 指定配給街口的ipv6地址與前綴長度 若要刪除就用該命令的no形式

![topo](https://hackmd.io/_uploads/rkDgchRVJe.png)
![ex4-1](https://hackmd.io/_uploads/SyFbqnAE1e.png)

note: 為了能路由ipv6封包需要啟用全局配置命令 ipv6 unicast-routing
![ex4-2](https://hackmd.io/_uploads/rJrwcnRNkl.png)

Dual stack: 要一個網路內同時能處理ipv4, ipv6封包
IPv6移轉技術有三個，雙協定堆疊（Dual Stack，以下簡稱雙協定）、穿隧（Tunneling）、轉換（Translation）

ipv4大多數驗證命令可用於ipv6，只要將ip替換成ipv6即可
show ipv6 interface brief

:::warning
EUI-64: extended unique identifier用EUI 64自動分配接口id(或用街口的MAC地址生成64接口id)，結合手工配置的前綴就可為接口生成一個全局單播地址
Router(config)# interface interface-type interface-number
Router(config-if)# ipv6 address ipv6-address/prefix-length eui-64
:::


可以用show interface fasternet 0/0來驗證乙太網MAC地址確實接口ID的low 64以乙太網MAC創建

:::danger
動態配置: SLAAC(前綴與前綴長度通過ND路由宣告(advertisement)消息來確認，接口ID則通過EUI-64創造)/ DHCPv6類似ipv4中的DHCP設備由dhcpv6 server service 獲取地址訊息

ND用ICMPv6交換訊息有五種: Router Advertisement(路由宣告)/ RSolicitation(路由請求)/NS鄰居請求/NA鄰居宣告/重定向

發現鄰居，設備可接收ND消息已自動確定它們前綴,前綴長度,以及其網路配置型態，DHCP支持staless, stateful(紀錄客戶端數據)
**** SLAAC屬於無狀態因此建立自己地址後要驗證唯一性
:::


![SLAAC](https://hackmd.io/_uploads/SJzMJTR41e.png)

![SLAAC-flow](https://hackmd.io/_uploads/ryjw1aRVJl.png)



![DHCPv6-flow](https://hackmd.io/_uploads/BJzckaRVJx.png)

鏈路本地單播地址: 僅用於單條link的單播地址 需要保證地址唯一性，封包不會發到link之外，配置: 動態用EUI-64/ 隨機生成接口ID/ 靜態手工輸入鏈路本地地址

為ipv6提供獨一無二的優勢，設備可自行創建自己鏈路本地地址，解決ipv4的雞蛋問題(i.e 向server要ip旦通訊前又需先有ip)

![link-format](https://hackmd.io/_uploads/rJXFg6CVkg.png)

![purpose-link](https://hackmd.io/_uploads/B1zkWaAVJg.png)

:::warning
隨機生成的接口id: 因地址中包含MAC有隱私問題，固有隨機生成的接口id

靜態鏈路本地地址:
Router(config-if)# ipv6 address ipv6-address link-local

重腹地址檢查(DAD: Duplicate address detection): 建地址後發DAD請求問link上其他設備這地址有無被使用

網關(關口)實質上是一個網絡通向其他網絡的IP地址
:::


內嵌ipv4地址: 在低32bit中乘載ipv4地址(映射ipv4的ipv6地址)前80bits設成0，中間16bits為1 再來放ipv4以dot表示的十進制32bits地址

單播地址可以唯一表示ipv6上設備接口 且送端ipv6地址必須是單播地址

:::success
Serial(bit by bit) interface vs fast ethernet
Ethernet 有500 m 限制 故區網 可用乙太網速度快來傳輸
但是router往外連 或是連ISP 就要用serial 線(跟CSU/DSU 設備的接口有關)了
而且WAN 有多種選擇 eg PPP/ Frame replay/ HDLC
早期的電信技術只有 circuit-switched (電路交換), 沒有辦法透過 packet 切割方式來傳 所以有多種協定做轉換
:::

![conclusion](https://hackmd.io/_uploads/HkfGVaCNJl.png)


### ch5 ICMPv6 and neighbor discover protocol(差錯訊息，目的不可能等問題與通知封包，路由宣告與鄰居請求發現重定向等解析，ARP SLACC UDN)

#### 主軸: 錯誤訊息(目的不可達,封包過大,超時,參數問題,ping命令)/通知訊息(多播 listenter query, 多播偵聽報告, 多播偵聽完成)/鄰居發現(RA/RS/NA/NS)

ICMPv6 next header value : 58

![icmpv6](https://hackmd.io/_uploads/SJ1PZygrJe.png)

:::success
type: 錯誤訊息(0 ~ 127)/通知訊息(128 ~ 255)
1(目的不可達)/2(封包過大)/3(超時)/4(參數問題); 128(ping 請求)/129(ping回復) 130查詢/131報告/132完成

目的不可達 標頭第二欄代碼: 0(無目的路由)/1(與目的通訊被禁)/2(超出源址範圍)/3(地址不可達)/4(端口不可達)...
:::


路徑MTU發現: (ipv6 MTU不小於1280 bytes限制)
![PMTU](https://hackmd.io/_uploads/By3hzkgB1l.png)

超時: traceroute 命令

通知消息: ping(請求與回復) 多播偵聽查詢,報告,完成, RA,RS,NA,NS

ping是最常見的TCP/IP工具，測試網路連接性，代碼片段始終為0，除了ipv6度址 其餘與ipv4的ping操作一樣

:::success
多播偵聽查詢: 路由周期發送主機成員關係查詢消息，"確定直連網路的成員"，有兩子類型(通用查詢/特定多播地址查詢)

多播偵聽報告: 偵聽者(主機)發送此消息向多播組進行註冊，可回復查詢或自主發送

多播偵聽完成: 偵聽者不希望收某特定多播組的流量時，發送此消息給路由器通知要離開此多播組
:::


![multi_report](https://hackmd.io/_uploads/HkGjDJgSkl.png)


鄰居發現協議: 設備使用鄰居發現協議的理由包括 SLACC自動確認前綴與網關，DAD，識別ipv6後確定其mac address

![ND-format](https://hackmd.io/_uploads/HJlO_1xr1g.png)

Cur Hop limit: 路由建議網路上各主機封包用的數值
M flag: 被管理地址配置標記 M為0時網路上主機用SLAAC,1則用DHCPv6
O flag: 其他配置 0表dhcp server 沒可用訊息，1告訴主機可從server獲得額外訊息DNS之類

Router LifeTime: 告訴主機該路由能被用作默認網關的持續時間

:::danger
R1(config)# ipv6 unicast-routing 
路由器R1通過命令ipv6 unicast-routing 被配置為ipv6路由器，用命令show ipv6 interface fasternet 0/0即可驗證
![ND-RA](https://hackmd.io/_uploads/Bksz51lHke.png)

鄰居請求和宣告 由此三個組成: 地址解析, DAD(重複地址檢測), NUD(鄰居不可達檢測)
可用show ipv6 neighbors 顯示路由器鄰居緩存表


SLAAC 無狀態地址自動配置: 允許主機組合本地訊息與路由器宣告的訊息生成自己擔播地址的機制
![SLAAC-step](https://hackmd.io/_uploads/HkaQ2ygByg.png)
:::


![ch5-conclusion2](https://hackmd.io/_uploads/rkt_n1lHkg.png)

![ch5-concolusion3](https://hackmd.io/_uploads/HyJtnklryg.png)


### ch6 IPv6 allocation(編址配置，全局單播與link本地地址修改路由案例)
配置全局單播 ipv6 address ipv6-address/prefix-length

![config](https://hackmd.io/_uploads/S1qfaklSyg.png)

#### 配置鍊路本地地址: 為接口分配了全局單播地址後，就會自動創建相應鍊路本地地址

:::success
鄰居發現協議的路由請求與路由宣告 用的都是鍊路本地地址，使用SLAAC時主機也將路由的鍊路本地地址作為默認網關，故鏈路本地地址重要
Router(config-if)# ipv6 address ipv6-address link-local

若沒指定全局單播地址的情況下，要在接口上啟用ipv6，可用ipv6 enable
Router(config-if)# ipv6 enable
為接口自動創建一個本地鏈路路徑
:::


利用show running-config令命可檢查Router的接口配置
no ipv6 address刪除接口的ipv6地址，若沒有包含特定地指責該接口上所有地址被刪除

#### ipv6路由器除了可以轉發ipv6封包，還可以用ND路由器宣告來通告前綴長度，默認網關
要啟用ipv6單播封包轉發 使用ipv6 unicast-routing

ipv6鄰居緩存表類似ipv6的ARP緩存表，可用命令show ipv6 neighbors顯示鄰居緩存表

:::success
ipv6訪問控制列表(IPv6 ACL: access control list) 的配置與ipv4 ACL相似，ipv4有兩種基本ACL 標準與擴展ACL，總體來說，標準ACL只能基於source地址來允許或拒絕流量
而擴展ACL可根據送端地址與目的地址 以及協議TCP/UDP來允許拒絕流量

#### 網關 IP 是裝置在將流量引導出本地網路時所使用的位址
:::




ipv6 僅支持名稱式ACL

ipv6 ACL: deny ipv6 any any/ permit icmp any any nd-na/ permit icmp any any nd-ns


### ch7 IPv6 routing概述(路由表與配置變化及Ipv6靜態路由與CEF)
:::warning
主軸: 路由表(connetc, local) 配置 與靜態路由 還有基礎概念(administration distance, metric)
show ipv6 route 可以顯示ipv6路由表
![show route](https://hackmd.io/_uploads/S1FU3ZWSye.png)
:::


address 顯示指定ipv6地址的路由訊息/ prefix顯示指定ipv6網路的路由訊息
updated 顯示帶時間戳的路由
interface interface-type 接口類型 可利用?在線幫助獲得支持的接口類型訊息

![connect](https://hackmd.io/_uploads/HJb_aZbrJl.png)

:::success
[0/0]表示管理距離和度量, 管理距離由訊息源可信度與優先權影響，僅具有本地意義，不會用路由去宣告
show ipv6 route connected 可只顯示直連路由

代碼L 為本地路由，本地路由是/128路由，本質上是路由器的ipv6單播地址的主機路由，能更"高效處理"直接"發給路由器自身的"封包(而不是發給該路由直連子網的封包)
:::


ipv6 vs ipv4路由表對比:
ipv4按照有類別網路地址組織，需要在每條類別路由下列出所有子網，ipv6則更直觀，不存在類別與無類別編址，僅顯示單個網路

![ipv4 table](https://hackmd.io/_uploads/Hk6MlzWHyx.png)

![ipv6 table](https://hackmd.io/_uploads/r1K8xGbBkx.png)

配置ipv6靜態路由
ipv6 unicast-routing 類似ipv4的ip routing
配置ipv6靜態路由: ipv6 route ipv6-prefix/prefix-length 

vrt: 指定所有vrf表(VPN routing/forwarding)
administrative-distance 管理距離，默認值為1，因此靜態路由優先及高於直連路由外的其他路由
unicast
multicast 

show running-config | include ipv6 route命令 過濾輸出結果 僅限是以ipv6 route開頭的行。利用show ipv6 section route static還可進一步驗證靜態路由的配置情況

![ipv6 static](https://hackmd.io/_uploads/SJyVozWSyg.png)

show ipv6 route summary 顯示local, connected, static,等等有幾條 概述的顯示

IPv6 CEF，前面討論了進程交換(process switching)與快速交換(fast switching)(還有CEF逐漸取代快速交換，對去往目地的封包做附載平衡)(debug ipv6 package)，對於高端Csico平台不用進程交換

CEF是用於ipv4 Cisco路由器與交換器的交換技術，也有CEFv6，優化了路由表的查找操作
使用兩種方法來實現快速轉發: FIB(forwarding information base)/Adjacency table

FIB類似IP路由表，是一種有序表，為路油表中每個ipv6前綴/前綴長度(ipv4網路/子網)列出明細的路由，是路油表轉發訊息的鏡像，用來維護next hop address 訊息

Adjacency table : FIB包含三層網路和下一跳訊息，為流水化處理封包轉發，將表中下一跳項相對應的二層訊息存在相鄰表中，是通過ipv6鄰居緩存表(或ipv4的ARP緩存表)構建成的。包含三層與二層地址的映射訊息





### ch8 IPv6 IGP routing protocl(RIPng IPv6 EIGRP與OSPFv3的三種路由協議解析與ipv4對等協議間區別)

#### 這章就想 三類協定(RIPng, EIGRP, OSPF) 每個根據其 "概念(distance vector, link-state)" 要做 "配置(router ripng, router opsf)" 與 "驗證(show,ping)" 最後是 "路由"(設置協定類型 路由id) 與 "接口"(interface 上設serial, ethernet等以及address)

靜態路由對於末端(stub)網路適用，也被用作默認路由，但對多台路由及冗於鏈路的網路不好，缺點: 配置維護費時/ 在大型網路配置易出錯/ 無法動態 擴展性差

默認路由(default router): 對IP封包中的目的位址找不到存在的其他路由時，路由器所選擇的路由

此章主軸在三種IGP(interior gateway protocol)路由協議: RIPng(routing information protocol next generation)/ EIGRP(enhanced interior gateway routing protocol)/ OSPFv3(open shorest path first v3)

note: (IS-IS(intermediate system to intermediate system) 是一種同時可用於v4和v6的IGP路由協議。BGP(border gateway protocol)是目前網路在自治系統間使用的BGP )

![ipv4](https://hackmd.io/_uploads/S1cP1Xbrye.png)

IPv6 RIPng: 一種距離向量路協議，缺少高級功能但具簡單性及長期使現狀，
![compare](https://hackmd.io/_uploads/Syo0y7ZrJl.png)

:::warning
配置RIPng需要先在用命令ipv6 router rip name 可以為ipv6啟用ripng路由程序，其中name 標示特定路由程序(啟用ipv6 為ipv6 unicast-routing)
在接口上使用命令ipv6 rip name enable以啟用RIPng。
為了將ipv6默認路由分發到RIPng局域中，要使用命令ipv6 rip default-information

離開interface 用exit, 離開到router用end, 進入interface用interface
配置用 ipv6 route address serial/ethernet 
驗證用show ipv6 route ripng , show ipv6 protocol 檢查可達性用 ping address
:::


![RIPng](https://hackmd.io/_uploads/r1H4vUZr1l.png)

show ipv6 rip next-hops可以顯示下一跳地址和使用該地址的RIPng路由數
debug ipv6 rip顯示路由發送和接收到的RIPng路由訊息
no ipv6 rip name可以刪除RIPng程序包括其使用的所有接口配置
show ipv6 rip database 顯示RIB(routing information table)路由表資訊 

#### EIGRP(enhanced interior gateway routing protocol)一種距離向量無類別路由協議，是Cisco 專有協議，只能運行在Cisco路由器上，獨特處在於RTP(reliable transport protocol)，DUAL(diffusion update algorithm)擴散更新算法是其核心，會選一條主路由安裝到路由表中，同時維護備用路由列表，
![EIGRP](https://hackmd.io/_uploads/HJPYKLWBJg.png)

進interface用 interface port-name prefix
![interface](https://hackmd.io/_uploads/SJWP5UZHJl.png)

show ipv6 route 顯示路由資訊
show ipv6 protocol 顯示協議 與connected, static等狀態
show ipv6 route eigrp顯示路由表中eigrp路由
show ipv6 eigrp neighbors驗證與路由的鄰居鄰接關係

#### OSPFv3: 是一種鏈路狀態路由協議，主要代替RIP，是一種無類別路由協議
OSPFv2 對應ipv4, OSPFv3對應ipv6
度量定義為將封包從接口送出去的cost
:::success
OSPF基本封包類型: Hello/ DBD(database description)/ LSR(link state reques)/ LSU(link state update)/ LSAck(link state acknowledgment)
:::


#### OSPFv2可用明文認證或MD5認證機制，而OSPFv3使用IPsec提供的認證或加密功能

![OSPF](https://hackmd.io/_uploads/B1qni8WBkx.png)

![rip_con](https://hackmd.io/_uploads/r1ol38WHyx.png)

![EIGRP-con](https://hackmd.io/_uploads/r1CMnU-S1e.png)

![OPSF-con](https://hackmd.io/_uploads/S12E3I-H1l.png)

### ch9 DHCPv6 (狀態化/無狀態化 , 服務/通訊)
:::success
狀態化: 主機可從DHCP伺服器 取得所有編址及其他配置訊息  而無狀態化則是 主機從路由器取得編址與配置訊息

DUID(DHCP Unique identifier): 每個參與者(客戶 服務器)的唯一識別DUID
IA(身分關聯): 是分配給客戶的地址集，為每個用dhcp的接口分配一個IA 有一個客戶分配的IAID用來識別
多播地址 all_dhcp_relay_agents_and_server(FF02:: 1:2): 所有伺服器與中繼代理 都是該 鏈路本地範圍組的成員，客戶用此地址與鍊路上伺服器通訊
all_dhcp_servers(FF05:: 1:3): 伺服器是該 站點本地範圍多播組的成員 

使用UDP端口 546(伺服器發 客戶偵聽),547(客戶發 伺服器聽)
四種主要DHCPv6消息: Solicit(客戶發請求來找伺服器)/ Advertise(伺服器宣告來回應solicit)/ Request(客戶發要求 根伺服器請求配置參數)/ Reply(伺服器回應分配地址與配置參數)
![DHCPv6](https://hackmd.io/_uploads/rJDdF0nDyl.png)

:::

建 無狀態DHCP 三步驟: 配置DHCP服務器池及參數 -> 接口啟用DHCP服務器池 -> 修改RA消息參數 讓主機知道可從服務器獲取配置訊息
![stateless_DHCP](https://hackmd.io/_uploads/B1LLq0nPke.png)

![dhcp_command](https://hackmd.io/_uploads/BkrY5Rnvyx.png)

狀態化DHCP: 
![DHCP-with-proxy](https://hackmd.io/_uploads/rk4ZiR2D1l.png)

以上使通訊部分，再來是服務(其他上層協議 配合DHCP的變化)
:::warning
由於TCP與UDP都將IP地址作為checksum的一部分，要根據IPV6的地址做修改 還有像DNS等上層協議也有改動

DNS: 用於ipv4,ipv6的 負責處理域名與地址間的mapping
![DNS](https://hackmd.io/_uploads/BJHCsR3Dkg.png)
可用nsloopup命令 解析域名

TCP與UDP標頭都有16bits的checksum片段，v6與v4的checksum計算方法相同，只重寫以適應更長地址的部分，然後ipv4標頭有checksum所以UDP通過ipv4傳輸時 checksum是可選項 但ipv6不包含checksum所以通過ipv6傳輸時UDP必須強制checksum

SLAAC(M bit = 0 , O bit = 0 表示不從DHCP伺服器取得配置，要動態地址分配的主機就從RA消息取得配置)
狀態化DHCP(M bit = 1 須從伺服器直接取得全部編址與配置訊息)
無狀態化DHCP(Mbit = 0, O bit = 1 主機從RA取得前綴,默認網關和MTU等訊息，但還可從DHCP伺服器取得額外配置(eg DNS服務器地址))
:::



### ch10 channel(討論IPv4 to IPv6遷移與過存階段的機制)
過度技術分三類: Dual-stack/ Tunneling/ Translation

:::warning
Dual-stack允許v4和v6共存於同一網路，設備可以是同時配置v4和v6的主機 服務器 路由器
Tunneling: 一種在ipv4-only網路中傳輸ipv6封包方法，將v6封包封裝到v4封包中
Translation: NAT64從ipv6到ipv4的網路地址轉換，利用與ipv4 NAT相似技術，實現ipv6設備與純ipv4設備間通訊

Dual-stack設備與ipv4設備通訊時，行為特性就像純ipv4設備，與ipv6設備通訊時就像純ipv6設備
:::


![URL-ipv6](https://hackmd.io/_uploads/BJOwQ8zSyx.png)

![ipv4-ospfv2](https://hackmd.io/_uploads/BJ3kELzSye.png)

OSPFv3並不使用network命令在接口上啟用OSPF，而是通過命令ipv6 ospf直接在接口上啟用OSPF

#### (啟用ipv6 用ipv6 unicast-routing/ 確認可達性用ping/ 驗證路由表用show ipv6 route)


Tunneling: 本質是ip封包封裝到另一層協議中，也就是ipv6的疊加(overlay)通道將ipv6封包封裝到ipv4封包並通過純ipv4網路進行傳輸

![tunneling](https://hackmd.io/_uploads/SyCDB8zBkg.png)

tunnel management: 管理由管道入口與出口點完成，負責封裝/解封 編址 MTU 分段與錯誤處理

重點在端點 做出選擇: 配置多少通道?(手工vs動態if 量大) 和特殊需求?(如通過通道傳輸非IP封包)

:::success
6to4 tunnel: 點到多點的tunnel，目的的ipv4地址決定於封包目的的ipv6地址，需要v6前綴或網路地址與v4通道地址間關係，v6地址是從v4通道地址以反向工程得到，格是為2002:tunnel-ipv4-addr::/48，因而可以創建一條有多個目的端的通道

ISATAP(intra-site automatic tunnel addressing protocol站內自動通道尋址): 用在不具備純ipv6基礎設施的站點內傳輸ipv6封包

:::


### ch11 NAT64(從ipv6到ipv4的網路地址轉換):
允許純ipv6客戶 向純ipv4服務器發起通訊 或 純ipv4客戶向純ipv6服務器通訊


與IPv4的狀態化NAT相似。狀態化NAT64在轉換時會同時創建或修改ipv6與ipv4間的綁定關係  使用此兩種算法 IP/ICMP translation algorithm轉換兩協議的標頭 或是ipv6 addressing of ipv4/ipv6 translation轉換兩協議的IP地址
:::danger
3類轉換用的 組件: 
NAT64前綴: 為通過純ipv6網路傳輸 而與ipv4地址一起用的/96前綴，NAT64可以是NSP(network-specific preifx)或是WKP(well-known prefix)。NSP是由組織分配的前綴

DNS64服務器: 為ipv6 AAAA紀錄完成普通DNS服務 ，還會在AAAA不可用時來試圖定位ipv4 A紀錄， 會用NAT64前綴將ipv4 A轉成ipv6 AAAA使ipv6主機認為其可通過ipv6與純ipv4通訊

NAT64路由器: 將NAT64前綴宣告到ipv6網路中 並執行轉換協定操作

![DNS64](https://hackmd.io/_uploads/ByCZX1pwJx.png)
![A to AAAA record](https://hackmd.io/_uploads/S1l4Q16vkg.png)
:::





![DHCPv6](https://hackmd.io/_uploads/SJJ99QWLyx.png)
![DHCPv6WithProxy](https://hackmd.io/_uploads/ByI6qQ-Lye.png)


![ipv6TunType](https://hackmd.io/_uploads/H1WK8IGH1e.png)

![example10-3](https://hackmd.io/_uploads/Bk5iDLGSkx.png)

![checkTun](https://hackmd.io/_uploads/BkOgdUGSyl.png)

![ISATAP](https://hackmd.io/_uploads/rJdqdLMH1l.png)


![GRE](https://hackmd.io/_uploads/HJbVdLfrkg.png)













reference: ipv6技術精要 與 http://hanteye01.blog.fc2.com/blog-entry-2.html