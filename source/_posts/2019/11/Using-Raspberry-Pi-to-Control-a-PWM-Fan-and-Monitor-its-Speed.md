---
title: 利用 Raspberry Pi 控制 PWM 風扇及轉速偵測 (軟體PWM控制)
tags:
  - RaspberryPi/樹莓派
  - DIY
keywords:
  - Raspberry Pi
  - 樹莓派
  - PWM Fan Control
  - PWM 風扇控制
  - Tachometer
  - 轉速偵測
abbrlink: '36297e41'
date: 2019-11-25 22:00:00
---

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574826956/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/Fur_chan_w_Noctua_fan_2.jpg)

相信不少人都會為熱情的 Pi 4 加上風扇散熱，不過小風扇噪音就成了問題，由於 Pi 也不是隨時都滿載需要風扇全速運轉，所以我們可以利用 [PWM (Pulse Width Modulation)](https://zh.wikipedia.org/wiki/%E8%84%88%E8%A1%9D%E5%AF%AC%E5%BA%A6%E8%AA%BF%E8%AE%8A) 來控制轉速。讓風扇在提高到一定溫度時才啟動，或是降低轉速來降低噪音。<!--more-->

# 硬體接線

我使用風扇是 [Noctua NF-A4 5V PWM](https://noctua.at/en/nf-a4x10-5v-pwm) ，一個 40mm、5V 的 PWM 控速風扇，下圖是本次所使用的腳位：

- 黃色黑色分別為 5V 電源及接地
- 綠色為轉速信號
- 藍色為 PWM 控制訊號輸入

{% colorquote info %}
若風扇不支援 PWM 轉速控制（只有兩線或三線）可以參考 [#138 Variable Speed Cooling Fan for Raspberry Pi using PWM and PID controller](https://www.youtube.com/watch?v=oJ32CMxliCQ) 此影片或 [PWM Regulated Fan Based on CPU Temperature for Raspberry Pi](https://www.instructables.com/id/PWM-Regulated-Fan-Based-on-CPU-Temperature-for-Ras/) 利用 BJT 電晶體來做 PWM 控制。
{% endcolorquote %}

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574608109/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_93F334AD3BFC-1.jpg)

轉速訊號線（綠）的部分，由於風扇內是一個開集極電路（OC），所以需要一個上拉電阻（Pull-up resistor）至 Vcc 才能夠讀取到波形，這邊可以參考 [Noctua PWM 規格白皮書(英文)](https://noctua.at/media/wysiwyg/Noctua_PWM_specifications_white_paper.pdf)（大部分的風扇轉速訊號應該都是這種設計，保險起見建議還是要查看廠商提供的資料）

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574608621/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_6F5175335AA4-1.jpg)

{% colorquote danger %}
注意！樹莓派的 GPIO 為 3.3V，所以上拉電阻 Vcc **一定**要接到 **3.3V**，若接到 5V 會造成樹莓派電路損毀！
{% endcolorquote %}

上拉電阻我這邊選擇了 1kΩ 1/4W，上面官方所提供的資料為電流不可以超過 5mA ，根據 $V=IR$ 可算出電阻應該要 660Ω 以上，如果過低會造成風扇電路過載。

PWM 訊號線因為大部分的微控制器或單晶片的 GPIO 都有做好穩定 PWM 方波的輸出電路了，所以這裡就不需要另外自製電路，直接接上 PWM 輸出腳即可（一般的 GPIO 貌似也能靠軟體設定輸出 PWM 訊號，不過硬體輸出較為穩定），[腳位參考](https://pinout.xyz/)。

順帶一提，由於風扇沒有附轉杜邦的線材，所以另外買了 Molex 2510 4PIN (2.54mm間距) 連接器，不過要另外將防呆卡槽消掉一部份，如果有現成的專用風扇線就不用（一般風扇為了相容 3P 連接器所以不會做滿）

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574610745/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_20191123_141251.jpg)

以下是接上 Pi 3 做硬體測試及軟體調教的照片：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574610248/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_20191124_234259.jpg)

硬體安裝完成後，接著就用軟體來控制風扇吧！

# 軟體控制

### PWM GPIO 測試

這裡可以先選擇使用 [Wiring Pi](http://wiringpi.com/) 來進行測試接線是否正確及 GPIO 是否正常，如果不測試可以直接跳到 [PWM 訊號控制轉速](#PWM-訊號控制轉速)。

記得之前的 Pi 版本都是有預裝的，不過目前新版 Pi 4B 還沒加入預設，我們可以透過手動的方式來升級：

```shell
cd /tmp
wget https://project-downloads.drogon.net/wiringpi-latest.deb
sudo dpkg -i wiringpi-latest.deb
```

檢查版本：

`gpio -v`

{% colorquote info %}
撰文時最新版為 2.52
{% endcolorquote %}

查看 GPIO 狀態：

`gpio readall`

把 PIN1 設定為 PWM 模式（注意這邊的編號為 wiring-pi 的編號，可參考[Pi GPIO Pinout](https://pinout.xyz/)）

`gpio mode 1 pwm`

設定 PWM 訊號寬度（數值可為 0~1023）

`gpio pwm 1 [數值]`

檢查風扇是否成功用 PWM 控制，如果有隨著設定數值改變就沒問題了！

### PWM 訊號控制轉速

我把控制程式放在 Github 上了，可以點擊參考：[DriftKingTW/Raspberry-Pi-PWM-Fan-Control](https://github.com/DriftKingTW/Raspberry-Pi-PWM-Fan-Contorl/blob/master/fan_control.py)

建立資料夾存放要用到的程式：

`mkdir -p Scripts`

`cd Scripts/`

下載程式：

```shell
wget https://raw.githubusercontent.com/DriftKingTW/Raspberry-Pi-PWM-Fan-Contorl/master/fan_control.py
```

開啟試試能不能成功控制：

`python fan_control.py`

沒問題的話就可以把程式設定為開機自動啟動：

編輯 `rc.local`

```shell
sudo vim /etc/rc.local
```

在 `exit 0` 之上加入以下指令：

```shell
python /home/pi/Scripts/fan_control.py &
```

{% colorquote info %}
檔案路徑可能會不同，記得使用絕對路徑。最後的 "&" 符號代表背景執行。
{% endcolorquote %}

如果你不想知道原理，可以直接轉跳閱讀：[讀取轉速訊號](#讀取轉速訊號)

PWM 控制原理簡單來說就是：輸出方波，利用工作週期（Duty Cycle 又稱佔空比）來控制風扇的轉速，假設方波訊號處於高電位所佔一個週期的時間是 50% 代表讓風扇處於半速狀態，以此類推 75% 即是風扇 75% 速度，更詳細可以參考維基：[脈衝寬度調變](https://zh.wikipedia.org/zh-tw/%E8%84%88%E8%A1%9D%E5%AF%AC%E5%BA%A6%E8%AA%BF%E8%AE%8A)

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574611525/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_AABD9EE6FAB5-1.jpg)

接著來分析程式的部分，這邊使用 Python 來做控制：

```python
# 載入需要的函式庫
import RPi.GPIO as GPIO
import time
import signal
import sys
import os

# 設定可照自身情況調整
FAN_PIN = 18            # PWM 控制腳位，設定成你想接的位置即可，注意是 BCM 編號
WAIT_TIME = 1           # 每次控制的更新頻率，單位為秒
PWM_FREQ = 25           # PWM 頻率，這邊根據 Noctua 規格使用 25kHz，其他風扇弄個 50 之類即可，若動作怪怪的可以在自行測試

MIN_TEMP = 40           # 最小啟動溫度
MAX_TEMP = 70           # 最高溫度
FAN_LOW = 40            # 風扇低轉速
FAN_HIGH = 100          # 風扇高轉速
FAN_OFF = 0             # 風扇關閉
FAN_MAX = 100           # 風扇全開

# 利用 vcgencmd 取得CPU溫度
def getCpuTemperature():
    res = os.popen('vcgencmd measure_temp').readline()
    temp =(res.replace("temp=","").replace("'C\n",""))
    #print("temp is {0}".format(temp)) # 把註解取消可觀察溫度
    return temp

# 設定風扇溫度
def setFanSpeed(speed):
    fan.start(speed)
    return()

# 控制風扇動作
def handleFanSpeed():
    temp = float(getCpuTemperature())
    # 如果溫度小於設定的最小啟動溫度即把風扇關閉
    if temp < MIN_TEMP:
        setFanSpeed(FAN_OFF)
        #print("Fan OFF") # 把註解取消可查看風扇訊息
    # 如果溫度超過最高溫度將風扇 100% 開啟    
    elif temp > MAX_TEMP:
        setFanSpeed(FAN_MAX)
        #print("Fan MAX") # 把註解取消可查看風扇訊息
    # 如果溫度介於最小及最大值之間，計算動態風扇速度
    # 每一階(step)為：(高轉速-低轉速)/(最高溫度-最小溫度)
    # 利用算出來的 step 算出線性的簡易 轉速/溫度 反應曲線
    else:
        step = (FAN_HIGH - FAN_LOW)/(MAX_TEMP - MIN_TEMP)   
        temp -= MIN_TEMP
        setFanSpeed(FAN_LOW + ( round(temp) * step ))
        #print(FAN_LOW + ( round(temp) * step )) # 把註解取消可查看風扇訊息
    return ()

# 主程式
try:
    # 設定 GPIO
    GPIO.setwarnings(False)
    GPIO.setmode(GPIO.BCM)
    GPIO.setup(FAN_PIN, GPIO.OUT, initial=GPIO.LOW)
    fan = GPIO.PWM(FAN_PIN,PWM_FREQ)
    setFanSpeed(FAN_OFF)
    # 無限迴圈，按照設定的時間間隔不斷偵測溫度來設定轉速
    while True:
        handleFanSpeed()
        time.sleep(WAIT_TIME)

except KeyboardInterrupt: # 如果使用者按下 ctrl+c 的例外處理
    setFanSpeed(FAN_HIGH)
    #GPIO.cleanup() # 此註解可以清除本程式用的 GPIO 設定，如果要用上一行的指令本行保留註解狀態
```

### 讀取轉速訊號

程式同樣放在 Github 上：[DriftKingTW/Raspberry-Pi-PWM-Fan-Control](https://github.com/DriftKingTW/Raspberry-Pi-PWM-Fan-Control/blob/master/read_fan_speed.py)

```shell
wget https://raw.githubusercontent.com/DriftKingTW/Raspberry-Pi-PWM-Fan-Control/master/read_fan_speed.py
```

開啟即可開始輸出風扇的轉速(RPM)，預設每秒偵測一次

`python read_fan_speed.py`

分析程式：

```python
# 載入需要的函式庫
import RPi.GPIO as GPIO
import time

# 腳位設定
TACH = 24       # 風扇轉速輸出腳位
PULSE = 2       # Noctua 規格為風扇轉一圈有兩次脈衝，大部分風扇也是兩次，少數四次
WAIT_TIME = 1   # 單位為秒，每次更新時間

# GPIO 設定
GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)
GPIO.setup(TACH, GPIO.IN, pull_up_down=GPIO.PUD_UP) # Pull up to 3.3V

# 要用到的全域變數
t = time.time()
rpm = 0

# 計算 RPM 的函數
def fell(n):
    global t
    global rpm 

    dt = time.time() - t    # 取得時間計算出週期
    if dt < 0.005: return   # 無視過短的脈衝（雜訊）

    freq = 1 / dt   # 取得頻率（週期倒數）
    rpm = (freq / PULSE) * 60   # 計算RPM：將頻率除以風扇轉一圈的脈衝數，在乘以60（每分鐘）
    t = time.time() # 更新時間

# 新增監聽器，偵測到波形降下來的時候呼叫 fell() 計算一次 RPM
GPIO.add_event_detect(TACH, GPIO.FALLING, fell)

# 主程式
try:
    # 無限迴圈不斷偵測
    while True:
        print "%.f RPM" % rpm   # 輸出轉速到螢幕上
        rpm = 0 # 歸零
        time.sleep(WAIT_TIME)   # 每秒偵測一次

except KeyboardInterrupt: # 同上處理 ctrl+c 例外
    GPIO.cleanup()  # 清除本程式用的 GPIO 狀態
```

# 參考

[Noctua PWM specifications white paper](https://noctua.at/media/wysiwyg/Noctua_PWM_specifications_white_paper.pdf)

[NF A4x10 5V PWM](https://noctua.at/en/nf-a4x10-5v-pwm/specification)

[Wiring Pi](http://wiringpi.com/)

[wiringPi updated to 2.52 for the Raspberry Pi 4B](http://wiringpi.com/wiringpi-updated-to-2-52-for-the-raspberry-pi-4b/)

[#138 Variable Speed Cooling Fan for Raspberry Pi using PWM and PID controller](https://www.youtube.com/watch?v=oJ32CMxliCQ)

[rxseger/tachfan.py](https://gist.github.com/rxseger/2b27e661221b6f350b859d13f256cf29)

[raspberry-gpio-python](https://sourceforge.net/p/raspberry-gpio-python/wiki/PWM/)