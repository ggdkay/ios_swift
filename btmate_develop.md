 
 
# BTMate应用接入库开发说明
##编译平台,编写语言与App版本
 >* Ide: Xcode 8.2
 >* 语言：Swift 2.3，ObjectC,C
 >* 版本：V1.0.4
 >* 工具：carthage (`打开BTMate.xcproject`)
 
##第三方库
 >* .MJRefresh(source code)
 >* .SVProgressHUD(`framework`)
 >* .MarqueeLabel (`framework`)
 >* .BabyBluetooth(`通讯必需的库`)
 >* .PrettyRulerClass
 >* .ColorPickerClasses
 >* .FloatTouchAssistive
 >* .AudioPlayer

 
## lib.framework
 添加下面的库以及添加到新的工程，才可以使用BLE接口与杰理低功耗控制协议进行开发
 >* 1.libJL.framework(`通讯必需的库，主要杰理协议库`)-BTMate/Embedded binaries ,libs will be auto setted   in Linked Framework and libraries
 >* 2.libCrc.framework(`通讯必需的库，协议crc校验库`)-BTMate//Embedded binaries ,libs will be auto setted   in Linked Framework and libraries
 >* 3.桥接头文件OC_ThirdParty／BTMate-Bridging-Header.h(`通讯必需的库与OC,C中语言的头文件导入`)
 >* 4.Bridge_Swift_oc/*(BabyBluetooth 开源源码位置)
 >* 5.lib4JL/* (`通讯必需的接口类`)
 
## 提示 Tips
 >* 1.浮动灯光 FloatLightButton;*
 >* 2.模式界面的子界面模式，如 `sub controller` ,需要初始化`initSubOfMode()`:
 
## 二次开发新模式的步骤
 >* 1 创建 x.swift extend **BaseViewController**
 >* 2 创建界面 storyboard set x for identify(String) 到自定义类 custom class
 >* 3 创建模式信息在 `ModeTaskInfo`
 >* 4 初始化 controller 于 `CommonUI/`ModeMenuViewController`类中的函数initModeTask()，添加相关模式信息;`
 
##  现已开发的若干种模式
### 1.蓝牙连接
 
 **主要是搜索，连接，断开，获取固件版本，获取模式信息**,其他请参考蓝牙库开发说明部分
 
 **注意**：
 >1. 蓝牙连接可以是超级模式，任何模式可以回到连接界面，命令模式号与主模式一致
 >2. 如固件模式比手机应用模式多，便会跳转到该模式，不进行任何操作
 
**涉及回调与命令传输**
 
>1. 版本号
>2. 超时
 
**获取超时时间时间后，发送获取模式信息命令**
 
### 2.手机音乐列表（iTunes导入）
### 3.设备音乐
### 4.灯光模式
### 5.收音模式
### 6.声卡模式
### 7.外部音源模式
 
##查看所有的本地开发接口，二次开发
 
 由jazzy生成的本地网页，请根据下面操作方式查看，进入`docs` 文件夹
 >* 0.打开终端
 >* 1.进入 BTMate Project
 >* 2.cd docs
 >* 3.open index.html,查看`BLEControl.swift,ModesViewController/,CommonUI/*`
 
##API
##初始化库，创建实例
 
```swift
 let bleCtrl = BLEControl()
```
 **组装协议并且发送命令**
 
 ```swift 
 let protocal = BLEProtocal()
 ```
 
**回调数据，使用系统的通知**
 
 ```swift 
 NSNotificationCenter.defaultCenter().addObserver(self, selector: #selector(observerUINotify(_:)), name: nil, object: nil)
 ```

### 连接设备
```swift
 func connectBLE(peripheral: CBPeripheral)
```
 **参数**：
    CBPeripheral peripheral
	
 **回调当前蓝牙设备成功连接信息**
 
 ```swift 
 BT_DEVICE_CONNECTED
 let currrentPeripheral = notify.object as! CBPeripheral
```
 **回调当前蓝牙设备成功设置通知**
 
```swift
 BT_DEVICE_NOTIFY_SUCCEED
 protocal.sendMainCmd(REQ_SDK_VERSION)
```
**回调当前蓝牙固件版本号（使用判断升级)**

`RECV_BLE_VERSION`

### 断开设备
```swift
 func disconnectBLE()
```
 
**回调当前为连接的设备信息**
 
```swift
 BT_DEVICE_DISCONNECT
```
#### 例子：
 >* bleCtrl.stopScanBLE()
 >* bleCtrl.disconnectBLE()
 >* bleCtrl.startScanBLE()
 
##数据通信解析
### 发送消息-BLEProtocal.swift
 
```swift
 //主要是LIb4JL/BLEProtocal.swift，利用协议组成数据包，调用BLEControl.swift 关键API与BLE小机通讯
 func setControl(blectrl : BLEControl)

 设置控制句柄
 func setCurrentMode(curModeNum : UInt8)
 设置当前模式号
 
 private func sendData2Firmware(data: NSData) -> Bool
 所有的命令组装成协议数据包，通过sendData2Firmware发送给库LibJL处理后,通知返回updateNotifyBLEDeviceData，发送对应格式的数据是self.sendDeviceCSW，self.sendDeviceCBW，调用 bleCtrl.writeCharacterCBWBytes(data)是真正的发送给固件数据的接口。
 
 /**
 It update Notify ble device data of JL Protocal 回调形成的协议数据：CBW,CSW
 
 - Parameter notify: need csw data or cbw data of NSNotification
 
 - Warning:
 Just match notify.name (String)
 */
 @objc private func updateNotifyBLEDeviceData(notify : NSNotification) {
 let name = notify.name
 
 if name.containsString(APP_SEND_CSW) {
 let data = notify.object as! NSData
 self.sendDeviceCSW(data)
 }else if name.containsString(APP_SEND_CBW) {
 let data = notify.object as! NSData
 self.sendDeviceCBW(data)
 }
 }

 
 
 func sendMainCmd(cmd : String) -> Bool
 组装主模式命令数据
 func sendMainCmd(cmd : String,value : UInt8) -> Bool
 组装主模式命令数据
 func sendMainEQCmd(cmd : String,value : UInt8,data : NSData?) -> Bool
 组装共同EQ命令数据
 
 //回调函数 底层回调 CommonUI/`ModeMenuViewController`
 func observerUINotify(notify: NSNotification)｛｝
 
 public class ConstructPacket {
 public var cbw_construct: JCbw!
 public var csw_construct: JCsw!
 public var data_construct: [JData] = []
 public var isFromFirmware: Bool = false //false from phone ,true from device
 
 public var mDeviceModeInfo : DeviceModeInfo!
 public var mDeviceInfols : [DeviceInfo] = []
 public var mDeviceFileInfols : [DeviceFileInfo] = []
 public var mDeviceID3 : UIImage!
 
 }
 
 
 //上层命令回调 CommonUI/`BaseViewController`
 override func updateCurrentUIStatus(info: DeviceModeInfo)
 public class DeviceModeInfo: NSObject {
 public var id : Int!
 public var command : String!//hex
 public var value : AnyObject!
 public var value1 :String!
 }
 
 //上层数据回调 CommonUI/`BaseViewController`
 override func updateCurrentUIData(infolist: [DeviceInfo])
 public class DeviceInfo : NSObject {
 public var id : String!
 public var value : AnyObject! = nil
 public var encoding : CharTypeValue! = nil
 }
 
 一、cbw命令是16byte（有限数据）+data（ ffff+crc+data_length＋data自定义数据）
 
  具体使用解析：
   //自定义数据字段 CBW+sub cmd (for 31 bytes)+ data(more than 31 bytes)制造子命令与帧数据(CustomProtocal.swift)
     public  func createSubAndFrameData(subCmdData : NSData , frameData : NSData) ->NSData{
         let sendDataCrc =  baseTool.uInt16_2Data(JCrc().checkDataCrc(frameData))
         let pkg_flag  = LAST_DATA_FLAG.dataFromHexadecimalString()
         let framesData = makeDataHeadAndFrameValue(frameData,pkgFlagNumber: pkg_flag!,dataCRC: sendDataCrc)
         
         let data = NSMutableData.init()
         data.setData(subCmdData)//cbw自定义的16byte
         data.appendData(framesData)//data
         
         return  mCBW.makeCBWProtocal(data)//16 byte＋data －> cbw (+data)
     }
 
 回调接收位置：
 
 //Mark: - 监听UI数据通知
     override func observerUINotify(notify: NSNotification) {
         
         switch notify.name {
    
         case BLE_PACKET:
             self.toParseProtocal(notify.object as! ConstructPacket)
             break
         default:
      
             break
             
         }
     }


 
 三、	不使用现有的模式开发的话，可以直接使用 以下收发数据（BLECcontrol.swift）
  
  发送数据：
   public func writeCharacterCBWBytes(bytes : NSData) -> Bool
   
  接收数据：
   notify2_Post(BT_RECV_NOTIFY_DATA, notifyObj: character.value!)
 

 
```

>Author:Tanhuiling，
>Version:V1.0.4
>Date:2016.12.30
 ---------------------------
 v1.03（2016.12.12）
 >* cocoapods
 
 
 ---------------------------
 v1.04（2016.12.30）

 >* 1 补充后台／Siri，不发通信命令
 >* 2 补充自定义数据字段
>*  3 Carthage->framework
 >* 4 修改libJL（NSObject） 兼容 OC,部分命令需要使用UtilCommand
 >* 5 Ide: Xcode 8.2
 >* 6 后台／Siri 打开不发通信命令
 >* 7 添加Bugly.framework  sdk 错误定位（libc++.thd,libz.thd,Security.framework,SystemConfiguration.framework）
 
 
