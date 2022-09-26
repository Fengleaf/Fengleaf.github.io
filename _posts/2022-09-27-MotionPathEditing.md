---
layout: single
title: Motion Path Editong
date: 2022-09-26 16:24:10
excerpt: Unity專案
categories:
  - Unity專案
tags:
  - Unity
  - 專案
  - 程式
---

## 專案介紹

臺灣科技大學資訊工程系 3D 電腦遊戲設計(I)第一個專案。

團隊成員: 2 人

透過設置路徑點，可以讓已經套用動畫的模型依照路徑前進，並配合路徑長度播放完整的動畫。專案同時包含載入模型、讀取 BVH 檔、調整路徑與模擬。

### 操作方法

![](/assets/imgs/Unity/MotionPathEditing/UI.jpg)

#### MENU-File

讀取 BVH 檔案 / 存取 路徑檔案。

#### MENU-Edit

清出場景、相機控制、播放。

#### MENU-List (左邊)

選取場景中不同的 BVH。

#### MENU-Edit (左邊)

指定 BVH 的顯示與否、Motion 連接、Motion blend。

### 畫面截圖

![](/assets/imgs/Unity/MotionPathEditing/1.png)
![](/assets/imgs/Unity/MotionPathEditing/2.png)
![](/assets/imgs/Unity/MotionPathEditing/3.png)
![](/assets/imgs/Unity/MotionPathEditing/4.png)

### 操作影片

{% include video id="sJjJ9F3Orag" provider="youtube" %}

### 連結

> [Github](https://github.com/Fengleaf/Motion-Path-Editing)  
> [Projtct](https://drive.google.com/drive/u/1/folders/1NVQ2gY32uHuJtmARXA7mKlgu8UxgMkSP)

## 技術文件

### 讀取 BVH

BVH 檔案分成兩個區塊，HIERARCHY 與 Motion

- HIERARCHY
  表示骨骼的位置、旋轉、子骨骼等資訊，其中 CHANNELS 表示骨骼使用到的維度資訊，position 表示位置、rotation 表示旋轉。

```BVH
HIERARCHY
ROOT Hips
{
	OFFSET	0.00	0.00	0.00
	CHANNELS 6 Xposition Yposition Zposition Zrotation Xrotation Yrotation
	JOINT LeftHip
	{
		OFFSET	 3.430000	 0.000000	 0.000000
		CHANNELS 3 Zrotation Xrotation Yrotation
		JOINT LeftKnee
		{
			OFFSET	 0.000000	-18.469999	 0.000000
			CHANNELS 3 Zrotation Xrotation Yrotation
			JOINT LeftAnkle
			{
				OFFSET	 0.000000	-17.950001	 0.000000
				CHANNELS 3 Zrotation Xrotation Yrotation
				End Site
				{
					OFFSET	 0.000000	-3.119999	 0.000000
				}
			}
		}
	}
}
```

- MOTION
  表示整個動畫每一個 frame 所有骨骼的旋轉，因為骨骼每一塊都是固定連接的，所以除了 root 以外不會有位置資訊。其中每一行的數字對應到 HIERARCHY 中美一塊骨骼的 CHANNELS，一行表示一個 frame。

```
MOTION
Frames:     20
Frame Time: 0.033333
 0.00	 39.68	 0.00	 0.65	-14.85	-0.72	 6.03	 18.85	-1.96	 1.84	 1.50	 13.41	-3.84	-3.16	 1.70
 0.00	 39.68	 0.00	 0.62	-14.79	-0.82	 6.08	 18.81	-1.97	 1.73	 1.48	 13.56	-3.84	-3.33	 1.69
```

因此讀取 BVH 時需要儲存 HIERARCHY 與 MOTION。

我們先為整個 BVH 定義兩個 class 來儲存資料，第一個是 BVHJoint，表示一個骨骼，當中包含 Channels 的順序、MOTION 中的對應數值，實際物件 GameObject 的骨骼以及父節點。

```C#
public class BVHJoint : MonoBehaviour
{
    public const string XPosition = "Xposition";
    public const string YPosition = "Yposition";
    public const string ZPosition = "Zposition";
    public const string XRotation = "Xrotation";
    public const string YRotation = "Yrotation";
    public const string ZRotation = "Zrotation";

    public BVHJoint parentJoint;
    public List<string> channels = new List<string>();
    public List<Dictionary<int, float>> frames = new List<Dictionary<int, float>>();
    public Dictionary<string, GameObject> bones = new Dictionary<string, GameObject>();
```

第二個則是整個 BVH，儲存一些像是 MOTION 中 frame time 與 Frames，儲存所有骨骼以及未來需要用到的路徑資料。

```C#
public class BVH : MonoBehaviour
{
    public PathManager pathManager;
    public List<string> motionString = new List<string>();
    public BVHJoint jointPrefab;
    public GameObject bonePrefab;
    public int MoveSpeed = 5;
    public BVHJoint root;
    public List<BVHJoint> joints;
    private int frameNumber;
    private float frameTime;
    public List<Vector3> originPathPointOrientation;
    private List<Vector3> pathPoints;
    private List<Vector3> orientationPoints;
```

讀取時，首先對於 HIERARCHY，逐行讀取，一開始會讀到 ROOT，表示整個骨骼的根節點，繼續讀取得到 OFFSET 和 CHANNELS，因為是 ROOT 所以 OFFSET 基本上都是 0，而 CHANNELS 則有六個，XYZ 的位置和旋轉。  
接著讀到 JOINT，表示一個新的子節點骨骼，接著一樣得到 OFFSET 和 CHANNELS 直到最後一個節點會得到 End Site 表示結束。

```C#
if (inputs[0] == "ROOT")
{
    jointName = line.Split(' ')[1];
    bvh.AddRoot(inputs[1]);
    yield return null;
}
else if (inputs[0] == "OFFSET")
{
    Vector3 offset = new Vector3(Convert.ToSingle(inputs[1]), Convert.ToSingle(inputs[2]), Convert.ToSingle(inputs[3]));
    bvh.SetJointOffset(jointNames[jointNames.Count - 1], offset);
}
else if (inputs[0] == "CHANNELS")
{
    List<string> channels = new List<string>();
    for (int i = 0; i < Convert.ToInt32(inputs[1]); i++)
    {
        channels.Add(inputs[i + 2]);
        bvh.motionString.Add(jointName + " " + inputs[i + 2]);
    }
    bvh.SetJointChannels(jointNames[jointNames.Count - 1], channels);
}
// 新的關節
else if (inputs[0] == "JOINT")
{
    // 堆疊最上層為父關節
    jointName = inputs[1];
    bvh.AddJoint(inputs[1], jointNames[jointNames.Count - 1]);
    yield return null;
}
else if (inputs[0] == "End")
{
    jointName = jointNames[jointNames.Count - 1] + " End Site";
    bvh.AddJoint(jointName, jointNames[jointNames.Count - 1]);
}
```
### 實現動畫
有了資料之後就要依照MOTION中的資料來實現動畫，透過每一個frame time來給每一個骨骼新的旋轉。同時，如果只套用MOTION中的資料，動畫會有明顯一格一格切換的頓感，所以我們再為每個Frame再插入更多Frame，利用線性插值的方式取得兩個資料間的數值並套用上去，得到更順滑的動作。  
接著只要依序呼叫每個BVHJoint.UpdateToFrame即可。
```C#
public void UpdateToFrame(int frameNumber, float time)
{
    if (frameNumber >= frames.Count)
        return;
    Dictionary<int, float> frameData = frames[frameNumber];
    // 取得下一次的旋轉
    Vector3 next = GetRotation(frameNumber, frameData).eulerAngles;
    // 取得下一次的位置
    Vector3 position = GetPosition(frameNumber, frameData);
    Vector3 interpolated = next;
    // 線性插值
    // 公式: x = x0 + (x1 - x0)t 
    if (frameNumber > 0)
    {
        Vector3 now = GetRotation(frameNumber - 1, frameData).eulerAngles;
        interpolated = now + time * (next - now);
    }
    transform.localPosition = position;
    transform.localRotation = Quaternion.Euler(interpolated);
}
```
### 綁定人物
BVH中的骨骼可以用來對應到實際的模型，找要找到骨骼名稱間的對應關係即可。這邊使用UnityChan來作範例。
```C#
private Transform SearchHumanBoneTransformByName(string name)
{
    HumanBodyBones temp = new HumanBodyBones();
    switch(name)
    {
        case "Hips":
            temp = HumanBodyBones.Hips;
            break;
        #region 左下半邊 ( 跟 Motion 的資料顛倒 )
        case "LeftUpLeg":
        case "LeftHip":
            temp = HumanBodyBones.RightUpperLeg;
            break;
        case "LeftLowLeg":
        case "LeftKnee":
            temp = HumanBodyBones.RightLowerLeg;
            break;
        case "LeftFoot":
        case "LeftAnkle":
            temp = HumanBodyBones.RightFoot;
            break;
        // -----
    }
}
```
同樣的不斷更新資料上去來移動人物。
```C#
public void UpdateMotionPos(GameObject people)
{
    int CurrentIndex = 0;
    BVH bvhPeople = people.GetComponent<BVH>();
    BVHJoint[] joints = bvhPeople.joints.ToArray();
    
    for(int i = 0; i < joints.Length; i++)
    {
        Transform TempBonesTran = SearchHumanBoneTransformByName(joints[i].name);
        if (TempBonesTran == null)
        {
            Debug.Log("No match: " + joints[i].name);
            continue;
        }

        // 把 Motion 套上去
        Quaternion org = new Quaternion(MotionPos[CurrentIndex * 4],
            MotionPos[CurrentIndex * 4 + 1],
            MotionPos[CurrentIndex * 4 + 2],
            MotionPos[CurrentIndex * 4 + 3]);
        
        Joints[CurrentIndex++].transform.rotation = joints[i].transform.rotation * org;
    }

    // 位移
    Vector3 pos = joints[0].transform.localPosition;

    if (!IsSetOffsetY && pos != Vector3.zero)
    {
        OffsetY = pos.y;
        IsSetOffsetY = true;
    }

    pos.y -= OffsetY;
    this.transform.localPosition = pos;
}

```
### 連接
兩個BVH檔的動作可以做連接，來得到一個更長的動畫，首先從第二個動畫的前幾個frame當中，找出與第一個動畫的最後一個frame最接近的frame，將兩個frame中間做插值後連接上去。