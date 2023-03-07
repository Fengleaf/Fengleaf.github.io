---
layout: single
title: space in Space
date: 2022-09-27 22:36:20 +0800
excerpt: Unity遊戲
categories:
- 作品
tags:
- Unity
- 遊戲
---

### 遊戲簡介 
臺灣科技大學資訊工程系 遊戲企畫與設計原理 專案  
團隊成員: 6人  
一場意外導致迷失在太空中的太空員，需要透過行星上的物品來逃離這片太空，設法打倒敵人、完成任務最後成功生還。

### 遊戲玩法  
* Enter: 下一句  
* F: 對話  
* 方向鍵: 移動  
* 空白鍵: 跳躍

### 遊戲系統
* 主要系統
節奏音樂遊戲，畫面下方會有節奏條，當它抵達中央時按下方向鍵即可觸發人物移動。
![](/assets/imgs/Unity/spaceInSpace/Main.jpg)
* 生命值
當沒有在節奏上按下方向鍵或是漏按時，生命值便會減少，生命值扣完玩家就會被傳送到原點重新開始。每五次成功在節拍上時可以恢復生命值。
* 障礙物
障礙物會阻礙玩家前進，當玩家撞到障礙物就會被往後彈。  
![](/assets/imgs/Unity/spaceInSpace/Obstacles.jpg)
* 敵人與特殊物件 
敵人包含Boss和小怪，他們會發出攻擊來使得玩家的生命值降低，需要一些操作技巧來躲避。
    * 小魷魚  
    他們會每隔一定間格發出子彈，攻擊玩家。子彈也會有固定的移動速度，有可能是節拍的兩倍或是一半。  
    ![](/assets/imgs/Unity/spaceInSpace/Enemy.jpg)
    * 雷射  
    雷射每隔一定間格發出，並持續一段時間，玩家一旦碰到就會直接失去所有生命。  
    ![](/assets/imgs/Unity/spaceInSpace/Lazer.jpg)
    * 加速帶  
    玩家經過後會被往前推動一段距離，可以用來快速前進或躲避攻擊。  
    ![](/assets/imgs/Unity/spaceInSpace/Speed.jpg)
    * 黑洞  
    黑洞會直接把玩家吸入，失去所有生命。    
    ![](/assets/imgs/Unity/spaceInSpace/Black.jpg)
    * 白洞  
    與黑洞相反，會將玩家往外推開。  
    ![](/assets/imgs/Unity/spaceInSpace/White.jpg)
    * 傳送門  
    玩家進入後會被傳送到另一個傳送門的位置。 
    ![](/assets/imgs/Unity/spaceInSpace/Tele.jpg)

* 存讀檔
遊戲可以存檔，以從中斷的地方繼續遊玩。

### 遊玩影片
{% include video id="_09EkrfhrQM" provider="youtube" %}

### 連結  
> [Game](https://drive.google.com/drive/folders/1jzYNt_xk2yRsRwoIykv2MM4kn1xZdInk?usp=sharing)

### 主要負責項目
1. 玩家移動
2. 障礙物、敵人、物件
3. 存讀檔