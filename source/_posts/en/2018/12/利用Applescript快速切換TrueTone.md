---
title: 利用Applescript快速切換TrueTone
abbrlink: ba06666d
date: 2018-12-02 00:20:52
tags:
  - MacOS
  - Touchbar
keywords:
  - MacOS
  - Touchbar
  - TrueTone
---

2018 Macbook Pro終於可以使用原先發佈在iPad Pro的True Tone功能，讓螢幕偵測周圍的光線來改變螢幕顏色，使用上相當舒服。
不過如果要使用影像處理或繪圖等對顏色準確度有需求的工作時就得把它暫時關掉才不會影響，然而每次都要進入設定調整也是挺麻煩的，不如利用Applescript爲我們代勞。<!--more-->

# How

[Github - Ture Tone Toggler](https://github.com/DriftKingTW/True-Tone-Toggler)

目前還找不到如何透過command line設定True Tone，所以用Applescript控制設定，可以把它跟BetterTouchTool結合，利用快捷鍵或Touchbar之類快速開關True Tone功能。

此程式碼修改自[Reddit上的一篇討論](https://www.reddit.com/r/applescript/comments/9a5gej/has_anyone_figured_out_how_to_return_the/)，我加上判斷以及提示的功能，讓使用者可以明確知道現在操作是開啓或關閉True Tone。

```applescript
tell application "System Preferences"
	
	if it is running then
		
		quit
		
	end if
	
end tell

delay 0.2

activate application "System Preferences"

tell application "System Events"
	
	tell process "System Preferences"
		
		click button "Displays" of scroll area 1 of window "System Preferences"
		
		delay 0.8
		
		click radio button "Display" of tab group 1 of window "Built-in Retina Display"
		
		set TrueToneCheckbox to checkbox "True Tone" of tab group 1 of window "Built-in Retina Display"
		
		click TrueToneCheckbox
		
		set TrueToneState to "ON"
		
		tell TrueToneCheckbox
			
			if not (its value as boolean) then set TrueToneState to "OFF"
			
		end tell
		
	end tell
	
	quit application "System Preferences"
	
end tell

display notification "True Tone " & TrueToneState with title "True Tone Script" sound name "Submarine"

delay 0.8

say "True Tone has been turned  " & TrueToneState

```

有需要自定義通知的部分可以修改 `display notification` 的部分，不想要語音提示可以註解掉 `say` 那行。

## 參考資料

[Reddit: Has anyone figured out how to return the true/false status for True Tone with AppleScript](https://www.reddit.com/r/applescript/comments/9a5gej/has_anyone_figured_out_how_to_return_the/)

[Displaying Notifications](https://developer.apple.com/library/archive/documentation/LanguagesUtilities/Conceptual/MacAutomationScriptingGuide/DisplayNotifications.html#//apple_ref/doc/uid/TP40016239-CH61-SW1)