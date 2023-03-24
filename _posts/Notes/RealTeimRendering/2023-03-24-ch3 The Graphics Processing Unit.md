---
layout: single
title: Ch3. The Graphics Processing Unit
date: 2023-03-24 10:05:09 +0800
excerpt: 筆記
categories:
- 筆記
tags:
- Render
- Note
mermaid: true
---

### Data-Parallel Architectures
&emsp;&emsp;CPU用來處理大量資料結構與程式碼。  
&emsp;&emsp;為了避免停頓會使用一些技術
* 分支預測(branch prediction)  
&emsp;&emsp;預測分支指令的執行路徑，減少分支錯誤導致的停頓。
* 指令重組(instruction reordering)  
&emsp;&emsp;重排指令的執行順序，以最大化指令之間的並行性。
* 動態重命名暫存器(Register renaming)  
&emsp;&emsp;動態重命名暫存器，避免由於名稱衝突而導致的停頓。
* 預取指令(Cache prefetching)  
&emsp;&emsp;預取指令和數據到高速緩存中，減少因為緩存未命中而導致的停頓。  

&emsp;&emsp;GPU由大量處理器(shader cores)組合而成。GPU 可以像流水線一樣依次處理類似的數據集合以及高速並行處理，如頂點或像素。且計算是獨立進行，不需要依靠其他處理器。  
&emsp;&emsp;GPU 的優化是針對 *吞吐量(throughput)* 進行的，吞吐量被定義為數據可以被處理的最大速率。