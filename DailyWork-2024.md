# Daily Work - 2024

- Common:
  - Containerbuild
  - GitLab
    - Feed token: LDzMXPkzNQV4dvL2NvEK
    - New personal access token: "REMOVED_FOR_SECURITY"
  - CBTPassBLE
    - Source code (https://gitlab.cybertan.com.tw:20443/9GA300A8/eap/CBTPassBLE/-/tree/main)
    - release 時需把編完之後的 CBTPassBLE 上傳到 (https://gitlab.cybertan.com.tw:20443/9GA300A8/eap/cybertan_eap_trunk/-/tree/main/feeds/ble_cbt/cbt_ble/files)
  - scp Containerbuild@192.168.20.102:/home/Containerbuild/eric_build/CBTPassBLE/CBTPassBLE ./CBTPassBLE
  - git reset xxxxx --hard (git log 取得前 5 碼)
  - registry.sonicfi-networks.com/66092e82/release/cbtmain:v2.6.0-arm64
  - screen /dev/tty.usbserial-FT2LZBUE 115200

# 工作日誌 - 2024
- 2024/12/31
  - 01_api_tests
    - test_001_dashboard_api.TestDashboardAPI()
      - test_get_dashboard_info_api: 比對回傳結構與結果
      - 新增測試 getControllerServiceDashboard
        - 比對回傳結構與結果
    - 
- 2024/12/24
  - UpdateParSwitchPortDto.ingressBandwidthRate / egressBandwidthRate 的 maximum value (1000 / 10000) ?
  - ethType 是否可修改? 不可
  - 由於沒有配合實體設備，所以無法修改 ethTye，目前只能測試 Copper / None ('2.5G'，'2.5G-Auto'測試失敗?)
  - issue 1: 黑盒測試需要搭配 DB 內的資料才能測試，其他 RD 要執行會失敗
    - 提供搭配的 DB 到 git? migration 也要考量此 DB?
    - 從 RD 的 nodeDB 撈所需的設備來測試，根據 compatible 來決定要執行的 TestCase
  - issue 2: 部分欄位(eg. lag.groupList)具有不可逆性，一旦設定了就無法恢復成初始樣子，這樣會影響 get 類型的測項
    - get 時不檢查這類型的欄位，只確認有這個欄位存在
  - issue 3: 部分欄位(eg. linkRate 需搭配 ethType 狀態)的測試需實際設備狀態的配合
    - eg1. linkRate 欄位由於設備限制(compatible)目前無法測到 copper 中的 2.5G 的值
    - eg2. 目前 port25/26 的 ethType 預設值是 none，故無法同時測到 fiber1g / fiber10g 的狀況
    - 方案
      - 找實際設備放進 DB 中
      - 測試前使用 SQL 語法修改 DB 資料 (eg. ethType)
      - 此種狀況採用白盒測試來進行
      
  - 提醒: 部分欄位(eg. qos.1pMapping 需搭配 qos.mode = '8021p')的恢復資料時需配合其他欄位才有效果.

- 2024/12/13
  - thirdParty.cbtSwitch
    - stp (舊功能: spanning tree)
    - dhcpSnooping (switch文件沒駝峰?)
    - fastPoe (PoE Management)
    - perpetualPoe (PoE Management)
    - lag
      - groupList (最多25組，每組 2~8 port，port不能重複) (lag = true)
    - lldp (true/false，true)
    - ieee8021x (舊功能: true/false，false)
    - radiusProfile (舊功能: all port 共用 profile) (ieee8021x = true)
    - captiveProfiles
  - thirdParty.cbtSwitch.portLists
    - lldpMode (0~3，default: 1) (lldp = true? no)
    - ieee8021xCtl (舊功能: auto/forceAuth/forceUnauth，forceAuth) (ieee8021x = true)
    - ieee8021xCtlType (portBased/macBased，portBased) (ieee8021xCtl = auto)
    - ieee8021xCtlVlanAssignment (true/false，false) (ieee8021xCtl = auto)
    - ieee8021xCtlMacFormat (aabbcc~，aa:bb:cc~?) (ieee8021xCtl = auto)
    - snoopingTrustedPort (true/false，true) (dhcpSnooping = true)
    - loopDetect (true/false，false)
    - radiusProfile (default = '' means disable) (ieee8021x: all port 共用 profile，舊功能)
    - captiveProfileName (每個port可以分開選? yes)
    - poe power cycle: (command: port reset) (ps. No test case: use owgw API directly)
      - GET /api/owgw/poePowerCycle => getPoePowerCycle 
      - POST /api/owgw/poePowerCycle => postPoePowerCycle
    - get lldp by serialNumber + portNumber (ps. No test case: use owgw API directly)
      - GET /api/par-switch-ports/${switchSerialNumber}/port/${portNumber}/lldp (ps. No test case: use owgw API directly)
      - getPortLldpBySerialNumber(serialNumber:string，portNumber:number)
      - response: SwitchStatisticCbtSwitchLldp[]
  - 黑盒測試
    - 測試 dhcpSnooping 為 true 時，可在任意 port 設定 snoopingTrustedPort = true，並反應在 configuration 上
    - 測試 dhcpSnooping 為 false 時，在任意 port 設定 snoopingTrustedPort = true 時，不會反應在 configuration 上 (ps. 目前不用測這項)
    - 測試 fastPoe 為 true/false 時，會反應在 configuration 上
    - 測試 perpetualPoe 為 true/false 時，會反應在 configuration 上
    - 測試 lag 為 true 時，會反應在configuration，並且可動態設定最多25組的port group list 
      - (每一組Group ID最多綁定8個實體port，最少需綁定2個實體port，每組 port 之間不能重複 )
      - 其中 group list 新增在 configuration 的哪個欄位? (Ans:thirdParty.cbtSwitch.groupList)
    - 測試 lag 為 false 時，會反應在configuration，但 port group list 不作任何更動?
    - 測試 lldp 為 true 時，任意 port 可以設定 lldpMode，並反應在confituration.
    - 測試 lldp 為 false 時，任意 port 仍有機會修改 lldpMode，會反應在confituration.(ps. 此時底層因lldp為false，所以 lldpMode 不起作用)
    - 測試 ieee8021xCtl 為 auto 時，任意 port 可以設定聯動欄位 ( ieee8021xCtlType，ieee8021xCtlVlanAssignment，ieee8021xCtlMacFormat) ，並反應在confituration
    - 測試 ieee8021xCtl 為非 auto 時，任意 port 若透過API修改聯動欄位 ( ieee8021xCtlType，ieee8021xCtlVlanAssignment，ieee8021xCtlMacFormat) ，會反應在confituration (ps. 此時底層因ieee8021xCtl 其他設定值，所以聯動欄位不起作用)
    - 測試 snoopingTrustedPort / loopDetect 修改是否可正確反應在 configuration 上
    - 測試 radiusProfile 是否可正確反應在 configuration 上
    - 測試 radiusProfile 是否可偵測出限制的狀況 (ps.若其他 port 已經有設定一組 radiusProfile，則必須選相同的 profile，否則需報錯)
    - 測試任意 port 都可以選擇 captiveProfile 來設定，並可正確的反應在 configuration 上
    - 測試任意 port 的 poe power cycle 是否有正確推送 command 指令


- 2024/12/11
  - date -s "2024-12-11 13:48"
  - "00:45:e2:b0:69:63"，
  - "24:fe:9a:0f:50:18"，

- 2024/12/05
  - wifi 設定新的 captive portal 會出現不用輸入帳密即可連線的狀況 (複製步驟待確認)
  - captive portal 不能修改
  - 使用 captive 時會出現 topology 沒有顯示 client 的狀況
  - 移除並新增相同名稱的 captive portal 似乎會出問題?

- 2024/11/29
  - registry.sonicfi-networks.com/66092e82/release/cbtmain:v2.6.0-arm64
  - CreateParCaptivePortalProfileDto
    - idleTime / connectTime: 作用?，單位? (hour)

- 2024/11/28
  - SysLog測試環境
    - garyLog server: 192.168.50.240:21601
    - myLog server: 192.168.153.10:514
    - 問題一: 公司 syslog server (grayLog) 顯示時間有8小時的誤差，應該跟 server 本身的時區設定有關，會再跟IT人員反映.
    - 問題二: MAC auth 失敗回報 sysLog，因底層尚未整合出一包image，故無法測試. (ps. 大Kelly 預計與 capative portal 的版本一起出一版)
- 2024/11/27
  - EAP vlan 測試情境
    - 目標: 讓一台 CeilingMount 提供兩組 SSID 具備不同網域，上行接 WallMount EAP-W，WallMount EAP 上行接 Controller
    - 作法:
      - EAP-C 的 SSID 各設定一組 native vlan (PVID) 分別為 (vlan-10，vlan-20)，EAP-C (CeilingMount) wan port 設定2組 tag vlan(vlan-10，vlan-20)，EAP-W (WallMount) 下行跟 EAP-C 的 port 設定兩組 tag vlan (vlan-10，vlan-20)，EAP-W 上行接 Controller 的 port 設定兩組 tag vlan (vlan-10，vlan-20)，最後在 Controller 建立兩組 network 分別綁定 vlan-19，vlan-20

- 2024/11/26
  - registry.cloud.cybertan.tw/dev/alpha/cbtmain
  - registry.cloud.cybertan.tw/66092e82/release/cbtmain
  - uci show wireless |grep mac_auth
  - iwconfig
  - logread -f |grep hostapd
- 2024/11/23
  - CreateParSsidDto.cbtMacAddressFilter
    - macs / type 是否需要加 '?'
  - ParSsid
    - 是否有 cbtRadiusMacAuthEnable ?
    - parRadiusProfileId 是 '?' 但 ParSsidsService.updateOne 卻是 required ?
  - ParRadiusProfile
    - interimUpdateInterval 拼錯字? 作用?
  - parSsidService
    - updateOne: 多呼叫一次checkUpdateApplyConfiguration (已確認，Jason修改)
- 2024/11/22
  - fail case
    - par-eap-ports.service.spec.ts (Done)
    - par-radius-profiles.service.spec.ts (Done)
    - par-ssids.service.spec.ts (Done)
    - par-switch-ports.service.spec.ts (Done)
    - par-vlan-profiles.service.spec.ts (Done)
    - systems.service.spec.ts (Done)
    - command-list.service.spec.ts (Remove，ps. 相關功能已經轉到sysLog，可以考慮移除此 unit test)

- 2024/10/24
  - // lldp 
  - // vlanProfileName
  - // rx_ant_avail
  - // tx_ant_avail
  - // connected
  - // lan4
  - // features (Fix)
  - // totalPortQuantity (已宣告)
  - ComplexDevice.name // eric-TODO: lost in data
  - ControllerConfiguration.unit.timezone // eric-TODO: original data doesn't have 'devAttrs'
  - ControllerConfiguration.third-party.devAttrs // eric-TODO: original data doesn't have 'timezone'

- 2024/11/05
  - capative portal認證同步issues: capative portal之間如何同步client的認證狀態.
  - GetFirmwaresDto.cid作用?
  - Should return [] when TaskUpgradeMap status is invalid (若狀態 <Complete 是否需轉成confirm類型的?)
  - ParseSystem.smtpServer / remoteServer / parDeviceConfig 為 optional 應該宣告為 '?' ?
  - mis@123451
- 2024/11/04
  - updateOngoingStatus
    - toStatus === TaskUpgradeMapStatus.InProcessDownloading 需檢查現行狀態?
    - should reset defer count when status from InProcessDownloaded to InProcessTriggerStageUpgrade?
    - 更新狀態允許一次往前跳多個步驟?
    - 不允許狀態回溯，已包括原狀態已經是結束的狀態；不允許再次變更狀態?
    - `${logMsgPrefix} [文法 not] allowed to change status to ${logMsgToStatus}，when not [語意需優化 unsupported] stage-[錯字 upgreade] or upgrade-flow not started`
    - "已經開始更新流程的項目，才允許更新狀態" : 此句下方的邏輯不太懂，看起來是相反的邏輯.

- 2024/11/01
  - emitStageDownloadCommand 任何失敗應該都要標示錯誤以免造成流程卡住?
  
- 2024/10/23
  - active log 的設定值需存於預設以利新裝置上線apply嗎?
  - 密碼長度應該還需要有上限以免遭遇攻擊?
  - how to mock EventEmitter2 APIs?
  - mail / (system + remote) log / ntp 存放位置?

- 2024/10/21
  - merge unit test to q4-develop need to update
    - par-interfaces.service.spec 
      - should create a new ParInterface successfully (Done)
      - should rollback transaction when configuration apply occurs exception (Done)
      - should rollback transaction when interface name is reserved (eg. "default) (Done)
      - should rollback transaction when interface name conflict (Done)
      - should rollback transaction when interface vlan conflict (Done)
      - should throw exception when interface name is reserved (eg. "default) (Done)
      - should throw exception when interface is not allow erased (Done)
    - par-radios.service.spec (Done)
  - consider to add test cases
    - firmware.service (Todo)
    - service-ver.service (ps. 邏輯單純 + file access)
    - users.service (Todo)
    - kafka.service (Todo)
    - task.service (Todo - batch fw upgrade)
    - task-upgrade-map.service (Todo - batch fw upgrade)
  - provider
    - api-cache.service (ps. 邏輯單純)
    - auth.service (Todo: 不清楚資料來源)
    - capabilities.service (Done: 部分API邏輯單純，主要測試有被外部調用的API)
    - cloud-access.service (ps. 直接與外部系統互動)
    - command-list.service (Done)
    - device-logs.service (Todo)
    - devices.service (ps. 邏輯單純)
    - owgw-devices.service (ps. API 直接使用 ucentralgw API)
    - par-radio-device-maps.service (ps. 邏輯單純 + 使用 SQL)
    - par-ssid-device-maps.service (ps. 邏輯單純 + 使用 SQL)
  - sse

- 2024/10/14
  - innerTopology
    - ControllersService (done)
    - DashboardService
    - EapsService (done)
    - SwitchesService (done)
  - devicesService.findOne
    - ControllersService (done)
    - EapsService (done)
  - statisticsService.findEap
    - EapsService (done)
- 2024/10/08
"Backend: 
    - 提供 firmwareAutoCheckTime  GET API //ps. 取得自動檢查內容
        - response: //eg. {scheduleType:'daily'，time:2400} 表示每日半夜12點執行自動檢查
        {
           scheduleType: string，
           time: number
        } 
        - 從 Task table 撈資料 (type:1，name: 'CheckFirmware' ) 
    - 提供 firmwareAutoCheckTime  POST API//ps. 設定自動檢查內容
        - 參數 //eg. {scheduleType:'daily'，time:0} 表示不做自動檢查
        {
           scheduleType: string，
           time: number
        } 
        - response: 同參數格式
        - 更新 Task table 資料 (type:1，name: 'CheckFirmware' ) 
        -  根據參數動態建立或移除 CronJob (name:  firmwareAutoCheck)
    -  CronJob firmwareAutoCheck
        - 根據schedule時間Call checkforNewFirmware ，並依據response內容決定是否要發信通知用戶
        - 檢查依據為 response 中 fwList 欄位不為空array(eg. [])，需發信通知用戶 (ps. call mailService.sendNewFwNotification(fwList: []))"
        
  - 1 to 1 面談準備
    - 1. 與過往比較
      - 完成任務和項目
        - 能獨立負責完成較大的任務: Unit test 開發(ps. 包括學習如何導入 Copilot AI 與 jest 框架 survey)，精品展假資料準備(包括Jack臨時提出真假資料混合的需求)也都如期完成.
      - 項目交付時間: 持續保持目標時間內完成工作項目.
      - 新技術學習: survey Copilot AI 並於讀書會分享使用心得，並持續關注AI的發展，以及網通相關security的AI運用.
      - 設計工作品質: 持續強化工作目標的釐清與開發規格的確認，
    2.  工作上你有觀察到那些沒效率的部分?
      - 在開發目標溝通上因為不夠精準導致開發時間上的重工
    3.  過去半年有甚麼開心不開心想分享的?
      - 對於後續兩項任務的開發感到開心，假資料的其他用途
    - 對於未來的期許
      - 跟主管溝通上氣氛可以更融洽愉快，並在開發品質上取得更多的信任
      - 多嘗試表達意見
      - 未來能有機會負責更重要的任務
      - APP 持續安排空檔開發，以利未來擔任後續維護的工作
      - unit test 後續API異動時，unit test調整責任歸屬? API新增或移除，API內容更新
- 2024/10/04
  - dashboard.service.spec.ts
    - this.parDevicesRepository.findOne
    - this.usersService.findAll();
    - await this.parRadiosRepository.findBy({ erasable: 0 })
  - 前端: 
    - 增加Auto check time menu.(不自動檢查(0)，00:30(30)，1:00(100)，1:30(130)，....23:30(2330)，24:00(2400))
    - Call FirmwareAutoCheckTime GET API並在依照response時顯示Auto Checked時間
    - Call FirmwareAutoCheckTime POST API並提供Auto Checked的類型與時間來設定auto check schedule
  - 後端:
    - Migration時建立一筆不自動檢查的資料
    - 提供GetAutoCheckTime GET API //ps. 目前僅提供每日的自動檢查
      - response: {schedule: string，time: number} //eg. {schedule:'daily'，time:2400} 表示每日半夜12點執行自動檢查
    - 提供SetAutoCheckTime POST API//ps. 目前僅提供每日的自動檢查
      - 參數 {schedule: string，time: number} //eg. {schedule:'daily'，time:0} 表示不做自動檢查
    - 根據 Schedule 建立 CronJob (Name: AutoFwCheck)，CronJob 會根據用戶設定的daily check時間來檢查所有設備的版本，並更新cache內容，若發現有最新版本，則寄信通知用戶.
    - 根據 Schedule 建立或移除 CronJob (Name: CheckFirmware)
        - CronJob時間到Call checkforNewFirmware ，根據response內容決定是否要發信通知用戶
        -  檢查依據為 response 中 fwList 欄位不為空array(eg. [])，需發信通知用戶 (ps. call mailService.sendNewFwNotification(fwList []))
    - 自動檢查建議新增一個flag欄位，這樣之後確認每週或每月檢查或不檢查時，可以更直覺
    - 提供用戶可以選擇每月最後一天的檢查?
- 2024/10/01
  - 第一輪(merge前)
    - eaps.service.spec.ts (Ready)
    - controllers.service.spec.ts (Ready)
    - switches.service.spec.ts (Ready)
    - clients.service.spec.ts (Ready)
      - getClientInfos (Pass)
      - getOne (Pass)
      - getClientInfosWithUplinkAndLinkRate
      - private handleSwitchWiredClientInfos
      - private handleEapWiredClientInfos
      - private handleCrontollerWiredClientInfos
    - par-vlan-profiles.service.spec.ts (Ready)
    - par-ssids.service.spec.ts (Ready)
    - par-radios.service.spec.ts (Ready)
    - par-radius-profiles.service.spec.ts (Ready)
    - par-interfaces.service.spec.ts (Ready)
      - should throw exception when interface name is reserved
    - par-controller-ports.service.spec.ts (Ready)
    - par-switch-ports.service.spec.ts (Ready)
    - par-eap-ports.service.spec.ts (Ready)
    - par-devices.service.spec.ts (Ready)

  - 第二輪(merge後)
    - eaps.service.spec.ts (Ready)
    - controllers.service.spec.ts (Ready)
      - should get WAN information successfully (ps. 需考量需考量checkWanLanConflict)
      - should update Controller upstream as static IP mode successfully (ps. 需考量需考量checkWanLanConflict)
    - switches.service.spec.ts (Fail)
      - should return an array of SwitchInfo objects when serialNumber is not provided
        - third-party device 缺 macAddr?
      - should return a SwitchDetailInfo object when serialNumber is provided
        - third-party device 缺 macAddr?
    - clients.service.spec.ts (Ready)
    - par-vlan-profiles.service.spec.ts (Ready)
    - par-ssids.service.spec.ts (Ready)
    - par-radios.service.spec.ts (Ready)
    - par-radius-profiles.service.spec.ts (Ready)
    - par-interfaces.service.spec.ts (Ready)
      - should create a new ParInterface successfully (ps. 需考量checkWanLanConflict)
      - should rollback transaction when configuration apply occurs exception (ps. 需考量checkWanLanConflict)
      - should update the parInterface successfully (ps. 需考量checkWanLanConflict)
      - should rollback transaction when interface save occurs exception (ps. 需考量checkWanLanConflict)
      - should rollback transaction when configuration apply occurs exception (ps. 需考量checkWanLanConflict)
    - par-controller-ports.service.spec.ts (Ready)
    - par-switch-ports.service.spec.ts (Ready)
      - should throw exception when taggedParVlanIds is too large (ps. spec修改，新增MAX_SWITCH_TAGGED_VLANS)
    - par-eap-ports.service.spec.ts (Ready)
    - par-devices.service.spec.ts (Ready)
  
- 2024/09/29
  - 員工自評

- 2024/09/27
  - eaps
    - getProfile vs profileMapIds get/put名稱建議對稱!

- 2024/09/16
  - EAP Demo名稱尚未修改成實際有Demo的設備為'[Demo]'開頭
    - innterTopology / toTreeNode 需移除[Demo]
    - recovery db真實設備需增加[Demo]
  - 移除pending設備，並反應到recovery DB.

- 2024/09/12
  - FWA是否需要可以Run？
  - Firebase的帳密?
  - NetMask useable host?

- 2024/09/11
  - Bluetooth文件缺失，未依照文件傳輸(跟文件的差異?)
  - Bluetooth Demon不明原因死亡(反覆setup? 指的是首次安裝結束後，馬上又factory default進行首次安裝?)
  - Controller restart WAN孔失效(同上)
  - 使用者聲明、隱私權與資料使用聲明(確認現狀?)
  - 部分API透過Bluetooth無法取得Data（待驗證，那些API?）
  - Reset後帳號密碼未清除(待驗證?)
  - 藍芽呼叫API Token沒有延長 (待驗證?)
  - Cloud Domain更換，等待IT確認後一同更換。(待驗證?)
  - 字型更換。
    - Montaerrat。
    - Arial可能不能商用，是否已購買授權，授權範圍有限制嗎？
    - 透過css使用系統字體可以，但若是直接指定是否可以？ 且APP不適用
  - APP Icon限制 (參考來源?)
    - 不得使用文字圖形顯示排名。
    - 不得使用文字圖形參與計畫。
    - 不得使用陰影。
    - 背景應填滿不得使用圓角
  - Splash Icon限制 (參考來源?)
  - 開發者Email: 不能使用cybertan信箱(是因為sonicfi品牌的緣故?)



- 2024/09/03
  - 精品展新需求
    - 假資料設備名稱要加上Demo字眼. (Eric)
    - 精簡假資料topology(保留一跳EAP與訊號過低的client，去除過多的client保留2~3個即可) (Eric)
    - topology的假資料節點與client的統計資訊也使用假資料 (Eric)
    - eventList使用假資料，保留20筆左右的資料量，另外不要擠在一起 (Eric)
    - qoEAI使用假資料 (Eric)
    - 增加各類profile讓畫面看起來豐富，包括「Static Route & Firewall」 增加 disable 的設定. (Eric)
    - 提供client設備以豐富WiFi 6 Scan內容 (Jack安排)
    - 各類default profile/WiFi/WAN的內容不允許修改? (ps. 這需請Jack決定，由前端Web/APP配後修改)
- 2024/08/30
  - 精品展isses
    - WAN 修改可能造成外網無法正常Access (ps. 建議WAN部分功能不能修改)
    - Default WiFi 修改可能造成參展手機無法連線.(ps. 建議幾組 Default profile都不要讓用戶修改.)
    - WAN / LAN 網域衝突會造成內網設備要不到IP. (ps. LAN的網域預設使用冷門一點的，並且不要讓用戶修改)

- 2024/08/27
  - 方案一: 前端根據假資料列表隱藏remove功能，用戶點選時告知虛擬資料不能移除.
    - 優點: 
      - 拓樸資料可持續維持完整性
      - 不會因為device資料移除而影響其他功能操作.
    - 缺點:
      - 用戶體驗較不真實
      - 前端需要改code
  - 方案二: 後端根據現存的device資料，決定是否顯示在topology與裝置列表，以及受影響的clients.
    - 優點: 
      - 用戶體驗比較真實
      - 可能需要每天進行假資料恢復(ps. 需要cloud帳戶在遠端透過tytt恢復資料)
      - 後端需要改code(effort比方案一大)
    - 缺點:
      - 拓樸資料會因remove操作消失
      - 需考量移除後的相關資料連動
- 2024/08/22
  - Demo注意事項
    - 設備 nickname 使用新的 model name (Done)
    - logo改為cybertan (ps. 僅限本地端)
    - nickname 移除 sonicfi 字眼
    - Sam 建議 FW version 改為 v2.x.x (2024xxyy) (展場版會先改)
    - 評審無法事先告知假資料，如何防範相關操作. (允許正常操作，但設備移除需要屏蔽掉? 建議展場版直接屏蔽設備移除功能)
    - 登入帳密: eg. demo@cybertan.com.tw Cbt@12345
  - parInterface測試結果建議
    - createNewAsOnboard: SwitchConfigurationThirdPartyCbtSwitchPortList.ethType 被移除但table欄位還在?
    - ParseSwitchCapability.Capabilities.cbtCommon.features (物件欄位名稱跟DB不一致features vs feature)
    - 實際DB資料多了ParseSwitchCapability.Capabilities.label_macaddr欄位
    - 實際DB資料多了ParseSwitchCapability.Capabilities.macaddr欄位
    - 結果不太正確 ingressBandwidthRate: 1
- 2024/08/20
  - unit test兩種策略模式
    - Socialty
      - 測試時與其他重複性較高(缺點)
      - 架構優化時比較不會受到影響(優點)
    - Solitary
      - 測試時與其他重複性較高(優點)
      - 架構優化時容易受影響(缺點)

- 2024/08/19
  - parInterface測試結果建議
    - Interface數量上限?
    - errorMessage.cannotModifiedWith('interface name') 描述跟實際有落差!
    - default name error 建議統一為 errorMessage.interfaceNameReserved

- 2024/08/16
  - ParRadiusProfile測試結果建議
    - optional欄位建議明確使用 '?' 表示，以利相關物件宣告.
    - function明確說明回傳型態.(eg. checkDuplicateByName)
    - 值域的比較建議不要直接使用===，改用<= or >=.
- 2024/08/15
  - Demo SOP
    - 準備opside nodeDB
    - Setup 一台乾淨參展用的 Controller，先不與其他設備連接.
    - 刪除 openside nodeDB 中的 controller 資料.
    - 把參展用的 nodeDB 中 Controller 相關資料 copy 到 openside nodeDB 中，最後使用整合過的nodeDB取代原有DB.
    - 把openside devices.db中的Capabilities 與 Devices(除了Controller) table資料 放到參展用的 devices.db.(ps. nodeJS在設定時會調用owgw的capabilities資料)
    - 接著連線並onboard其他設備
    - tssDB，參展前預先放置幾天讓其有真實資料.(ps. 若要整合假資料建議一開始比照nodeDB方式加入，以openside的tssDB為主加入Controller設備資料)
    - QoEAI 與 evenlist資料可預先透過Web介面收集openside資料然後以檔案形式放入參展設備中.

- 2024/08/14
  - Demo注意事項
    - 設備 nickname 使用新的 model name
    - logo改為cybertan
    - 登入帳密: eg. demo@cybertan.com.tw Cbt@12345
    - 評審無法事先告知假資料，如何防範相關操作.
    - 深色系造成部分按鈕顯示問題
  - EAP
    24fe9a0f4963
    24fe9a0f4a3f
    24fe9a0f4943
    24fe9a0f5d61
    24fe9a0f4f6f
    24fe9a0f5e5d
    24fe9a0f4fcf
    24fe9a0f5dad

- 2024/08/13
  - 假資料規劃
    - 原則: 真假資料混和
    - 在Controller下分成兩條線，一個走真資料，另一個走假資料
    - 統計資料正常打開. 以利後續設備的相關設定
    - Manual connected device 只針對假資料的設備.
    - Topology 假資料，以及 Controller 的統計資料一部分需要得假資料會在 NodeJS 加上去
- 2024/08/12
  - db connect mock
  - all topology / statistics data mock
  - cliemt service test fail.
  https://cybertancomtw.sharepoint.com/:x:/r/sites/ControllerFWA/_layouts/15/Doc.aspx?sourcedoc=%7B04b36992-9f90-42be-8fb7-1f514db6a5f0%7D&action=edit&wdinitialsession=edabc2fe-126c-2e3b-7c1a-88f283b61a62&wdrldsc=9&wdrldc=2&wdrldr=BootTimeMismatch

  https://cybertancomtw.sharepoint.com/:x:/r/sites/ControllerFWA/_layouts/15/Doc2.aspx?action=edit&sourcedoc=%7Bbae731b9-e4e2-4660-9fbb-9f4dbba82ab8%7D&wdOrigin=TEAMS-MAGLEV.teamsSdk_ns.rwc&wdExp=TEAMS-TREATMENT&wdhostclicktime=1723426618631&web=1
- 2024/08/07
  - delete mode 100644 src/api/controllers/models/controller-info-port-info.ts
    - txSum -> txBytes (Done)
    - rxSum -> rxBytes (Done)
    - add complexDevice (Done)
    - add ParSwitchPort.enableEgressBandwidth: Number(boolean)，(不影響?)
    - add ParSwitchPort.egressBandwidthRate: Number(1~10000)，
    - add ParSwitchPort.enableIngressBandwidth: Number(boolean)，
    - add ParSwitchPort.ingressBandwidthRate: Number(1~10000)，
  - create mode 100644 src/api/owgw/models/complex-device.ts
  - create mode 100644 src/api/service-ver/models/device-ver-info.ts
  - create mode 100644 src/migrations/1720144127068-AddColumnParSwitchPorts.ts
  - create mode 100644 src/migrations/1720149451044-UpdateEap6gHEModeConfigurationRadios.ts
  - create mode 100644 src/migrations/1722409177677-UpdateEapConfigurationPortTaggedVlanIds.ts
- 2024/08/02
  - par-ssids.service.spec issues
    - createOne
      - when cbtMacAddressFilter.enable = false will clear macs and type? if no clear will cause?
      - when encryption.proto is 'wpa2+ccmp' will clear radius value? if no clear will cause?
      - 6G is only allowed with 'sae' protocol? how about 'sae-mixed'
- 2024/07/26
  - mock資料型態分析
    - Controller: 0045e2b06963
    - EAP:
      - eap-ceilingmount-a1-online-ether: 24fe9a0f4963 (代表 a1)
      - eap-ceilingmount-a1-online-wds: 24fe9a0f4937 (代表 wds)
      - eap-wallmount-b1-online-ether: 24fe9a0f5ccd (代表 b1)
      - eap-wallmount-c1-online-ether: 24fe9a0f5e5d (代表 c1)
      - eap-wallmount-c1-offline: 24fe9a0f5d7d (代表 offline)
    - Switch: 24fe9a0f48ef
- 2024/07/22
  - Unit test參考
    - https://aws.amazon.com/tw/what-is/unit-testing/

  - 測試資料的透明度
    - 清楚描述測試目標與測試資料的內容，並標示適用的版本號
    - array length?
    - 非optional欄位，有機會為undefine? (ps. 參考截圖)
    -  BDD 或 TDD 的實踐者，那麼你的單元測試就可能是 跨多個類別的 社交型單元測試，因為測試的對象是 一個 "行為"，而非一個類別
    - 社交型的單元測試(ps. 跟整合測試的最大差別在於，若有與外部環境互動則算是 "整合測試" eg. HTTP API / DB...)
      - 直接調用其他Service的API，並假設其他API邏輯是正確的
    - 好的 Unit Test 目標定義有三個
      - 可以被整合在開發流程中
      - 專注在最重要的程式
      - 用最小維護成本提供出最大的價值
    - Unit Test 的四個面向
      - Protection against regression: 保護程式不出現 Bug (取捨)
      - Resistance to refactoring: 不因重構導致影響撰寫 Unit Test (取捨)
      - Fast feedback: 能不能快速給予結果，而不會等待很久 (取捨)
      - Maintainability: 能不能輕易理解/執行 unit test 的內容 (required)
    - F.I.R.S.T
      - Fast: 執行的速度要快
      - Independent: 測試間互不依賴
      - Repeatable: 可重複執行不會被環境影響
      - Self-Validating: 明確回應驗證的結果
      - Timely: 確保程式與測試是一起完成的
      - Thorough: 詳盡的測試
    - TDD 的三大法則
      - 步驟 1：在撰寫一個單元測試 (測試失敗的單元測試) 前，不可以撰寫任何產品測試。
      - 步驟 2：只撰寫剛好無法通過的單元測試。
      - 步驟 3：只撰寫剛好能通過當前測試失敗的產品
    - getEapInfos test cases
      - Unit of behavior
        - 取得所有的EAP裝置列表
        - 取得單一的EAP裝置資訊
          - 該EAP存在
            - 正確取出該EAP的clients/downlink
            - Ceiling/Wall mount: port list統計資料正確性 (重複)
            - uplink為有線/無線: upstream統計資料正確性 (重複)
            - 離線的EAP裝置資訊 (重複)
              - EAP statics is supported
              - EAP statics is un-supported
          - 該EAP不存在
      - Unit of code
        - EAP statics is undefine or supported (重複)
        - All statics optional test (ps. 避免API因欄位缺少造成的Exception)(重複)
        - All empty array test (重複)
    - findEapInfoPartialNetInfo
      - Unit of behavior(RD)
        - 取得EAP裝置的PortList統計資訊
          - 該EAP統計資料存在
            - online: 取得IP address否則回傳預設值
            - uplink連線type為無線時，需收集upstream流量數據.
          - 該EAP統計資料不存在，回傳預設值
      - Unit of code
        - All statics optional test (ps. 避免API因欄位缺少造成的Exception)
        - All empty array test
    - findEapInfoWifiInfo
      - Unit of behavior(RD)
        - 取得EAP裝置的WiFi統計資訊
          - 該EAP統計資料存在
          - 該EAP統計資料不存在，回傳預設值
      - Unit of code
        - All statics optional test (ps. 避免API因欄位缺少造成的Exception)
        - All empty array test
        - 此EAP設備的Capabilities找不到
        - 此EAP設備的Capabilities.compatible跟topology.compatibole不一致
        - topology.clients出現不存在的connectType
        - 收集 wireless 流量數據時，capability.Capabilities.wifi跟統計資料的值不一致
    - findEapInfoPortList
      - Unit of behavior(RD)
        - 取得EAP裝置的PortList統計資訊
          - 該EAP統計資料存在 (Ceiling mount/ Wall mount)
          - 該EAP統計資料不存在，回傳預設值
      - Unit of code
        - All statics optional test (ps. 避免API因欄位缺少造成的Exception)
        - All empty array test
        
    - reference
      - [Unit Test 實踐守則](https://yu-jack.github.io/2020/09/14/unit-test-best-practice-part-1/)
      - [Manning.Unit.Testing.Principles.Practices.and.Patterns.2020.1.pdf](https://github.com/wuzhouhui/misc2/blob/master/Manning.Unit.Testing.Principles.Practices.and.Patterns.2020.1.pdf)(Vladimir Khorikov)
      - [Growing Object-Oriented Software，Guided by Tests](https://www.books.com.tw/products/F011525012))
      - https://ithelp.ithome.com.tw/articles/10229734
      - (https://brightsec.com/blog/unit-testing/)
      - (https://waynecheng.coderbridge.io/2021/02/13/Unit-Test/)
      - (https://tidyfirst.substack.com/p/canon-tdd)
      - (https://levelup.gitconnected.com/dont-unit-test-methods-test-behaviours-bf42c8498b65)
      - (https://testguild.com/unit-tdd-and-bdd-testing-whats-the-difference/)


- 0224/07/17
  - 建立可產生並輸出 InnerTopologyClient 的 function，其中欄位皆為非預設值的合法格式的亂數資料 
  - 建立可產生 InnerTopologyClientNetInfo物件的 function，其中欄位皆為非預設值的合法格式的亂數資料
- 2024/07/15
  - Github copilot token limitation: CopilotClientException:invalid_request_error:context_length_exceeded:This model's maximum context length is 8192 tokens. However，you requested 8221 tokens (4221 in the messages，4000 in the completion). Please reduce the length of the messages or completion.
  - 
  - Fix utils import eaps modules cause circle dependency issues: using imports modules directly.
    - copy all modules on eaps.modules
    - add the nodeDBModule，securityDBModule (ps. 不確定為何需要securityDBModule?)
- 2024/07/12
  - Unit Test (Controller)
    - handleSwitchInfos
    - findStateChildInfo
    - statisticsService.findPartialDeviceInfoFromUnit(Other test)
    - jest.mock('src/providers/capabilities/capabilities.module'，() => ({
  findEap: jest.fn((serialNumber: string) =>
    JSON.parse(
      readMockDataFromCollect('capabilitiesService.findEap'，serialNumber) ??
        '{}'，
    )，
  )，
}));

- 2024/07/10
  - Unit Test (Controller)
    - getControllerInfos
    - findControllerInfoPartialNetInfoAndPortInfo
    - findControllerInfoPortList
- 2024/07/03
  - Unit Test (EAP)
    - Principle
      - mock owgw api
      - mock db access
      - test optional fields
    - 測試涵蓋範圍
      - EAP A1(24fe9a0f4943) / B1(24fe9a0f5ccd) / C1(24fe9a0f5e5d)
      - default value with all optional value
      - default value with full set (Done)
      - undefine (Done)
      - exception
  - getEapInfos
    - Test Suite
      - owgwService.getInnerTopology() // Mock
      - devicesService.findOne() // Mock
      - parDevicesService.isEap() // Mock: Other test suite
      - statisticsService.findEap() // Mock
      - statisticsService.findPartialDeviceInfoFromUnit() // Mock: Other test suite
      - this.findEapInfoPartialNetInfo() // Sub test (Done)
      - this.findEapInfoWifiInfo() // Sub test (Done)
        - capabilitiesService.findEap // Mock
      - this.findEapInfoPortList() // Sub test (Done)
        
- 2024/06/30
  - docker logs openwifi-owgw-1 --tail 50 -f 2<&1 | grep -E "triggerEAPfor11k_11v|status_code|QoE_main|uplinkInfoList|WS-SVR"
  - docker logs openwifi-owgw-1 --tail 100 -f 2<&1 | grep -E "=====DEBUG=====|=====ERROR====="
  - docker logs openwifi-owgw-1 --tail 100 -f 2<&1 | grep -E "=====ERROR====="
- 2024/06/27
  - Migration Q&A
    - devices.cb中的controller default config是永遠不變的，所有的變更應直接反映在Cntroller中.
- 2024/06/26
  - AI Copilot的應用
    - SMB 讀書會 report
      - 介紹Github copilot的實務上的使用分享
      - 商務用 GitHub Copilot 簡介 (https://learn.microsoft.com/zh-tw/training/modules/introduction-to-github-copilot-for-business/)
      - 
    - RD Test Scope: eapInfo/ ControllerInfo / SwitchInfo / ClientsInfo
      - Release 前的RD自測
      - Bug的DB初步分析
- 2024/06/24 ~ 25
  - Github Copilot簡報規劃
    - 關於Copilot目前初步規劃如下: (由於單純介紹Unit Test有點單薄，有額外增加一些Topic，你這邊可以再補充)
    - 設定相關說明，個人版與商業版差異
    - 輔助程式語句生成 (實際案例分享)
    - 錯誤檢查
    - 程式優化
    - Unit test
    - 整合測試? (目前網路討論較少，若有找到會再加進報告中)
  - Topology相關Issues:
    1. 可以直接從 db 裡看出哪幾台裝置是 online 的嗎？(High)(5)
    有時候一個 topology 問題丟過來，你手上只會有 db 的資料，連網頁的樹狀圖、裝置列表的資訊都沒有
    在這種情況下，你能從何得知，哪些裝置是 online 的，哪些又是 offline 的
      - 目前是從health來判斷
      - 另外就是從c的Recodered轉成時間來判斷
      - 還有其他方式?

    2. 沒有 switch 的影響，分析的結果會是？(High)(1)
    也許是你還不夠清楚的了解 innerTopology 收集資料的邏輯
    可以再深度去了解、弄清楚在 innerTopology 在做些什事
    昨天有提點你，offline 的裝置在 topology 中的影響為何
    所以目前這一份分析結果是錯的
    請再次重新分析一次
        - 沒有switch的影響會導致原本接在其下方的RAP7110C-341X，uplink會改連網域(Network)或(WDS) RAP630C-311G(ps. 如果這台為online)
        - 目前就是以switch為offline的前提下進行分析，分析細節後補
        - 先說結果，根據db資料應該顯示如下: (ps. 但實際(WDS) RAP630C-311G沒有顯示出來，原因是根據health check推測是offline所致)
        network(172.150.10.0) ->(ether) RAP630W-211G --> (WDS) RAP630C-311G
                                        ->(ether) RAP630W-311G
                                        ->(ether) RAP7110C-341X
        
    3. 有一台 EAP 沒有 wanPort 資訊會造成什麼影響？分析的結果又會是？(High)(2)
    如昨晚最後你說有發現一台沒有 wanPort 資訊
    依照這個發現，去分析會對 topology 造成什麼影響
        - 若沒有 wanPort資訊的這台(WDS) RAP630C-311G 若其狀態為online，則network反而整個消失只剩下Ronto Service，原因是這台uplink type為WDS，因此eth0的port全被認定是他的client，也包括他的parent，所以自然畫不出任何節點)
        - 照理說uplink 為WDS的Ceiling EAP不應該出現這麼多eth0的client，畢竟他只有一個port
        - 缺少wanPort會導致innerTopology誤判eth0的port街的設備都是他的client，這點我還要查證一下.

    4. owgw 是如何區分環境是 controller 還是 ronto service ？(Middle)
        - 根據環境變數license是否有值

    5. 上線的裝置會傳給 ucentral server 哪些資訊？ (Middle)
        - health 和 state(我們分析拓樸所使用的資訊)
        - 重新連線或新上線Device會送configuration UUID上來比對
        - 還有其他?

    6. 是否有更好更快的方式區分裝置是 online 還是 offline？ (Low)
    除了 health check 還有其它途徑嗎？ 要再想想
    此議題可以區分 controller 跟 Ronto service 不同環境來看
        - 除了health 其他目前還沒想到
        - 另外就是從Statistics的Recodered轉成時間來判斷
        - 區分controller與Ronto service對區分online/offline的影響，還需要再想想!

    7. 整理條列出 innerTopology 做了哪些事？又做了什麼？ (High)(3)
        - (Backend Topology 作法與邏輯.pptx)
        - 邏輯整理如下，若有需要截圖對照，我再補充:
          - Step 1: 取得每台Devices IP，Controller clients host/ip address，Switch port/client list info，EAP AP/Station interface bssid info
            - Controller
              - 取得這台Controller ip address資訊
                - 從uplink(interfaces.[i].ipv4)
              - 取得client的hostname與address資訊
                - 從uplink/downlink (interfaces.[i].ipv4.leases)
              - 收集這台Controller所有的client
                - 從downlink(interfaces.[i].clients)
            - Switch
              - 收集portList資訊
                - cbtSwitch.portList
              - 收集這台Switch所有的client
                - cbtSwitch.clients (ps. 根據portNumber把port資訊也帶入)
              - 取得這台Switch ip address資訊
                - 從uplink interface (interfaces.[i].ipv4.addresses[0])
            - EAP
              - 收集WDS AP 和 Station interface bssid 與這台 EAP MAC 的對照表，以利後續查找.
                - interfaces.[i].ssids.[i].wds = true
                - interfaces.[i].ssids.[i].mode = ap or station
                - interfaces.[i].ssids.[i].ssid
              - 收集這台EAP IP address資訊(ps. 此處有判斷連線狀態，為何不在一開始就跳掉，原因?)
                - 從uplink interface (interfaces.[i].ipv4.addresses[0])

          - Step 2: 取得所有EAP網路狀態資訊
            - 取得latency(網路健康度)資料
              - Statistics.latency
            - 根據wanPort取得連線速度(speed)與duplex?
              - link-state.upstream.[wanPort].speed
              - link-state.upstream.[wanPort].duplex
            - 若EAP為WDS station(uplink為WDS)，則建立Station NetInfo(uplink MAC，connectType，quality)，並幫parent增加WDS client
              - WDS條件
                - interfaces.[i].ipv4.address is array (ps. interfaces.[i].name = up0v or up1v0)
                - interfaces.[i].ssids.[i].mode == station
                - exist interfaces.[i].ssids.[i].associations
            - 若EAP為AP，則收集每個station的ssid網路連線資訊(txRate，rxRate，uptime)
              - interfaces.[i].ssids.[i].mode == ap
              - interfaces.[i].ssids.[i].wds == false
            - 收集wifi client並建立client的netInfo
              - fronthaulClients.[i].macAddr
              - fronthaulClients.[i].netInfo
            - 收集ether client，包括幫wireless client取得IP address (Q: 若uplink走ether則需排除wanPort interface)
              - 取得wanPort(ps.此處有分版本，取得方式不一樣)
                - 若沒有值則會以預設作法進行(CeilingMount: 只有一個port，所以只確定是否要處理ether，WallMount: 直接新增upstreamPorts eth0，eth1)
              - 從Capabilities取得downstream port list
              - interfaces.[i].clients.[i].ipv4_addresses[0]

          - Step 3: 補充EAP的WDS station net info資訊:Station NetInfo(snr，channelBandwidth，channelUtil，signal)
            - 透過AP backhaulClients.[i]

          - Step 4: 找出devices所屬的第一層的clients
            - 概念是每次取出每個device已收集到的clients，濾掉非第一層的client. 剩下就是此device的第一層clients
            - 對於device之間關係有Loop的狀況，會透過是否已經有處理過的device方式過濾Loop.(ps. 細節需要觀察幾個case來確認實際狀況)

          - Step 5: 利用 Step4 的 device 第一層 clients，建立 uplink map(ps. 建立 topology.devices 會需要) 與 topology.clients 所需的結構與資訊
            - Ronto Service: 不需過濾無IP的Client，原因?
            - 如果發生cleint重複的狀況，會發command讓EAP移除upTime時間較短的client.(ps. 細節需再釐清?)

          - Step 6: 主要建立topology devices(controllers/eapes/switches)所需的結構與資料
            - 另一個目的是針對沒有uplink的device，建立network(網域)資訊，並把該device的uplink設定為該網域，以利後續toTreeNode在Ronto Service狀況下可建立topology.

    - eg. 以此次Jira bug為例，收集每台設備(node)的clients與ssid相關資訊

        - switch(SKF424-C1): offline，(不收集)

        - 65-b1(RAP630C-311G): 判斷uplink (WDS)，收集fronthaul clients + eth0 clients資訊 + ssid Interface MAC (mode為station)

        - 5d-a5(RAP630W-211G): 判斷uplink (ether)，收集fronthaul clients + 非eth1 clients + ssid wds Interface MAC (mode為ap)

        - 5c-8d(RAP630W-311G): 判斷uplink (ether)，收集fronthaul clients + 非eth0.4 clients + ssid wds Interface MAC (mode為ap)

        - b0-10(RAP7110C-341X): 判斷uplink (ether) ，收集fronthaul clients (不用理會 eth0 clients) + ssid wds Interface MAC (mode為ap)

      - 結論: 此次四台EAP中，根據組網邏輯，其他三台EAP的uplink是 65-b1(RAP630C-311G)，但 65-b1(RAP630C-311G) 的 uplink 卻是 5d-a5(RAP630W-211G)，因此狀況下並不會產生Network節點，進而導致無法正常畫出topology只剩下Ronto節點.



    8. 整理條列出 TopologyToTreeNode 做了哪些事？又做了什麼？ (High)(4)
        - Step1: 建立根節點
            - Ronto Service
              - 根節點為RontoCloud，並根據InnerTopology.network資料往下建立Network節點，並儲存起來以利後續的treeNode處理.
              - 取出並儲存Controller節點，以便後續的treeNode處理.
            - Service on Controller
              - 根節點為Controller(ps. 通常只有一筆資料，若有多筆則以for迴圈取到的最後一筆Controller為firstRoot?)

          - Step2: 收集所有的TreeNode節點與葉子資訊: InnerTopology(switches，eaps，clients)

          - Step3: 根據 Step1，Step2 所收集的節點資料，建立TreeNode巢狀結構與資料.
            - 按照類型轉成TreeNode並增加欄位結構(ps.包括children欄位)與預設值.
            - 透過 node.netInfo.uplink 把節點新增到parent的children欄位中.(ps. 有檢查client是否重複)
            - 對於pending中的EAP另外儲存.

          - Step4: 若為 Ronto Service，則把RontoCloud節點與Network節點關係補起來.
  - 2024/06/24
    - Client Air state需做防呆?
  - 2024/06/20
    - 收集每台設備(node)的clients與ssid相關資訊
      - 24:fe:9a:0f:48:f7 switch(SKF424-C1): offline，(不收集)
      - 24:fe:9a:0f:65:b1 (RAP630C-311G): uplink (WDS)，收集fronthaul clients + eth0 clients資訊 + ssid Interface MAC (mode為station)
      - 24:fe:9a:0f:5d:a5 (RAP630W-211G): uplink (ether)，收集fronthaul clients + 非eth1 clients + ssid Interface MAC (mode為ap)
      - 24:fe:9a:0f:5c:8d (RAP630W-311G): uplink (ether)，收集fronthaul clients + 非eth0.4 clients + ssid Interface MAC (mode為ap)
      - 24:fe:9a:77:b0:10 (RAP7110C-341X): uplink (ether) ，收集fronthaul clients (不理會 eth0 clients) + ssid Interface MAC (mode為ap)
    - 過濾clients找出沒有在total clients中的node，這些node會根據up0vx中ipv4中的IP來決定他的network node parent.
    - InnerTopology與TopologyToTreeNode邏輯解析
      - InnerTopology
        - Step 1: 取得每台Devices IP，Controller clients host/ip address，Switch port/client list info，EAP AP/Station interface bssid info
          - Controller
            - 取得這台Controller ip address資訊
              - 從uplink(interfaces.[i].ipv4)
            - 取得client的hostname與address資訊
              - 從uplink/downlink (interfaces.[i].ipv4.leases)
            - 收集這台Controller所有的client
              - 從downlink(interfaces.[i].clients)
          - Switch
            - 收集portList資訊
              - cbtSwitch.portList
            - 收集這台Switch所有的client
              - cbtSwitch.clients (ps. 根據portNumber把port資訊也帶入)
            - 取得這台Switch ip address資訊
              - 從uplink interface (interfaces.[i].ipv4.addresses[0])
          - EAP
            - 收集WDS AP 和 Station interface bssid 與這台 EAP MAC 的對照表，以利後續查找.
              - 條件一: interfaces.[i].ssids.[i].wds = true
              - 條件二: interfaces.[i].ssids.[i].mode = ap or station
              - 來源: interfaces.[i].ssids.[i].bssid
            - 收集這台EAP IP address資訊(ps. 此處有判斷連線狀態，為何不在一開始就跳掉，原因?)
              - 從uplink interface (interfaces.[i].ipv4.addresses[0])

        - Step 2: 取得所有EAP網路狀態資訊
          - 取得latency(網路健康度)資料
            - Statistics.latency
          - 根據wanPort取得連線速度(speed)與duplex?
            - link-state.upstream.[wanPort].speed
            - link-state.upstream.[wanPort].duplex
          - 若EAP為WDS station(uplink為WDS)，則建立Station NetInfo(uplink MAC，connectType，quality)，並幫parent增加WDS client
            - WDS條件
              - interfaces.[i].ssids.[i].ipv4.address is array
              - interfaces.[i].ssids.[i].mode == station
              - exist interfaces.[i].ssids.[i].associations
          - 收集wifi client的網路連線資訊，並放到wifiClientNetInfoMapPtr.
            - 條件一: interfaces.[i].ssids.[i].mode == ap
            - 條件二: interfaces.[i].ssids.[i].wds == false
            - 來源: interfaces.[i].ssids.[i].associations.(txRate，rxRate，uptime)
          - 收集wifi client並建立client的netInfo
            - 來源: fronthaulClients.[i].macAddr
            - 來源一: fronthaulClients.[i].netInfo
            - 來源二: wifiClientNetInfoMapPtr
          - 收集ether client，包括幫wireless client取得IP address (Q: 若uplink走ether則需排除wanPort interface)
            - 取得wanPort(ps.此處有分版本，取得方式不一樣)
              - 若沒有值則會以預設作法進行(CeilingMount: 只有一個port，所以只確定是否要處理ether，WallMount: 直接新增upstreamPorts eth0，eth1)
            - 從Capabilities取得downstream port list
            - interfaces.[i].clients.[i].ipv4_addresses[0]
              
        - Step 3: 補充EAP的WDS station net info資訊:Station NetInfo(snr，channelBandwidth，channelUtil，signal)
          - 透過AP backhaulClients.[i]

        - Step 4: 找出devices所屬的第一層的clients
          - 概念是每次取出device已收集到的clients，濾掉非第一層的client. 剩下就是此device的第一層clients
          - 對於device之間關係有Loop的狀況，會透過是否已經有處理過的device方式過濾Loop.(ps. 細節需要觀察幾個case來確認實際狀況)

        - Step 5: 利用 Step4 的 device 第一層 clients，建立 uplink map(ps. 建立 topology.devices 會需要) 與 topology.clients 所需的結構與資訊
          - Ronto Service: 不需過濾無IP的Client，原因?
          - 如果發生cleint重複的狀況，會發command讓EAP移除upTime時間較短的client.(ps. 細節需再釐清?)

        - Step 6: 主要建立topology devices(controllers/eapes/switches)所需的結構與資料
          - 另一個目的是針對沒有uplink的device，建立network(網域)資訊，並把該device的uplink設定為該網域，以利後續toTreeNode在Ronto Service狀況下可建立topology.

      - TopologyToTreeNode: 主要把InnerTopology得資料結構轉成前端顯示所需的巢狀結構
        - Step1: 建立根節點
          - Ronto Service
            - 根節點為RontoCloud，並根據InnerTopology.network資料往下建立Network節點，並儲存起來以利後續的treeNode處理.
            - 取出並儲存Controller節點，以便後續的treeNode處理.
          - Service on Controller
            - 根節點為Controller(ps. 通常只有一筆資料，若有多筆則以for迴圈取到的最後一筆Controller為firstRoot?)

        - Step2: 收集所有的TreeNode節點與葉子資訊: InnerTopology(switches，eaps，clients)

        - Step3: 根據 Step1，Step2 所收集的節點資料，建立TreeNode巢狀結構與資料.
          - 按照類型轉成TreeNode並增加欄位結構(ps.包括children欄位)與預設值.
          - 透過 node.netInfo.uplink 把節點新增到parent的children欄位中.(ps. 有檢查重複機制)
          - 對於pending中的EAP另外儲存.

        - Step4: 若為 Ronto Service，則把RontoCloud節點與Network節點關係補起來.

- 2024/06/19
  - jira RONTOSC-2
    - issue1: devices.db確實有 switchx1 + eapx4(都標示onboard = 1)，nodeDB只有switchx1 + EAPx2
    - switch(SKF424-C1): uplink (00:45:e2:b0:69:73) down: (24:fe:9a:77:b0:10)
    - 65-b1(RAP630C-311G): (缺wan port資訊) uplink (wireless) 26:fe:9a:1f:5d:a7 
    - 5d-a5(RAP630W-211G): uplink (ether) down: (RAP630C-311G)
    - 5c-8d(RAP630W-311G): uplink (ether)(無ssid)
    - b0-10(RAP7110C-341X): uplink (ether)(無ssid) switch
    - 從統計資料分析有3群資料
      - (00:45:e2:b0:69:73) -> switch -> (24:fe:9a:77:b0:10)
      - RAP630W-211 down ---> RAP630C-311G
      - 172.150.10.1 -> 24:fe:9a:0f:5c:8d
    - 由於QA表示switch最後狀態顯示為offline，所以應該只剩下network -> RAP630W-311G (24:fe:9a:0f:5c:8d)
- 2024/06/13
  - Test case unit test
  - Code refine
  - better than code complete
  - Amazon Q is more latency than github copilot
  - Devin vs Devis
- 2024/06/12
  - 分析ey006-286
  - 分析ey006-256
  - Update for disable telnet review
- 2024/06/11
  - 分析EWW631C1-50
  - 討論解決方案: 底層提供port mapping
- 2024/06/07
  - 驗證ey006-284
  - 分析ey006-289
  - 關閉telnet after device onboard
  - API顯示管理機制?
- 2024/06/05 ~ 06/06
  - RAP7110C-6: Ethernet link speed always show 1G on Web GUI
    - 目前1G寫死，等EAP底層跟 switch 一樣，在 statistics 中提供 portList 資訊，後端再根據資訊實作真正的連線速度，目前已在Gira加註解.(ps. 可考慮轉給EAP底層先處理.)
  - EY006-275: 每個wifi band client最高數量限制跟預期不一致.
    - 實際驗證為32個，預期應該是64個(Ps.待跟底層確認完規格，再決定是否要轉給底層Cheng處理)
  - EY006-279: 2G/2.4G 名稱未統一
    - 前端會進行轉換改顯示2.4G.(Astrid已認領)
    - 名稱統一即可，目前後端都統一為2G，前端統一顯示為2.4G，但有個欄位是前端直接把後端提供的值顯示出來導致不一致的問題.
  - EY006-284: topology 異常
    - switch onboarding失敗所致，email server issue.(ps.已解決，待實機驗證)
  - EY006-287: Client table number count is incorrect on Dashboard page and it is miss 6G count.
    - 尚未支援6G.(pending)
    - dashboard client個數跟topology不一致.(ps. 已解決，待實機驗證)

- 2024/06/04
  - 
24fe9a0f5149
24fe9a0f5cf1
24fe9a0f4f93
24fe9a0f4f97

0045e2b06972
24fe9a0f6525
24fe9a0f55e1
00facb6a006d
24fe9a0f48f7

24fe9a10cfb5
00cbea770028
003ecb091890
24fe9a0f5d35

- 2024/0529
  - 專利相關
    - 之前有提一個專利，目前申請美國專利被駁回，公司的專利部門在詢問是否要進行答辯，目前不建議再花錢進行答辯，主要在於步進性比較不足，你這邊有沒有其他想法或補充.
    - 專利內容簡述: 當router透過藍芽連接其他藍芽裝置時，藍芽裝置透過MQTT協議與外部Broker進行安全連線並傳送資料，藍芽裝置為保持低功耗，所以每次連線傳送資料後會進行斷線，因此此專利的重點是在Router增加一個Agent與負責建立安全連線，故每次外部藍芽裝置要傳送資料時只要建立連線，由Agent把資料進行加密並傳送資料，可省去藍芽裝置要跟broker耗時耗能的安全連線建立.
        
- 2024/05/23
  - email_reboot_log
    - <Feat> Add serialNumber on DeviceLog::to_json()
- 2024/05/22
  - Sonicfi reboot 通知信目前觀察到以下幾個 issues:
    - commandList
      - [Schedule:CommandList] 有觸發但似乎未註冊成功，所以基本上只執行一次 commandList 檢查.
      - findAllConfigureNewestCompleted 只撈'configure'的 command，Reboot 應該不會被撈到並通知用戶
    - deviceLogs issues
      - 取得 owgw 回傳的 DeviceLogs 欄位名稱有誤.(ps. 大小寫，缺 SerialNumber 欄位)
        - 直接加一個 OwgwDeviceLog 結構
      - devices.db 有 reboot 的 log 但現況 email 只會針對 Exception(serverty:3)以上的 event 進行發送，所以基本上撈不到 serverty 為 6 的 reboot 資料
      - Controller 和 EAP reboot 行為不一樣，EAP 會 double check 有 7 秒的考慮時間
      - Controller 的 reboot 沒有紀錄在 devices.db 中
    - reboot 是否會記錄 log 觀察
      - CTL / EAP / Swtich
        - WebUI reboot(v)
        - reboot command (x)
        - hardware reboot (x)
        - power on/off (x)
- 2024/05/15
  - p.40: 製作 uplink 對照表 ​，既然已經透過排除法取得屬於自己的 client，還需要製作這張 uplink 對照表的原因? (給拓樸圖使用)
- 2024/05/14
  - Controller: 24:fe:9a:0f:51:49
    - down1v0:
      - 0c:9a:3c:e4:30:40 (eth3)
      - 3c:18:a0:c9:85:c9 (eth1)
      - 24:fe:9a:0f:5c:f1 (EWW631-B1)(eth2)
        - wanPort:eth0.4034
        - fronthaulClients
          - f4:8c:50:f1:d6:d7
        - up1v20
          - 24:fe:9a:0f:4f:97(eth0.4034)
        - up2v30
          - NA
    - down2v20:
      - EWW631-A1(Room1): 24:fe:9a:0f:4f:97
        - wanPort: eth0.40
        - fronthaulClients
          - 2e:67:db:62:be:bc
        - up0v0
          - eth0
            - 24:fe:9a:0f:4f:93
            - 3c:18:a0:c9:85:c9
        - up1v20
          - 24:fe:9a:0f:5c:f1 (eth0)
          - 2e:67:db:62:be:bc (ath11)
        - up2v40
          - 24:fe:9a:0f:51:4a (eth0)
    - down3v30:
      - EWW631-A1: 24:fe:9a:0f:4f:93
        - wanPort: eth0
        - up0v0
          - eth0:
            - 24:fe:9a:0f:4f:97
            - 24:fe:9a:0f:51:4a
            - 24:fe:9a:0f:5c:f1
            - 3c:18:a0:c9:85:c9
          - ath1
            - 0c:9a:3c:e4:30:40
    - down4v40: NA
- 2024/05/10

  - NodeJS 改由 owgw API 存取 devices.db //\*\*\* keyword: @InjectDataSource('devicesDB')
    - ClientQualityService (ps. 已改由新檔案處理)
      - 透過 isTssQueryFromApi 來決定是否使用 owgw API
      - getOneCbtClientQuality(/cbtClientQuality/${mac})
    - LatenciesService (ps. 已改由新檔案處理)
      - 透過 isTssQueryFromApi 來決定是否使用 owgw API
      - getCbtLatencyConcat (/cbtLatencyConcat)
    - QualityService (ps. 已改由新檔案處理)
      - 透過 isTssQueryFromApi 來決定是否使用 owgw API
      - getCbtQualityConcat (/cbtQualityConcat)
    - SystemStaticsService (ps. 已改由新檔案處理)
      - 透過 isTssQueryFromApi 來決定是否使用 owgw API
      - getDayChartInfoBySerialNumber (/cbtSystemStatics/${serialNumber})
    - TrafficStatisticService (ps. 已改由新檔案處理)
      - 透過 isTssQueryFromApi 來決定是否使用 owgw API
      - getAllClientMacs (/cbtTrafficStatistics/allClientMacs)
      - getServiceTrafficStatisticsByMacs (/cbtTrafficStatistics/serviceTrafficStatisticsByMacs)
      - getClientTrafficDataByType (/cbtTrafficStatistics/clientTrafficDataByType)
      - getAppServiceTrafficStatisticsByType (/cbtTrafficStatistics/appServiceTrafficStatisticsByType)
    - TrafficStatisticTotalService (ps. 已改由新檔案處理)
      - 透過 isTssQueryFromApi 來決定是否使用 owgw API
      - getTrafficStatisticTotalInfoByType (/cbtTrafficStatisticsTotal/trafficStatisticTotalInfoByType)
      - getDayAmendTrafficStatisticTotal (/cbtTrafficStatisticsTotal/dayAmendTrafficStatisticTotal)
      - getNewestAppTrafficStatistics (/cbtTrafficStatistics/newestAppTrafficStatistics)
    - StatisticsService (ps. 已改由新檔案處理)
      - 已經未使用，可直接移除
    - DeviceLogsService
      - findAllNewestRecorded: - owgw API: getAllNewestRecorded (/cbtDeviceLog/findAllNewestRecorded)
        //\*\*\* 以下需要實作
    - CapabilitiesService (Ready)
      - fineOne:(Ready)
        - 新增 owgw API 並使用原生 RESTAPI_device_commandHandler::GetCapabilities(SerialNumber)
      - findAll:(Ready)
        - 新增 owgw API(GetAllCapabilities)，另外再 ucentralgw 新增 RESTAPI_cbtCapabilities 內有 GetAllCapabilities API 表示要取全部 devices 的 capabilities，另外需要在 stoarage_capability 新增 API. (是否要整合連取單個 capabilities?)
    - CommandListService (Ready)
      - findAllConfigureNewestCompleted
        - (/cbtCommaondList/findAllConfigureNewestCompleted) (Done)
      - findOneWithRebootTimeIsLatest(serialNumber)
        - 方案一: 透過 findAllConfigureNewestCompleted 的內容來過濾出某裝置的最後一筆 reboot 值.(x)
        - 方案二: RESTAPI_cbtCommaondList_handler 新增 owgw API(findOneWithRebootTimeIsLatest)並在 storage_command 增加相對應的 API(findNewestOneByParameters 之後過濾需要的資料)
    - DevicesService
      - findOne
        - Owgw API getDeviceBySerialNumber (/api/v1/device/${serialNumber}) (Done)
      - findAll(先不理會)
        - 方案: 新增 owgw API findAllDevices: RESTAPI_devices_handler. DoGet(/api/v1/devices)
      - getFirmwareInfo (Ready)
        - 調整 findOneBy(serialNumber) => findOne(serialNumber)
    - ServiceVerService
      - findOne (被 getMcuVerInfo，getDockerImageVerInfo 使用)
        - 方案一: 在 RESTAPI_cbtFirmware 新增 getServiceVer (x)
        - 方案二: 新增 RESTAPI_cbtServiceVer 新增 DoGet (v)

- 2024/05/08
  - switch fw 假資料驗證.
  - Table Index 優化調整.(unique)
  - EAP/Controller onboarding 後移除 Telnet 功能.
  - WiFi7 任務卡了解與確認是否有缺漏.
- 2024/04/26
  - RD Test
    - Port 點選區域與預期有出入.
    - 1714125202
    - 1714125064
  - Debug device command
    - /usr/share/ucentral# ./state.uc
    - /etc/init.d/cbt_manager_init stop
    - /etc/init.d/cbt_manager_init start
    - cbt_manage_tool list
    - cbt_manage_tool cmd sysinfo EVT_SYSINFO_UPLINK_L2_DEVICE
    - cat /etc/openwrt_release
    - ip route
    - arp -an
    - vram unset l2_uplink_mac
    - vram show
- 2024/04/23
  - nodeJS schema 同步更新 cbtNativeVlan title 漏改"The cbtPvidProfile Schema"
  -
- 2024/04/22

  - getControllerByParVlanId(vlanId) SQL 語法問題
    - 功能概述: 找出 Controller 中有使用到此參數 vlanId 的 interface 與 port，主要關聯 ParVlanProfiles，ParDevices，ParInterfaces，ParControllerPorts.
    - 優化方式: 由於使用 Inner Join 的前提是 Join 的 table 都要能撈到值，如果該 vlan 只影響 ParInterfaces 或 ParControllerPorts 其中一邊，則資料就會撈不到，故改用 LEFT Join，他允許右邊資料撈不到時欄位可填寫 NULL 值，所以還是可以撈到資料.
    - 問題一: Join 的條件需考量額外的過濾條件，否則會出現欄位應該為 NULL，但卻有值的狀況. eg. 附件 nodeDB_eric_0422.db 中 sql 的 ParVlanProfiles.id 的值給 5，此時應該帶出兩筆資料，但卻只找到一筆資料帶出來且應該出現 NULL 的欄位也有資料.
      - 針對此問題有在 join interface 時加入過濾條件解決欄位值的問題，但仍衍生問題二
        LEFT JOIN ParInterfaces
        ON ParInterfaces.parVlanId = ParVlanProfiles.id
        AND (ParControllerPorts.serialNumber IS NULL OR
        (ParControllerPorts.serialNumber IS NOT NULL AND ParInterfaces.serialNumber = ParControllerPorts.serialNumber))
    - 問題二: sqlite 語法只支援 LEFT join 它允許右邊資料為空，我們遇到的問題是左右兩邊都可能遇到 null 值，所以問題只解決一半，例如: 先 Join ParControllerPorts 再 join ParInterfaces 時一旦 port 的資料為空，則 Interface 即使有值也撈不到，反之亦然.
  - getEapByParVlanId(vlanId) - 功能概述: 找出 EAP 中有使用到此參數 vlanId 的 ssid 與 port，主要關聯 ParVlanProfiles，ParDevices，ParSsids，ParSsidDeviceMaps，ParEapPorts. - 優化方式與問題類似，細節可參考以下 SQL 內容. -
    SELECT
    ParVlanProfiles.id as parVlanId，ParVlanProfiles.profileName，ParVlanProfiles.vid，
    group_concat(DISTINCT ParEapPorts.id) as parEapPortIds，group_concat(DISTINCT ParEapPorts.portNumber) as portNumbers，
    group_concat(DISTINCT ParSsids_2.id) as parSsidsIds，group_concat(DISTINCT ParSsids_2.profileName) as parSsidsProfileName，
    ParDevices.serialNumber，ParDevices.macAddress，ParDevices.nickname，ParDevices.compatible，ParDevices.configuration
    FROM
    ParVlanProfiles
    LEFT JOIN ParEapPorts
    ON ParVlanProfiles.id = ParEapPorts.parVlanId OR ParVlanProfiles.id IN (SELECT value FROM json_each(ParEapPorts.taggedParVlanIds))
    LEFT JOIN ParSsids AS ParSsids_1
    ON ParSsids_1.parVlanId = ParVlanProfiles.id
    LEFT JOIN ParSsidDeviceMaps
    ON ParSsids_1.id = ParSsidDeviceMaps.parSsidId
    AND (
    ParEapPorts.serialNumber IS NULL OR
    (ParEapPorts.serialNumber IS NOT NULL AND ParEapPorts.serialNumber = ParSsidDeviceMaps.serialNumber))
    LEFT JOIN ParSsids AS ParSsids_2
    ON ParSsids_2.parVlanId = ParVlanProfiles.id
    AND (
    ParEapPorts.serialNumber IS NULL OR
    (ParEapPorts.serialNumber IS NOT NULL AND ParEapPorts.serialNumber = ParSsidDeviceMaps.serialNumber))
    INNER JOIN ParDevices
    ON ParEapPorts.serialNumber = ParDevices.serialNumber OR ParSsidDeviceMaps.serialNumber = ParDevices.serialNumber
    WHERE ParVlanProfiles.id = :id
    GROUP by ParDevices.serialNumber

- 2024/04/19
  - SQL 語法問題彙整
    - GROUP 還找不到正確的規則
    - 基本上 JOIN 所搭配的多組 GROUP 的欄位，會在該欄位 NULL 時異常，可避掉
    - Controller SQL bug:
      - 當有多個 interface 且 deviceSerial 不同時，GROUP 會有問題，可避掉
    - EAP SQL(ssid+map+port+valan+device): 在使用相同的 ssid 時會有問題
- 2024/04/15
  - DB: Not null = 有 default value?
- 2024/04/10
  - registry.cloud.cybertan.tw/66092e82/alpha/cbtmain:v2.0.0
  - gitlab.cybertan.com.tw:5050/9ga300a8/eap/cybertan_eap_trunk/kafka 2.8.1_1
  - registry.cloud.cybertan.tw/66092e82/alpha/kafka:2.8.1_1
  - gitlab.cybertan.com.tw:5050/9ga300a8/eap/cybertan_eap_trunk/zookeeper 3.4.13
  - registry.cloud.cybertan.tw/66092e82/release/zookeeper:3.4.13
- 2024/04/09
  - state 是 Controller 時要寫 latencies 至/tmp/latency/目錄下(switch 也要? 原始寫死 ey006...更彈性的寫法?)
  - 資料來源 Latences
  - 原始邏輯並未提到 Controller?
- 2024/04/08
  - checkConfigureNewestCompleted 使用時機?
  - 目前由於我們有設定 RunAt，導致第一時間不會往下推 configuration，而是等下次 commandManager 才會往下推 config.
  1. EY006-241: 目前在 Controller v1.4.2 有複現，在 v2.0.0 已解決.(Root cause: 算式寫錯應該是"total-free"才對)
  2. EY006-239: 此 bug 需要 EAP(b1，c1)，目前遠端環境沒有.
  3. 另外 Chris 反應的問題也有復現出來，但還未找出規則，只知道目前我們 applyConfig 時，有設定時間(now)所以並非第一時間推送 config，反而是 AP_WS_Connection::LookForUpgrade 所推送的(ps. 會調用此功能的只有 Process_state，Process_healthcheck，Process_connect)三支 API 會調用，目前可推論是 cnfiguration 發生改變時，剛好 state/health 同時出現時導致 configuration 重複發送的狀況.
- 2024/04/03
  - regex 優化
    - 原始: /(?:^|[a-z0-9]+\_)(?:[rc]ap([567])(?:18|36|30|110|\d+)([cpwt]))-[123][0-9][0-9a-z][gax]([o]?)$/
    - 優化: /(?:^|[a-z0-9]+\_)[rc]ap[567](?:18|36|30|110|\d+)[cpwt]-[123][0-9][0-9a-z][gax][o]?$/ (ps. 移除不必要的大括號)
- 2024/04/01
  - 強化 Backend 橫向溝通，功能開發告一段落，建議主動揭露總結與概述此次修正的內容.
  - 每隔 10 秒呼叫 3 次 treeNode，行為不合理?
  - cybertan_ey006-a1-64b_v2.0.0_release_202403291711695826 (目前最新的 controller 版本)
  - cybertan_eww631-a1_v2.0.0_release_202403281711623675(EAP 版本)
  - SOP
    - 首次登入頁 default account 名稱優化
    - 發驗證信同時進到登入頁面? 用戶未驗證時的提示?
    - switch toast 提示用戶更新? 每次都需要提示?
    - multiple account 說明?
- 2024/03/21
  - Controller infor
    - "label_macaddr": "00:3e:cb:87:66:06"
    - "label_macaddr": "00:3e:cb:21:02:aa" (192:178.20.102)
- 2024/03/20
  - 中央氣象局:
    - API token: CWA-D73F58A8-1D57-4371-A8AE-F4CE66760514
- 2024/03/13
  - Sharp / WiFi7 / New Switch (ps. 3 月底提測)
- 2024/03/12
  - /^(?=._?[A-Z])(?=._?[a-z])(?=._?[0-9])(?=._?[\{\}\(\)~_\+\|\[\]\;\:\<\>\.\，\/\\\?\"\'\`\=#?!@$%^&*-]).{8，64}$/
  - /^[0-9a-zA-Z~!@#$%^&*()_\-+=[\]{}|;':"<\\>?，./\s]{1，32}$/Untitled spreadsheet
  - APP IPv4: ^((25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[1-9]).((25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[1-9]|0).){2}(25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[0-9]))\$
  - APP netmask: ^((255.){3}(255|254|252|248|240|224|192|128|0+))|((255.){2}(255|254|252|248|240|224|192|128|0+).0+)|((255.)(255|254|252|248|240|224|192|128|0+)(.0+){2})|((255|254|252|248|240|224|192|128|0+)(.0+){3})$
  - 無類別域間路由（英語：Classless Inter-Domain Routing，簡稱 CIDR/ˈsaɪdər，ˈsɪ-/）
  - ^((25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[1-9]).((25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[1-9]|0).){2}(25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[0-9]))\/(?:24|25|26|27|28|29|30)$ (ps.後端 netmask 使用 CIDR(IPv4/遮罩位數))
  - ^(?:25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[1-9]).(?:(?:25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[1-9]|0).){2}(?:25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[0-9])\/(?:24|25|26|27|28|29|30)$ (ps.建議增加'?:'可增加效率)
  - ^(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(?:\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)){3}\/(?:3[0-2]|[0-2]?[0-9])$ (ps.網路搜尋)
- 2024/03/29

  - switch

    - sfp 插入時 Event 通知: disconnect -> config apply -> connect -> disconnect -> connect
    - sfp 移除後 Event 通知: config apply
    - sfp 異動時，會告知 ethtype 變更修改失敗，但實際卻成功的狀況.
    - 通知用戶時，應告知 configuration 修改是由系統內部發起的修改，以免造成用戶混淆.
    - 停留在 port list 的頁面，不會反應 sfp 的插拔，但會收到 configuration 變更的通知.

  - wifi steering
    - 切換到其他頁面出現非預期的修改提醒

- 2024/03/28
  - 1. 主動的說說自己的優點（放心大膽的說，不管是「大」優點還是「小」優點，只要覺得它是「優點」就把它說出來就對了）
    - 願意主動學習對專案有幫助的技術.
    - 樂意採納別人具有建設性的建議.
    - 樂意與人溝通並一起解決問題.
    - 樂於規劃並且開發出有用的功能，會覺得很有成就感.
    - 對於主管交付的任務，都會盡力完善.
    2. 除了優點，也可以說說自己的價值，覺得可以為團隊帶來什麼效益
    - 勇於跳出舒適圈嘗試新的技術，像如何利用 AI 技術來加速專案開發.
    3. 工作下來，覺得自己對 project / coding / 工作上有什麼顯眼突出的貢獻付出
    - 目前還看不出來，未來希望能透過技術 survey，讓產品更具競爭力.eg. 導入 websocket，移除 kafka 的溝通方式，讓溝通成本可以降低.
    4. 覺得自己有什麼缺點，需要改進的地方
    - 開發時對非邏輯相關的細節不夠用心確實.
    - 面對容易有情緒的同仁，在溝通上容易退縮.
    - 表達能力需要再加強
    5. 對自己有什麼期許、目標、方向
    - 未來期許自己也能接前端開發任務，另外就是希望能嘗試做前期 SA/SD 的工作.
    6. 團隊運作下來帶給你的感覺為何
    - 團隊成員之間合作氣氛融洽，大家都願意互相幫忙.
    7. 團隊整體有什麼優點
    - 同上.
    8. 團隊有什麼改善之處
    - 橫向的資訊傳遞仍有欠缺，對於非參與的開發的項目建議定期總結簡單介紹.
    9. 對主管的評分，有沒有需要改進的部份
    10. 對部門有什麼建議

著重於個人優點的部份唷~
著重於個人優點的部份唷~
著重於個人優點的部份唷~

核心重點：只要你不尷尬，尷尬的就是別人；盡可能去突顯出「優點」，哪怕是一丁丁一米米一點點，都可以把它放大再放大的講出來 XDD

- 2024/03/05
  - Bug verification: Controller(003ecb876606)
  - // Stop BLE
    try {
    this.owgwService.postBleStop();
    } catch (error) {
    Logger.warn(`postBleStop: ${error}`);
    }
  - 正規規則會議(03/20)
    - Eric/chien/anna/arisa/astrid
  - Merge All Branch(03/15)
- 2024/02/27
  - Gira switch bug(SKF24-29)經分析屬於前端的 bug，已請 Kelly 處理，由於 kelly 尚未在 Gira Switch 專案中，所以暫時還無法轉給他，已請人幫忙加.
  - Web 首次安裝關閉藍芽的方法
    - 方案一: 觸發 "/usr/bin/ble_tool.sh ble stop"(ps. 參考 cbtPing 的做法)
    - 方案二: 新增 config.third-party.cbtBle 欄位來控管.(ps. 仕銓建議)
  - 欄位 ragular 檢查
    - POST /api/cloudOauth2
      - userId: emailRegex
    - PUT /api/controllers/{serialNumber}/configuration/updateQuery
      - portForward
        - name: ^[0-9a-zA-Z~!@#$%^&*()_\-+=[\]{}|;':"<\\>?，./\s]{1，32}$ (nameRegex)
        - internal-address: ipv4Regex
      - ipv4Firewalls
        - name: nameRegex
        - srcIP: ipv4Regex / ipv6Regex
        - srcPort: ^([0-9]|[1-9][0-9]{1，3}|[1-5][0-9]{4}|6[0-4][0-9]{3}|65[0-4][0-9]{2}|655[0-2][0-9]|6553[0-5])$ (portRegex)
        - destIP: ipv4Regex / ipv6Regex
        - destPort: portRegex
      - ipv6Firewalls(未實作)
      - ipv4StaticRoutes
        - name: nameRegex
        - targetIP/gatewayIP: ipv4Regex / ipv6Regex
      - ipv6StaticRoutes(未實作)
      - servicesLog
        - host:No regex (pattern: ^[0-9a-zA-Z~!@#$%^&*()_\\-+=[\\]{}|;':\"<\\\\>?，./\\s]{1，32}$)
        - port:No regex(portRegex?)
      - servicesNtp
        - servers:No regex
      - upstreamConfig
        - broad-band
          - user-name:No regex
          - password:No regex(passwordRegex?)
        - ethernet.macaddr:No regex
        - ipv4
          - gateway: ipv4Regex
        - subnet: ^(((25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[1-9]).((25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[1-9]|0).){2}(25[0-5]|2[0-4][0-9]|[1][0-9]{2}|[1-9][0-9]|[0-9]))\/(24|25|26|27|28|29|30))$ (subnetRegex)(APP: Not sure if it's equivalent)
        - use-dns: ipv4Regex
        - name:No regex
    - POST /api/owgw/ping/{serialNumber}
      - dest:No regex(ps. hostname + IP address )
    - PUT /api/par-devices/{serialNumber}/nickname
      - nickname: nameRegex
    - POST/PUT /api/par-interfaces
      - serialNumber: ^[a-f0-9]{12}$ (ps. 用戶應該不會手動輸入)
      - name: nameRegex
      - ipv4.subnet: subnetRegex
    - POST/PUT /api/par-radios/profile
      - radios.profileName: nameRegex
    - POST/PUT /api/par-radius-profiles
      - name: nameRegex
      - authServer
        - ipAddr: ipv4Regex
        - port:No regex(portRegex?)
        - secret:No regex(passwordRegex?)
      - accounting.acctServer
        - ipAddr: ipv4Regex
        - port:No regex(portRegex?)
        - secret:No regex(passwordRegex?)
    - PUT /api/par-switch-ports/{serialNumber}/port/{portNumber}
      - portName: nameRegex
    - POST/PUT /api/par-vlan-profiles/{id}
      - profileName: nameRegex
    - PUT /api/switches/{SN}/configuration/updateQuery
      - ieee8021x.radiusProfileName:No regex(用戶採選擇方式輸入)
    - POST /api/oauth2
      - email: emailRegex
      - password: ^(?=._?[A-Z])(?=._?[a-z])(?=._?[0-9])(?=._?[\{\}\(\)~_\+\|\[\]\;\:\<\>\.\，\/\\\?\"\'\`\=#?!@$%^&*-]).{8，64}$ (APP: 規則目測相同，但最大長度為 63)(passwordRegex)
    - POST /api/users/setup/create
      - email: emailRegex
      - password: passwordRegex
      - name: (長度 8~64)
    - POST /api/users/setup/login
      - email: emailRegex
      - password: passwordRegex
- 2024/02/21
  - response.SessionId 必要性? (ps.移除)
- 2024/02/20
  - 在藍芽首次安裝過程中，按照現有的 email 使用 url 激活機制，我的建議是 APP 註冊新帳號之後，跳到下一頁告知用戶去收信激活帳戶(此頁面有下一步跟取消)，接著用戶去收信點選 url 激活帳戶，然後回到 APP 按下一步，這時 APP 會使用新帳密去嘗試登入，如果成功就繼續後續的設定(SSID/WAN)，如果失敗就告知帳號尚未激活停在收信激活帳戶頁面.
  - BLE 底層 issues
    - Command header 錯誤重送機制尚未 ready.(ps. request decode 失敗用戶會得到 timeout 錯誤，response decode 失敗用戶會得到 something error，command 需重送)
    - checksum 目前的有效檢測範圍 512bytes，內容太大檢測會失效.(ps. 錯誤檢測輔助，若因內容太大失效，則後續發生 decode 錯誤的狀況與上述相同)
    - Command header index 目前沒有作用.
    - 失敗率高有可能是因為 QRCode 識別的問題造成的，會建議 QA 把掃 QRCode 與直接手動 keyin 兩種方式分開測試.
- 2024/02/17
  - /api/controllers/{serialNumber}/configuration/query
    - 來源直接由 configuration 轉換，沒有 Number/boolean 互換的問題 (boolean)
  - /api/controllers/{serialNumber}/wanInfo
    - const { Data } = await this.statisticsService.getController(serialNumber)
    - carrier: Data['link-state'].upstream[Capabilities.network.wan[0]].carrier (number)
  - /api/eaps/{serialNumber}/info
    - EapInfoWithTrafficStatistics.netInfo.activity (number)
  - /api/eaps/{serialNumber}/profiles
    - EapProfiles.ParseParRadio.allowDfs (integer 或 null)
    - EapProfiles.ParseParSsid
      - enable (integer)
      - erasable (integer)
      - cbtDisplay (integer)
      - hiden-ssid (frontEnd: 不提供此欄位，DB: integer)
      - cbtMacAddressFilter.enable (frontEnd: boolean，DB: json string)
      - rrm.neighbor-reporting (frontEnd: boolean，DB: json string)
  - /api/par-interfaces/{serialNumber}/role/{role}
    - ParseParInterface
      - erasable (integer)
      - ipv4.port-forward.enable (frontEnd: boolean，DB: json string)
      - ipv6.enable (frontEnd: boolean，DB: json string)
  - /api/par-radius-profiles/{id}
    - accounting.acctEnable (frontEnd: boolean，DB: json string)
    - interimUpdateInterval.enable (frontEnd: boolean，DB: json string)
  - /api/par-switch-ports/{serialNumber}/port/{portNumber}
    - flowCtl(number)
    - portIsolation (number)
    - jumboFrame (number)
    - taggedVlan (number)
  - /api/par-vlan-profiles
    - enabled (number)
    - erasable (number)
    - igmpSnooping (number)
  - /api/switches/{serialNumber}/configuration/query
    - stp.enable (string)
    - ieee8021x.enable (frontEnd: boolean，DB: json string)
    - qos.enable (frontEnd: boolean，DB: json string)
  - /api/switches/{serialNumber}/info
    - netInfo.active (boolean)
    - portList
      - active (boolean)
      - flowCtl (frontEnd: boolean，DB: integer(offline 時會使用))
      - jumboFrame (frontEnd: boolean，DB: integer(offline 時會使用))
      - portIsolation (frontEnd: boolean，DB: integer(offline 時會使用))
      - taggedVlan (frontEnd: boolean，DB: integer(online/offline 時都會使用))
- 2024/02/16
  - BLE golang 交接
    - 開發環境: VSCode
    - 編譯環境: Linux(Build code server)
    - images released: 注意事項.(已提供 release git 位置)
    - 這次解的 issue 還未 commit? (已協助 commit)
- 2024/02/15
  - APP 問題統整：分為上架前要解決的問題與後續必要處理問題
  - 上架前要解決的問題：
    - 部分 API 透過 Bluetooth 無法取得 Data
      - Fix
    - Bluetooth 文件缺失，未依照文件傳輸
      - 底層未實作錯誤重傳功能.
    - Bluetooth Demon 不明原因死亡
      - 需有能複製流程.
    - Controller restart WAN 孔失效
      - 有一定機率會發生，底層帶解.
    - API Support List（API 編碼）
      - 待實作
    - Bluetooth Discover Mode 未帶上 Service ID
      - 新需求
    - QR Code 資料密度過高，雷雕品質不佳
      - 需有其他可行之替代方案.
    - Controller 出廠韌體是否要能直接搭配 APP
    - 使用者聲明、隱私權與資料使用聲明
  - 後續必要處理的問題：
    - Error Code
    - 沒有針對 Setup 獨立 API
    - LCM 啟動進度與 Controller 不同步
    - 使用網頁 Setup 後藍芽未關閉
    - QoE Event 由後端自行紀錄
    - Cloud API 相容性
    - API 短暫無法使用
    - API 設計相容性不足
    - 部分 API 欄位尚未 Ready
    - Bluetooth 非預期錯誤碼 1033
    - Packet Size 最大會 Timeout
    - 無網路時 Check Firmware timeout
    - QoE Event 沒有資料導致 X 軸無法繪製
- 2024/01/25
  - 24fe9a0f4d36 / 24:fe:9a:0f:4d:36
  - 003ecb551301 / 00:3e:cb:55:13:01
- 2024/01/24
  - Configuration backup/restore
    - Configuration backup
      - 原始的作法為打包所有類型的 default configuration.
      - 方案一: 單獨備份 profile 類型的資料.
      - 方案二: 全系統備份，去除 Statistics 之後備份 nodeDB.db 與 devices.db 資料.
    - 優缺點比較
      - 方案一:
        - 細節說明: 如果 restore 到舊設備，若舊設備已存在 profile 資料，則需移除現行設備上的 profile relation，並 sync 到相關的 configuration.(ps. 考慮重新作設備的 onboarding 來進行 configuration 重設).
        - 使用情境: 比較建議使用在全新設備或因設備故障做完 factory default 之後使用.
        - 優點:
          - 用戶不用重建 profile，可加速系統設定，以降低用戶設定成本.
        - 缺點:
          - Restore 時需單獨考量 profile 的版本相容性問題，會增加開發成本.
          - Restore 時若採用覆蓋模式匯入，需清除現行 profile relation 並 sync 到相關的 configuration，用戶需重設 profile 關聯.
          - Restore 時若採用 Insert 模式匯入，則關係不用重建，但需規劃匯入的流程細節，開發成本較高.
      - 方案二:
        - 細節說明: 接近全系統備份，作法上較為單純，備份時後把 nodeDB.db 與 devices.db 檔案匯出(ps. 去除 db 統計資料)，restore 時直接覆蓋現有的 db，並執行缺少的 migration 描述檔，最後由用戶根據設備現況來移除或新增設備.
        - 使用情境: 建議使用在舊設備故障或系統資料毀損時，用戶想快速恢復全系統設定.
        - 優點:
          - 用戶若版本異動不多的狀況下，幾乎不用重設系統，可加速系統設定，以降低用戶設定成本.
        - 缺點:
          - Restore 時仍需考量所有 table 的版本相容問題. (但由於可直接使用現成的 migration 機制，所以問題不大.)
          - backup 時可能需要的空間較大.(ps. device.db 需複製一份在本地端並去除統計資料?)
- 2024/01/23
  - 完善 Bluetooth Doc
    - Checksum 計算方式。
      - 計算時原先 checksum 位置先以 0x00、0x00 計算？
        - YES
      - 有 data 與無 data 時計算方式是否一樣？
        - 無 data 時不計算 checksum，checksum 的位置直接補 0x00
      - Ack 的 checksum 需要檢查？
        - 不檢查.
    - Timeout 限制
      - 底層 Read: TIME_OUT_RECEIVED 3 秒.
      - 底層 Write: TIME_OUT_ACK 3 秒.
    - Request、Respond 完整格式。
      - 為何有 Url 了還要 command 欄位？
        - 是預留給其他底層的實作技術(eg. MQTT/websocket)時使用的，目前無作用可以不填.
      - 為何 data 後面要加上####？
        - 底層要求讓其預判是否為有效的封包.
    - Command Failed（0x07、0x0B）場景。
      - checksum 檢查錯誤時.
    - 當 Data 長度不超過 MTU 時，Command 要給 Sequential 還是 Squential_End？
      - Squential_End.
  - 修正 Kelly 提出的 switches queryConfig 的 swagger API.
  - Configuration backup/restore
    - Configuration backup
      - 原始的作法為打包所有類型的 default configuration.
      - 新作法則根據 serialNumber 打包該 device configuration.
    - Configuration restore
      - 原始作法會 restore 所有類型符合的 default configuration.
    -
- 2024/01/22

  - 關於 BT 連線穩定度的 Issue，目前預計朝下列幾個方向來進行:
    - 完善 cbtmain 尚未 ready 前的 request command 處理.
    - 複製藍芽 command 收送失敗，是否有觸發重送或 Timeout 機制，是否會造成狀態異常，進而造成 daemon 卡住.
    - 確認 command 重送機制是否正常:
      - APP 重送 Request 封包機制: 複製藍芽 command 收送失敗，並觀察是否會正常觸發 command 重送機制.
      - BT daemon:
    - 觀察 command 重送機制與 timeout 機制的配合是否正常.

- 2024/01/18 - command 雖然立即執行，若遇到上個指令還沒完成時的處理機制? - command 何時會從 db 中移除? complete?

      - QoEAI跟owgw收送使用兩個topic嗎?
      - 臨界點的處理方式，避免過度頻繁切換
      - 11v能告知用戶跳到哪個EAP?

  新工作日誌
  Arisa 應該不會看非常小的細節，主要大方向能讓他快速理解就行了，有些重點說明可以調整一下順序，以下是我的建議的說明順序:

1. 從 frontend、backend、DB 的資料一致，都採用 parVlan table ID 方便關連與處理.
2. 最後寫到 configuration 時由 NodeJS 轉換為 vid.
3. NodeDB 的欄位名稱改為 taggedParVlanIds 做為區別.
   其他都歸類在影響範圍即可.

- 2024/01/10
  - config 異動，是否要強迫 factory default.
  - vlan default profile 是否能編輯，以及 vid 預設值.
- 2024/01/05
  - ethx: 欄位變多
  - ieee8021x: 新增
  - portList:
    - "nativeVlan": "Default"，(ps. 原 vlanProfileName)
    - "taggedVlan": "true"，
    - "taggedVlanId": "22，23"，
    - "ieee8021xCtl": "forceAuth"
  - SwitchStatisticLinkState: Need to declare for getSchemaPath(StatisticDataLinkStateStream)?
  - SwitchStatisticCbtSwitchPort: 是否有需要增加 taggedVlan，taggedVlanIds，'1pPort'? (ps. 目前有 ieee8021xCtl)
- 2024/01/04
  - swith config issues
    - third-party.cbtSwitch.vlanProfiles.vlan{"enabled": false，"id": 0} (org)
      - third-party.cbtSwitch.vlanProfiles.vlanId (0102)
      - 以~0102.json 為主.
    - taggedVlan，taggedVlanId，ieee8021xCtl，1pPort.(excel 沒寫，目前 default value 以現行 config 上的值為主)
    - third-party.stp.enable 為 string 型別，原因?
    - qos 物件欄位的預設值，以 config 為的值為主?