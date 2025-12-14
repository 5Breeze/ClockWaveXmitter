# ClockWaveXmitter - 电波钟信号发生器

**[English](README.md)|[中文](README_ZH.md)**

支持 ESP32 和 ESP32-C3 的无线电信号时钟发射器，带网页配置界面、WiFi 设置以及多时间、多协议调度功能。

<img width="2468" height="1428" alt="image" src="https://github.com/user-attachments/assets/548f9941-76ea-40ca-8b33-df68f4d291de" />

## 功能

### 1. 硬件支持

* ✅ **ESP32**（GPIO 26, 27, 25）
* ✅ **ESP32-C3**（GPIO 3, 4, 5）
* 自动检测芯片型号并配置对应引脚

### 2. WiFi 配置

* 🌐 **网页配置页面** – 可通过浏览器设置 WiFi
* 📡 **自动配置模式**：

  * 首次启动如无配置，自动进入 AP 模式
  * 默认 AP 密码：`12345678`
  * WiFi 连接失败（30 秒超时）时自动进入 AP 模式
* 🔧 **设备名称** – 使用 MAC 地址生成唯一名称

### 3. 支持无线电信号标准

支持 7 种无线电标准：

* JJY-E（40 KHz）– 日本福岛
* JJY-W（60 KHz）– 日本福冈
* WWVB（60 KHz）– 美国
* DCF77（77.5 KHz）– 德国
* BSF（77.5 KHz）– 台湾
* MSF（60 KHz）– 英国
* BPC（68.5 KHz）– 中国

### 4. 调度功能

* ⏰ **多无线电调度** – 可设置不同无线电发射时间段

---

## 硬件连接

### ESP32

```
GPIO 26 → PWM 模拟 RF 信号输出
GPIO 27 → 蜂鸣器
GPIO 25 → LED 指示灯（当前编码）
```

### ESP32-C3

```
GPIO 3 → PWM 模拟 RF 信号输出
GPIO 4 → 蜂鸣器
GPIO 5 → LED 指示灯（当前编码）
```

---

## 安装

### 1. 所需库

在 Arduino IDE 中安装以下库：

* WiFi（内置）
* WebServer（内置）
* SPIFFS（内置）
* ArduinoJson（通过库管理器安装）
* BluetoothSerial（内置）

### 2. 开发板设置

**ESP32:**

* 板子：ESP32 Dev Module
* Flash Size：4MB
* SPIFFS Size：1.5MB

**ESP32-C3:**

* 板子：ESP32-C3 Dev Module
* Flash Size：4MB
* SPIFFS Size：1.5MB

### 3. 上传代码

1. 连接 ESP32/C3 到电脑
2. 在 Arduino IDE 中打开代码
3. 选择正确板子和端口
4. 点击上传

---

## 使用说明

### 初次设置

1. 设备自动进入 AP 模式
2. 手机/电脑连接 WiFi `RadioStation_XXXX`（XXXX = MAC 后四位）
3. 浏览器打开 `http://192.168.4.1`
4. 输入 WiFi SSID 和密码，点击保存
5. 设备连接网络并开始 NTP 时间同步
6. 后续可通过设备 IP 修改设置

### 网页界面功能

#### WiFi 配置

* 重置 SSID
* 重置密码
* 修改时区

#### 无线电选择

* 查看支持的无线电标准
* 查看各无线电频率

#### 调度设置

* **添加调度** – 点击“Add Schedule”创建新时间段

  <img width="2468" height="1428" alt="image" src="https://github.com/user-attachments/assets/1a7a58f6-e8e6-41ca-9f20-6754986ca1c2" />  

<img width="1893" height="939" alt="image" src="https://github.com/user-attachments/assets/a226a92e-564a-4efa-9dbd-479df388a1c3" />

* **设置时间范围** – 输入开始和结束时间

  <img width="1611" height="481" alt="image" src="https://github.com/user-attachments/assets/9d7059b2-c5fc-4b56-90dd-fc0715419925" />  

* **选择无线电** – 为时间段选择无线电标准

  <img width="1878" height="908" alt="image" src="https://github.com/user-attachments/assets/659a54ce-d6a7-4952-b343-9133e6c337d0" />  

* **删除** – 点击“Delete”移除调度

  <img width="1658" height="293" alt="image" src="https://github.com/user-attachments/assets/aaf16a7a-f7ed-454f-ab49-027b9483d1c3" />  

---

## API 接口

### 获取配置

```
GET /api/config
返回: {"ssid":"network_name"}
```

### 保存 WiFi 配置

```
POST /api/config
参数: ssid, password
```

### 获取可用无线电列表

```
GET /api/stations
返回: [{"id":0,"name":"JJY-E (40KHz)"}, ...]
```

### 获取当前调度

```
GET /api/schedules
返回: [{"station":0,"start":0,"end":1439}, ...]
```

### 保存调度

```
POST /api/schedules
参数: JSON 数组
```

### 获取实时状态

```
GET /api/status
返回: {"time":"12:34:56","station":"JJY-E (40KHz)"}
```

---

## 配置文件

设备会自动在 SPIFFS 中生成以下文件：

### `/config.json`

```
SSID
PASSWORD
```

### `/stations.json`

```json
[
  {"station":0,"start":0,"end":360},
  {"station":1,"start":360,"end":1080},
  {"station":3,"start":1080,"end":1439}
]
```

---

## 常见问题排查

### 无法进入 AP 模式

* 检查 SPIFFS 是否挂载
* 查看串口监视器日志

### 网页界面无法访问

* 确认连接到 `RadioStation_XXXX` WiFi
* 访问 `http://192.168.4.1`
* 检查防火墙设置

### NTP 时间同步失败

* 确认 WiFi 已连接
* 检查 DNS 设置
* 查看串口监视器错误

### 无线电输出不稳定

* 检查天线连接
* 调整天线长度
* 检查 GPIO 接线

---

## 串口监视器输出示例

```
freq 68.500000kHz, timer intr: 80M / (583 x 1), buzz/radio: /137
bits60 pattern: 400000000000000000004100000000000000000042000000000000000000
second pattern: 0111111111...
encode BPC format - 
400102230030323012104101022300303230121042010223003032301210
(re)started timer...
.radio started.
...~~~~~~2025-12-14, 347(0) 16:43:52 (sched=1/1)
....~~~~~~2025-12-14, 347(0) 16:43:54 (sched=1/1)
....~~~~~~2025-12-14, 347(0) 16:43:56 (sched=1/1)
..~~~~~~~~2025-12-14, 347(0) 16:43:58 (sched=1/1)
```

---

## 编码规范

* 时间以分钟表示（0–1439 表示一天）
* 无线电 ID 0–6 对应支持的标准
* GPIO 引脚在编译时通过宏自动定义

---

## 许可

本项目为原 nisejjy 项目的增强版：
[https://github.com/tarohs/nisejjy](https://github.com/tarohs/nisejjy)

---

## 支持浏览器

* Chrome/Chromium 90+
* Safari 14+
* Firefox 88+
* Edge 90+

---

**版权**：5Breeze

用于再分发、改编或商业用途，请注明来源。

