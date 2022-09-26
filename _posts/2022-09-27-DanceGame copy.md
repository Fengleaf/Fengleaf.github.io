---
layout: single
title: Dance Game
date: 2022-09-26 17:30:11
excerpt: Unity專案
categories:
  - Unity專案
tags:
  - Unity
  - 專案
  - 程式
---

## 專案介紹

臺灣科技大學資訊工程系 3D 電腦遊戲設計(I)第二個專案。

團隊成員: 2 人

使用相機偵測真人動作來匹配預先錄製的動畫並計算分數的跳舞遊戲。

### 操作方法

![](/assets/imgs/Unity/DanceGame/Game.jpg)
遊戲畫面，等待載入完成後就可以照著右邊的角色的動作去做，左下角會
算分數。左上角 Export File 可以把你做的動作再匯出變成關卡。

### 畫面截圖

![](/assets/imgs/Unity/DanceGame/4.jpg)
![](/assets/imgs/Unity/DanceGame/1.jpg)
![](/assets/imgs/Unity/DanceGame/3.jpg)

### 操作影片

{% include video id="0J9V79OcQ-I" provider="youtube" %}

### 連結

> [Github](https://github.com/Fengleaf/Dance-Game)  
> [Projtct](https://drive.google.com/drive/folders/1-exPNEd7b4MB9FfoBX8HTUHTRNwa5NNc?usp=sharing)

## 技術文件
### 專案Template
使用 Github 專案:  
https://github.com/digital-standard/ThreeDPoseUnityBarracuda  
使用 unity 的 Berracuda 之 ResNet 吃 RGB 影像來分析出 3D 動作。
這個專案可以實時辨識影片或相機照到的畫面然後讓角色作出動作。
### 動作比對
NPC 做出動作後，紀錄每一個 frame 的關節旋轉，在玩家做動作的時
候，紀錄玩家每個 Frame 的動作，然後去比對。  
玩家可能做了一連串的動作，所以先對玩家的動作中找出最接近 NPC
動作的 frame，從那個 frame 開始去對接下來的每個 frame 比對。
```C#
for (int i = 0; i < player.boneFrames.Count; i++)
{
    total = 0;
    // 找最接近
    foreach (KeyValuePair<int, Transform> pair in npc.boneFrames[0])
    {
        Transform npcTransform = pair.Value;
        Transform playerTransform = player.boneFrames[i][pair.Key];
        float distance = ComputeRotationDistance(playerTransform, npcTransform);
        total += distance;
    }
    if (total < min)
    {
        min = total;
        index = i;
    }
}
```
```
for (int j = 0; j < npc.boneFrames.Count; j++)
        {
            if (j + index >= player.boneFrames.Count)
                return 0;
            float total2 = 0;
            foreach (KeyValuePair<int, Transform> pair in npc.boneFrames[j])
            {
                Transform npcTransform = pair.Value;
                Transform playerTransform = player.boneFrames[j + index][pair.Key];
                float distance = ComputeRotationDistance(playerTransform, npcTransform);
                float score = 0;
                if (distance < 40)
                    score = 1;
                else if (distance < 20)
                    score = 2;
                else if (distance < 15)
                    score = 3;
                else if (distance < 10)
                    score = 4;
                total2 += score;
            }
            total += total2;
        }
```

### 邊界判斷
玩家超出偵測邊界之後需要告知玩家，判斷部分主要是透過抓取 Ground Truth 的人體部位比例來得知。當玩家離開相機範圍內時，因 Heatmap 少了 Confidence 較高的點(身
體部位)，導致剩下的 Pixel confidence 都頗低，以至於辨識出來的人體骨
架非常詭異，而骨架詭異的同時，各個身體部位的比例也會變得很詭異，
因此演算法就簡單地透過人體比例來判定。