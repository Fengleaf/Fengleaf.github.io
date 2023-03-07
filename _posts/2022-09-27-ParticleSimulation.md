---
layout: single
title: Particle Simulation
date: 2022-09-26 17:45:41
excerpt: Unity專案
categories:
  - 作品
tags:
  - Unity
  - 專案
  - 程式
---

## 專案介紹

臺灣科技大學資訊工程系 3D 電腦遊戲設計(I)第三個專案。

團隊成員: 2 人

利用粒子來實現布料模擬。

### 操作方法

![](/assets/imgs/Unity/ParticleSimulation/Game.png)
### 使用
右側為 MENU，可透過滑鼠拖曳上下滾動。
### MENU-Particle
直接點選布料的 Particle 可以透過 Cursor 移動 Particle，並且可以透過 MENU 更改布料的屬性，如演算法 ( `Euler` / `Runge-Kutta 2` / `Runge-Kutta 4` )、Particle 固定與否、Particle 質量。
而新增布料前，可以設定其初始位置、粒子間距、邊長等參數，按下 Add 按鈕新增一塊新的布料。

### MENU-Display
可以選擇是否顯示 `彈簧與粒子的受力` `粒子` `布料材質`

### Control-Display
可以控制播放、Particle 參數的存讀檔、以及清除場景回到初始狀態。

### 畫面截圖

![](/assets/imgs/Unity/ParticleSimulation/1.png)
![](/assets/imgs/Unity/ParticleSimulation/3.png)
![](/assets/imgs/Unity/ParticleSimulation/4.png)

### 操作影片

{% include video id="8LEHbU6n7h0" provider="youtube" %}

### 連結

> [Github](https://github.com/Fengleaf/Particle-Simulation)  
> [Projtct](https://drive.google.com/drive/folders/107tbvvs6LjKEdFei2Zdv0gKf9bZXn7ol?usp=sharing)

## 技術文件
### 粒子生成
每塊布料用一個ClothSystem.cs 來管理。需要儲存每一顆粒子，它們的位置還有為了能繪製實際圖片所需要的UV和三角形頂點資訊。  
布料模擬是利用彈簧系統，我們使用Mass-spring model架構來進行模擬。因此還需要建立每一顆粒子之間的彈簧，
```csharp
public List<GameObject> Particles = new List<GameObject>();
public List<ParticleCollider> Colliders = newList<ParticleCollider>();
public List<Vector3> Vertexes = new List<Vector3>();
public List<Vector2> UVs = new List<Vector2>();
public List<int> TrianglesIndexes = new List<int>();
private List<SpringSystem> springArray = new List<SpringSystem>();
```
![](/assets/imgs/Unity/ParticleSimulation/MassSpringModel.jpg)
如上圖Mass-spring model，一塊布料的粒子之間可以有三種受力，包含拉力、剪切力與彎曲力，分別可以藉由不同粒子間的連接來達成彼此的約束進而模擬這三種力。 

首先產生粒子，產生的同時計算uv與彈簧。因為布料為方形，uv座標中左下角為(0,0)，右上角為(1,1)，所以粒子的索引值除以邊長就能得到它的uv座標。
```csharp
for (int i = 0; i < SideCount; i++)
{
    for (int j = 0; j < SideCount; j++)
    {
        // 產生粒子
        Vector3 position = new Vector3(i * UnitDistance + InitialPosition.x, InitialPosition.y, j * UnitDistance + InitialPosition.z);
        Vertexes.Add(position);
        GameObject particle = Instantiate(ParticlePrefab, position, Quaternion.identity, transform);
        forceLineRenderers.Add(particle.transform.GetComponent<LineRenderer>());
        particle.name = $"Particle {i} * {j}";
        Particles.Add(particle);
        Colliders.Add(particle.GetComponent<ParticleCollider>());
        userAppendForce.Add(Vector3.zero);
        wallAppendForce.Add(Vector3.zero);

        // UV
        float u = (float)i / (SideCount - 1);
        float v = (float)j / (SideCount - 1);
        UVs.Add(new Vector2(u, v));

        // 速度，初始化為0
        speedArray.Add(Vector3.zero);

        // 每個點連接彈簧
        AddSpringWithIndex(i * SideCount + j, particle);
    }
}
// 為了貼材質計算三角形，相鄰的三個點會組成一個三角形
// 三角形
// 相鄰的三個點形成三角形
// 。
// 。。
for (int i = 0; i < SideCount - 1; i++)
{
    for (int j = 0; j < SideCount - 1; j++)
    {
        // Index 資訊
        int index = i * SideCount + j;
        TrianglesIndexes.Add(index);
        TrianglesIndexes.Add(index + 1);
        TrianglesIndexes.Add(index + SideCount);
        TrianglesIndexes.Add(index + 1);
        TrianglesIndexes.Add(index + 1 + SideCount);
        TrianglesIndexes.Add(index + SideCount);
    }
}
mesh.vertices = Vertexes.ToArray();
mesh.uv = UVs.ToArray();
mesh.triangles = TrianglesIndexes.ToArray();
```
![](/assets/imgs/Unity/ParticleSimulation/Triangle.jpg)
```csharp
private void AddSpringWithIndex(int index, GameObject parent)
{
    int NextIndex;
    // Structural Springs
    // 向上
    NextIndex = index + 1;
    // 確保在同一行
    if (NextIndex / SideCount == index / SideCount)
    {
        springArray.Add(new SpringSystem(index, NextIndex, UnitDistance));
        NewLineRenderer(parent, structSpringMat);
    }
    // 向右
    NextIndex = index + SideCount;
    if (NextIndex / SideCount < SideCount)
    {
        springArray.Add(new SpringSystem(index, NextIndex, UnitDistance));
        NewLineRenderer(parent, structSpringMat);
    }
    // Shear Springs
    // 右上
    NextIndex = index + SideCount + 1;
    // 避免超出邊界且要在隔壁
    if (NextIndex / SideCount < SideCount && NextIndex / SideCount == index / SideCount + 1)
    {
        springArray.Add(new SpringSystem(index, NextIndex, UnitDistance * Mathf.Sqrt(2)));
        NewLineRenderer(parent, shearSpringMat);
    }
    // 左上
    NextIndex = index - SideCount + 1;
    if (NextIndex > 0 && NextIndex / SideCount < SideCount && NextIndex / SideCount == index / SideCount - 1)
    {
        springArray.Add(new SpringSystem(index, NextIndex, UnitDistance * Mathf.Sqrt(2)));
        NewLineRenderer(parent, shearSpringMat);
    }
    // Bending Springs
    // 向上
    NextIndex = index + 2;
    if (NextIndex < SideCount)
    {
        springArray.Add(new SpringSystem(index, NextIndex, UnitDistance * 2));
        NewLineRenderer(parent, bendSpringMat);
    }
    // 向右
    NextIndex = index + SideCount * 2;
    if (NextIndex / SideCount < SideCount)
    {
        springArray.Add(new SpringSystem(index, NextIndex, UnitDistance * 2));
        NewLineRenderer(parent, bendSpringMat);
    }
}
```
### 粒子模擬
彈簧之間互相拉扯，根據公式計算力以及重力。
```csharp
public Vector3 CountForce(Vector3 startSpeed, Vector3 endSpeed, Vector3 startPos, Vector3 endPos)
{
    // Damped spring
    float distance = Vector3.Distance(startPos, endPos);
    return -(Ks * (distance - OriginLength) + Kd * Vector3.Dot(startSpeed - endSpeed, startPos - endPos) / distance)
        * (startPos - endPos) / distance;
}
```
```csharp
Vector3 tempForce = springArray[i].CountForce(startSpeed, endSpeed, startPos, endPos);
// 彈簧拉扯，對起始粒子來說是正向，對終點粒子來說是負向
tempspeedArray[startIndex] += tempForce / Mass * TimeStep;
tempspeedArray[endIndex] -= tempForce / Mass * TimeStep;
// tempspeedArray會存進speedArray
// 重力
speedArray[i] += Vector3.up * Gravity * TimeStep;
```
接著決定力的運作，分別有Euler和Runge Kutta。  
* Euler即最基本的x = vt;
```csharp
private Vector3 EulerMethod(int index, float time)
{
    // x = vt
    return speedArray[index] * time;
}
```
* Runge Kuuta
Runge Kuuta有Runge Kuuta-2與Runge Kuuta-4，即表示做幾次微分運算，讓布料模擬變得更穩定。
![](/assets/imgs/Unity/ParticleSimulation/RungeKutta.jpg)

### 布料碰撞
每次粒子移動時，給它打一條射線，如果偵測到障礙物，就讓它速度=0，即會立刻停止移動、停留在表面上。