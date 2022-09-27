---
layout: single
title: 事件編輯器
date: 2022-09-27 05:05:05
excerpt: Unity專案
categories:
  - Unity專案
tags:
  - Unity
  - 專案
---

## 專案介紹

自製RPG遊戲用事件編輯器，可以設計一連串事件，包含選項分歧、輸入密碼等。

### 編輯器介紹
![](/assets/imgs/Unity/EventEditor/Editor.jpg)
上圖為編輯器介面，包含多種選項可以設置。
* 距離、方向
* 事件點
    * 觸發方式
    * 觸發條件
    * 命令行

### 詳細介紹
![](/assets/imgs/Unity/EventEditor/DisDir.jpg)
![](/assets/imgs/Unity/EventEditor/DisHint.jpg)
* 距離、方向
可以調整觸發這個事件玩家所需要在多少距離以內，方向則表示物品的正向為左邊或是右邊，來決定scale要調正或負來轉向。同時場景上還會顯示物件與玩家的距離，用以快速決定要調整多少距離限制。

* 事件點
![](/assets/imgs/Unity/EventEditor/EventPoint.jpg)
一個事件點及表示一個獨立的事件，擁有顯示對話、播放動畫、音樂、移動物件或角色、獲取物品或分歧事件...等功能。  
* 事件點-觸發條件
![](/assets/imgs/Unity/EventEditor/Condition.png)
觸發條件包含觸發方式與前置條件。
    * 觸發方式
    包含點擊、觸碰與自動觸發三種。
        * 點擊
        玩家用滑鼠點擊事件時觸發。
        * 觸碰
        玩家角色觸碰到事件點時觸發。
        * 自動
        滿足條件後直接觸發。
    * 觸發條件
    觸發條件包含開關條件與物品條件。
        * 開關條件
        玩家完成某些事件而使得開關打開或關閉，用以當作flag來決定事件是否能夠觸發。
        * 物品條件
        玩家擁有某樣物品幾件的時候可以觸發。
例如，上圖門的事件中，觸發方式為點擊，開關為2號開關(ex: 第一次開門)滿足條件後即會觸發下方待說明的命令行。
* 事件點-執行命令行  

![](/assets/imgs/Unity/EventEditor/Command.jpg)
命令行即為事件觸發後，會依序執行的行為。每一條事件可以透過右鍵最下方的黑色處來新增，旁邊也有三個按鈕-插入、重複、刪除快捷鍵可以使用。  
上圖中第一條指令為Dialogue，即為顯示對話，觸發後便會顯示出對話框。
![](/assets/imgs/Unity/EventEditor/Dialogue.jpg)
Dialogue同時支援一些特殊指令，如\h即為顯示主角名字。
* 事件點-命令行  

![](/assets/imgs/Unity/EventEditor/NewCommand.png)
目前支援的命令為上圖中的項目(Show Balloon未實作)。

    * Condition Branch
    條件分歧，滿足指定條件後執行行為，未滿足條件則執行另一條行為。
    ![](/assets/imgs/Unity/EventEditor/Branch.jpg)
    如上圖條件中，須滿足房間門開關為Off狀態，則會執行Condition Ok Commands，顯示完成對話框。若房間門開關為On，則會顯示失敗。兩條線都可以再繼續設置更多命令或是條件。
    ![](/assets/imgs/Unity/EventEditor/BranchSwitches.png)
    選擇開關條件會顯示遊戲中設置的所有開關以供快速瀏覽。

    * Destroy Event、Dialogue
    ![](/assets/imgs/Unity/EventEditor/DesDia.jpg)
    Destroy Event即為直接破壞這個事件，可用於取得物品時，場頸上的物品需要消失時。  
    Dialogue即為顯示對話框。

    * Gain Item、Set Switches
    ![](/assets/imgs/Unity/EventEditor/ItemSwitches.jpg)
    獲取物品-可以設定要獲得什麼物品與獲得數量。  
    設置開關-可以設定完成什麼事件後，將開關打開作為Flag。

    * Game Over
    ![](/assets/imgs/Unity/EventEditor/GameOver.jpg)
    遊戲結束，會顯示設定的結束畫面，Is Win用來作為判斷是勝利或是失敗，用以顯示不同的畫面。

    * Input
    ![](/assets/imgs/Unity/EventEditor/Input.jpg)
    顯示輸入框，並可以設定正確答案，輸入正確的話執行Correct Commands，錯誤則會執行Wrong Commands。
    ![](/assets/imgs/Unity/EventEditor/InputBox.jpg)

    * Play BGM、Play SE
    ![](/assets/imgs/Unity/EventEditor/Audio.jpg)
    播放背景音樂與音效、可以設定音量。

    * Selection
    ![](/assets/imgs/Unity/EventEditor/Selection.jpg)
    選項分歧，可以設定兩個選項讓玩家選擇，並根據選擇的選項來執行不同的命令。如上圖選擇選項1則為顯示對話框，選項2則為直接遊戲結束。
    
    * Set Animation Parameter
    ![](/assets/imgs/Unity/EventEditor/Animation.jpg)
    設定動畫參數，為了執行unity的Anamation，可以設置參數，包含Trigger、int、bool、floats。
    
    * Set Direction、Player Name、Screen Color
    ![](/assets/imgs/Unity/EventEditor/DNC.jpg)
    設定物件方向、設定輸入玩家名字、設定畫面顏色。
    
    * Transition
    ![](/assets/imgs/Unity/EventEditor/Transition.jpg)
    玩家或物件轉移，可以移動物件或是快速設定位置。根據Speed來決定速度。
    
    * Wait
    ![](/assets/imgs/Unity/EventEditor/Wait.jpg)
    等待，即為等待指定時間後再繼續往下執行。

### 操作影片
{% include video id="hPvCY2s_dd0" provider="youtube" %}