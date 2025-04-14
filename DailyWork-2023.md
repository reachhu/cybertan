# Daily Work - 2023

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

# 工作日誌 - 2023
- 2023/12/28
  - 資訊安全管裡.
    - 後台溝通的密碼.
      - wifi backend ssid 連線密碼.
      - 藍芽通訊密碼
      - FRP 之間溝通的密碼.
      - LCM 調用 controller API 的機制.
  - 雲端存取
  - 多帳號管理
- 2023/12/27
  - 多台裝置推送 configuration 時
    - 會造成 NodeJS API timeout.
      - 方案 1: owgw 提供批次方式更新 Config，如此才能進行後續的 rollback.
      - 方案 2: config 設備推送與更新資料庫分開，資料庫一旦更新成功即回復 NodeJS API.
    - 中途推送設備失敗，會造成 NodeJS rallback，進而與 devices config 不一致.
      - 方案 1: NodeJS 直接更新 devices.db 中的 configuration，發生錯誤可同時 rollback，並在成功更新完之後通知 owgw 推送 config.
      - 方案 2: owgw 提供更新資料庫的 API，出錯時也需 rollback.
- 2023/12/22

  - /api/eaps
    - 用戶 profileNames 無法明確知道是哪種 profile name，用處不大.
    - trafficStatistics.services 只有單個卻使用 s?
    - 未連線的 pending eap 應顯示 offline?
    - PUT wifi steering 6~7 秒.
  - /api/par-ssids
    - response Internal error.

- hermes controller 的頭從 network 來找?

- 2023/12/13

  - follow controller 個別 port 設定? 涉及 port 都要設定?
  - STP/RSTP 跟 vlan trunk 共存?
  - 一般 QoS 功能做在 Switch?
  - 802.1x Radius 需要 switch/EAP 配合?

- 2023/12/06

  - updateQueryConfigData 使用 checkConfiguration 對於新增的額外欄位未報錯!

- 2023/12/05
  - Hermes 相關 issues
    1. cbtTopology/topology 多一層 topology key，移除 or 保留? (Arisa 確認中)
       - 跟 Arisa 確認是否有特殊原因，另外將同類型的 API 一併修改.
    2. cbtTopology/topology controller 為 array (Hermes only)，整合 Hermes 時如何與原本的 API 區隔? (跟版控有關)
       - 現行 API 分開來區隔不同的 model.
       - 未來共用 model(增加 NetInfo，Network，controller uplink 可能為 subnet ip)，並在 service 利用 flag 區隔，仍要考量版控的可行性.
    3. GET/PUT default configuration，改用 par-device/{SN}/configuration 取代，那 modelName 要改用 serialNumber? Hermes model 無法區別 controller?
       - NodeJS API 不提供 default configuration 修改，改用個別 device serialNumber 取代，Hermes 的環境會有多個 controller 也不會有問題.
- 2023/11/30
  - service_NPT 多了 local_server(bool)
  - PUT service_NPT 的 toParDevicesSystemConfiguration()需要吃原來 GET 的資訊.
  - ipv4 / ipv6 model 共用
  - enable all 不用定義型別.
  - 需要 Firewalls? (Firewalls{enable:bool，Firewalls:[Firewall]})
- 2023/11/29
  - switch vlan profile
  - native 直接存 vid? 方便了解與 tag 的連動? 資料結構更簡潔?
  - default vlan profile on network/switch 衝突問題.
- 2023/11/21
  - 創建雲端帳號 ​
    - 目前只有具備雲端帳號的 Owner 需要密碼同步?
    - Cloud 帳號能本地登入嗎?(T 公司可以做設定)
    - Cloud 與 local 完全分離，優點方便管理，缺點登入比較不方便.
      - T 公司具備雙重登入的帳號，會以 Cloud 優先，離線狀況下亦可本地登入.
    - 建議提供 Owner 更便利的方式來創建帳號(Owner 直接發邀請信)即可創建帳號，密碼的建立是一個問題，用戶第一次登入時建立密碼.
    - Owner 通常會更傾向於使用同一組帳密做管理，所以 owner 的帳密應該在遠端與本地各存一組並需要同步.
    - 對已有 local 帳戶的 Owner 來說，應該會希望類似一鍵生成的方式來建立雲端帳戶.
    - 本地帳戶需要 email?
    -
- 2023/11/10
  - EAP remote onboarding SA.(Todo)
    - Need Jessie confirm.
  - Radios APIs Update review by KK.(Done)
  - Hermes APIs verified by RD.(Onging)
- 2023/11/08
  - EAP remote onboarding SA.(Todo)
    - Need Jessie confirm.
  - Radios APIs Update review by KK.(Done)
  - Hermes APIs verified by RD.(Onging)
- 2023/11/08
  - 建立 PC 端 owgw 測試環境
  - Fix and test delDeviceBySerialNumber bugs.(Done)
  - Fix and test getCbtInterfaceStatusList bugs.(Done)
  - Refine radios code from KK review.(Open)
  - Trace EAP APP code.(Open)
  - EAP remote onboarding SA.(Todo)
    - Need Jessie confirm.
  - Radios APIs Updatge review by KK.(Ongoing)
- 2023/11/07
  - Add delDeviceBySerialNumber for Hermes.(Done)
  - Add getCbtInterfaceStatusList for Hermes.(Done)
- 2023/11/06

  - EAP API nodes
    - tag 改成 eaps?
    - profiles 與 configuration 順序與名稱統一
    - 更新 eap profiles 帶個資訊與 get 應該類似.
      {
      radios:[ID1，...]，
      ssids:[ID1，...]，
      }
    - get eaps 的 profiles 欄位
  - par_radio_apis
    - KK 已經有加入 comment，若時間允許較幫忙改這部分.
  - Srource code 開發慣例
    - Jason: 你在執行 start-sync pipe 跟 main.ts 的 pipe 是否會衝突?
    - entity integer
    - entity date bigint
    - entity befaor Hook 與之對應的 migrate + service 測式
      - ?
    - entity class-validator 每欄都要 + errorMessage
    - entity default 拉到常數存
      - 建立 Default class(eg. RadioDefault)? 比較好找定義值.
    - dto PickType
    - 只用 POST/PUT/GET/DELETE
    - 改名 createOne(save) removeOne getOne updateOne((save))
    - Controller 命名規則為了跟 Service 分開，所以有下列命名方式
      - Controller(get) - Service(getOne)
      - Controller(gets) - Service(getAll)
      - Controller(update) - Service(updateOne)
    - Eric: 共用常數與功能是否可改成更容易尋找的方式? (eg. CbtDefault / CbtUtil)

- 2023/11/01
  - 自評
    - 責任感 : 8 (最高分: 全體)
    - 細心度 : 6 (最高分: Arisa，KK，Astrid)
    - 專案理解度 : 8 (最高分: Arisa，KK)
    - 程式熟悉度 : 6 (最高分: KK，Jason)
    - 邏輯推演能力 : 8 (最高分: KK)
    - 專注度 : 7 (最高分: Arisa，KK)
    - ps. 最高分人選主觀成分非常高，會受限於比較常互動的人.
- 2023/10/31
  - parInterface missing fields
    - ethernet(default value)(ignore)
    - services.(ignore)
    - ipv4(v)
    - role(v)
  - parRadios
    - allowDFS(migration 需調整為 allow null)(Done)
    - 定義 radioProfileList 物件?
    - async getAllProfiles() (+ promise / exception)
    -
- 2023/10/26
  - Hermes owgw wifi/radio api 捕到 nodeJS.(Done)
  - VlanProfie API 補錯誤說明.(Done)
  - Interface API 優化.(Done)
- 2023/10/19 ~ 20
  - 協助 BLE 首次安裝 refactor
  - 實作 Radio Profile
    - API 名稱規則是否要跟 vlan 一致?
    - channel: default 'auto'?
  - backlog 87 - APP 是否需要同步修改?
- 2023/10/18 ~ 20

  - Vlan profile 開發.
    - entity: ValidRule 改為 ValidNameRule 更合理.
    - vlan migration process
      - 確認或建立 parIntefaces / parVlanProfile
      - 透過 devices.db 的 DefaultConfigs table compatible 取得

- 2023/10/16 ~ 17
  - Swagger tag for openAPI.
  - vlan 現況了解與 source code.
- 2023/10/13
  - 工作事項討論 - Jessie(Open)
    - 風險評估: 是否要並行開發，降低風險?(改為部分效能需求較高的 api 改由 owgw 實作)
  - Review APP merge request.(Done)
    - Internet settings.
  - Internet StaticIP + PPPoE test.(Open)
  - QoEAI by event trigger.(關鍵在於 user experience 的分數計算)(Open)
  - Review gira bugs
    - PPPoE/static IP settings on firsttime setup.
    - token expire.
      - 客戶有機會在 wan 設定頁面停留過久造成 token expired，在首次安裝提供自動重新登入機制?
    - Request timeout continue to occur.
      - token expire 發生之後沒有恢復機制.
- 2023/10/11
  - 工作事項討論 - Jessie(Open)
  - 理頭髮(Done)
  - App 提測(Done)
  - 明日請假 + 便當取消(Done)
  - 上週 + 本週工時(Done)
  - 牙醫: 確定牙套材質.(Done)
- 2023/10/06
  - cbtTopologyEapByMacAddr 是否要改名? 多參數命名規則?
- 2023/10/03
- 2023/10/02
  - 驗證 LCM 與 APP 配合問題 on Controller service on v1.2.0 branch.(Jason/Eric)(Open)
  - DB schema 設計 issue 討論.(KK，Jessie)(Open)
- 2023/09/27
  strData = {"command":"getControllerStatistics"，"http":{"url":"https://localhost:16002/api/v1/device/24fe9a10cffb/statistics?lastOnly=true"，"method":"GET"，"accessToken":"4b03a975efb2f6f02d17fba98391a906ff922dbda40b061866e677f3010cb97b"}，"data":{}}####，strMagic = #### dataSend length: 256
  - 驗證 LCM 與 APP 配合問題 on Controller service v1.2.2.(Done)
    - owsec 的 code 尚未放到 v1.2.2.
  - DB Migration issue from Arisa.(KK，Arisa)
  - DB schema 設計 issue 討論.(KK，Jessie)
  - Profile config next stage
    - ssidProfile + vlanPofile id.
    - parSwitchPort + vlanProfile id.
    - parInterface.(remove)
    - new networkProfile(data from parInteface) / new NetworkToDeviceMap.
    - new vlanProfile(data from parInterface).
    -
- 2023/09/19 ~ 2023/09/25
  - Profile config webUI scope 說明.(Done)
  - Profile config WebUI 第一階段定案(Done)
  - nodeJS 開發環境準備.(Done)
  - APP for Wallmount EAP 提測驗證與準備.(Done)
  - APP Demo 文件準備.(Open)
  - 藍芽手次安裝流程 Demo 與文件準備.(Open)
- 2023/09/13
  - WebUI 共識取得(Done)
    - group
- 2023/09/13

  - Hermes WiFiConfig APIs Docs.(Open)
    - ymal.
  - 參與 Partial Config 會議.(Open)
  - Pending jobs:
    - EAP controller initial by web，BT need to close.
    - BackLog2_36 switch port verify.
    - WiFi signal 熱力圖.

- 2023/09/12
  - Hermes WiFiConfig APIs Docs.(Open)
    - ymal.
  - 參加 BT Module 送認證文件會議.(Done)
    - Ethan 9/15 提供文件.
- 2023/09/07
  - Implemen Hermes WiFiConfig APIs.(Open)
  - Involve node.js metting.(Done)
- 2023/09/07
  - 提交 RadioConfig APIs.(Open)
  - Fix switch port SFP port bug.(Open)
  - APP SNR - API 文件.
  - APP AIQoE - API 文件.
    - 設定 wifi 額外調用的 API.
    - QoE history stats APIs.
- 2023/09/06
  - RadioConfig API 開發.(95%)
  - ucentral client 架構與流程.(Done)
- 2023/09/05
  - RadioConfig API 開發.(Open)
- 2023/09/04
  - APP 輔助 EAP 佈點方案討論.(eg. WiFi 訊號熱力圖)(Done)
  - RadioConfig API 開發.(Open)
- 2023/08/31

  - DB migration 討論.
    - 跨版本更新時 DB migration 流程.
      - v1: customer current
      - v2: device 新增 f1 欄位.
      - v3: 新增 profile table.(ps. profile 的部分資料會從 defaultConfig 匯出.)
      - v4: 修改 bug.
      - Customer A firmware upgrade from v1 to v4，db migration 的具體流程為何?
      - 如果客戶是從 v3 upgrade 到 v4，那資料庫不做任何變動也不會有任何問題.
      - v4 版本會包括所有修改的 SQL Script，配合欄位或 table 的判斷，就可達到任一版本的 DB 升級.

- 2023/08/30
  - DB migration 討論.(Done)
  - Controller 編譯環境優化.(Open)
    - On windows.
      - owgw(Done)
      - owsec(Open)
    - On Mac.
- 2023/08/28
  - Switch APP release note.(Open)
    - SFP port spec. update and ethType plugIn check.
  - Controller 編譯環境優化.(Open)
  - Hermes API 整理.(Done)
    - 會議說明.
- 2023/08/25
  - Controller 編譯環境優化.(Open)
  - Partial/Profile Config.(雛形文件整理)(Done)
    - Unique ID.(透過 ID 區間錯開，統一管理自增 ID，複合 key(自增 ID 配合其他欄位)，GUID)
    - Group 僅限於 WiFi profile.
    - Jennie 問題釐清.
  - Hermes API 整理.(Done)
    - WiFi/Radio 相關 API.
    - 另外開 Branch for Hermes?
    - cbtWifiInterfaceStatusList / cbtDefaultConfigurationAndApply (cybertan_eww631-a1/b1)
- 2023/08/24
  - Switch 急單.(Done)
  - Controller 編譯環境優化.(Open)
  - Partial/Profile Config.(雛形文件整理)(Open)
    - Unique ID.
  - TIP OpenWiFi Server for Switch.(Done)
    - Self Sign CA ready.
  - Version controller 討論 (Part 2).(Done)
  - Build Release code for switch.(Done)
    - Image 編完後上傳到 git 權限不足，需請 Dax 幫忙，此次仍由仕銓做最後上傳的動作.
- 2023/08/23
  - 升級 Flutter SDK 至 v3.13 並驗證 EAP source code.(Done)
  - Controller 編譯環境優化.(Open)
  - Partial/Profile Config.(雛形文件整理)(Open)
  - TIP OpenWiFi Server for Switch.(Open)
  - Backlog issue 討論.
- 2023/08/22
  - Get cbtParSwitchPort 有可能造成 SSL bug.(ps. KK 發現，無法重現於自己的環境)
  - Partial/Profile Config 相關 Issues
  - v1.2.0 與 main branch 同步問題，owner 是？
  - MSTeams Task Issues
    - 分析與規劃是否能放入？(eg. 參與相關會議討論)
    - APP 新功能發想.
    - APP 與 Controller 版控機制討論與規劃.
      - 橫向: 產品，專案，客戶.
        - 增加彈性欄位做區分.(畫面、風格)
        - API 版本列表: 透過欄位表示是否支援.
      - 縱向: 新舊相容性.(透過相容區間，來判斷是否隱藏、灰化...)
        - API 版本列表: API 現行版號，相容的最低版號.
        - Controller 與 APP 各有一份 API 版本列表.
      - 第三方使用者.
- 2023/08/21
  - TIP OpenWiFi Server for Switch.(Open)
  - Controller 編譯環境優化.(Open)
- 2023/08/18

  - 板子上的 cbtmain 剛 build 過，主要拿來 trace ethType 的更新時機，但需要重 load.(Open)
  - 開發 backlog 163.(Ongoing)
    - 觸發頻率高.(4 秒/6 秒各一次)
    - 無法實際執行 switch config update.(json 結構認知錯誤)
    - handleSwitchSPFPort 需要 critical section 保護機制？
    - 使用到共用的 State\_變數，可能會改到其他裝置的 State？
  - QoEAI
    - APP Follow Web 圖表顯示.

- 2023/08/17
  - Partial/Profile Config 相關 Issues
    - Hermes 以單純提供 API 不改 table 的方式提供 Partial Config API.(共識)(7 天)
    - Profile Config 開發手順 Issues.
      - 目前預計 WebUI 先不動的情況下進行功能開發.(SA -> DB -> API -> Web UI).
      - Arisa 建議把 profile config 需求談清楚，webUI 按照需求規劃之後，再配合規劃 DB，如此 DB 的更動可以一步到位，之後比較不會異動.
        (SA -> Web UI -> DB -> API)，原因在於 controller 升級，不會造成 DB 頻繁異動.
      - 考量現況因時程考量很難避免 DB 異動，所以建議 partial config 仍需分階段進行，並配合 DB Migration 的工具來做升級.
      - 時程粗估
        - Profile Config.(2 人月)
    - DB Migration.(人力安排？)
      - Controller 效能優化是否會影響 DB Migration.(不會影響)
      - Migration 工具 survey.
        - 工具如何輔助資料轉移.
        - DB 應有自己的獨立版號，用戶 Controller 更新時需根據更新時應包括：軟體版本，韌體版本，DB 版本.
      - 時程粗估
        - 2 週.
- 2023/08/16
  - 開發 backlog 163.(Ongoing)
  - survey db.
  - AIQoE 討論.
  - 新舊版欄位異動需求，方案評估如下：
    - 方案一: 在沒有相容版本管理下，欄位異動後 API 若可以做到向下相容，原用戶在進行 Controller 升級之後，舊版 APP 可以照常使用不影響，基本上 Controller 會根據缺漏的欄位額外的預設處理.(ps. 若不相容，則用戶在調用此 API 時出現格式錯誤，進而建議用戶可能需要升級，此錯誤狀況也可能是 bug 引起，所以比較不精準.)
    - 方案二: 透過 APP 的版本管理，登入前 Controller 提供相容的 APP 版本區間，若低於此需求，則建議強制要求用戶升級.
    - 方案三: 透過 API 的版本管理，登入前 Controller 提供相容的 API 的版本區間，若不相容則使用到該 API 的相關畫面需隱藏或做其他處理，若是核心 API 不相容，則建議強制要求用戶升級.
    - 方案四: 以方案三為基礎，再加上一個彈性欄位，此彈性欄位可以區隔不同客戶或專案，讓 APP 可以根據此欄位顯示不同的功能頁面，此狀況發生在不同客戶或專案，針對相同的功能頁面，有不同的畫面需求.
- 2023/08/15
  - 驗證並提交 backlog 164.(Open)
  - 開發 backlog 163.(Ongoing)
  - 首次安裝結束時以 config 的方式來關閉 BT.(Open)
  - API firstLogin 的邏輯增加判斷帳號個數.(Open)
  - Partial Profile
    - 現階段以 API 形式提供給 Hermes.
    - 完整規劃後再分階段進行 partial config.(Need document)
      - UI 會影響 DB 規劃.
      - DB migration issue.(Discussion)
- 2023/08/14
  - 實做 Switch - ethType 規格修改問題.(backLog 164)
- 2023/08/10
  - 整合 main branch 並重新驗證 Controller bug(backlog 135)
  - 了解並討論 tssClient 解決方案.
  - 說明 MS Teams for APP weekly report.
- 2023/08/10
  - 新增 backlog: API auth2 for APP 增加判斷帳號個數，再決定是否完成首次安裝流程.
  - 驗證 Controller bug(backlog 135)
  - 驗證 Controller cbtTopology 新增 speedRate 欄位.
  - 協助 Switch SQA 人員驗證設備.
- 2023/08/09
  - 討論 Partial Profile(VLAN/Radio/Wireless).(Done)
    - 原則採用方案一的修正，但不用 sync 到 DefaultConfigs，init device 時需要從 profile 置換掉 config 內容.
    - 增加一個 default boolean 欄位決定是否要在 Init device 時 apply.
    - 增加一個欄位決定是否可以讓用戶修改該筆 profile.
    - 增加一個 global table 去紀錄 Steering 相關資料.(ps. 有 AI 輔助之後是否有需要設定Ｓ teering).
    - VLAN 採用 profileId 的方式關聯 cbtParInterface.
  - 討論 AI/QoE.
- 2023/08/08
  - 討論 Partial Profile(VLAN/Radio/Wireless).(Done)
    - 原則採用方案一的修正，但不用 sync 到 DefaultConfigs，init device 時需要從 profile 置換掉 config 內容.
    - 增加一個 default boolean 欄位決定是否要在 Init device 時 apply.
    - 增加一個欄位決定是否可以讓用戶修改該筆 profile.
    - 增加一個 global table 去紀錄 Steering 相關資料.(ps. 有 AI 輔助之後是否有需要設定Ｓ teering).
    - VLAN 採用 profileId 的方式關聯 cbtParInterface.
  - 討論 AI/QoE.
- 2023/08/07
  - APP Wallmount EAP(Open)
    - 釐清與確認Ｗ allmount EAP 的修改範圍.(Done)
      - Add new type of topology EAP icon.(Open)
      - WiFi/Radio 修改需 sync 到Ｗ allmount EAP.(Open)
    - 預估時程.(Done)
    - 確認 Wallmount APP 的必要性.(Open)
    - 協調 switch icon.(Open)
  - 規劃 Partial Profile(VLAN/Radio/Wireless).(Done)
  - Survey Conteroller service on MacOS(Ongoing)
    - 加速 Controller 開發.
  - 反應 EAP(v1.1.1)的 bug
    - 未正確提供 frounthaul list.(ps. 初步看到 EAP ethernet.client 正確，fronthaul list 只出現最後一筆 client，已提供 db 給仕銓/Rock 分析.)
- 2023/08/04
  - APP 複驗 Controller 提測版後再 release.(Open)
    - Switch 設備.
  - 規劃 Partial Profile(VLAN/Radio/Wireless).(Doing)
- 2023/08/03
  - 規劃 Partial Profile(VLAN/Radio/Wireless).(Doing)
  - APP 第六輪提測 RD 自測.(Done)
  - APP 提測準備.(Doing)
    - 缺 contrller/switch 提測版號.
    - 整理 GUI fution.(Doing)
      - PoE 相關名詞定義說明.(Open)
    - 整理 know issue.(Open)
      - Switch 底下接 EAP 時，會出現 EAP 的 client.(ps. 根據兩者的 client list 集合個數來判斷.)
      - Switch 底下接 Switch 時，連拓樸圖都無法 conver client 的錯誤.
  - Switch SPF Port 變更需求(Open)
    - 記載於 backlog.(Done)
  - Wallmaount EAP/Switch topology 現行問題討論.(Done)
    - Ｓ witch 之間在處理 clientList 時需考量順序性，以 client 多者先做.
    - Ｗ allmount EAP 的 client 除了 Fronthaul client，還需支援能區分 WAN/LAN port.
- 2023/08/02
  - APP 第六輪提測 RD 自測.(Done)
  - APP 提測準備.(Doing)
  - 規劃 Partial Profile(VLAN/Radio/Wireless).(Doing)
  - Switch SPF Port 變更需求(Open)
    - 記載於 backlog.(Done)
- 2023/08/01
  - Switch APP RD 自測.
    - Controller FW 更新.(Beta)
    - Switch FW 更新.(Done)
    - port - link speed 修改後畫面有誤.(Verify)
    - 這版 Controller 的 cbtTopology/topology 的 client 的 netInfo 的 channel 會出現 Null.(Open)
      - 由於 EAP 來不及修改，剛剛跟 Jessie 討論後，APP 此欄位先改為允許 null 跟 Web 一致.
    - Port 25/26 Link speed 選項跟 web 不一致.(Open)
      - 討論後開任務卡.
  - Switch SPF Port 變更需求(Open)
    - 因應 SPF Port plugIn 機制，Switch Statistics 的相關配套處理流程.
    - GET cbtParSwitchPort 額外撈 ethType.(？addition=[ethType])
    - PUT cbtParSwitchPort 欄位額外檢查 Switch Statistics 的 ethType.
    - Web 的配套修改.
      - Switch port detail 根據 ethType 決定 speedRate 選項.
      - PUT cbtParSwitchPort 遇到 ethType 不一致的錯誤，需顯示錯誤並重撈 switch port
    - APP 配套修改.(ps. 細節內容同上)
  - LCD screen
    - APP bug: 出現帳號未建立就被通知已完成首次安裝.
      - auth API 的調用若額外給 newPassword 會導致 controller 生成一個 adminPassword 的檔案，進而造成 LCM 的誤判.(ps. 初步的解決方案是 auth API 的 newPassword 會多判斷帳號數量，來決定是否要生成 adminPassword 的檔案.)
  - owgw 開發說明 for Steve.
  - Partial profile 規劃目前不會動到 UI.
  - Survey 遠端工作連回公司的板子 debug.
- 2023/07/31
  - Switch APP RD 自測.
    - Controller FW 更新.(Beta)
    - Switch FW 更新.
  - LCD screen
    - APP bug: 出現帳號未建立就被通知已完成首次安裝.
      - auth API 的調用若額外給 newPassword 會導致 controller 生成一個 adminPassword 的檔案，進而造成 LCM 的誤判.(ps. 初步的解決方案是 auth API 的 newPassword 會多判斷帳號數量，來決定是否要生成 adminPassword 的檔案.)
  - owgw 開發說明 for Steve.(Open)
  - Partial profile 規劃目前不會動到 UI.(Open)
- 2023/07/26
  - APP 驗證 switch 功能
    - downlink 缺 connectionType，linkRate.(ps. 列為下次測試)
    - EAP 接於 switch 下方時，會出現 EAP 底下的 client.(ps. 列為 know issue，下次測試)
- 2023/07/25
  - Web BLE 關閉機制
    - 方案一:
      - 底層準備一隻 daemon 去 pooling admin file，發現之後去調用 ble_tools.sh.
    - 方案二:
      - Controller 在 firstLogin 成功之後改調用一隻 script(eg. firstLogin.sh)，裡面可包括關藍芽等一些額外首次安裝成功後需要的動作.
- 2023/07/24
  - Steve/詩晴討論 APP 的首次安裝流程細節.(Done)
  - 規劃安排 radio/wifi/vlan partial 與 profile 化的任務.
    - radio/wifi/vlan 畫面調整.(optinal)
    - radio profile table on init.
    - radio restful (CRUD)API.
    - wifi profile table on init.
    - wifi profile restful (CRUD)API
    - vlan profile table on init.
    - vlan profile restful (CRUD)API
  - 討論並解決 wallmount eap 未處理 lan port client 的 issue.
  - 討論並解決 switch 與底下設備 client 重複的 issue.
  - 因應底層 Switch SPF port 熱差拔機制，需調整 ethType 欄位內容.
- 2023/07/20

  - BackLog 122，124

    - Desc:

      - 後端 API GET cbtDashboard 的 client 數量統計有誤(backlog 122)
      - 後端 API GET /api/v1/cbtTopology/switch netInfo.macAddr 資料有誤.(backlog 124)

      * Reason:
        - 未處理 vlan client.(backlog 122)
        - netInfo.macAddr 需更正為 switch 本身的 wan port mac.(backlog 124)
      * Solution:
        - 增加處理 vlan clients.(backlog 122)
        - netInfo.macAddr 更正為 switch mac.(backlog 124)

- 2023/07/20

  - BackLog 85，86，89，90，97，120:

    - Desc:

      - API GET /api/v1/cbtTopology/client?macAddr=xxxx always return first matched client.(backlog 85)
      - API GET /api/v1/cbtTopology/client uplink 資料有誤.(backlog 86)
      - API GET /api/v1/cbtTopology/swtich
        - portList.devMacAddr 資料有誤.(backlog 89)
        - netInfo.uplink 的 macAddr 資料有誤.(backlog 90)
        - API GET /api/v1/cbtTopology/client 中 clients 是錯誤的 MAC.(backlog 97)
        - clientList 有缺 client.(ps.使用 vlan 的 client)(backlog 120)

    - Reason:
      - macAddr 比對邏輯有誤.
      - Controller lease client prepare bug cause duplicated.
      - Switch 的 uplink 使用錯誤的來源(topology.clients)來尋找問題.
      - innerTopology 的 lease client 錯誤，缺少處理 vlan 的 interface.
    - Solution:
      - Fix macAddr 比對邏輯.
      - Fix Controller lease client bug.
      - Switch 的 uplink 的來源改為(topology.switch)來尋找.
      - Fix innterTopology 的 lease client bug.

- 2023/07/19
  - Merge main branch 並驗證相關 bugs.
    - cbtTopology/topology 的 client 資料有誤.(ps. 跟 main branch 有關，釐清中.)
    - backlog 119.(Ongoing)
      - Eap fronthaul list 沒有出現 client.(ps. KK 釐清中).
      - 即便 fronthaul list 有正常出現 client，但若同時也出現在 switch，該以誰為主？(ps. 現行因為 EAP 比較慢做，所以後蓋前，以 EAP 為主.)
    - Wallmount EAP 有 lan port，現行做法 topology 會有潛在風險，需要進一步討論改善方法.
  - 參與 Partial config for vlan/radio/wireless profile 會議.
    - 負責後續 API/Table 規劃與任務列表.
    - 規劃 API 時考量單台多台皆適用的狀況.(ps. 初步想法是都 group 化.)
- 2023/07/18

  - backlog 86:
    - API GET /api/v1/cbtTopology/client uplink 資料有誤.(Fix)
  - backlog 89/90: API GET /api/v1/cbtTopology/switch?macAddr=XXXX netinfo.uplink 有誤.(Fix)
  - backLog 97: API GET /api/v1/cbtTopology/client 中 clients 是錯誤的 MAC.(Fix)

- 2023/07/17
  - backlog 86:
    - API GET /api/v1/cbtTopology/client uplink 資料有誤.(Fix)
    - API GET /api/v1/cbtTopology/client?macAddr=XXXX 資料有誤，總回第一筆.(Fix)
  - backlog 89/90: API GET /api/v1/cbtTopology/switch?macAddr=XXXX netinfo.uplink 有誤.(Fix)
  - backLog 97: API GET /api/v1/cbtTopology/client 中 clients 是錯誤的 MAC.(Fix)
- 2023/07/14

  - MTIV00285 24:fe:9a:10:cf:a1
    MTIV00289 24:fe:9a:10:cf:b0
    MTIV00295 BT-board
    MTIV00302 00:45:e2:b0:69:72
    MTIV00303 00:45:e2:b0:69:95

- 2023/07/10

  - Web EAP offline 卻顯示在 pendding?
  - 首次安裝成功，BLE 訊號還在...
  - {"command":"oauth2"，"http":{"url":"https://localhost:16001/api/v1/oauth2"，"method":"POST"}，"data":{"userId":"admin@cybertan.com.tw"，"password":"Cbt@12345"，"newPassword":"Cbt@12345"}}

  - {"sessionId":""，"http":{"statusCode":200}，"data":{"access_token":"48cb641f23fec5ce89bae163ef623d72fe4db428080bd2b285b741754bca7c76"，"aclTemplate":{"Delete":true，"PortalLogin":true，"Read":true，"ReadWrite":true，"ReadWriteCreate":true}，"created":1689035916，"errorCode":0，"expires_in":900，"firstLogin":true，"idle_timeout":300，"lastRefresh":0，"refresh_token":"d81250a3b5af2b5601866be08f355f714dbb66310cab9cd212ef9cf193db4779"，"token_type":"Bearer"，"userMustChangePassword":false，"username":"admin@cybertan.com.tw"}}

  - {"command":"firstLogin"，"http":{"url":"https://localhost:16001/api/v1/firstLogin?oldUser=false"，"method":"POST"，"accessToken":"48cb641f23fec5ce89bae163ef623d72fe4db428080bd2b285b741754bca7c76"}，"data":{"email":"eric2@g.com"，"currentPassword":"Cbt@12345"，"name":"eric2"}}
  -
  - {"sessionId":""，"http":{"statusCode":400}，"data":{"ErrorCode":"CLD10200"，"ErrorDescription":"CLD10200: Record could not be created."，"ErrorDetails":"POST"}}

- 2023/07/07
  - BLE 測試
    - bluetooth branch
      - default login 時第一次 ble connection 會出現 unhandle Exception，感覺沒有觸發等待機制.
    - register_view_model
      - firstLogin 時 email，password 為空.(ps. state 資源被釋放掉)
- 2023/07/05

  - cbtTopology
    - bugs
      - cbtTopology/client by macAddr 沒找到正確的.(ps. 沒比對 macAddr，需再確認.)(Verify)
      - cbtTopology/client switch client uplink 不正確.(ps. 沒考量 switch client.)(Verify)
      - cbtTopology/switch by macAddr 的 portList[x].devMacAddr 不正確.(ps. 原因待確認.)(Open)
    - issues
      - EAP find all radio bssids? (ps. Comment 跟後續邏輯不一致.)
      - 連接 switch 的 client 是否會有 qualityData? (ps. 應該沒有.)
      - Ｃ ontroller 的 lease table 表中透過 interface.counters 可取得 tx/rx 的資訊，應該僅限於直接接在 port 裡的 client? (ps.認知應該沒錯.)
      - 關於 GetClientQualityData，switch 的 client 也可以取得？
      - Controller 直連的 client 沒看到 tx/rx 相關訊息？

- ## 2023/07/04
- 2023/06/26
  - SDWAN
    - by API.
      - Web UI 風格統一.(優點)
      - 開發時程較長，問題變數較多.(缺點)
      - 對方資料掌握度較高.
    - by Web
      - 開發時程短.
      - Web UI 風格差別較大.
      - 需額外提供同步資訊.
    - 對於快速提供給客戶做 demo，我的建議是採用提供 web 整合的方式進行.
    - 以技術角度來看，Controller 當 portol 網站，
    - APP 評估：
      - Topology 沒有 controller 需要改寫，SDWan 當頭，用 type 區分.
      - settings 跟 config 有關都會影響，需要移除 firewall、static route 的欄位？(影響相容性)
      - 要如何找到 devices 中的 controller 設定 config？（EAP main 是哪一台？） 以前寫死 modelName，現在？ 在登入時直接取得資訊？
      - 支援 API 列表(序號)或是軟體的 capability，登入時告知.(影響相容性)
- 2023/06/20
  - APP 多裝置綁定 on first time setup.(Verify)
- 2023/06/16

  - cbtTopology vlan clients.(Verify)
  - cbtTopology controller by macAddr.(Open)
  - portal.cybertan.tw(原始 portal host)
  - cybertan.portal.com(work around)

- 2023/06/13
  - 前後端開發文件維護討論.(Done)
  - Controller Issues
    - uplink tx/rx packet，activity 前端顯示為 0.(Done，Fixed)
    - EY006-89 [App-iOS/ App-Android]2.4G wireless clients status is not consistent.(Done，Not a bug)
- 2023/06/09
  - Controller 帳號多裝置管理
    - APP 首次安裝
      - 需增加使用雲端帳號登入與綁定的機制.
      - 建立帳號時會得知有無遠端綁定.
      - Special Case 1: 建立的新帳號在遠端已經有組相同帳號，此時本地會建立一組相同帳號？
      - Special Case 2: 使用遠端帳號登入與綁定若密碼錯誤，此時帳號會建立為本地帳號？(建議回失敗，因為用戶已經明確想用雲端帳號綁定本地設備)
    - 一般登入頁面
      - 遠端登入時有多台裝置時以列表方式呈現.
        - 相關配套包括用戶能夠設定裝置名稱來區隔列表裝置.
      - 登入預設是遠端優先，用戶可選擇本地優先.(Ｔ公司有一組本地帳號可使用.)
        - 當用戶弄不清他本地是哪台裝置時.
        - 本地設備被解綁但還想登入時.
- 2023/05/31
  - Topology 頁面對於 node 異常時的處理方式.
    - 找不到上層 node
      - 不顯示.(目前 API 不會回 offline 的裝置)(prefer)
        - Web hang on loop is only safe on first 4 layer.(列 Issue)
        -
        - 在 Device 列表那邊以 section 來區分 offline/無 uplink 的節點.(?)
      - 另外顯示.(需討論呈現方式)
        - APP 使用不同的圖示代表，點選之後顯示列表彈窗.(eg. 加號代表 adapt，驚嘆號代表異常圖示之間分類排序)
        - Web 使用不同列分別代表 Adapt 與異常節點，考慮點選擴展或單行顯示.
  - 新 type node(eg.switch/camera...)的擴充彈性規劃. (跟 APP 或 Web 比較有關)
    - 前端使用彈性高(Map)的方式來收 topology response，對於 Node 共用的欄位定義為 must have.(prefer)
    - 是否要把共用資料提出來，非共用結構另外放一起.(此方式 Controller 須改 code.)
  - Switch Document maintentance.(Open)
  - topology 的 poE 統計資料格式(列討論 issue.)
    - 已列入 Backlog.
  - topology client by macAddr.(Verify)
  - topology 架構優化.(open)
  - eap disconnect detect.(需進一步討論)
  - cbtTopology/switch change request
    - clientList/downlinkList 保留.(Done)
    - clientList/downlinkList 補充 portNumber.(Verify)
    - downlinkList 以前端共用框架結構為主.(Done)
    - uplink 補充 name.(Verify)
- 2023/05/29
  - topology 的 poE 統計資料格式(列討論 issue.)
  - topology eap/client by macAddr.(Verify)
  - topology 架構優化.(open)
  - eap disconnect detect.(需進一步討論)
  - cbtTopology/switch change request
    - clientList/downlinkList 保留.(Done)
    - clientList/downlinkList 補充 portNumber.(Verify)
    - downlinkList 以前端共用框架結構為主.(Done)
    - uplink 補充 name.(Verify)
  - RC5 - RD 自測
    - EY006-37: [EAP_Controller]Topology Client information issue (Done)
    - EY006-72: [EAP_Controller] Wifi Insights show incorrect vender info (Done)
- 2023/05/26
  - topology 的 poE 統計資料格式(列討論 issue.)
  - topology eap/client by macAddr.(open)
  - topology 架構優化.(open)
  - eap disconnect detect.(需進一步討論)
- 2023/05/25
  - topology 的 poE 統計資料格式
    - Spec 定義為 string
    - Table 定義為 int
    - 建議都改為浮點數? 須考慮浮點數 0 的處理，以及無資料的處理.
      - 列討論 issue.
- 2023/05/23

  - Partial Switch Issues
    - poeInfo 位置有誤.
      - Chris 修正.
    - uplinkPortNumber 有缺.
      - 改使用 kelly 提供的 db.
    - uplinkMacAddr 有缺.
      - 改使用 kelly 提供的 db.
    - Kelly 反應 Issue
      - temperature 有缺.
      - cpu/memory 統計資料有缺.

- 2023/05/18
  - 統計資訊相關優化.(ps. 低於某個量級，應該量級單位 eg. MB -> KB，或精確到小數點後三位.)(Open)
  - switch by serial 考慮改為 mac address.(Open)
  - Controller 斷線偵測的時間修改.(EY006-71).(Open)
    - Removing 1 old connections.
    - number of seconds the AP should wait for a connection
    - "Exception({}): Text:{} Payload:{} Session:{}"
- 2023/05/17
  - 統計資訊相關優化.(ps. 低於某個量級，應該量級單位 eg. MB -> KB，或精確到小數點後三位.)(Open)
  - switch by serial 考慮改為 mac address.(Open)
  - Controller 斷線偵測的時間修改.(Open)
- 2023/05/05
  - 討論 1 對多的帳號管控流程.
  - 實作 Topology/switch IPv4 address.
  - Survey UOI service for manufacturer field.
  - Survey client os information solution.(nmap lib)
  -
- 2023/04/27
  - 調整 API topoloty/switch response 資料結構，目的讓前端處理結構統一.(done)
  - 調整 poeInfo 位置到 cbtCommon.(done)
  - uplink 沒有時需補 netInfo 空物件.
  - Devices->EAP 列表若需要顯示 Switch 就要有更 common 的稱呼.
- 2023/04/26
  - Switch
    - switch model/name 使用哪個欄位 Manufacturere or DeviceType?
    - PortNumber 目前為 int，若有可能為 0，則建議改用 string.
    - 欄位 null 的原則，是不存在還是使用 default 值？(uplink 為例)
    - cdtGetStatisticsLatestData(dev.SerialNumber，Stats)應該只會取道一筆資料？
    - 錯誤發生時的 return 方式，是否有範例？
    - 前端 API portInfo 改成 portList
- 2023/04/25
  - 驗證 partial switch tables 是否有正常建立
    - docker tag 825a32f597ec gitlab.cybertan.com.tw:5050/9ga300a8/eap/cybertan_eap_trunk/cbtmain:v1.0.2
  -
- 2023/04/24
  _ cbtmain.tar 編好之後如何在 PC-Linux 上執行.
  _ downlink 的 port 如何從底層統計資料取得？ \* download_source_code()
  {
  token="REMOVED_FOR_SECURITY"
  GIT_SSL_NO_VERIFY=1 git clone https://oauth2:${token}@gitlab.cybertan.com.tw:20443/9GA300A8/cloud/backend/ucentralgw.git --branch ${owgw_version} gw27
	GIT_SSL_NO_VERIFY=1 git clone https://oauth2:${token}@gitlab.cybertan.com.tw:20443/9GA300A8/cloud/backend/wlan-cloud-ucentralsec.git --branch ${owsec_version} sec27
	GIT_SSL_NO_VERIFY=1 git clone https://oauth2:${token}@gitlab.cybertan.com.tw:20443/9GA300A8/cloud/frontend-base.git --branch ${frontend_version} frontend-base
  }

- 2023/04/21
  - GIT_SSL_NO_VERIFY=1 git clone https://oauth2:"REMOVED_FOR_SECURITY"@gitlab.cybertan.com.tw:20443/9GA300A8/eap/docker_related/service_dockerfile.git
- 2023/04/20
  - 建立 par config table 表.
    - MySQL
      - ParInterfaces
        - CREATE TABLE IF NOT EXISTS ParInterfaces (id INTEGER PRIMARY KEY AUTO_INCREMENT，serialNumber VARCHAR(30) NOT NULL，name VARCHAR(32) NOT NULL，role VARCHAR(10) NOT NULL，vlan JSON NOT NULL，ipv4 JSON NOT NULL，ipv6 JSON NOT NULL，erasable INTEGER NOT NULL，UNIQUE (serialNumber，name，role)，INDEX ParInterfacesIndex (serialNumber ASC，name ASC，role ASC));
        - insert into ParInterfaces(serialNumber，name，role，vlan，ipv4，ipv6，erasable) values("1234"，"vlan1"，"downstream"，'{"vlan1":1}'，'{"ipv4":"192.168.1.1"}'，'{"ipv6":"dfjaskf"}'，1);
        - insert into ParInterfaces(serialNumber，name，role，vlan，ipv4，ipv6，erasable) values("1234"，"vlan2"，"downstream"，'{"vlan1":1}'，'{"ipv4":"192.168.1.1"}'，'{"ipv6":"dfjaskf"}'，0);
        - select \* from ParInterfaces;
      - ParSwitchPorts
        - CREATE TABLE IF NOT EXISTS ParSwitchPorts (id INTEGER PRIMARY KEY AUTO_INCREMENT，serialNumber VARCHAR(30) NOT NULL，portNumber INTEGER NOT NULL，portName VARCHAR(32) NOT NULL，vlanProfileName VARCHAR(32) NOT NULL，poE VARCHAR(10) NOT NULL，opMode VARCHAR(10) NOT NULL，mirrorPort VARCHAR(100) NOT NULL，linkRate VARCHAR(10) NOT NULL，flowCtl BOOLEAN NOT NULL，portIsolation BOOLEAN NOT NULL，jumboFrame BOOLEAN NOT NULL，UNIQUE (serialNumber，portNumber)，INDEX ParSwitchPortsIndex (serialNumber ASC，portNumber ASC));
        - insert into ParSwitchPorts(serialNumber，portNumber，portName，vlanProfileName，poE，opMode，mirrorPort，linkRate，flowCtl，portIsolation，jumboFrame) values("1234"，1，"port1"，"vlan1"，"PoE+"，"op1"，"1，2，4"，"10M"，true，false，true);
        - insert into ParSwitchPorts(serialNumber，portNumber，portName，vlanProfileName，poE，opMode，mirrorPort，linkRate，flowCtl，portIsolation，jumboFrame) values("1234"，3，"port1"，"vlan1"，"PoE+"，"op1"，"1，2，4"，"10M"，1，1，1);
        - select \* from ParSwitchPorts;
    - SQLite
      - CREATE TABLE IF NOT EXISTS ParInterfaces (id INTEGER PRIMARY KEY AUTOINCREMENT，serialNumber VARCHAR(30) NOT NULL，name VARCHAR(32) NOT NULL，role VARCHAR(10) NOT NULL，vlan JSON NOT NULL，ipv4 JSON NOT NULL，ipv6 JSON NOT NULL，erasable INTEGER NOT NULL，UNIQUE (serialNumber，name，role));
      - insert into ParInterfaces(serialNumber，name，role，vlan，ipv4，ipv6，erasable) values("1234"，"vlan1"，"downstream"，'{"vlan1":1}'，'{"ipv4":"192.168.1.1"}'，'{"ipv6":"dfjaskf"}'，1);
      - insert into ParInterfaces(serialNumber，name，role，vlan，ipv4，ipv6，erasable) values("1234"，"vlan2"，"downstream"，'{"vlan1":1}'，'{"ipv4":"192.168.1.1"}'，'{"ipv6":"dfjaskf"}'，0);
      - SELECT \* FROM ParInterfaces;
  - Cloud Portal
    - Default account 在建立帳號後的處理機制.
    - Cloud portal 的安全性考量.
    - 忘記密碼的可能處理方式.
  -
- 2023/04/18
  - EAP Restart/FactoryReset/Upgrade/Remove 處理原則如下：(Done)
    Local Connect: 現階段除了 Remove(ps.需等底層提供統一的 API)其他操作都可以做，但要額外提醒可能會斷線的風險.
    Cloud Connect: 都可以做.
    ps. Cloud Connect 判斷依據 BaseApiClient.isCloud
  - APP follow Web 處理 IP mask 原則.(Done)
  - First Setup: (Done)
    - Add QRCode Format.
    - Fix BT Scan refresh bug.
  - Todo
    - 版號(v0.0.2).
    - APP image.(AdHoc)
      - 改用 AdHoc 的憑證.
      - debug log
    - Release note.
    - EAP 操作驗證.
    - 電子申請流程.
- 2023/04/17
  _ APP 第二輪自測文件(Open)
  _ 文件撰寫
  _ APP 自測問題反應
  _ Firewall 儲存時會出現 something.(有待複製)
  _ WiFi Insights: table select Item 選擇之後，畫面 scroll 之後會被 reset 掉.(已複製)
  _ 進入 WiFi Settings 頁面出現 error.(已複製)
  _ 首次安裝驗證相關
  _ 新格式
  "DM":"00:3E:CB:16:98:A1"，
  "VN":"CT"，
  "SN":"1234567890ABCDEF"，
  "MN":"EC420-G1"，
  "HW":"02"，
  "SS":"CBT*WIFI"，
  "WP":"1234567890"，
  "BT":" CyberTAN*"，
  "CU":"admin@cybertan.com.tw"，
  "CP":"Cbt@12345"

"DM":"00:3E:CB:16:98:A1"，
"VN":"CT"，
"SN":"1234567890ABCDEF"，
"MN":"EC420-G1"，
"HW":"02"，
"SS":"CBT*WIFI"，
"WP":"1234567890"，
"BT":" CyberTAN*"，
"CU":"admin@cybertan.com.tw"，
"CP":"Cbt@12345"

- 2023/04/14
  - BT 產測工具 survey(Open)
    - python3 to exe.(SOP Ready)
  - APP 第二輪自測文件(Open)
    - 文件撰寫
    - APP 自測問題反應
      - Static Route: IPv4 進入新增畫面出現 error.(已複製)
      - Firewall 名稱欄位出現不預期的防呆錯誤訊息.(已複製)
      - Firewall 名稱衝突時錯誤訊息可以更明確.(已複製)
      - Firewall 儲存時會出現 something.(有待複製)
      - (Firewall/StaticRoute/PortForward)列表 Enable/Disable 標示與 Web 不一致.(已複製)
      - WiFi Insights: table select Item 選擇之後，畫面 scroll 之後會被 reset 掉.(已複製)
      - Devices:EAP 列表的 client 數量與 detail 內的個數不一致.(已複製)
      - Firewall/Static Route 的總開關，一旦關閉就先隱藏以下的子 item.(Jennie 建議)
      - Jessie 有要求 IPv6 的功能要先隱藏起來，影響範圍包括:(需修改)
        - Controller -> Overview -> IPv6
        - IPv6 Static Route.
        - IPv6 Firewall.
      - 進入 WiFi Settings 頁面出現 error.(已複製)
      - Token 過期的操作失敗應該至少要有個 Toast 提示.(優化)
- 2023/04/12
  - BT 產測工具 survey(Open)
    - python3 to exe.
  - SQA 檢討會議(Done)
  - APP 第二輪自測文件(Open)
- 2023/04/10
  - 雲端帳號(1 對多)issues
    - Cloud 主動解綁的動作若 Controller 回應解綁失敗的 Cloud 如何處置？另外如果解綁成功，則本地登入的帳號是否還存在？
      - 處理機制類似 Controller 離線，無論 Controller 是否成功都不予理會，把 log 存下來之後強制移除該綁定關係.
      - Cloud 應該會解除綁定關係，Controller 則切成 local 帳戶，但不移除帳戶.
    - 密碼錯誤強制切成 local 帳號的使用情境？
      - 密碼修改時的離線裝置處理流程，是否會跟上一個問題的使用情境衝突？
    - 某用戶 Account U 有管理一台 Controller A 的雲端帳號，該 Controller A 在離線狀況下進行 factory default，然後創建新帳號進行綁定，則原始的雲端帳號會如何處理?
      - 使用舊帳號登入並成功綁定 Controller A，即使該綁定關係已經存在.
      - 建立新帳號並成功綁定 controller A，且移除舊帳號的綁定關係.
      - 忘記舊帳號密碼，但又想使用舊帳號進行綁定，是否有方法？
        - 目前在無 email 驗證下無法解決此問題.
        - 另一種 workaround 的做法是使用新帳號綁定，間接讓舊帳號被 cloud portal 移除，然後再重新建立舊帳號來綁定.
- 2023/04/07
  - APP Partial Config 前期規劃.(Done)
  - Partial Config
    - 後湍開發.(Ongoing)
    - APP 前期規劃.(Done)
  - FWA routing table 討論.(Done)
  - Android/iOS 用戶賬資料新規定討論.(Done)
- 2023/04/06
  - 藍芽首次安裝 DS 文件撰寫.(Done)
  - APP Partial Config 規劃.(open)
  - QRCode format 格式修改與討論.
    - 格式調整為 Json 格式的影響評估.(Done)
    - 資料增加的風險評估.(待確認)
    - 部分資源相容性評估.(待確認)
  - APP 開發軟體工具整理.(Done)
- 2023/03/31
  - iptables -F
  - screen /dev/tty.usbserial-FT2LZBUE 115200
  - sudo launchctl load -F /System/Library/LaunchDaemons/tftp.plist
  - sudo launchctl start com.apple.tftpd
- 2023/03/29
  - 第二、三階段相關細節規劃
    - 首次安裝溝通方式
    - 帳號綁定(Done)
    - Partial config
- 2023/03/28
  - 第二、三階段相關細節規劃
    - 首次安裝
    - 帳號綁定
  - 雲端帳號(1 對多)issues (review)
  - VLan Partial Config 規劃會議.
    - Partial config 細節討論.(final)
    - Controller 軟體升級.(DB migration)
    - Configuration backup / restore.
- 2023/03/27
  - 雲端帳號(1 對多)issues (review)
  - VLan Partial Config 規劃會議.
    - Partial config 細節討論.(final)
    - Controller 軟體升級.(DB migration)
    - Configuration backup / restore.
- 2023/03/24
  - 雲端帳號(1 對多)issues (review)
  - EAP/Controller 相關操作:
    - Remove EAP 先不做，等Ｃ ontroller 有提供 remove API 再做.
    - 等待原則 follow Web，EAP 只有 upgrade 需要等待，Controller 則(reboot/reset/upgrade)都需要等待.
- 2023/03/23
  - 雲端帳號(1 對多)issues
    - 某用戶有管理一台設備的雲端帳號，該設備在離線狀況下進行 factory default，然後創建新帳號進行綁定，則原始的雲端帳號會如何處理.
      (ps.合理的做法應該是刪除原始帳號，並讓新帳號可以正常綁定，雲端的檢查流程會如何進行？又如果帳號相同時會如何進行？)
  - VLan Partial Config 規劃會議.
    - default config 跟 current config 的關係.(不同一個)
    - Switch config 中的 vlan profile 只存 partial 欄位，後續詳細設定如何 apply 到每台 switch 的 port 中.
    - partial config table 和 current cofig 之間是否會有同步問題.
      - 目前評估後覺得不影響.(ps.其他設定使用的 config 仍會保有 partial 內容，因為 vlan 屬於 openwifi 的現有結構)
    - 最終 current config 將會被 table 表格全部取代?
      - 現有 current config 資料與 table 表格資料重複的問題，後續取代之後就會解決.
      - 需考量 openwifi 的 config 的其他延伸功能影響性.
        - 原始 config 預寫與排程 apply 功能.
      - 需考量未來的功能 scope.(ps. )
- 2023/03/22
  - VLan Partial Config 規劃會議.
    - API 簡化資料結構.(ps. 已經開發好的畫面使用原始結構，全新的畫面考慮使用更直覺的 json 結構．)
    - Talbe schema 的結構細拆.(部分細拆)
- 2023/03/21
  - VLan Partial Config 規劃調整.(Arisa)
    - API 簡化資料結構.(ps. 已經開發好的畫面使用原始結構，新的畫面考慮使用更直覺的 json 結構．)
    - Talbe schema 的結構細拆.
- 2023/03/20
  - VLan Config 規劃調整.(KK)
    - API 新增兩個 for switch port 列表與狀態.
    - Partial config 重點在設定.
- 2023/03/17
  - VLan Config
    - switch port management
      - PUT
        - cbtDefaultConfigurationAndApply/{modelName}
        - Switch Model Name: cybertan_skf424-c1
        - Q: 應該是 deviceId 比較合理？因為 port 的設定是每台 switch 都不一樣？
      - portId 應該不能設定?
        - 對，只有 portName 可以設定.
- 2023/03/16
  - 整理第二階段 APP ES.
  - FirstSetup
    - 流程以 QRCode 為主還是 side survey 為主?
    - 設備上有無識別碼可確認 Conroller?
  - VLan Config
    - vlanId 的名稱不一致.
      - 已修正.
    - portInfo 與 profile 個關聯方式，建議改用 vlanId.
      - 經討論目前維持使用可變動的 name.
- 2023/03/15
  _ Partial Config
  _ default config
              _ 未來會有 3 種(Controller，EAP，Switch)，現行 2 種(Controller，EAP)?
              - Ans: Yes.
              _ 文件和報告的 vlan profile (networkProfile) 位置不一致.
              - Ans: 文件內容有誤.
          _ EAP 是否有 vlan config，是否可以設定 vlan?(ps. 報告的內容表格有提到 EAP 具備 vlan)
          - Ans: 報告內容表格有誤 EAP 沒有 vlan，已修正，另外 Controller 與 Switch 指的 vlan 意義不一樣(ps. VLAN profile 在 controller，在 switch 則可根據不同 port 選用 profile.).
          _ Controller 目前應該是存 vlan profile?
              - Ans: 存在於 openwifi 結構中的 interfaces，會有一組預設 vlan profile(ps. default)，不可刪除.
          _ Switch 所有的 vlan profile 都存在 3'rd party? 內容應該含網域的範圍...
              - Ans: Switch 都會有 vlan profile 只存必要資訊.(name，enable)
          _ 每個 switch 裡 3'rd party 的 vlan profile 一樣，但 port Info 不同？
          - Ans: Yes，每個 switch 會有所有的 vlan profile(只存部分欄位)，portInfo 都不同.
          _ switch 的 portInfo 與 vlan profile 的關聯值？(目前看起來是 vlan 名稱，但名稱是可變更的)
          - Ans:vlan
          _ vlan ID 跟順序有關嗎？不建議用順序當成 ID，但 ID 的 assign 機制為何，來確保唯一值?
          - vlan ID 是由用戶設置的數字跟順序無關且不可重複.
  _ scp 範例
  _ scp -r ./service_dockerfile Containerbuild@192.168.180.119:/home/Containerbuild/eric_build
- 2023/03/14

  - Others
    - MIS 需求訪談.
    - Security
      - BT AES key by using device serial reverise.
    - Partial Config 開發環境
      - KK
    - BLE close issue after first login ready.
  - Partial Config
    - EAP 是否有 vlan config，是否可以設定 vlan?
    - Controller 目前應該是存 vlan profile?
    - 所有的 vlan profile 都存在 3'rd party? 內容應該含網域的範圍...
    - 每個 switch 裡 3'rd party 的 vlan profile 一樣，但 port Info 不同？
    - vlan ID 跟順序有關嗎？不建議用順序當成 ID，但 ID 的 assign 機制為何，來確保唯一值?

- 2023/03/10
  - 專案開發原則
    - 新開發的 code 可以使用新的或更有優勢的寫法，舊有的 code 可先維持現狀，後續再視情況修改且應該搭配 CI/CD 為佳.
    - 新技術或寫法的導入，建議先提出優缺點評估比較，讓同仁確認後比較好.
- 2023/03/09
  - Review.
    - Reconnect
      - 增加可自動切換 remote/local 機制.
    - Segment
      - 部分頁面漏改.
- 2023/03/08
  - ble 藍芽關閉機制.
    - 設備端.(Dax)
    - APP 端.(Eric)
  - first setup issues
    - qrcode scan from image on iOS fail.
    - ble disconnect is not clear.
    - New accout cannot be default account.
  - others
    - When build_runner file changed need to rebuild it by following command:
      - flutter pub run build_runner build --delete-conflicting-outputs
- 2023/03/07
  - git commands
    - git remote add cbt_source git@gitlab.cybertan.com.tw:9GA300A8/app/third-party/mobile_scanner.git
    - git log
    - git push cbt_source master
    - git add -A
    - git commit -m "[Fix] Error of type convertion."
    - git push cbt_source master
    - others
      - git console 圖形工具
        - git config --global alias.lg "log --color --graph --all --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --"
      - git rebase
      - git branch
      - git status
      - git checkout -b milestone ec94f6d
    - eg. mobile_scanner 退版到之前 mile stone 版本，並推到另一個 remote 端的 git repostory.
      - 細節如下
        1025 cd flutter/git/mobile_scanner
        1026 git lg
        1027 git checkout -b milestone ec94f6d
        1028 git lg
        1029 git push cbt_source milestone
        1030 git lg
        1031 git cherry-pick 372748f
        1032 git lg
        1033 git push cbt_source
        1034 git lg
- 2023/03/01
  - IoT 相關報告.(Done)
  - EAP Features
    - ## First Time Setup
  - EAP Bugs.
    - RD self test.
    - QA test.
  - FWA
    - Dashboard.
    - Tools
      - Ping
      - Annetane.
  - Others
    - 請假.
      - 請補修在家上班.
- 2023/02/22
  _ StatefulHookConsumerWidget
  _ Stateful + Hook?
  _ Pass variables between Pages.
  _ 除了層層傳遞，簡單易用的方法？ \* 共用 Provider？
  舊工作日誌

##

05/23

- Survey AWS ELB，ALB，NLB for ALBHealthCheckServer subSystem.(Done)
- Write technical document:
  - Refine ucentral-security acrhitecture，include REST_action_links，RESTAPI_signup_handler，ALBHealthCheckServer，TotpCache subsystem.(Done)
  - Write "Create New User / Regist" process flow.(Done)
- Patent: Checked the patent application document.(Ongoing)

05/24

- Survey TOTP (Time-Base One Time Password).(Ongoing)
- Write technical document:
  - Write "Login" process flow.(Done)
- Patent: Checked and sign the patent application document.(Done)

05/25

- Discussion the FWA development scope with Johnny.(Done)
- Weekly Metting of EAP.(Done)
  - Report the owsec architecture.
  - Report the Register process flow.
  - Report the Login process flow.
- Survey the UBNT - AirView production features.(Done)

05/26

- Report the AirView production features and confirm
  the FWA development items with Johnny/Weinij/Jesse.(Done)
- Survey ucentral-schema source code.(Ongoing)
- Survey UCode basic syntax.(Ongoing)

05/27

- Survey UCode basic syntax.(Ongoing)
- Survey ucentral-provision souce code.(Ongoing)
- Compare and Update FWA items with Neil draft.(Done)

05/30

- Compare and Update FWA items with Neil and Johnny version.(Done)
- Survey UCode basic syntax.(Ongoing)
- Write technical report of ucentral-provision souce code.(Ongoing)

05/31

- Write technical report of ucentral-provision souce code.(Ongoing)
- Report and discussion the FWA items priority with Barry and Johnny and Jesse.(Done)

06/01

- Survey UCode basic syntax.()

06/08

- Dexy: Item 與 Barry 的有出入，

06/13

- Norman: 討論 OpenWifi Device Disconnection 流程.
- Johnny: 討論 FWA 後續開發順序.(QSDK6.1 轉為 QSDK5.3 並作相關的 porting 與驗證)
- 整理 OpenWiFi Technical Report of Controller/AP Source Code Survey 並分享給相關同仁.

06/14

- MDR/PDR 文件項目了解與相關技術 survey.(SCADA，NAT，UPNP，NAT Travel Protocol...etc)
- MRD: APP 佈點協助細節討論.
  - Google MAP API 內容與收費狀況了解.(Google ElevationService)
  - QSDK Driver support query 天線訊號頻率調整？
- SCADA (Supervisory Control and Data Acquisition) System 的中文譯名為「監控及數據收集系統」。 顧名思義，SCADA 系統的作用是幫助我們監控主要廠房或其他偏遠廠房的運作的，並收集各種監測儀器的數據，然後將這些數據發送到中央電腦作處理。
- NAT: Network Address Translation.
- UPNP: skype，gaming 可透過 NAT Travel 解決 port mapping 的相關問題，security issues?
- NAT Travel protocol: NAT 的行為是非標準化的，UDP 路由驗證和 STUN。除此之外，還有 TURN、ICE、ALG，以及 SBC。

06/15

- PDR 文件項目了解與相關技術 survey.(SCADA，NAT，UPNP，NAT Travel Protocol...etc)
- PDR 項目整理: AP/Station/Router/Bridge Mode.

06/16

- FWA - Google MAP 技術 survey: Google Elevation Service 了解與申請(需要信用卡資料，300 美元免費額度有時間限制，建議與 Android 人員同步測試).(Ongoing)
- EAP/FWA - Websocket Notify 技術 survey: Golang server.(Done)

06/17

- FWA - 討論 ucentral AP daemons.(ucentral，wifi，state，schema，event)
- EAP - Websocket Notify 技術 survey: Client 端 APP.(Ongoing)

06/20

- FWA - Upgrade code for spf5.3(QSDK).(ps. orignal is spf6.1)
- EAP/FWA - Websocket Notify 技術 survey: Client 端 APP. - iOS APP Websocket development. - SwiftUI New syntax: @StateObject，@State，@Published，@ObservedObject，swiftUI. - Websocket lib APIs. - Golang Websocket server development. - Timer vs Ticker. - Websocket lib APIs.
  06/21
- FWA/EAP - iOS/Golang Websocket security.(Ongoing)
- FWA - AP/Station function list compare and discussion.(Ongoing)
  - NanoBeam(AP Mode)(https://192.168.63.105/) A:ubnt，P:DP3KafGt.
  - LiteBeam(Station Mode)(https://192.168.63.106/) A:ubnt，P:iUv6IraF.

06/22

- FWA/EAP - iOS/Golang Websocket security.(Ongoing)
- FWA/EAP - Websocket event notify on Controller.(Ongoing)
  - Discussion and write technical document.(Done)
  - Survey libwebsockets.

06/23

- FWA - Compre the AP/Station，Bridge/Router，PTP/Non-PTP Mode items on T company. (Done)
- FWA/EAP - Websocket event notify on Controller.(Ongoing)
  - Discussion source code and architecture with Jack.(Done)
  - Survey libwebsockets - mirror source.(Ongoing)

06/24

- FWA - Verify Generic Router features on MR9000 (spf-5.3.1). (Ongoing)
- FWA/EAP - Websocket event notify on Controller.(Ongoing)
  - Survey libwebsockets sample for event notify to multiple clients.(Done)
    - ~/minimal-examples/ws-server/minimal-ws-broker is most matched our requirement.

06/27

- FWA - Verify Generic Router features on MR9000 (spf-5.3.1). (Done)
- FWA/EAP - Websocket event notify on Controller.
  - Refine the CBT event notify architecture on OpenWifi Controller.
  - Write tech. document of CBT event notify architecture.(Done)

06/28

- FWA : Verify Generic Router features on MR9000 (spf-5.3.1)(Part 2). (Ongoing)
  - Verify pending features and double check fail cases.
- FWA/EAP : Sruvey new architecture(SwiftUI) of swift.(Ongoing)

06/29

- EAP 架構討論.(Done)
  - Survey Docker/Podman technology.
- FWA/EAP: Websocket security survey and implement.(Ongoing)

06/30

- FWA/EAP: Sruvey new architecture(SwiftUI) of swift.(Ongoing)
- EAP: Event notify of websocket on Controller refine.(Jack)
- FWA/EAP: Websocket security survey and implement.
  - Server(Golang).(Done)
  - iOS APP.(Onging)

07/01

- FWA/EAP: Sruvey new architecture(SwiftUI) of swift.(Ongoing)
- EAP: Event notify of websocket on Controller refine.(Jack)
- FWA/EAP: Websocket security survey and implement.
  - iOS APP.(Done)

07/04

- FWA/EAP: Sruvey new architecture(SwiftUI) of swift.(Ongoing)
- EAP: Event notify of websocket on Controller refine.(Jack)
- FWA/EAP: Websocket security survey and implement.
  - Fixed the security 2 way issues.(Done)

07/05

- EAP: Event notify of websocket on Controller refine.(Jack / Eric)
  - Install and setup environment.(Done)
  - Implement APP for demo.(Ongoing)
- All Hands Meeting.
- Weekly Report.

07/06

- EAP: OpenWifi weekly meeting.
- EAP: Event notify of websocket on Controller refine.(Jack / Eric)
  - Implement APP for demo.
  - Survey libwebsockets on Swift.

07/07

- FWA 測試 bug: (John，Eric)
  - LAN Network Settings.
- EAP: Event notify of websocket on Controller refine.(Jack/Eric)
  - survey 並分析 libwebsockets 的封包.(ps. 需移除加密改用 ws.)
  - Implement APP for demo.(Done)

07/08

- FWA 測試 bug: (John，Eric)
  - LAN Network Settings.(Ongoing)
  - WAN Network Info.(Ongoing)
- EAP: AP issues 討論.(Kelly，Eric)
  - Fork() maybe cause flag conflict.
  - File as flag between UCode files is inefficient.
    (ps. maybe use vram flag.)

07/13

- UISP Mobile (UNMS) UI survey.

07/14

- Setup UISP 產品線設備.
- 處理 IT 設備與資產轉移.

07/18

- Write technical report of UISP on T company.(Eric)(Ongoing)
- Discussion the report.(Eric / Jennie)(Done)
- Survey Flutter.
  - Setup the development.
  - Create and install simple iOS APP on iphone.

07/19

- Write technical report of UISP on T company.(Eric)(Ongoing)
- Survey Flutter.
  - Setup android and flutter plugin.
  - Run android app on Android/iPhone/MacOS/Chrome Emulator.
  - Test and Study more samples.

07/20

- Write technical report of UISP on T company.(Eric)(Ongoing)
- Discuss and report the UISP features.

07/21

- Survey Flutter.
  - Survey APP state management framework.(Provider)
  - Write sample of Provider framework.
  - MaterialApp / DefaultTabController / BottomNavigationBar.

07/22

- Survey Flutter
  - Riverpod
  - Flutter Hooks
  - Websocket lib: web_socket_channel
- FWA SW PRD Review.
- UISP: Support Device Management by Cloud Account?

07/26

- Survey flutter
- UISP APP UI/Flow survey.
  - wss://cbt-18016.uisp.com:443+0FZwzBq4k9aVETUAECTxalqb2nows7E3CWNAB39nZ7iAARIB+allowSelfSignedCertificate
- FWA 時程規劃 - 大項目.
- FWA 時程規劃 - 細項.
- UI 設計與 APP 開發相關工具 sync.

07/27

- EAP
  - Onboarding flow of T company APP.(by WiFi)
  - Configure usage for APP features.
  - Management API recommend : 建議與 controller 的 API 一致，若有差異則考慮加上一層轉換.
  - Event notify by websocket.
- FWA
  - First time setup of T company APP.(https://app.diagrams.net/#G1yFcpfEri6Z4PB079ZNhcy0aSvg3HpfEa)
  - UISP key usage.(https://help.tw.ui.com/articles/360000336262/)
  - Backhaul(https://upsangel.com/router-2/mesh-wifi-system-3-advantages/)
- Others
  - Generic APP re-develope by flutter language.
  - Auto Detect 攔截封包.
  - DPP.(device provision protocol)
  - 評估 ReactJS & React Native on APP.
    - ReactJS 主要是開發網頁前端的 UI 與相關操作，若要應用在 APP 平台則需要使用 React Native 這套基於 ReactJS 的 JavaScript 語法，除非整個 APP 只是單純的透過 HTTP/Websocket 來跟外界互動，這樣也許可以直接使用 ReactJS 搭配 RWD 的以 webview 的方式呈現在 APP 中，但如此一來就使用 Browser 會更直接，以我們目前的 APP 來說，會使用到 Camera 來掃 QR Code，另外使用 Wifi 相關操作的頻率也很高，所以比較不適合直接使用 ReactJS.
    - 由於我對於兩邊 RN/Flutter 都沒有開發過，所以比較難以過去的開發經驗去評估兩者的優劣，單就要學習成本考量可能直接使用 Flutter 會比較佳，我目前習慣使用 iOS swift 開發 APP，預計進來的 2 位 Android 工程師目前擅長的語言是 Flutter，對我來說 React Native 與 Flutter 的學習成本接近，但另外 2 位同事可能要改學 React Native.
    - 網路上針對 2 者的優劣比較也是以 Flutter 勝出，舉幾個比較值得提出來的點，eg. 系統更新時 Flutter 的 APP 比較穩定，原因是 Flutter 使用自己的 UI component，而 RN 則橋接使用原生的 Component.

07/28

- EAP
  - Survey Unifi Netowrk devices setup.
- FWA

  - Survey UISP APP - Tools and 整理發信.

- UniFi Network
  可幫助您設置和管理 UniFi Network 設備，並享受對網絡流量、安全性和無線性能的全面監督和控制。 無論您是管理單個網絡還是遠程管理多個站點，此應用程序還允許您：

  - 輕鬆設置和採用新的 UniFi 交換機和接入點。
  - 創建和定制有線和 WiFi 網絡。
  - 實時評估網絡流量和客戶端利用率。
  - 阻止不需要的網絡設備、設置速度限制並檢查連接信息。
  - 查看可幫助您立即對網絡問題進行分類並識別引發設備的警報。
  - 使用應用內增強現實快速掃描機架式 UniFi 設備，區分連接的設備並解決性能問題。

- AmpliFi
  AmpliFi 应用程序简化了连接和管理所有 Amplifi Wi-Fi 设备的过程，包括：Alien、HD、Gamer's Edition、Instant 和 Meshpoint 产品。

  - AmpliFi 应用程序提供了添加、监控、配置和升级网络的最简单方法。 您将能够：
    - 查看关键诊断信息，例如连接设备摘要、上传/下载活动、您的 IP 地址和实时网络速度数据。
    - 为来访的家人和朋友创建一个家庭访客网络，或通过 AmpliFi Teleport VPN 应用程序让他们远程访问您的本地网络。
    - 通过暂停连接设备的能力来控制家人的屏幕时间。

- AmpliFi Teleport
  AmpliFi Teleport 应用程序可让您随身携带家中的 digital comforts，无论您在多远的地方——所有这些都无需支付月费。 这个应用程序允许您： - 通过 AmpliFi Wi-Fi 应用程序快速生成访问代码，连接到您的家庭 AmpliFi 路由器。 - 与传统 VPN 相比，体验卓越的网络可靠性，因为您依赖自己的网络。 - 在公共网络上浏览时保持隐私和安心。
  请注意，使用 Teleport 应用程序需要 AmpliFi 路由器。
  Devices
- UDM-Pro
  - Network controller.
  - WiFi AI but No WiFi.
  - Switch + Gateway.
  - UniFi Protect with HD.
- USW-Enterprise-8-PoE
  - Switch.(支援 PoE 供電的第 3 層交換器，具備 8 個 2.5GbE PoE+ RJ45 乙太網路連接埠以及 2 個 10G SFP+ 光纖網路連接埠。)
- U6-Pro x 2
  - Access Point(AP) : U6 Pro 能夠透過 5 GHz (4x4 MIMO) 和 2.4 GHz (2x2 MIMO) 頻段達到高達 5.3 Gbps 的傳輸效能.
- UAP-AC-HD

  - AP: 4x4 MU-MIMO 技術的高效能 802.11ac Wave 2 Wi-Fi，適用於高密度和重要環境。

- 安裝方式.
  - No UDM-Pro
    - U6-Pro A factory reset and setup.
      - Web.
      - Install Netwrok(Controller) PC.
      - APP - BLE.
      - APP - WiFi?
    - U6-Pro B factory reset.
    - Add U6-Pro B by using Mesh?
    - Wireless Meshing.
      - 50% performance decrease? (https://help.ui.com/hc/en-us/articles/115002262328)
    - Support Remote Access?
    - UniFi U6 Mesh Access Point(https://www.youtube.com/watch?v=GySDfm0CVQc)
    - How to Controller?
  - UDM-Pro
    - Controller Setup.
      - Web.
      - APP - BLE.
    - U6-Pro A factory reset and setup.
      - Wire connect and setup by UDM-Pro.
    - 帳號管理.
- 安裝彙整

  - Cloud Console(Cloud Controller) / Physiscal Console(UDM-Pro)
  - Without UDM-Pro
    - By Web(Need to Install Controller in PC).
      - Find device by using ubiquiti data packet
    - By APP
      - By QRCode / Manual Input
      - By ubiquiti data packet
    - BLE is no used on U6-Pro? (management interface)
    - Stand alone Device is need to factory reset When add to the UDM-pro.
    - Stand alone Device can not management on cloud.
    - Ubiquiti Devices will broadcast ubiquiti data packet per 5 seconds.
  - UDM-Pro setup

    - Cloud Console(Network Controller)/ Phsyical Console(UDM-Pro)
    - Firstime setup by APP
      - Only Bluetooth.
        - Help to find and connect UDM-Pro.
        - Status/Introduce message alert.(eg. plug WAN slot...)
        - Setup WAN settings.
        - Setup account / password on unifi portal cloud account. (ps. offline version setup fail...)
      - Other Solution
        - Zero touch onboarding. (ps. WAN setting is need prepare on cloud Controller)
        - Cloud access.
    - Layer 3 Device Adoption

      - Layer 3 adoption is the process of adopting a UniFi device to a remote UniFi Network Application. This is only recommended for advanced users，or those adopting devices to the UniFi Cloud Console.
      - set device data(eg. name/MAC address/IP...) to Console.
      - set inform url (eg. http://ip-of-host:8080/inform) to device.

      - By UniFi Network Mobile App (Cloud Console only)
        - Refer to our UniFi Device LED Status guide to ensure the device is in a factory-default state.
        - Connect your mobile device to the same local network as your UniFi device.
        - Open your UniFi Network Mobile App and connect to your Cloud Console.
        - Your device should appear for adoption.
      - By SSH
        - Make sure your device is in a factory-default state. You can refer to our UniFi Device LED Status guide.
        - SSH into the device. You may refer to our guide on how to Login with SSH.
        - The UniFi device will now show up for adoption and can be treated as a standard L2 adoption.
      - DHCP Option 43
        - This option leverages your DHCP server to inform your UniFi device of the location of your remote Network Application host. Those with a UniFi Gateway can easily accomplish this by entering the IP address of the remote Network Application in Option 43 Application Host Address field located in the Network Settings.
        - For those using a third-party gateway or DHCP server，we recommend consulting your manufacturer’s documentation to learn more.
      - DNS
        - You'll need to configure your DNS server to resolve 'unifi' to your remote UniFi Network Application host.
        - There are two methods of specifying the Network Application host:
          - IP Address: http://ip-address:8080/inform
          - Fully Qualified Domain Name (FQDN): http://FQDN:8080/inform

- Conclusion
  根據觀察與測試 survey T 公司的 Unifi 產品，初步結論如下：
  - 沒有 WiFi 設備的產品(eg. UDM-Pro)對比到我們的是 Controller 設備，會建議使用藍芽跟 APP 溝通，主要在 UDM-Pro 透過藍芽與 APP 進行首次安裝時，過程非常順暢使用者體驗很好，另外是透過 WiFi 的方式溝通也是我們擅長且成熟的方式，但如此一來硬體成本會比較高，故建議使用藍芽來當作 Controller 的溝通媒介.
  - 另外關於 Access Point 的設備，基本上是透過 WiFi 與 APP 之間的溝通，另外觀察到 T 公司對於 Layer3 組網功能是針對進階用戶提供的，安裝流程不是很方便，加上考量客戶對於局端 Controller 跟 AP 之間跨網域組網的需求不高，故初期先支援 L2 的 Onboarding，等 Cloud Controller Ready 之後再考慮使用掃 QRCode 的方式來支援跨網域的 L3.

08/03/2022

- 跨平台語言評估
  - Web / APP 跨平台評估.
    - Facebook 作法參考.
      - 各平台的畫面架構基本一致.
      - .
    - T 公司作法參考.
      - 網頁與 APP 頁面架構差異大.
      - 網頁 portal 管理多個 Application，分別對應各 APP.(Network/Protect/Access)
    - OpenWifi 商業化的產品參考.
    - EAP/FWA 專案考量
      - OpenWifi 現有資源.(ReactJS/Luci)
      - Web 與 APP 的 UI 畫面架構的差異性.(是否對齊 T 公司產品？還是我們有自己的發展策略)
      - 跨平台的共用程度.
        - 語言共用.(RN/Flutter)
          - UI 元件共用.
          - 頁面共用.(需配合頁面架構設計)
        - 非 UI 邏輯共用.(RN/RectJS)
      - 人員相關經驗值.(Flutter/RectJS/Swift/Kotlin/Java)
      - 各跨平台語言的起源與優勢.(RN 從 Web 角度去跨平台，Flutter 從 APP 角度去跨平台)
  - Flutter / RN / RectJS(Rect.js)
    - APP 大小
    - 效能
    - 功能
    - Debug
    - 上架友善度

08/08/2022

- RESTful API
  - Survey RESTful API 相關原則.
- Security issues of product.
  - T 公司做法(AES Application Key)
  - OpenWifi 作法.
- Controller
  - OpenWifi schema/command change issues.
  - Event notify issues.(health/state 各有一隻 daemon 來傳送)
- API Architecture issues.
  - BLE.
  - 跟 Sam 討論並確認實作 cmd.uc/ucentral.uc/ubus 的調用方式.
- Multiple language evaluation
  - Report.
- PDR
  - 大原則: 平價奢華.
  - 比對手更貼近 Customer 需求，表格資訊更精準.

08/10/2022

- EAP - Conteoller API 目前支援 reasful，那 websocket 的必要性評估，device 要用哪一套？，最後做個總結與討論
- EAP - 關於 onboarding 考量由 App 或透過廣播封包的回覆來告知 controller 位置，取決於要採用的加入策略是用哪套流程
- EAP - ES 之前的客戶訪談與需求分析.
- AP 憑證的 controller 位置詳解.
  - 之前的概念沒錯，Jason 只是解釋更細部的動作，我們在製作憑證時會添加 Controller 的位置，後續 AP 會根據憑證內容得到 Controller 的位置.
- Capabillity 的產生方式與作用詳解（先看文件）以及跟 yaml 或 schema 的關係，最後是跟 command 的關係.
- Openapi 的定義與使用方式，AP 這邊只針對 schema 的定義來做，方法也許可以參考.
- 討論 UI/UX 的 Web 與 APP 通用資源格式.(Eric/Chien/Jennie)
- 討論與補充 Device discover process.
  - Unifi AP (eg. U6-Pro) 藍芽功能，可在 WAN 口有接通的狀況下被 UDM-Pro 發現，但不管有沒有 WAN 口都可以掃描到 U6-Pro 的藍芽，差別在於廣播的封包內容有點差異.
  - first contact 需要修改才能調整 OpenWifi 的預設行為.

08/11/2022

- EAP - 協助 Todd 做藍芽開發測試.
- EAP - 確定 Controller 的 Web 層級.(Network Only / Physical Console)
- FWA -

08/12/2022

- RESTful API
  - Survey OpenAPI.
  - swagger tool 的使用.
  - Evaluate our project resource and define the API Strategy.
- 協助 BLE solution 開發
  - Build code.
- 學習 Docker 的使用.

08/15/2022

- EAP BLE issue: build image additional info

  - http://192.168.180.70/wiki/index.php/Generic_Elise_Board_(MR7350)
  - git password: Mis@1234
  - build server account/pass: erichu/wh6839003
  - Docker build is recommended
    - Run "dock-run.sh" under ".../Elise" folder.
    - enter "exit" when to want exit docer environment.
  - step3. build all image
    - build all image 名稱說明有誤改為(make -f Makefile.cbt build_all_img)
    - 需移到此目錄下再執行. (.../Elise/GenericRouter/working/qca-networking-2021-spf-11-4_qca_oem-r11.4_00003.0_CSU2/qsdk)

- EAP: Flow confirm
  - Controller Setup Flow (Jennie)
  - AP standard flow.(Jennie)
  - AP onboarding flow.(Eric)
  - Support APP cut views for reference.

08/16/2022

- APP Team Common
  - 合作默契討論.
- OpenAPI/Swagger survey and define.
- Communicated Security(discussion)
  - JWT: json web token.
  - OAuth2、ApiKey.
  - HTTP Security issues.(HTTPs/WSS)
- Onboarding 流程與格式討論
  - Controller 的 response 規格討論
    - OpenWifi 現況: Hostname 需固定，認證時會使用到.
    - 應具備 hostname，IP，port 等三種資訊.
  - URL 建議使用 Hostname，IP 另外安排欄位來放.
  - URL 可以使用 hostname 與 IP
  - 目前憑證與 url 規格有關，初步的想法如下
    - Controller/EAP 的出廠時會使用 self-signed 的方式各自生成憑證，root 憑證共用，Controller 端保存 root 的 private key.
    - Controller
  - 加密用的 key 是用對稱式，預放在 Controller 與 EAP 兩端？

08/17/2022

- 關於 EAP Onboarding 流程，有以下三種方案:
  - Case 1:(T company Style) Always broadcast adv. package per 3~5 seconds before user accept onboarding request to Controller which follow T company.(find by BLE/Ethernet)
  - Case 2:(Already find status on EAP) Add already find status on EAP，EAP keep controller host info and stop broadcast.
    - EAP will keep listening specific port for Controller accept onboarding message.
  - Case 3:(Ready to onboarding status on Controller) Means controller allow EAP first contact after discover，the Controller will not allow EAP access Controller until user accept EAP onboarding.
  - 補充早上的發現，有以下兩點：
    - T 公司的產品行為中，若已經 configure(under controlled)的 EAP 透過 hard key 去 factory reset，若 Controller 並沒有移除該設備，則設備會自動 adapt 到 Controller 中.
    - Controller 若透過"forget"的指令去移除 EAP，則 EAP 會自動 factory reset，並從 Controller 設備中移除.
- 團隊文件管理系統：
  - Wiki 規劃
    - 集中管理
    - 系統分類
    - 方便分享
    - UI 編輯
      - 操作便利性與豐富性.
      - 其他類型文件的支援度 eg.圖表嵌入與鏈結.
    - 協同作業
    - 版本控管
    - 權限控管
- ## OpenAPI/Swagger survey

08/22/2022

- Help to BLE solution.
  - 0x39，0x23，0xCF，0x40，0x73，0x16，0x42，0x9A，0x5c，0x41，0x7E，0x7D，0xC4，0x9A，0x83，0x14
  - 觀察 T 公司，在首次安裝主要透過藍芽進行下面幾種設定:
    - 外網設定(eg. PPPoE，sttic IP，dynamic IP)或 Offline 的設定.
    - 設定 clone MAC 的功能(ps. 此功能是因應特定 ISP 業者會鎖特定 MAC 的需求)
    - Speed Test(ps. 現階段我們不提供此功能)
    - (例外) 透過外網 Create or login cloud 帳號以利後續綁定，若只透過藍芽設定僅能提供本地存取功能.
- 藍芽首次安裝流程規劃.

08/23/2022

- EAP_ES (Controller Setup + LCD)
  - Controller First Time Setup
  - CyFi Controller 角色與功能
    硬體：根據說明書啟動 CyFi Controller 直到 LCM 顯示 Ready to Setup...
    WebUI：使用電腦網線連接至設備，通過瀏覽器 Web UI 進行設置.
    APP : 使用 APP 透過藍芽進行設置
  - CyFi Controller 首次安裝 by APP BLE (使用雲端帳號)
    APP 透過藍芽找到 CyFi Controller
    若藍芽處於 disable 狀態下，會提示用戶打開手機藍芽功能.
    APP 以自動方式在背景掃描 Controller BLE beacon 訊號.
    若持續找不到藍芽會提示一些問題解決方案.
    提示用戶在 WAN 端口連上外網. (ps. 或是透過 Other Setup Options，在無外網的情況下進行設定)
    提供用戶設定 WAN 端的相關設定.
    預設為 dynamic IP.(ps. 考慮使用自動偵測？)
    Static IP
    PPPoE
    Clone a Router’s MAC Address. (Not supported on Stage 1.)
    Share the CyFi Controller’s MAC Address with your ISP. (Not supported on Stage 1.)
    Controller 設備命名.
    提示並讓用戶設定雲端帳號.(提供遠端管理)
    綁定帳號與 CyFi Controller.
    等待速度測試完成後，設備會自動填入測試結果。(Not supported on Stage 1.)
    APP 透過遠端帳號進入 Cyfi Controller dashboard 首頁

CyFi Controller 首次安裝 by APP BLE (未使用雲端帳號)
APP 透過藍芽找到 CyFi Controller
若藍芽處於 disable 狀態下，會提示用戶打開手機藍芽功能.
APP 以自動方式在背景掃描 Controller BLE beacon 訊號.
若持續找不到藍芽會提示一些問題解決方案.
提示用戶在 WAN 端口連上外網.
提供用戶設定 WAN 端的相關設定.
預設為 dynamic IP.(ps. 考慮使用自動偵測？)
Static IP
PPPoE
Clone a Router’s MAC Address. (Not supported on Stage 1.)
Share the CyFi Controller’s MAC Address with your ISP. (Not supported on Stage 1.)
用戶進入 Other Setup Options -> Setup CyFi Controller Offline.
Controller 設備命名.
提示用戶設定管理者密碼.
等待速度測試完成後，設備會自動填入測試結果。(Not supported on Stage 1.)
APP 連入 CyFi Controller 區網，進入 Cyfi Controller dashboard 首頁.(ps. 建議使用 PC - Web)
APP can access CyFi Controller by BLE.(ps. Only for onboarding EAP)
後續可建立或登入帳號，並綁定 CyFi Controller.(ps. Not support by T compay)

08/24/2022

- EAP
- Retrofit，Dio.

08/26/2022

- Enhance BLE performance(from 3k~5k/sec to 12~15k/sec) and stability(checksum/resend/fix bugs) and features(support streamming file/Security) on Generic Router and APP.
  20% - 藍芽功能改善細節如下 - 增加穩定度(加入 Checksum 機制，重傳機制，bug 解決) - 強化功能與效能(增加檔案傳輸的功能，透過傳輸規格優化來改善傳輸速率) - 安全性.(增加對稱式加密機制)

- Develop APP Mesh features on OpenWRT base - VLAN、IPTV、Fixed SQA Bugs
  10%

- Patent drafting and application - Enhance BLE devices security and performance by MQTT Agent.
  15%

- EAP - Survey and Report OpenWiFi Technology，build up a OpenWiFi Controller and help EAP onboarding，help define our product spec.
  35%

- FWA - Survey and Report T Company Product，include UISP and UniFi products and help define our product spec.
  20%

- Others - Study Flutter language for multiple platforms.
  10%

2022/08/29

- Flutter APP 與 Controller API 串接.(Ongoing)
- Survey mDNS on golang/Flutter.(Done)
- Refine ES of EAP Controller.(Done)
  - 無遠端帳號或 Controller 離線時，APP 連線會卡住的解決方案
    - 透過同網段的 WiFi Router，controller IP 則透過 mDNS or UDP broadcast 方式取得，基於安全性考量，不建議透過 WAN 端的網段來執行 mDNS，LAN 口的 Wifi 設備就算尚未入網，也應該可以提供 WiFi 接通 Controller，目前第三方 WiFi 設備可以這樣.
    - 透過藍芽得知 Controller 的狀況，並提供新增後續的 EAP 設備的流程.
- Document 格式學習->Jessie.

2022/08/30

- OpenWiFi 連線測試與討論.
  - Token reflash API
- Controller usecase 討論
  - EAP 無線有線身份互轉的配套規劃.
  - Controller set default 的 EAP 重新入網流程規劃.
  - first time setup by WebUI
    - Config recovery 畫面.
    - 離線密碼設定畫面.
    - 最後仍需登入畫面？

2022/08/31

- 解決 Xcode 無法編譯的問題
  - com.cybertan.wifi.mesh.inHouse
- 討論 FWA-DS 軟體架構細節.
- 討論 EAP-DS 網頁架構層級，雲端帳號細節.

2022/09/02

- Controller First Time Setup ES 會議記錄，此次會議記錄如下：

1. FWA/EAP ES 的會議邀請應包括：
   - 內部: Raoul，Jack，Lily，Penny，Peggy，Johnson，其他同仁視情況增加.
   - 外部:
     - Stanely，PLM(Neil)，硬體主管(PC)，
     - HWSQA:
       Chiaming: Chiaming.Chan@cybertan.com.tw
       King: King.Fang@cybertan.com.tw
       Ajax: Ajax.Lin@cybertan.com.tw
       Cliff: Cliff.Wu@cybertan.com.tw
     - SWSQA:
       EAP:
       William: William.Li@cybertan.com.tw
       FWA:
       Wane: Wane.Chen@cybertan.com.tw
       Lawrence : lawrence.lin@cybertan.com.tw
2. 此次 ES 的內部共識不足，導致問題有些發散，後續會先取得內部 ES 的共識，然後在跟外部報告.
3. Neil 提問雲端帳號是否具備 local 端管理權限？
   - 是的，目前我們初步規劃是雲端帳戶預設可以在 local 端登入 Controller.(ps. 在畫面上顯示並勾選本地端登入權限.)
4. Roual 建議對齊 T 公司的自動偵測外網機制，若可以連到外網，則直接讓用戶進入後續設定階段，
   但可以在 EAP Controller 名稱設定畫面，增加一個管道讓用戶可以設定 WAN 端內容. - 同意，自動偵測機制對齊 T 公司，但增加 WAN 端設定管道的建議會再進行評估.

2022/09/05

- Controller First Time Setup by APP
  - 未建立雲端帳號時 EAP Onboarding 配套規劃與討論.
- APP 與 EAP Controller Restful API 串接測試 On IPQ6018.

2022/09/06

- Controller First Time Setup by APP
  - DS 流程規劃與討論.
  - UI 架構討論.

2022/09/07

- Flutter Language Learning
  - 为什么要将 build 方法放在 State 中，而不是放在 StatefulWidget 中？
- 討論 FWA 的可行架構.
- 討論 Configure 的解決方案，並找出取得 Device current configuration 的方法.
  - 前端呼叫時提供完整的 cofiguration.
  - partial configuration 的配套方法.
  - 分享 Controller 的架構與流程.

2022/09/12

- Scrum 相關討論
  - 介入時機?(技術卡點已解決，終極時程已經評估完)
  - 時程精準度
  - 技術可行性分析
    － 工具使用 - 任務相依性. - Overview 整的專案狀況. - By 專案類別來分任務 - 任務需細分到 1 人？

2022/09/13

- Daily
  - APP 也一併參加前後端 Daily

2022/09/14

- FWA Function List and API 比對.
- ## Flutter Survey

2022/09/15

- Scrum 討論
  - UI/API Spec 制定，需在下個 Spring 之前完成.(Owner?，Task 形式存在？)
  - 跨小組的任務設定原則.(eg. UI/API spec.規劃與討論，技術卡點解決)
  - 任務延遲處理原則.(需另外估時間嗎？主要是讓團隊知道該任務延遲多久，發生原因，解決方案)
  - 任務等待處理原則.(時間較短期的 1~2 天)
- Flutter Survey
  - 輸入框/表單
  - 進度指示器
  - Layout

2022/09/16

- Scrum Backlog
  - ES/DS
    - [Controller] Controller APP External Specification
    - [FWA] FWA APP External Specification
    - [EAP] EAP APP External Specification
  - 可行性評估
    - [Controller] Support mDNS portocol for APP/Web/EAP on LAN
    - [Controller] APP sidesurvey Controller by BLE (APP，FWA/Todd)
    - [Controller] Portal User/Controller 管理機制.(APP，Jason)
    - [Controller] OpenAPI auto gen code mechanism.(APP/Web，Dax)
    - [Controller] Partial Config.(APP，EAP)
    - [Controller] Email Server 認證機制
    - [FWA] HTTP Web support React on EAP/FWA(Web，FWA/Sam)
    - [FWA] Management IP by mDNS(APP，FWA)
    - [FWA] APP sidesurvey FWA/EAP by WiFi(APP，FWA)
- EAP Conroller ES 討論.
- FWA ES 討論.
- Flutter Survey
  - 各種容器組件(Padding，裝飾容器，Transform...)

2022/09/19

- FWA ES 討論
  - AP/Station Mode 的設定欄位的差異?
  - Antenna
    - 上下箭頭代表的涵意.
  - Speed Test 安全機制.
  - Sensitivity Threshold.(Signal 靈敏度)

2022/09/22

- Controller
  - Mesh 數量限制加入 Capability.
- FWA
  - 遠端管理相關配套
    - 首次安裝
    - 設備綁定(AP/Station)
  - 畫面共用
    - 基於 Controller 的畫面功能，進行相關調整.

2022/09/26

- Planing
  - 所有 EAP 共用一組 WiFi 一組變多組，底下 EAP 編輯?
  - EAP 之間的 Backhaul WiFi 可編輯嗎？
  - Security 選單值不一致?
  - submask 選單值由前端自己算出來？還是從 capability 撈?
  - 2G/5G 在取得與設定時內容值？
    2022/09/27
- Controller ES 外部報告
  - WiFi security 類型是否有缺？
  - LAN DHCP DNS Server? (ps. Neil 評估)
  - APP 不提供 EAP 升級，避免升級斷線造成相關問題.
  - Remote Controller 配套?
  - LCM 外網移除或沒要到 IP 或斷線時，是否有提示？ (ps. LCM 只有在 WAN plugout 才提示...)

2022/09/29

- 下面為文化的部分.有任何問題請通知我，謝謝.

- 我們公司/部門未來的方向與目標是什麼?

  - 持續關注並改善現有的開發效率，讓團隊更有效率的開發出優質產品.

- 你未來的工作方向與目標是什麼?

  - 積極配合同仁達成長官交付的任務，大家一起合作建立新的產品線.

- 你認為自己可以勝任愉快的部份是什麼?

  - 帶領同事一起持續使用新的開發工具，讓 APP 的開發更有效率.

- 你認為可以進一步改進的是什麼?

  - 更優質的職場環境.

- 你希望我提供哪些協助與資源?

  - 建議開放公司的健身房，以利同仁身心健康發展.

- 對於我希望自己成為更好的主管這方面，你有什麼建議?

  - 已經很好，持續保持現有的熱情，一起開發新的產品.

- Scrum

  - Daily: 督促感，可以提前知道哪些項目可能滑掉，提前因應.
  - Planing: DoMore - 期待新的專案工具可以帶來哪些好處，但有會因為階段的實際細節沒有跑過，會有點不知所措.(例如：像這次會議重點是感想)
  - 剛開始的時候會議效率比較差，也覺得蠻多的.
  - 估工時: 新專案工具，新開發工具 Scrum vs Water flow 的方式估工時.
  - 不預期的討論很需要也非常費時，但這類的工作常常沒有具體反映在工作任務中，是否需要統計這類的討論，影響 pring 工時.
  - 共識與結論的 sync.

- - 臨時且必要的討論: 頁面共用討論，capability 值域的討論.
  - APP 示意圖與功能的調整，時程預估調整.
  - 重要的小結論應該 sync 到歐陽這邊.

2022/10/13

- 前端開發 UI/API 的 Q&A 結論統一放在哪個地方?
  - Web 開發經驗可以被 APP 復用.(ps. 欄位與 API 的對應關係)
  - Q&A 需要描述跟彙整.(ps. Nice to have)
  - 文件 API/UI update 要 sync.
  - 關於 Chien 會議中提出的 API 的異動與結論可以有統一的地方彙整，在跟 Chien 討論之後，發現其實更深層的需求是 APP 跟 Web 之間開發經驗可以復用，例如: Web/APP 開發時關於的 API/UI 的問題與結論有個共用的檔案或位置可以簡單的紀錄與分享，這樣 APP 在開發時就不用同樣的問題或坑再重複問一次相關人員，如此一來可以大幅減少關鍵 RD 被同樣的問題中斷現有工作，對團隊整體效率是有幫助的，下面是關於此次議題的 Action Item，我們再找時間討論把方法給定下來:
  1. API/UI 文件異動與通知機制.
  2. API/UI 開發相關 Q&A 的紀錄與分享機制.

2022/10/14

- UI 人力資源調配 - APP UI 去擠壓到 FWA 的 UI，現階段還存在嗎？ - Jennie 現階段是 UI 設計，後續可以考慮轉到 Web 前端開發，前提是有找到 UI 設計人員.
  2022/10/18
- Cancel Token 作用.
- IPv6: (([0-9a-fA-F]{1，4}:){7，7}[0-9a-fA-F]{1，4}|([0-9a-fA-F]{1，4}:){1，7}:|([0-9a-fA-F]{1，4}:){1，6}:[0-9a-fA-F]{1，4}|([0-9a-fA-F]{1，4}:){1，5}(:[0-9a-fA-F]{1，4}){1，2}|([0-9a-fA-F]{1，4}:){1，4}(:[0-9a-fA-F]{1，4}){1，3}|([0-9a-fA-F]{1，4}:){1，3}(:[0-9a-fA-F]{1，4}){1，4}|([0-9a-fA-F]{1，4}:){1，2}(:[0-9a-fA-F]{1，4}){1，5}|[0-9a-fA-F]{1，4}:((:[0-9a-fA-F]{1，4}){1，6})|:((:[0-9a-fA-F]{1，4}){1，7}|:)|fe80:(:[0-9a-fA-F]{0，4}){0，4}%[0-9a-zA-Z]{1，}|::(ffff(:0{1，4}){0，1}:){0，1}((25[0-5]|(2[0-4]|1{0，1}[0-9]){0，1}[0-9])\.){3，3}(25[0-5]|(2[0-4]|1{0，1}[0-9]){0，1}[0-9])|([0-9a-fA-F]{1，4}:){1，4}:((25[0-5]|(2[0-4]|1{0，1}[0-9]){0，1}[0-9])\.){3，3}(25[0-5]|(2[0-4]|1{0，1}[0-9]){0，1}[0-9]))
- 前端先寫死的值域，後續若要改為 capability 提供，是否先不要宣告 enum? 而是由某個 capability 物件來提供，後續就由這個物件提供值域內容.
- 現行 Model 為 copy by value，若要 copy by reference?

2022/10/19

- statistics
- Radios
  - 不一定存在.
  - chanel: number，"auto".//\*\*\* APP 目前用 string 承接，不支援 0.
  - tx-level -> cbtTxLevel
  - cbtTxHigh: number
  - cbtTxMedium: number
  - cbtTxLow: number
  - tx-power //\*\*\* 不使用
  - channel-mode //\*\*\* 不使用
- Services
  - ssh //移除
  - wifi-steering //\*\*\* optional: EAP must，Controller 沒有.
    - requre-snr //\*\*\* 移除
    - cbtSensitivity: high/medium/low
    - cbtRequredHigh //int
    - cbtRequredMedium //int
    - cbtRequredLow //int
  - log? //\*\*\* only on controller 參考 Arisa
  - ntp? //\*\*\* only 時間校準的 server
  - mdns? //\*\*\*
- capabilities
  - deviceType //==compabiable 可以區別 Controller/EAP，設定 config 可以直接透過 deviceType 來區分設定的對象，不用 Serial
- https://192.168.180.21:20443/cloud/frontend-base/-/tree/sp1/src/models

2022/10/20

- ssid
  - hidden-ssid 欄位一定會存在，但假資料有不存在的狀況.(owner: Dax)
    - Solution: 改為有可能不存在.
  - 欄位 roaming，rrm 會出現 null 問題需進一步釐清.(owner: 前端)
    - Solution: 改為有可能不存在.
  - cbtMacAddressFilter，cbtWpaGroupRekey 有可能不存在?(owner: 前端)
    - Solution: 改為有可能不存在.
- radios
  - cbtTxHigh/cbtTxMedium/cbtTxLow 型別為 int，假資料為字串'N'.(owner: Dax)
    - Solution: Dax 修正為數字.
  - country 欄位一定會存在，假資料沒有.(owner: Dax)
    - Solution: Dax 補上.
- services
  - wifi-steering 需要重對 Arisa 的內容跟 Dax 有出入.(high/low..型別)(owner:Jessie/Dax)
    - Solution: Dax 修正型別.
  - ntp.servers Arisa 為 string array，假資料為字串.(owner: Dax)
    - Solution: Dax 改為字串的 array.
  - 假資料缺 lldp.(owner: Dax)
    - Solution: 改為有可能不存在.
- configuration
  - APP 的 uuid 型別為 string 需修改為數字，但改為 int 還是會出錯，另外假資料 UUID 欄位名稱全大寫.(owner: APP)

2022/10/21

- UI 尺寸
  - widget_style
  - text_style
  - color_style
- Theme(定義在 main.dart)
  - appBar

2022/10/25

- API 介接狀況
  - APP 目前無法測試真資料.
  - Statistics 假資料測試狀況
    - interfaces.counters 需為 Array.(Owner:Dax)
    - startDate/endDate/limit 欄位需補上.(Owner:Dax)
    - interfaces.ipv4.lease.address 欄位名稱有誤.(Owner:Web)
    - link-state.carrier 資料正確介接出現 null?.(Owner:APP)

2022/10/26

- Expanded 只能是 Flex/Row/Clumn 的 child，flex 預設值為 1?
  - Ans: yes.
- Internet Status: 第二 header 如何安排，目前做法 table 會消失...
  - Ans: 直接加在 EasyRefresh
- ClassicHeader? 搭配 EasyRefresh?
  - Ans:
- Internet status:
  - uptime: UI 使用%來表現，計算公式？ experience?

2022/10/27

- Flutter regexpattern:
  - https://pub.dev/packages/regexpattern
- IPv6
  - None/Relay 模式修正.
- CBTDefualtConfiguration 假資料.
  - 名稱不一致: (另外在建立一個新 model)
    - created vs createdTimestamp?
    - description vs ?
    - lastModified vs modified?
    - modelIds vs ?
    - name vs compatible?
  - 欄位不一定存在
    - configuration.services.wifi-steering
    - configuration.UUID
  - 變數命名盡量直覺一點
    - ipv4RegexRange -> ipv4RegexInputCheck
    - ipv4RegexFormat -> ipv4RegexFinalCheck

2022/10/31

- 徵人條件
  【職務說明】

  1. 負責產品 APP 功能開發與維護。
  2. 建構模組化且可測試的程式碼。(ps. 陳述再更具體，有能力寫自動化測試案例經驗嗎？)

  【所需經驗】

  1. 三年以上原生 APP 開發經驗（Android：Kotlin 或 iOS：Swift），能重頭建立到上架 App。
  2. 一年以上 Flutter 開發經驗。
  3. 熟悉 Flutter plugin。
  4. 熟悉 Unit test。
  5. 熟悉 MVVM 開發框架。
  6. 熟悉 Restful API、WebSocket 串接。
  7. 請附上您的 GitHub 或 GitLab 連結。

  【加分條件】

  1. 熟悉原生藍芽與 WiFi API 使用。
  2. 熟悉 CICD 自動化測試部署。
  3. 熟悉 App 加固，防反編譯機制等資安措施。
  4. 熟悉 Scrum 開發流程。
  5. 參與並貢獻開源專案。
  6. 個人作品上架於 App Store。

  - 依能力狀況可遠端

2022/11/01

- API: {{baseUrl}}:16002/api/v1/devices
  - configuration.interface.ipv6: mode:none/relay.需改為 enable: true/false
  - configuration.services.lldp: describe 欄位名稱錯誤，應為 description.
- topology
  - mac address：wire/ wireless mac 不同？ mac 可唯一.

2022/11/02

- API 介接相關 issue

  - 問題一：假資料 API 之間沒有串連，導致資料撈取失敗.
    - 假資料 API 之間應該要能串連，若前端反應應該也要及時更新.
  - 問題二：假資料仍維持舊做法，未及時反應討論的結果，導致料撈取失敗.
    - 假資料不要維持舊做法，應以新作法為主.
    - 若要考量之前開發的程式能正常運作，則要採用雙系統方式.

  e89f807c71b3

2022/11/03

- 人員面試
  【職務說明】

1. 負責產品 APP 功能開發與維護。
2. 建構模組化且可測試的程式碼。

【所需經驗】

1. 三年以上原生 APP 開發經驗（Android：Kotlin 或 iOS：Swift），能重頭建立到上架 App。
   - 有使用 Fluuter 開發過哪些產品？參與度？
   - 使用過哪些技術元件或 Lib? HTTP(Dio)/狀態共享管理元件(RiverPod)/
   - 是否有作品上架經驗.
2. 一年以上 Flutter 開發經驗。
3. 熟悉原生藍芽與 WiFi API 使用。
4. 熟悉 Flutter plugin。
5. 熟悉 Unit test。
6. 熟悉 MVVM 開發框架。
7. 熟悉 Restful API、WebSocket 串接。
8. 請附上您的 GitHub 或 GitLab 連結。

【加分條件】

1. 熟悉 CICD 自動化測試部署。
2. 熟悉 App 加固，防反編譯機制等資安措施。
3. 熟悉 Scrum 開發流程。
4. 參與並貢獻開源專案。
5. 個人作品上架於 App Store。

- 依能力狀況可遠端

yapi
API/Data Model 內容修改管控 - 文件管控.

2022/11/04

- Static IP: 設定 Subnet mask 為空時的處理方式?

2022/11/09

- APP 實機與板子真資料 Demo.(LAN)(Done)
- Wifi 狀態與設定功能.(Done)
- WAN 狀態與設定功能.(Done)
- Topology 開發.(Ongoing)
- System 開發.(Ongoing)
- Dashboard 開發.(Todo，預計這週)
- Wifi 缺 Client 資訊.(Todo)

- 填寫人資人力需求單.(today)

- Server LoginInfo of Demo
  192.168.180.21
  tip@ucentral.com
  Cbt@12345

- 缺 configuration.third-party.language

2022/11/14

- Account Management 相關 issues
  - 本地登入.
    - server 主機位置搜尋
      - mDNS 機制.
      - UDP broadcast.
      - 直接以 gateway IP 當 server 位置，bridge mode 不合適.
    - 登入機制改用 CBT 自己，需改寫 gateway 的 token 檢查機制.
  - 本地帳密管理.
  - 遠端登入.
  - 遠端帳密管理.(綁定/解綁定)
  - 本地遠端帳密共用/同步.
    - 目前 Jennie 那邊規劃的流程是用戶選擇本地則遠端自動解綁定，但這樣與實際情境不合，應該可同時存在.
- BLE 藍芽 QR Code 掃描需求.
  - 直接透過藍芽廣播封包解析並直接連入，固定時間內掃描到一台以上時列出讓用戶選擇接入的設備.
  - 讓用戶掃描 QR Code 直接連入.(內容包括設備的 UUID 名稱與連線預設密碼.)

2022/11/15

- 18002/api/v1/cbtTopology/eap
  - eap.netInfo.netInfoActivity.(假資料 activity? vs APP 定義為 netInfoActivity)
    - APP 改為 activity
  - eap.netInfo.connectType null.(String vs 假資料為 int)
    - APP 改為 int 並使用 enum (ConnectionType).
  - eap.wifiInfo.type null.(假資料無此欄位)
    - APP 移除.
  - eap.wifiInfo 缺 clientNum.(假資料有，APP 補上)
    - APP 補上.
  - eap.systemUptime (假資料: 空字串/數字)
    - 假資料修正.
  - eap.status 名稱內容有誤( 'online )多一個單引號.
    - 假資料修正.
- 18002/api/v1/cbtTopology/controller
  - 應用到 eap 列表時 controller 資訊缺 name(目前先以 model 代替)，quality(直接填 100)，client 數量(controller 本身也可以直接連接有線的 client).
- ## 18002/api/v1/cbtTopology/topology

2022/11/16
Weekly Report

- WiFi (100%)
  - MAC Filter 缺列表選擇，改成用戶自行輸入.
- Settings/System (100%)
  - System Log.
  - NTP Server.
- Topology

  - Topology 圖的顯示，EAP/client.(100%)
    - ES 只顯示 EAP 的拓樸結構，目前 Chien 超出預期把 Client 也做進來，透過縮放來進行顯示.
  - 節點點選進入 Detail.(50%)
  - Onboarding.(todo)

- Account(Local/Cloud)帳號管理.
  - UI Layout.(50%)
  - API 與流程規劃.(Jack/Jason/Jessie)
- EAP List

  - List.(80%)
  - Detail.(todo)

- 18002/api/v1/cbtTopology/topology/client
  - macaddr(多)，model(無)，uptime(型別)，wifiQualityTable(內容跟 dashboard 不同).

2022/11/17

- Topology 與 Device 資料不同步的處理機制.

2022/11/23
Weekly Report

- Topology
  - 節點點選進入 Detail.(100%)
  - Onboarding.(100%)
    - Ready for test.(todo)
- Device List(EAP/Client)
  - List.(100%%)
  - Detail.(100%)
  - Stats.(100%，圓餅圖後續視情況捕進來.)
  - Overview.(100%)
  - Manage command.
    - Led.(80%，11 月底後)
    - Remove EAP.(70%，11 月底前底層對測.)
  - reivew and refine code.(ongoing)
- Account(Local/Cloud)帳號管理.
  - UI Layout.(50%)
  - API 與流程規劃.(100%)(Jack/Jason/Jessie)
  - Remote control for demo.(todo)
    - APP without hosts file so need proxy server help.
    - Cloud 帳號預先放一組，demo 直接使用登入.
- 人力需求(缺一位)
  - 主動投遞履歷.(初步過濾比較偏 Web 前端)
  - 篩選出 2 位提交給人資聯繫

2022/11/30
Weekly Report

- Topology
  - UI refine for Demo.(Done)
- Device List(EAP/Client)
  - reivew and refine code.(Done)
  - Manage command.
    - Led.(80%，11 月底後)
    - Remove EAP.(70%，11 月底前底層對測.)
- Internet 技術債: Code refine and bug fixed.
- Account(Local/Cloud)帳號管理.
  - UI Layout.(50%)
  - API 與流程規劃.(100%)(Jack/Jason/Jessie)
  - Remote control for demo.(Done)
    - APP access by DNS support.
    - remote 跟 local url path.(port vs url)
- 人力需求(缺一位)
  - 透過 fb.
  - 篩選出 5 位提交給人資聯繫.
    192.168.1.1
    22:22:22:22:22:22

2022/12/05

- Android 帳號
  - https://developer.android.com/ - Jc.liou@cybertan.com.tw - cybertan5699
- Unifi

  - jessie.chan@cybertan.com.tw
  - Mis@cbt123

- BLE on Elise
  - Device: 0xc770e6f5-
  - Service: UUID(0x14839ac4-...)
    - Properties: 0x0734594a

2022/12/07

- Weekly Report
  - Last week
    - APP for Topology Demo.
      - Topology UI Refine.
      - Devices(Client/EAP) List / Detail.
      - Remote Login and Access 功能實作.
    - Inrernet 設定頁面 - 技術債.
  - This week
    - APP 首次安裝
      - BT API 與架構確認.(Done)
      - UI Flow 微調與確認.(ToDo)
    - Account(Remote/Local/Binding)
      - API 確認.(Done)
      - UI Flow 確認與實作.(ToDo)
    - Error Code 規範
      - 定義一版在 controller function list.(ps. 之後考慮獨立放在一般版的文件中)(Done)
      - 規劃與 openwifi 現行 error code 定義值的相容方法.(Todo)
    - 本地與遠端登入與切換.(Chien)
    - Statistics.(Chien)
    - 面試
      - 週四安排一場面試.(ToDo)
- 面試 - 藍芽 - 說明 BLE 整體連接步驟. - bluetoothGatt 的 close 和 disconnect 區別，調用 close 的注意事項. - 曾經配對過的設備資訊，ios 跟 android 的區別？ - 關於 MTU ios 跟 android 的區別？ - MTU on iOS is set automatically，maximum value is 185. - BLE 安全機制. - Out of Band. - BLE 密鑰協商. - 實務開發 - 透過何種方式找到設備? - 簡述藍芽資料收送這邊的定義與規範. - CI/CD - GitHub/GitLab. - 針對 APP 做了哪些事情.
  2022/12/08
- Account Management
  - binding
    - normal : user/device is not exist.(success)
    - user + device already exist.(fail，是否考慮也回成功，factory reset 後可能會出現此狀況.)
    - binding 由 APP 來調用還是 Controller?(ps. 早前的共識是由 Controller 提供.)
  - unbinding.(ps. 此階段是否有提供的必要?)
  - user name update.
  - user account update.
    - Needs to sync Cloud.
  - user password update.
    - Needs to sync Cloud.
  - Factory reset
    - Binding relation is exist.(eg. userA，controllerA)
    - First time setup
      - user create userA + controllerA binding.(fail?)
        - Online
          - check userA on cloud.
            (process1: userA already exist and return false)
            (process2: userA forece create and update binding relation.)
            (ps. forget password process?)
        - Offline
          - can't check userA on cloud.
      - user login by userA + controllerA binding.(fail?)
      - user create new userB + controllerA binding.(success)
    - ps. When online controllerA will delete orginal relation by controllerID，and update new relation.

2022/12/12

- Cloud DNS server: 192.168.180.74
- User account
  - root user
    {
    "userId": "tip@ucentral.com"，
    "password": "Cbt@12345"
    }
  - default user
    {
    "userId": "admin@cybertan.com.tw"，
    "password": "Cbt@12345"
    }
- Controller EAP 板子設定 FRP client.
  [Controller 關機]
  cd /mnt/docker-compose
  ./docker-compose-linux-aarch64 down

  [Controller 開機後]
  cd /mnt/docker
  ./dockerd&
  cd /mnt/docker-compose
  ./docker-compose-linux-aarch64 up -d

  [Cloud frpc]
  docker ps|grep cbt => 下一行最前面會顯示 container id
  docker exec -itd 37505fc9b532 bash -c "frpc -c /frp/frpc.ini" =>37505fc9b532 是 上行指令的 container id

2022/12/14

- Weekly Report
  - Account 管理(Remote/Local/Binding)
    - UI Flow 確認與實作.(Done)
      - 用戶名稱，密碼修改.
      - 遠端帳號同步測試.(Todo)
  - APP 首次安裝
    - APP 透過藍芽與 EAP 溝通
      - APP 藍牙溝通系統架構討論與驗證.(Done)
      - Flutter APP BT sulution survey.(Done)
      - iOS 藍芽的溝通邏輯搬到 Flutter 上.(Eric，Todo)
      - Flutter APP 與 Dispatcher 溝通.(Eric，Todo)
      - APP 與 EAP 完整溝通.(Eric，Todo)
    - UI Flow 微調與確認.(Chien，ToDo)
    - Other issues
      - APP 從手機系統取得的藍芽設備 ID 會變動.
        - 解決方案 1：透過設備名稱來確認.
        - 解決方案 2：serviceID 不會改變，每台設備額外提供一組辨識用的 service ID.
      - Controller 提供
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(Todo)
  - APP Refine and survey
    - 技術債.
      - 修復 WiFi 列表動畫失效、Lan 頁面邏輯優化
    - API HTTP Client 針對 remote/local 連線方式進行重構.
    - Logger release 模式調整.
    - ping 與 mdns 研究.
      - Need ping server，目前 controller 有提供，但 bridge mode 時就可能會有問題.
      - mdns 需研究針對 host 的服務，目前有看到提供 service.
  - 面試
    - 取消 1 場，被婉拒 2 位.

2022/12/19

- APP 首次安裝 by 藍芽
  - 是否要設定 DeviceName.
  - Controller 移除緩存.
  - 先不緩存連線歷史資訊.
  - plugInCable 的提示畫面顯示原則.(未連實體線，未連外網?，未取得 IP?)
  - 主要方案掃描 QRCode(藍芽裝置名稱，預設帳密)，次要方案藍芽 beacon Scan(用戶自行選擇並輸入帳密).

2022/12/20

- 謝維州面試
  - 室內定位系統
    - 主要功能介紹.
    - 簡單說明一下定位邏輯.(定位演算法來源？)
    - 參與度: 獨立開發? 或參與哪一部份的功能開發?
    - java/kotlin.
  - 雲耳機檢測
    - 主要功能介紹.
    - 各系統扮演的角色.
    - 參與度.
  - IoT Led 彩燈控制
    - lib?
    - 簡單說明系統架構環境.(Server/Client)
    - IoT 安全管控.
  - MangaViewer
    - 大量圖片下載優化方式.
    - 交換 AES key?
    - 收費.
  - QuadOnvif
    - lib rtsp decoder?
    - streaming? 壓縮格式？
  - 藍芽
    - 說明 BLE 整體連接步驟.
    - bluetoothGatt 的 close 和 disconnect 區別，調用 close 的注意事項.
    - 曾經配對過的設備資訊，ios 跟 android 的區別？
    - 關於 MTU ios 跟 android 的區別？
      - MTU on iOS is set automatically，maximum value is 185.
    - BLE 安全機制.
      - Out of Band.
      - BLE 密鑰協商.
    - 實務開發
      - 透過何種方式找到設備?
      - 簡述藍芽資料收送這邊的定義與規範.

2022/12/21

- Weekly Report

  - Account 管理(Remote/Local/Binding)
    - UI Flow 確認與實作.
      - 遠端帳號同步測試.(Todo，Jason)
  - APP 首次安裝
    - APP 與 EAP 藍芽溝通
      - iOS 藍芽的 Procotol 溝通邏輯搬到 Flutter 上.(Eric，Todo)
        - Send Request protocol 實作.(header/checsum/ACK).(Eric，Done)
        - Response handler protocol 實作.(Eric，Ongoing)
      - Flutter APP 與 Dispatcher 溝通.(Eric，Todo)
      - APP 與 EAP 完整溝通.(Eric，Todo)
    - UI Flow 微調與確認.(Jennie，Done)
    - UI 畫畫面與流程作.(Eric，Todo)
    - Json 格式討論.(Eric，Todo)
    - Other issues
      - APP 從手機系統取得的藍芽設備 ID 會變動.
        - 解決方案 1：透過設備名稱來確認.
        - 解決方案 2：serviceID 不會改變，每台設備額外提供一組辨識用的 service ID.
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(ps. Dax，規劃 2 個方案)
      - openWifi 的 errorCode 保留(四位數)，CBT API 除了原有欄位，再另外加上 type 欄位，型別 String.(Done)
      - 方案二：openWifi 的 errorCode(四位數)，CBT API 使用原有欄位(最高五位數)，原有的 openwifi 列為(保留的四位數)，型別 int.(Done)
      - 說明與討論.(Todo)
  - 統計圖表頁面.(Chien，Done)
  - FWA.(Chien，Ongoing)
  - 面試
    - 2 位主動投遞履歷，1 位安排明天面試.

  intl: ^0.17.0
  flutter_reactive_ble: ^5.0.2
  json_annotation: ^4.7.0
  crclib: ^3.0.0

  2022/12/26

  - BLE 溝通邏輯 P 轉移(Swift to Flutter)
    - 語法框架不同.

2022/12/27

- ble device info
  - deviceId: C9A994C0-0DA2-ADC6-2776-3EC7E3EAAD2C
  - serciceId: 14839ac4-7d7e-415c-9a42-167340cf2339
- AI mechaine learning.
  - 頻道錯開.
  - 增加 AP.
  - edge core.(鈺登)
  - multiple link optimization.(MLO)
  - 公司對於衛星通訊發展，目前商機是否成熟，有沒有想法跟著墨？
  - 公司支援的人力未來扮演哪些角色？
  - 公司現有業務能力與能量需要再強化.(ps. 部署 SMB 新的業務人力)
  - 新客戶
    - 基本功能 + 穩定性.(ps. 利用這半年使用公司資源來 field try).
    - 新需求.(ps. 業務主管研發背景，友商)

2022/12/27

- Weekly Report

  - Account 管理(Remote/Local/Binding)
    - UI Flow 確認與實作.
      - 遠端帳號同步測試.(Todo，Jason)
  - APP 首次安裝
    - APP 與 EAP 藍芽溝通
      - iOS 藍芽的 Procotol 溝通邏輯搬到 Flutter 上.(Eric，Todo)
        - Send Request protocol 實作.(header/checsum/ACK).(Eric，Done)
        - Response handler protocol 實作.(Eric，Done)
        - AES Encode/Decode.(password using default password 'admin')(Eric，todo)
        - Base64 encode/decode.
      - Flutter APP 與 Dispatcher 溝通.(Eric，Todo)
      - APP 與 EAP 完整溝通.(Eric，Todo)
    - UI Flow 微調與確認.(Jennie，Done)
    - UI 畫畫面與流程實作.(Eric，Todo)
    - Json 格式討論.(Eric，Todo)
    - Other issues
      - APP 從手機系統取得的藍芽設備 ID 會變動.
        - 解決方案 1：透過設備名稱來確認.
        - 解決方案 2：serviceID 不會改變，每台設備額外提供一組辨識用的 service ID.
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(ps. Dax，規劃 2 個方案)
      - openWifi 的 errorCode 保留(四位數)，CBT API 除了原有欄位，再另外加上 type 欄位，型別 String.(Done)
      - 方案二：openWifi 的 errorCode(四位數)，CBT API 使用原有欄位(最高五位數)，原有的 openwifi 列為(保留的四位數)，型別 int.(Done)
      - 說明與討論.(Todo)
  - 統計圖表頁面.(Chien，Done)
  - FWA.(Chien，Ongoing)
  - 面試
    - 2 位主動投遞履歷，1 位安排明天面試.

- tg2qn+Rzjh5WTi32ysH9WqpfhNrQ+HRj1xeyMauzVvSyYuz+IWpyjzk/XsfkRD7OLS9ojuwz9wG9SVrE+dBMbw==

{"ble_uuid":"BE582DA5-8D86-7CD0-C637-E6BAD39DBFF7"，"ssid":"ericMesh"，"password":"mis12345"，"admin_name":""，"admin_pass":"admin"}

{"version":"v1.0"，"requestId":"AD35301E-6928-4C52-B811-E098E54AE658"，"command":"authAdminPWD"，"data":{"adminPass":"admin"，"uuid":"AD35301E-6928-4C52-B811-E098E54AE658"}}

2304ae0000003414af19624fbce22cf8f8c119027033c0fc7070997f081a91a04ba29746f20fa53ad56604ea0d6925cf9e2c9b3ae4e81b36b521b8a8bf87a749232e61df1d378515167d39d2242579eb69e1adc271f1c63d262633c180cf51786e00b2cccea69d0f4be8173f2494a290aedb5058b6de501945e489172249d41b4bf45f4d3629b7f2c1f1c5aee6d3e52cbf31cc7a65136a17c5d7ef5e35190d232dab1472889c7616af61bff077b393eaa742a2ba49ac

23054200010095ffde3a05690511bed3dc25b00f0f1eb1654733d846aeb7f37284f7a1386b12ac7c66d322a1fb53563a1b997128c61c890a6bd6b4febf5c65108cf5a739847a694fd0db
check_sum:ff95

3C0F970F-4786-9AFE-12ED-FFF9583A7F24
flutter: device: DiscoveredDevice(id: 3C0F970F-4786-9AFE-12ED-FFF9583A7F24，name: UDM-PRO，serviceData: {252a: [120，69，88，235，64，165]，2119: [0，56，137，51]，2018: [127，0，0，1]}，serviceUuids: [26816cf6-334b-4580-bc3f-f1b72ef5d93e]，manufacturerData: []，rssi: -86)

flutter: device: DiscoveredDevice(id: C9A994C0-0DA2-ADC6-2776-3EC7E3EAAD2C，name: CBT_Controller，serviceData: {}，serviceUuids: []，manufacturerData: []，rssi: -36)

D/BluetoothLeScanner(21577): onScannerRegistered() - status=0 scannerId=4 mScannerId=0
I/BluetoothAdapter(21577): STATE_ON
I/BluetoothAdapter(21577): STATE_ON
D/BluetoothLeScanner(21577): Stop Scan with callback
I/flutter (21577): Device scan fails with error: Exception: GenericFailure<ScanFailure>(code: ScanFailure.unknown，message: "Need android.permission.BLUETOOTH_CONNECT permission for AttributionSource { uid = 10231，packageName = com.example.wifi_bluetooth，attributionTag = null，token = android.os.BinderProxy@e013689，next = null }: AdapterService getRemoteName")

2022/01/04

- Weekly Report
  - APP 首次安裝
    - APP 與 EAP 藍芽溝通
      - iOS 藍芽的 Procotol 溝通邏輯搬到 Flutter 上.(Eric，Todo)
        - Send Request protocol 實作.(header/checsum/ACK).(Eric，Done)
        - Response handler protocol 實作.(Eric，Done)
        - AES Encode/Decode.(password using default password 'admin')(Eric，Done)
      - Flutter APP 與 Dispatcher 驗證.((Eric，Dax)，Ongoing)
      - APP 與 EAP 完整驗證.(Eric，Todo)
    - QRCode survey.(Chien，Ongoing)
    - UI Flow 微調與確認.(Jennie，Done)
    - UI 畫畫面與流程實作.((Chien，Eric)，Todo)
    - Json/QRCode 格式討論.(Eric，Todo)
    - Other issues
      - APP 從手機系統取得的藍芽設備 ID 會變動.
        - 解決方案 1：透過設備名稱來確認.
        - 解決方案 2：serviceID 不會改變，每台設備額外提供一組辨識用的 service ID.
  - Account 管理(Remote/Local/Binding)
    - UI Flow 確認與實作.
      - 遠端帳號同步測試.((Jason，Eric)Todo)
  - Devices
    - Remove.(Done)
    - LED 閃爍定位.(Todo)
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(ps. Dax，規劃 2 個方案)
      - openWifi 的 errorCode 保留(四位數)，CBT API 除了原有欄位，再另外加上 type 欄位，型別 String.(Done)
      - 方案二：openWifi 的 errorCode(四位數)，CBT API 使用原有欄位(最高五位數)，原有的 openwifi 列為(保留的四位數)，型別 int.(Done)
      - 說明與討論.(Todo)
  - API Client 重構.
  - FWA 專案環境建置.(Chien，Done)
    - LAN 的 UI 完成，等底層的 API ready 進行介接驗證. (Chien，Ongoing)
  - 面試
    - 1 位蔡先生(週二面試)，1 位安排明天面試.

2023/01/05

- APP 隱私權與著作權網站.
- APP BLE/Wifi/權限索取描述.

- 首次登入需帶 newPassword
  curl -k -X POST -H "Content-Type: application/json" --data "{\"userId\":\"tip@ucentral.com\"，\"password\":\"openwifi\"，\"newPassword\":\"Cbt@12345\"}" https://127.0.0.1:16001/api/v1/oauth2

curl -k -X POST -H "Authorization: Bearer e668048dc1646281b657726479b68b4cb47d38bb2cb72a7c0c9c44250dc28de9" -H "Content-Type: application/json" --data "{\"email\":\"erichu2@cybertan.com.tw\"，\"currentPassword\":\"Mis@12345\"，\"name\":\"erichu2\"}" https://127.0.0.1:16001/api/v1/firstLogin

curl -k -X POST -H "Content-Type: application/json" --data "{\"userId\":\"erichu@cybertan.com.tw\"，\"password\":\"Mis@12345\"}" https://127.0.0.1:16001/api/v1/oauth2

- 面試
  - 藍芽
    - 說明 BLE 整體連接步驟.
    - bluetoothGatt 的 close 和 disconnect 區別，調用 close 的注意事項.
    - 曾經配對過的設備資訊，ios 跟 android 的區別？
    - 關於 MTU ios 跟 android 的區別？
      - MTU on iOS is set automatically，maximum value is 185.
    - BLE 安全機制.
      - Out of Band.
      - BLE 密鑰協商.
    - 實務開發
      - 透過何種方式找到設備?
      - 簡述藍芽資料收送這邊的定義與規範.
  - EAP
    - 針對中小企業提供的覆蓋率更完整的 Wifi 訊號覆蓋以及流量分析.
    - APP 主要功能透過藍芽進行首次安裝
    - Controller 與衛星節點的資訊與設備管理.
  - FWA
    - 戶外型的骨幹 Wifi 通訊設備.
    - APP 主要跟 FWA 設備溝通，安裝時提供的即時 WiFi 訊號資訊給工程人員調整.
    - FWA 的資訊與設備管理.

var req = FirstLoginReq(state.value!.email，newPass，state.value?.name ?? '');

2023/01/6

- Device detail
  - remove 後需跳至 device 列表頁面，並重新 query 一次.
  - remove 之前須調用一次 factory reset.
- 雲端帳號
  - 密碼錯誤跟雲端帳號不存在需分開處理?
- Single user 機制.

- Debug mode:
  class Configs {
  static const String localHost = 'https://192.168.180.69';//'https://192.168.180.69';//'https://192.168.180.64';//String.fromEnvironment('local');

  static const String cloudHost = String.fromEnvironment('cloud');//'https://cybertan.cloud';//'https://192.168.5.100'; //String.fromEnvironment('cloud');

  static const String port = '16';//String.fromEnvironment('port'); //String.fromEnvironment('port'); / '16'
  }

2023/01/09

- EAP Controller Issues:
  - Account.
    - UI scoll will cause dashboard data 重撈.
    - 登入流程確認，局端與遠端.
      - 本地端優先.(省流量)
        - mdns 機制還沒 ready.
      - 遠端優先.(T 公司: 連線比較穩定，不會因為設備操作造成 APP 斷線或重連.)
    - 若使用雲端帳號進行局端登入，是否不允許用戶修改帳密資料以免造成同步問題.(ps. 目前修改系統會回失敗，但要如何提示用戶? 或是透過 UI 來防範)(Done)
    - Bug: 確認密碼未輸入但可以更新資料.(Fixed)
    - Review:
      - Current password by using preference.
  - 隱私權與版權宣告的網站.
    - FirstTimeSetup
    - Json format 定義.
      - Resful API header + body.
    - response 封包驗證.
      - send response ack type 有誤.(需修改為 MESH_RESP_Sequential_ACK)
      - 封包大小切割為 512bytes，目前誤用 pack limited(182).
      - checksum 也因為封包尚未接收完就開始計算，造成誤判.
      - checksum 只有第一次是對的，接下來會因為封包累加造成 checksum 錯誤，因為 checksum 是針對個別的 package 封包.(done)
      - \_getDevResp 封包處理需要等\_handleRespSequence 多次組合成完整的封包再處理.
    - UI 與底噌串接.

2023/01/11

- Weekly Report

* EAP
  - Controller 藍芽首次安裝
    - APP 與 EAP 藍芽溝通
      - Flutter APP 與 Dispatcher 驗證.((Eric，Dax)，Ongoing)
        - Response handler protocol 驗證(ps. Todd 提供 response string).(Eric，Done)
      - APP 與 EAP 完整驗證.(Eric，Todo)
    - QRCode survey.
      - 工具 survey.(Chien，Done)
      - UI 畫面整合.(EricHu，Todo)
    - UI 畫畫面與流程實作.((Chien，Eric)，Todo)
    - Json/QRCode 格式討論.(Eric，Todo)
    - Other issues
      - APP 從手機系統取得的藍芽設備 ID 會變動.
        - 解決方案 1：透過設備名稱來確認.
        - 解決方案 2：serviceID 不會改變，每台設備額外提供一組辨識用的 service ID.
  - Account 管理與登入(Remote/Local/Binding)
    - UI Flow 確認與實作.
      - 遠端帳號同步測試.((Jason，Eric)Done)
    - UI 調整與實作.(Todo)
    - APP 自動判斷 Controller 連線方式.(Ongoing)
      - 局端 IP 查找與實作.(Done)
  - Devices
    - Remove.(Done)
    - LED 協助定位.(Chien，Done)
  - APP Bug 與優化.
    - 修復 EAP Internet 帳號密碼邏輯錯誤
    - 修復 EAP System log 邏輯錯誤
    - 優化 Dashboard、Topology View API refresh 機制
  - 提測
    - 部分提測項目確認.
* FWA

  - Network.(Ongoing，Chien)

* Others
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(ps. Dax，規劃 2 個方案)
      - openWifi 的 errorCode 保留(四位數)，CBT API 除了原有欄位，再另外加上 type 欄位，型別 String.(Done)
      - 方案二：openWifi 的 errorCode(四位數)，CBT API 使用原有欄位(最高五位數)，原有的 openwifi 列為(保留的四位數)，型別 int.(Done)
      - 說明與討論.(Todo)
  - API Client 重構.
  - FWA 專案環境建置.(Chien，Done)
    - LAN 的 UI 完成，等底層的 API ready 進行介接驗證. (Chien，Ongoing)
  - 面試
    - 1 位呂先生(週二面試)，嘗試聯絡.

2023/01/11

- Weekly Report

* EAP
  - Controller 藍芽首次安裝
    - APP 與 EAP 藍芽溝通
      - Communicate with Dispatcher.(Eric，Done)
    - QRCode survey.
      - UI 畫面整合工具.(EricHu，Done)
    - UI 畫畫面與流程實作.
      - UI 畫面與 BLE 整合.((EricHu)，Done)
      - 畫面流程調整.((EricHu，Chien，jennie)，Todo)
      - 完整 UI 流程實作.((EricHu，Chien)，Todo)
    - BLE Json/QRCode 格式討論.(Eric，Dax，Jack)
      - 規格設計.((Eric，Dax)，Todo)
      - 溝通驗證.((Eric，Dax)，Todo)
    - APP 與 EAP 完整驗證.(Eric，Todo)
    - Other issues
      - APP 從手機系統取得的藍芽設備 ID 會變動.
        - 解決方案 1：透過設備名稱來確認.(選擇此方案)
        - 解決方案 2：serviceID 不會改變，每台設備額外提供一組辨識用的 service ID.
  - Account 管理與登入(Remote/Local/Binding)
    - UI Flow 確認與實作.
      - 遠端帳號同步測試.((Jason，Eric)，Done)
      - 新 API 調整.((Jason)，Done)
        - 針對帳號不存在與密碼錯誤代碼分開.
    - UI 調整與實作.(Todo)
    - APP 自動判斷 Controller 連線方式.(Done)
      - 局端 IP 查找與實作.(Done)
        - 新局端 host IP 查找機制確認.(ps. by dhcp server 提供)((Eric，Jack)，Todo)
      - ping 遠端 host 機制.(Todo)
  - Devices
    - Remove.(Done)
    - LED 協助定位.(Chien，Done)
    - 其他功能.(Todo?)
  - APP Bug 與優化.
    - 修復 EAP Internet 帳號密碼邏輯錯誤
    - 修復 EAP System log 邏輯錯誤
    - 優化 Dashboard、Topology View API refresh 機制
  - 提測
    - 部分提測項目確認.
* FWA

  - Network.(Ongoing，Chien)

* Others
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(ps. Dax，規劃 2 個方案)
      - openWifi 的 errorCode 保留(四位數)，CBT API 除了原有欄位，再另外加上 type 欄位，型別 String.(Done)
      - 方案二：openWifi 的 errorCode(四位數)，CBT API 使用原有欄位(最高五位數)，原有的 openwifi 列為(保留的四位數)，型別 int.(Done)
      - 說明與討論.(Todo)
  - API Client 重構.
  - FWA 專案環境建置.(Chien，Done)
    - LAN 的 UI 完成，等底層的 API ready 進行介接驗證. (Chien，Ongoing)
  - 面試
    - 1 位呂先生(週二面試)，嘗試聯絡.

2023/01/18

- Weekly Report

* EAP
  - Controller 藍芽首次安裝
    - UI 畫畫面與流程實作.
      - 畫面流程調整.((EricHu，Chien，jennie)，Done)
      - 完整 UI 流程實作.((EricHu，Chien)，Open)
    - BLE Json/QRCode 格式討論.(Eric，Dax，Jack)
      - Json 規格設計.((Eric，Dax)，Done)
      - QRCode 提供相關規格給 Jack.(Eric，Done)
      - BLE Json 溝通驗證.((Eric，Dax)，Open)
      - QRcode 內容工廠端確認.(Jack，Open)
    - APP 與 EAP 完整驗證.(Eric，Todo)
    - Other issues
      - APP 從手機系統取得的藍芽設備 ID 會變動.
        - 解決方案 1：透過設備名稱來確認.(選擇此方案)
        - 解決方案 2：serviceID 不會改變，每台設備額外提供一組辨識用的 service ID.
  - Account 管理與登入(Remote/Local/Binding)
    - 登入機制配合首次安裝進行調整.(Eric，Open)
    - APP 自動判斷 Controller 連線方式.(Done)
      - 局端 IP 查找與實作.(Done)
        - 新局端 host IP 查找機制確認.(ps. by dhcp server 提供)((Eric，Jack)，Todo)
      - ping 遠端 host 機制.(Todo)
  - Devices
    - Remove.(Done)
    - LED 協助定位.(Chien，Done)
    - 其他功能.(Todo?)
  - APP Bug 與優化.
    - 修復 EAP Internet 帳號密碼邏輯錯誤
    - 修復 EAP System log 邏輯錯誤
    - 優化 Dashboard、Topology View API refresh 機制
  - 部分提測
    - 部分提測項目確認.(Done，Chien)
    - EAP API 測試(Done，Chien)
      - 問題已回報韌體端.(Done，Chien)
    - 優化 EAP devices 畫面。(Open，Chien)
    - EAP Token 無效強制登出。(Open，Chien)
* FWA

  - Network.(Done，Chien)
  - FWA System.(Open，Chien)

* Others
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(ps. Dax，規劃 2 個方案)
      - openWifi 的 errorCode 保留(四位數)，CBT API 除了原有欄位，再另外加上 type 欄位，型別 String.(Done)
      - 方案二：openWifi 的 errorCode(四位數)，CBT API 使用原有欄位(最高五位數)，原有的 openwifi 列為(保留的四位數)，型別 int.(Done)
      - 說明與討論.(Todo)
  - API Client 重構.
  - FWA 專案環境建置.(Chien，Done)
    - LAN 的 UI 完成，等底層的 API ready 進行介接驗證. (Chien，Ongoing)
  - 面試
    - 目前沒有合適的人選，面試(2/7，李先生)

- QRCode 格式
  00:3E:CB:21:01:A1
  1234567890ABCDEF
  CYFI_01A1
  1234567890
  cybertan.wlan.local
  https://cybertan.eap.com
  FF
  eww631-a1
  V1.0.0
  V1.0.0
  CBT_Controller
  tip@ucentral.com
  openwifi

- BLE 首次安裝 issues
  - 做到一半跳掉，後續如何判斷是否已經完成首次安裝.(ps. 目前緩存的 email 需要為非預設？)
  - 取消 Email 欄位自動大寫.

{"command":"oauth2"，"http":{"url":"https://192.168.180.35:16001/api/v1/oauth2"，"method":"POST"}，"data":{"userId":"tip@ucentral.com"，"password":"openwifi"，"newPassword":"Cbt@12345"}}

2023/01/30

- git 轉移.
- ble 驗證.
  - Camera 有時會無法正常開.
  - 特殊結尾
  - Androd 藍芽 scan 權限不會觸發 permission 問題.(AndroidManifest.xml 問題)
  - iPhone se2
    - [CoreBluetooth] API MISUSE: <CBCentralManager: 0x280da8100> can only accept this command while in the powered on state
      - Scan devices not wait state change into powered on.
  - Open by Xcode :
    - fatal error: module 'firebase_core' not found.
      - Root cause: Use error configuraion
      - Ans: Need select prob / dev not Runner.

2023/02/01

- Weekly Report

* EAP
  - Controller 藍芽首次安裝
    - UI 畫畫面與流程實作.
      - 完整 UI 流程實作.((EricHu，Jennie)，Open)
    - BLE Json/QRCode 格式討論.(Eric，Dax，Jack)
      - QRCode 提供相關規格給 Jack.(Eric，Done)
        - QRCode 資訊量與圖形大小相關 issue.((Eric，Jack)，Open)
        - QRcode 內容工廠端確認.(Jack，Open)
      - BLE Json 溝通驗證.((Eric，Dax)，Done)
        - default account/password login.(Done)
        - firstLogin.(帳號建立與綁定)(Done)
        - 取得 WAN 口/Internet 狀態.((Eric，Dax)，Open)
        - 設定 Internet.((Eric，Dax)，Open)
    - APP 與 EAP 完整驗證.(Eric，Todo)
    - Other issues
      - Android BLE 權限畫面未觸發.((Eric，Chien)，Open)
  - Account 管理與登入(Remote/Local/Binding)
    - 登入機制配合首次安裝進行調整.(Eric，Done)
  - Devices
    - Remove.(Done)
    - LED 協助定位.(Chien，Done)
    - 其他功能.(Todo?)
  - APP Bug 與優化.
    - 修復 EAP Internet 帳號密碼邏輯錯誤
    - 修復 EAP System log 邏輯錯誤
    - 優化 Dashboard、Topology View API refresh 機制
  - 部分提測
    - 按原計劃在第二輪提測.(包括首次安裝)
    - 部分提測項目確認.(Done，Chien)
    - EAP API 測試(Done，Chien)
      - 問題已回報韌體端.(Done，Chien)
    - 優化 EAP devices 畫面。(Open，Chien)
    - EAP Token 無效強制登出。(Open，Chien)
* FWA

  - FWA System.(Done，Chien)

* Others
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(ps. Dax，規劃 2 個方案)
      - openWifi 的 errorCode 保留(四位數)，CBT API 除了原有欄位，再另外加上 type 欄位，型別 String.(Done)
      - 方案二：openWifi 的 errorCode(四位數)，CBT API 使用原有欄位(最高五位數)，原有的 openwifi 列為(保留的四位數)，型別 int.(Done)
      - 說明與討論.(Todo)
  - API Client 重構.
  - FWA 專案環境建置.(Chien，Done)
    - LAN 的 UI 完成，等底層的 API ready 進行介接驗證. (Chien，Ongoing)
  - 面試
    - 目前沒有合適的人選，面試(2/7，李先生)

2023/02/07

- BLE issues
  - local URL
  - dialog display
  - first login info keep fail.
  - pppoe info is disappear but model has this info.
  - 連線斷不乾淨的問題還是沒有完全解決.

2023/02/08

- Weekly Report

* EAP
  - Controller 藍芽首次安裝
    - UI 畫畫面與流程實作.
      - 完整 UI 流程實作.((EricHu，Jennie)，Open(95%))
        - BLE permission 權限畫面 Refine.
        - BLE QRCode 引導畫面的圖示 Refine.
      - Code review/merge.((Eric，Chien)，Open(50%))
    - BLE Json/QRCode 格式討論.(Eric，Dax，Jack)
      - Controller BLE 根據規則產生廣播封包.(Todd，Open)
        - EAPC_xxxx(Serial Number)
      - BLE Json 溝通驗證.((Eric，Dax)，Done)
        - 取得 WAN 口/Internet 狀態.((Eric，Dax)，Done)
        - 設定 Internet.((Eric，Dax)，Done)
    - APP 與 EAP 完整驗證.((Eric，Todo)，Done)
    - Other issues
      - Android BLE 權限畫面未觸發.((Eric，Chien)，Done)
  - QA 提測
    - 按原計劃在第二輪提測.(包括首次安裝)
    - 提測文件項目確認.((Eric，Chien)，Open)
      - 下週先內部 Review.(Jack，Open)
    - APP 內部 Bug 修正.
      - 已知 bug 的修正.
* FWA

  - Ping Tool.(Chien，Done)
  - Dashboard(Chien，Open(50%))

* Others
  - Error Code 規範
    - 規劃與 openwifi 現行 error code 定義值的相容方法.(ps. Dax，規劃 2 個方案)
      - openWifi 的 errorCode 保留(四位數)，CBT API 除了原有欄位，再另外加上 type 欄位，型別 String.(Done)
      - 方案二：openWifi 的 errorCode(四位數)，CBT API 使用原有欄位(最高五位數)，原有的 openwifi 列為(保留的四位數)，型別 int.(Done)
      - 說明與討論.(Todo)
  - API Client 重構.
  - 面試
    - 預計一位 APP 資深同仁會進來(Steve).

2023/02/08

- <key>NSBluetoothAlwaysUsageDescription</key>
  <string>Need BLE permission</string>
  <key>NSBluetoothPeripheralUsageDescription</key>
  <string>Need BLE permission</string>
  <key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
  <string>Need Location permission</string>
  <key>NSCameraUsageDescription</key>
  <string>Need Camera permission</string>
  <key>NSPhotoLibraryUsageDescription</key>
  <string>Need Photo permission</string>
- PingUtil Completer 生命週期確認.
- BLE 資源釋放不乾淨，無法重新建立連線.(done)
- ble not support status 的處理頁面應顯示 permission request 當底.
- QRCode page : Camera disappear when child back.
  - solution: parent page reload when child navigation back.
- 首次安裝完成後使用建立的帳號可以正常登入，此時若跳去重新設定一次首次安裝，雖然不會成功，但卻會造成原先可登入的帳密變成無法登入.
- 首次安裝成功後需要清除緩存預設的密碼(修改後).(Done)
- 近端登入失敗改用遠端？
- Issue: 若 APP 有緩存預設帳密，會以緩存帳密進行首次安裝設定，但考量設備可能再次重新恢復出廠值，因此可能需要再登入失敗後再用原始預設帳密登入看看.

{
"userId": "tip@ucentral.com"，
"password": "Cbt@123451"
}

00:3e:cb:16:98:a1
1234567890ABCDEF
1234567890
CyFi\_
admin@cybertan.com.tw
Cbt@12345

2022/02/14

- 準備提測文件.
  - ES 根據實際開發狀況進行補充.
    - First Setup.(Done)
    - Main Functions.
  - Release note 文件.
  - 驗證自測項目.
    - First Setup.(Done)
- 首次安裝.
  - 協助驗證首次安裝完成後 Controller 自動關閉藍芽功能.
  - 驗證 BLE debug 模式.(Done)
  - 提交一版 merge request.(Done)

2023/02/15

- 準備提測文件.
  - ES 根據實際開發狀況進行補充. - First Setup.(Done) - Main Functions.(Eric)(Open)
  - Release note 文件.(Eric，Chien)(Open)
  - 驗證自測項目.(Eric)(Open)
  - 解決自測項目衍生的 bug.(Eric，Chien)(Open)
- 首次安裝.
  - 驗證 BLE debug 模式.(Done)
  - 提交一版 merge request.(Done)
  - 協助驗證首次安裝完成後 Controller 自動關閉藍芽功能.(Dax)(Open)
  - 確認 CBTPassBLE/BLE Daemon 已提交 git.(Dax，Todd)(Open)
- 協助新人 Onboarding.(Eric，Chien)(Open)
  - 開發環境.
  - 開發設備.
  - 網路環境.
  - 專案介紹.

2023/02/15

- Weekly Report

* EAP
  - Controller 藍芽首次安裝
    - UI 畫畫面與流程實作.
      - 完整 UI 流程實作.((EricHu，Jennie)，Done)
        - BLE permission 權限畫面 Refine.
        - BLE QRCode 引導畫面的圖示 Refine.
        - BLE firstLogin 之後的藍芽關閉.(Dax)
          - Web 調用 firstLogin 則無法關閉藍芽.(已知 Issue)
      - Code review/merge.((Eric，Chien)，Done)
    - BLE Json/QRCode 格式討論.(Eric，Dax，Jack)
      - Controller BLE 根據規則產生廣播封包.(Todd，Done)
        - CyFi_xxxx(Serial Number(MAC addresss 去":"))
  - QA 提測
    - 按原計劃在第二輪提測.(包括首次安裝 By 藍芽)
    - 提測文件項目確認.
      - 硬體資源.
      - 下週先內部 Review.(Jack，Done)
      - 提測文件修改.(Jack，Done)
    - 自測項目.(Eric)
      - 字體超出邊界問題.(Chien，Done)
      - 新增 wifi 倒數 Dialog。
      - 修復部分 Provider exception 無法正常釋放。
* FWA

  - Ping Tool.(Chien，Done)
  - Dashboard(Chien，Open(50%))

* Others
  - 新增 wifi 倒數 Dialog。修復部分 Provider exception 無法正常釋放。
  - API Client 重構.
  - 面試
    - 預計一位 APP 資深同仁會進來(Steve).

- 2023/02/16

  - 準備提測文件.
    - ES 根據實際開發狀況進行補充. - Main Functions.(Eric)(Done)
    - Release note 文件.(Eric，Chien)(Done)
    - 驗證自測項目.(Eric)(Open)
    - 解決自測項目衍生的 bug.(Eric，Chien)(Open)
    - Login : Password refine.(Open)
    -
  - 首次安裝.
    - 協助驗證首次安裝完成後 Controller 自動關閉藍芽功能.(Dax)(Pending，列為 issue)
    - 確認 CBTPassBLE/BLE Daemon 已提交 git.(Dax，Todd)(Open)
  - 協助新人 Onboarding.(Eric，Chien)(Open)
    - 開發環境.
    - 開發設備.
    - 網路環境.
    - 專案介紹.
  - Others
    - 第二階段：
      - web socket 取代 https.
      - 參與後端開發.

- 2023/02/17

  - APP 版號顯示位置.
  - Client 流量統計時間不即時，ALL Traffic Stastistics 有即時.
  - Locate trigger 成功之後，下一次的間隔無法預測容易失敗.

  QA Release node:

  - Remove mark of filter on logger.(ps. 讓 logger 可以輸出 debug，正式 release 需關閉.)
  - File_Sharing 要改為 true.(ps. 讓 logger 可以把資料寫入 file 中，正式 release 需關閉.)

003ecb2101aa

003ecb2102a1

003ecb2102aa

003ecb2104aa

- 測試申請單

  - RvR 不用打勾，其他照填.
  - 使用 H/W P/N: 3763-63100002R.

- 2023/02/20
  - EAP 2rd stage issues
    - First Login process refine
      - Connect BLE by side survey
    - Controller
      - Factory Reset
      - Restart
    - EAP
      - Factory Reset
      - Restart
      - Remove EAP
    - ARP Table
    - WiFi channel side survey
  - FWA issue
- 2023/02/22
  - 會議紀錄
    - Team work issues
    - EAP
    - FWA
    - Common
      - Unit test
      - CI/CD
