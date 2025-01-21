清大計算機網路概論 ch1-ch3 Ethernet, Wifi

學習過程與筆記-james ray

### ch1 Introduction
1-1 網路連接性
1-2 網路架構


1-1 術語:
:::success
Circuit Switched : 交換網路以實體的電路連接構成，需要在傳輸前先建立通訊路徑。
Packet Switched : 網路將資料已封包的結構，用路由器交換，不需要在傳輸前先建立通訊路徑。

Multiplexing (多工) : 將多組訊號在單一link上傳輸
De-multiplexing : 將單一link上多組訊號傳到對應host

FDM (分頻多工) : 一條link上依據可用頻段分給用戶使用
TDM (同步分時多工) : 使用預先訂好的時間區間分配給用戶使用獨佔所有頻率

統計式多工 (Statistical multiplexing) : 資料依據data flow 需求來傳送
:::


logical channel : user process 間通訊路徑

1-2 架構:
Protocols : 定義資料收送的格式，服務，區分網路架構的模組區塊
基礎要定義 : 提供的服務介面與同層的訊息交換

Encapsulation : OSI 網路架構中 高層的(eg: TCP header)的訊息被封裝在低層的訊息中(eg : IP header)

OSI 7 layer: ![OSI](https://hackmd.io/_uploads/HysVHT0XJe.png)

DataLink layer : 傳輸單位 : 訊框(Frame) 
此層常見protocols: Ethernet(CSMA/CD) host/node自主偵測link 狀態, idle則送data, collision 則停, idle送的機率為100%因為式1-persistent protocol
MAC(media access protocol) : access link的基礎協議，網卡有固定MAC addr，區網內的通訊收送根據此協議處理

wifi LAN (CSMA/CA) : Distributed system ，使用電波做通訊媒介

裝置 : Switch(多孔加強bridge，有addr learning, filter等功能), Bridge(橋接多個LAN，有addr learning, filter等功能), Hub(加強的中繼器，增強訊號收發)

網路層: 處理封包透過網路傳送到目的主機
路由器 : 存forward table (IP/MAC) and router table (IP/IP) ，IP協議 定義了全球網路上各主機的地址，Class A/B/C，有階層式的分成網路位址跟主機位址


傳輸層: (不同主機process間的資料傳輸)，TCP 可靠，3 handshake。 UDP 不可靠，segment 次序會亂會掉

### ch2 Ethernet (802-3):
2-1. Intro
2-2 Ethernet topology(講網路連接的方式)
2-3 Frame Format
2-4 Ethernet MAC -- CSMA/CD


2-1 Ethernet 特點
Connetionless : 送端網卡與收端網卡傳輸前不需提前建連線
Unreliable : 收端網卡不會傳確認消息(ACKs) or NACKs to 送端網卡
MAC 協定 : CSMA/CD

2-2 Bus 匯流排 : 資料傳輸的線路
基礎以同軸電纜且最常500公尺，一直線的行事作為Bus線路，直線兩端點有終端器吸收訊號，線路上個host用收發器(網卡)去偵測目前線路狀態與決定是否發送資料，這類CSMA/CD的協議實作在網路介面卡上。 10Base2 meaning 10Mbps, Base 基頻系統 , 2表示每一往段最常200公尺
10BaseT ， T表示雙絞線，長度限制在一百公尺以下，HUB(集線器)也稱多重中繼器

Star Topology : 交換機放於star topo 中間，交換機一個port就一個網卡，主機間訊息不互撞，交換機每port都可同時傳輸

:::success
2-3 Frame Format: 
Preamble 先導位元64: 用來使收送端訊號同步的
FCS frame檢查碼32
SA/DA mac addr 48bits
![E_frame](https://hackmd.io/_uploads/rk-Y2k14yx.png)

可知frame size min 64 bytes 常用來確認封包是否collision
frame size max 1500 + 18 bytes 此限制來避免佔用過久頻寬

Ethernet 網路地址收發:
每個在link上傳輸的frame被所有該線路上主機收到，若網卡辨識出MAC址是自己就往上給主機。
又分成unicase (mac addr 導向)，以及所以位元為1的乙太網路位址為broadcase addr，第一個位元為1但非broadcase的為群播groupcase
:::


2-4 Ethernet MAC:
CSMA: 傳輸前先聽link 是否idle 若idle就傳frame若busy就延後，仍可能碰撞因為propagation delay傳輸延遲
CSMA/CD碰撞偵測，測輛訊號強度，比較傳送的與接收的，若碰撞發生立即中斷傳輸

#### 每一傳送端皆有能力知道是否碰撞，當偵測到碰撞時，發32bis的 jamming sequence 擾亂訊號並且停止送frame，然後會等一段時間再重送，此策略稱指數後退exponential backoff

介面卡第一次等時間0 or 51.2us隨機挑，在第n次失敗傳送，算法選0-2^n - 1 選一值k，然後等k*51.2us


Ethernet 效能:
![E效能](https://hackmd.io/_uploads/B1Y0AykEkx.png)

### ch3 Wifi LAN (IEEE802.11):

3-1 intro
3-2 Distributed system
3-3 frame format
3-4 wifi MAC -- DCF/PCF

3-1 Intro:
以無線電波為傳輸媒介，802.11定跳頻(frequency hopping)  and 直接序列(direct sequence) 11-bit sequence兩種無電線基礎的實體層標準

802.11a 使用正交多頻分工(OFDM) 54Mbps
802.11n 使用多輸入多輸出(MIMO) 108Mbps

隱藏節點問題 : 因通訊範圍的問題，若節點A想與節點B通訊，但A不知道B的鄰居C正與B通訊，則A發訊號而collision，則AC是Hidden node

Exposed node 問題 : B有鄰居AC，C有鄰居BD，B想傳給A以及C想傳給D的訊號會給彼此BC知道，但其實互不干擾到，可同時傳送

:::success
CSMA/CA : 
送端開送前交換control frame 送端送RTS(Request to send) 收端回CTS(clear to send) 以及雙方欲溝通的時間duration
其他聽到RTS/CTS的節點在指定時間內不傳送

若兩節點同時想傳RTS並衝撞，會發現沒收到CTS，節點會隨機等時間後重試，由expoenetial backoff algorithm決定重等時間
:::


#### 3-2 分散式系統: 
由節點可自由移動，產生移動性與局部連通等特點要處理，故允許節點roam漫遊or固定接有線(Access point 無線基地台)，在透過分散式系統相連

node 使用scanning 選合適AP: 
1.節點送probe frame, ap reply probe reponse
2. node 選一AP 送association request 連接要求， AP reply Association response

分主動掃描與被動掃描: 主動為node自發scan，被動為AP定期發Beacon frame廣告自己這台AP能力

#### 3-3 frame format:![wifi_format](https://hackmd.io/_uploads/S1hCezg4Je.png)

4個mac addr 的解讀會依據ToDS , FromDS設定
若這兩Bits設為1代表的是此frame從DS來與去 
addr 1:RA(收端位址)對應從DS來 2:TA(送端位址) 3(DA)對應最後目的mac addr 4:SA(最後來源)

#### 3-4 wifi MAC:

![wifi_mac](https://hackmd.io/_uploads/rJp3WfgNyl.png)

:::danger
DCF分散式協調使用CSMA/CA的，且station工作站與AP都須提供DCF
PCF提供優先存取，由point coordinator決定誰有權傳送
自從wifi沒載體做collision dection，自然偵測link idle手段享用idle時長做區分，延伸出Inter-Frame space (IFS)

SIFS < PIFS < DIFS

DCF 在CSMA/CA基礎與random back time上，要求正確收到要立即回傳ACK frame，虛擬載波感測機制，又稱網路配置向量NAV，由RTS/CTS中的duration 來預測未來流量，並強制非通訊中的host設定NAV來避免他們通訊

基礎通訊協調規則:
送端送後，未偵測frame遺失或錯誤，收端必須立即回傳ACK frame ，間隔一個SIFS

SIFS : for ACK, CTS, poll(PCF用)，任何工作站要等link idle SIFS後才可傳這類frame
PIFS : PCF用在傳送免競爭之frame，等idle PIFS後可傳輸
DIFS : DCF用在傳送競爭frame，等idle DIFS後可船(還要考量backoff period)

開始傳非同步MPDU(MAC Protocol Data Unit)前，偵測link狀態若idle 等一DIFS後產bacloff period額外延遲避免競爭，等backoff period 變成0時才可船，period在idle 超過difs後才開始減

其他station透過RTS/CTS frame之durarion 設定NAV標記link被占用時間，此時間內非通訊雙方的station都不會傳輸直到NAV = 0 , link idle 等SFIS/PIFS/DIFS

![nav](https://hackmd.io/_uploads/Bk1q8feVke.png)
:::




PCF提供免競爭服務
工作站可成為point coordinate , PC能產生superframe(免競爭+競爭frame組成)，包含contention free period and contention preiod
以polling scheme為基礎，PC向master 工作站 傳CF-Down frame 給某一station A讓其免競爭，若A要傳免競爭frame，則一SIFS候傳CF-Up frame，仍一SIFS後PC繼續找station B傳CF down，若B沒意願則在超過PIFS後，PC就知道B不用，則傳CFdown給station C etc...
![pcf](https://hackmd.io/_uploads/ryRD8MgEke.png)





清大計算機網路 ch4-ch6 心得筆記 Bridge, VLAN, sliding window

#### ch1講了網路基礎 topo以及基礎術語與OSI架構和l2,l3介紹
#### ch2講了第二層實體區網ethernet topo, mac protocol (CSMA/CD), frame format
#### ch3講了第二層虛擬無限區網wifi concept, frame format, mac (CSMA/CA -- DCF/PCF)

現在ch4要講橋接器區網彼此連接及避免迴路的spnning tree algo
ch5會要講虛擬橋接網路，用邏輯方式把不同區網接一起更深度了解第二層，橋接器轉送與addr learn
ch6會講滑動視窗協議，是怎樣把不可靠傳輸變成可靠的

### ch4. IEEE 802.1D spanning tree algorithm
4-1 intro
4-2 frame轉送與addr學習
4-3 spaning tree algorithm

:::success
4-1. intro
橋接器(Bridge) MAC層的設備讓終端主機將實體分離的區網，試為邏輯上同一區
提供功能 : frame轉送過濾，mac addr學習，解決topo中迴圈
但是不對frame format , content修改
優點 : 可靠，效能，安全，跨區

為了轉送封包與addr learning，橋接器紀錄filter database 知道所有工作站位址

4-2 frame轉送與addr學習:
![4-2](https://hackmd.io/_uploads/SkZ8YGlNkl.png)
:::


4-3 spanning tree algorithm : 
橋接器有為一Bridge id 8 bytes(2 bytes for priority programable, 6 bytes for mac addr)

![def](https://hackmd.io/_uploads/S1n3FzgNJe.png)
:::danger
每個port有其Path Cost(Transmission cost)，計算方式為從此port出去到區網就要算此Cost
根據上圖定義可知，對每一bridge最重要就是決定root port亦即到root最低cost就是走這port就對了，對每一LAN最重要就是代理port亦即到這一LAN就是走這代理port有最低cost，由此就可訂出spanning tree algo

![alg](https://hackmd.io/_uploads/S17F5Me4Jx.png)
:::


在一開始大家都想當root bridge，但不知道其他人的bridge id ，所以就廣播，若A id 10  to B id 20，雙方互送就知道A be root ，B就轉發A的消息並且認A為老大

spanning tree 維護:
BPDU(橋接器協議資料單元 以RPC, root bridge id, root port id, bridge id為主)的傳輸由根橋接器發動，每隔一段時間送configuration BPDU，給所有區網

當一個橋接器收到其root port來的BPDU時，傳給她所有區網中有代理port的地區

跟橋接器會設定topology change flag告知拓樸異動，過濾資料庫的內容可能重學


ch5. Virtual Bridge LAN (IEEE 802.1Q)
5-1 vlan intro
5-2 vlan 架構，基於port皓的vlan
5-3 vlan 網路標籤

:::success
5-1 VLAN 目的優點
沒虛擬區網時，bridge轉送frame至所有port浪費頻寬與安全有問題
因此虛擬區網"管理邏輯群組內主機:，也具有類防火牆機制與流量控管

每個虛擬區網友VLAN ID(VID)，建虛擬區網需要轉送/過濾/protocol支援

三層式架構: 1.vlan 參數設定(MIBs) 2.配送/解析(Distribution/Resolution) 3.訊框轉送 Relay(Ingtess/Forwarding/Egress)

1.包刮設定虛擬區網包含那些port(based on port/mac/subnet等)以及參數(VID)，以port為大宗為例

2.配送Distribution將VLAN成員資訊送到橋接器，使橋接器能決定怎麼送
透過declaration protocol(eg GARP 通用屬性註冊協議) or Request/response protocol(eg SNMP 簡易網路管理協議)

3. 訊框轉送Relay負責frame上tag，Ingress rules將收到frame對應VLAN，Forawrding rule決定frame由那些port，egress rules針對frame轉換format增刪tag，顯性標籤在frame 放入VLAN ID 隱性標籤不放VID 只由frame本身資料(MAC位址or 第三層協議辨識碼等)來辨識其虛擬區網
:::


VLAN 拓樸:
![vlantopo](https://hackmd.io/_uploads/SyyckXeNye.png)

Access link是一個網路區段將多台非虛擬(no vid)連置虛擬橋接(has vid)的接口，此link上frame都用隱性標籤，不貼VID

Trunk link在VLAN BRIDGE間的網路區段轉送不同VLAN frame，要求frame要填顯性標籤

Hybrid link是依網路區段連接虛擬與(非)虛擬設備，指要求標籤要統一，統一顯性or統一隱性





再來把spanning tree 與VLAN和橋接器結合
由sapnning tree提供任一主機傳輸路徑，所有區網傳輸沿著擴張樹建構
，區網拓樸是動態變動，而橋接器對每個VLAN上紀錄(member set,port) (untagged set, port)兩個表，member包含該vlan host, switch(VID)而untag如其名不記錄(VID)的switch，舉例如下圖

![vlan2t](https://hackmd.io/_uploads/Hyfg4MbEkx.png)

虛擬區域網路的位址學習
分為共享Shared VLAN Learning(SVL)與獨立Indepedent VLAN Learning(IVL)

虛擬區域網路標籤結構
使用Tag Protocol Identifier (TPID)標籤協議辨識碼(SNAP編碼)以及Tag Contorl Information (TCI)標籤控制訊息裡面包含用戶優先權，VID辨識碼

![TCI](https://hackmd.io/_uploads/Sk5vrfZ4ke.png)

標籤格式使用SNAP(subnetwork access protocol)子網路存取協議，是再區網上傳輸IP封包的標準協議

![snap](https://hackmd.io/_uploads/SyTgUf-41g.png)


### ch6 可靠傳輸機制
6-1 intro
6-2 stop and wati
6-3 silding windown protocol

6-1 intro
可靠傳輸地為了處理link傳輸錯誤(甚至IP層的不可靠問題 TCP用可靠sliding window處理)，要可靠必須重送，使用ACK與time out機制達到重送，又稱自動重複請求ARQ (Automatic Repeat ReQuest)

6-2 stop and wait
觀念是送端送完一個frame後必須等收到ACK回復，若未收到則發生time out就會重送，並且分以下四場景
(a) ACK再time out 前抵達 
(b) 原始送frame遺失
(c) ACK遺失
(d) time out 太快 or ACK延遲

c,d延伸出重複訊框，收端是否要收，可用1 bit sequence 標示訊框是否重送，收端就可忽略不收

但是同一時間只有一個frame在link上太浪費頻寬，因此送端使用sliding window 一次送多個

:::danger
6-3 sliding window
送端給每個Frame 一個seq num序號，並且記錄
傳送視窗大小SWS
最後收到肯定LAR
最後送的訊框LFS
並且維持LFS - LAR <= SWS

送端對每個送出的frame有計時器，超時則重送，並且需要一SWS的緩衝區放置送出的frame，方可重傳時找到frame

收端紀錄三個變數
接收視窗大小RWS
最大可接受訊框LAR
最後收到訊框LFR
並且維持LAF-LFR <= RWS
收端只接收 LFR < seq num < LAF的訊框
:::


累積是肯定回覆: seqNumToAck表示未收到肯定的最大序號，及所有小於此值得都已收到
![seq](https://hackmd.io/_uploads/SyBtYM-VJg.png)

sliding window當封包遺失，就無法使link滿載
改進方式為NAK/附加肯定回覆/選擇性肯定回覆
![3wq](https://hackmd.io/_uploads/rklyczWVkg.png)


清大計算機網路ch7 IP,ch8 TCP/UDP

個人心得筆記 -- james ray 

回顧一下
ch4講了bridge 跟spanning tree algorithm
ch5講了vlan and bridge
ch6講了可靠傳輸機制做法跟silding windows

現在ch7要進到全球網路定址 Internet protocol

#### ipv6 很多有趣的包括 unicase/anycast/multicast , 拓展標頭 使標頭長度固定, 路由不用分段 只由源端主機分段, ICMPv6 等細節再ipv6技術的心得筆記，與wifi的原文書心得筆記一樣 很多細節 要時常溫故知新

ch7 Internet Protocol
7-1 intro and ip and router
7-2 ip子網路subnetting
7-3 Classless addressing
7-4 routing protocol -- Distance vector protocol and Link state protocol

7-1 intro
IP : 一個全球定址的網路協議，提供一種識別網路上所有主機的方法，定義基礎架構使節點與網路相連通
特點: connetionless (傳輸前不須先建連線), unreliable 盡力式傳送(封包可能遺失亂序重複delay)

路由器，第三層的設備處理layer 3 data package，以store and forward (全收到才送)，提供IP addr 查詢表[ip,next host/router](查路由表 由路由協議RIP/OSPF/BGP等建構)，儲存IP/MAC對應表[ip,mac]

新的路由器使用第三層交換器晶片與IP位址查詢晶片(採用快速硬體查詢技術)，不具隨插即用特色

IP package format:

![ipfor](https://hackmd.io/_uploads/ByUtmEbVJe.png)

IP header 中的type of servise (ToS):
![ipTos](https://hackmd.io/_uploads/SJRimN-4yl.png)

Ip address特性:
32bits(ipv4) 具階層性結構: 網路位址+主機位址

路由器IP封包轉送作法:
每封包含有目的IP位址，若router與目的主機網路直連，直接發給目的主機
，若不直連主機就發給某路由根據路油表有登錄網路編號與對應下一站。

IP封包切割組合:
可傳輸單元最大直(MTU):
ethernet(1518bytes),ieee802.11wifi(2312bytes),FDDI光纖(4500bytes)
策略:當收到封包超過要送的區網大小MTU，就切成多個小封包segment，由收端IP協議負責組合為原封包，這些片段攜帶相同辨識碼，任一片段遺失則IP丟棄該封包

example:
![ip切](https://hackmd.io/_uploads/B1xXSNW4yx.png)

網路層路由器特色:
過濾MAC層的廣播與群播，只處理網路層協議相關內容
提供封包切割與重組(重組只在主機端)
使用ip addr過濾封包
可支援流量計費

端點主機與路由器間直接溝通:
路由功能強可彈性設定技術門檻高
可調節不同傳輸媒介的網速差異
支援壅塞控制與避免機制 (安全機制等 這裡面很多有趣的 查看computer network a top down approach 7 edition)

路由協議以任意拓樸架構將第三層網路互連
動態路由協議 RIP(routing information protocol) and OSPF ( open shorest path first)

bridge vs router:
![bridge vs router](https://hackmd.io/_uploads/ryfnLVZN1x.png)


7-2 ip subnetting:
對位址/路由階層架構 新增一層:子網路

子網路遮罩subnet masks: 針對class a and class B host addr 定義區分方式，
目的: "規範子網路大小"，為了允許一個單一的站點能擁有多個區域網路。
![subnet](https://hackmd.io/_uploads/B1O8Fj-4Jl.png)

子網路遮罩 不依定全是連續的1
一個實體網路分成多個子網路，在互聯網的其他地方看不到這些仔網路


7-3 classless addressing(無分級網址)
位處了網路規模問題，32 bit ip 位址不夠用，且固定分址分配效率低class b 65535, class c 255個 不易分

CIDR使用累積路由(aggregate routes) 在路油表中使用單一項目告訴路由器，如何到達大量不同的網段

因此 想法是 不分隨機往段，改分發連續往段方便管理，eg 192.4.16~192.4.31
發現這些往段前20bits相同前墜
可以表達為192.4.16/20 ， /x表示錢xbits都相同
路由協議對無分級的位址 要了解 "網路號碼"可能是任何長度 用<長度,數值>一對數字表示
CIDR意思是往段的錢墜可以世界於2~32位元的任一直
以最長前墜匹配優先

位址解析協議(ARP):
由IP位址找出對應的MAC網路位址
ARP: 有IP位址與實體MAC位址的對照表，若IP不再對照表中則廣播請求封包，目的地主機收到請求後回應他實體MAC位置，對照表項目具時效性，太久沒更新則刪除

ARP package format:
![arpf](https://hackmd.io/_uploads/H1xlzwtGVke.png)

DHCP(Dynamic host configuration protocol)
由DHCP伺服器負責提供設置資訊給主機，在管理網域內至少一台DHCP server維護管理分配可用IP給主機，自動化設置

新連接主機發送DHCP Discover 至特殊IPaddr 255.255.255.255
DHCP轉送代理傳給server等回復

![dhcp](https://hackmd.io/_uploads/HJXowFMV1x.png)

ICMP (internet control message protocol) 互聯網控制訊息協議
定義一組錯誤訊息，當路由無法處理ip封包能將錯誤訊息回給來源主機
包括 TTL = 0 (防止無盡迴圈) ip checksum 找到錯誤 封包重組失敗，link or host 故障無法到達目的地

7-4 路由協議
轉送forwarding: 根據目的為址與路油表選擇輸出port，轉送表含有網路編號與MAC資訊
路由routing: 建立路油表的程序 由路由協議建立 包含網路編號與下站對應

![routtab](https://hackmd.io/_uploads/ryEYdYME1g.png)

網路可視為圖，路由就是要找任意兩節點的 "最小成本路徑"lcp
分散且動態協議 -- Distance vector/Link state

Distance vector protocol:
每節點建立1D array 記錄到所有其他點距離並傳給鄰居知道，鄰居根據此表update自己table也傳給自己鄰居

每過T秒，每個路由將它路遊表送給它的鄰居，問題是好的(cost low)消息反應快，壞的反應慢，還有無限技術問題，ABC有迴圈，且E址與A連通，若AE link斷掉，A雖會公布與E距離inf但是BC會以為能從A到E而不段增加distance vector

解決就是水平分割(當發路遊給鄰居，不將從此鄰居學的路徑回送)或是水平分割毒藥反向(會將從鄰居學的路徑放負面消息回傳)

Link state protocol: 策略是將所有直聯的link資訊發給所有節點
LS Package 內包含產此package之node id , link cost, SEQNO(序號)，TTL
![brod](https://hackmd.io/_uploads/ByTPqtGE1x.png)

Open Shortest path first(OSPF) 每個路遊根據Dijkstra algorithm及目前LSP封包去計算路油表，找出從該路由器到所有節點的最短路徑




ch8 傳輸層通訊協定:
8-1 intro end to end 
8-2 UDP
8-3 TCP

8-1 此層協定期望 "保證傳送" "順序正確" "不重送" "流量控制" 等特性
但IP層是unreliabe 服務

因此此層挑戰是 "設計演算法"將下層不可靠的網路層服務變成應用程式所需的服務 (openwrt?)
不可靠ip ->不可靠 UDP
不可靠ip -> 可靠 TCP (用到ch6 wilding window, ack/3 handshake/ retrasmission等機制)

7-2 UDP(簡易反多工器):

![udp format](https://hackmd.io/_uploads/rJn3itzNyl.png)

![demut](https://hackmd.io/_uploads/HkDRitMNkl.png)

7-3 TCP 可靠位元組串流協議
提供reliable, connection orientded, byte-stream service

Flow control: 預防送方過多流量超出負荷
congestion control: 預防網路被注入過多資料使router/switch or link overhead.

TCP sliding window algorithm需要考慮:
1.TCP支援不同主機APP建立logical connection
2.不同TCP之RTT時間差異大
3.封包在互聯網傳遞可能重新排序


![tcp header format](https://hackmd.io/_uploads/ry1KnYMNJx.png)

ACK/sequenceNum/AdvertisedWindow用於TCP sliding window algorithm
因TCP位元導向，資料位元組有序號，而sequenceNum為此segment上第一個位元組的序號

control flag 有SYN fin ack 用於initial(or end) connection，reset 用於當未預期的封包進時(attack. tcp close but come in package) 給回覆
，URG 表示urgent data 應有priority over un-urgent data傳輸，push表示發送者要求啟動推擠動作，只是收端通知APP有此動作

TCP送端將APP傳送的位元組儲存，等位元組數量夠形成資料段segments，再送至TCP目的主機
TCP收端將segment放入接收buffer在等APP讀取

TCP connect 流程:
![tcp connect](https://hackmd.io/_uploads/HJSIxCQVye.png)

3-way handshake:
![3way](https://hackmd.io/_uploads/H1Pdx0mNkg.png)

close connection:
![close1](https://hackmd.io/_uploads/H1-3lRQVkl.png)

![close2](https://hackmd.io/_uploads/H1XagR7Ekx.png)

![TCP-client](https://hackmd.io/_uploads/Hy3dyvcSyx.png)

![TCP-server](https://hackmd.io/_uploads/Bkq9kvqSye.png)





Time out 設定: 對每個segment/ack測量sampleRTT並設計weight RTT 
EstRTT = a * EstRTT + (1-a)*sampleRTT
TimeOut = 2* EstRTT
![est](https://hackmd.io/_uploads/r16V-AX4yl.png)

問題是congestion使RTT變異 變化大等問題沒處理好-->延伸出Jacobson/Karels algorithm

![est2](https://hackmd.io/_uploads/HJauZRXEkl.png)

![fast-retry](https://hackmd.io/_uploads/BJg0JDcrJe.png)

![self-clocking](https://hackmd.io/_uploads/SJelgPqr1g.png)



TCP congestion control:
概念是讓每個算端知道目前網路能力(容量)，以便自覺要送多少，稱self-clocking(自我時序調節)，透過ACKs調節傳輸封包節奏

AIMD算法(additive increase Muldiplicative decrease)

congestionWindow: 限制送端在連線上能送多少data
未回覆資料最大允許值是congestionWindow與advertised window(接收方在一個RTT 內，最多能接收多少位元組資料？)的最小值

TCP有效視窗被修改為 maxWindow = MIN(congestionWindow, AdvertisedWindow)
Effective Window = maxWindow - (lastByteSend - lastByteAck)
注意與ch6 sliding window 使用frame不同 ，TCP是byte oriented (一堆byte合成一個Segment) 所以式last byte sent/ack

TCP送端允許傳送的速度 不可超過此兩者最慢速度  網路(壅塞控制) 目的主機(流量控制)

![AIMD](https://hackmd.io/_uploads/rkFSWDqH1e.png)

![AIMD-increment](https://hackmd.io/_uploads/S17d-D9Syl.png)

Slow start: 初始快速增加傳送的data量，指數方式(每一RTT將封包加倍)增加contestionWindow而非線性，值到偵測壅塞用倍數減少

![control](https://hackmd.io/_uploads/r1bkGv5BJl.png)

![table](https://hackmd.io/_uploads/SJ5Hfw5rJx.png)



