#Unity 
Galium maximowiczii 2023-12-26 17:12:33  
```csharp
timeCounter += Time.deltaTime;
if (timeCounter >= 5)
{
	timeCounter -= 5;
}
```
  
Galium maximowiczii 2023-12-26 17:12:41  
主程说推荐这么写  
  
Galium maximowiczii 2023-12-26 17:12:42  
为什么  
  
Trarizon 2023-12-26 17:17:31  
不然呢（  
  
Galium maximowiczii 2023-12-26 17:22:49  
@Trarizon 为什么不是直接 timeCounter = 0啊  
  
Trarizon 2023-12-26 17:23:31  
deltatime不是整数啊  
  
Trarizon 2023-12-26 17:24:28  
timecounter-=5后一般来说应该会留个0.几  
  
Trarizon 2023-12-26 17:25:38  
多来几次就出误差了  
  
Trarizon 2023-12-26 17:28:14  
就假设deltatime是0.3，4帧1.2秒，7帧后2.1秒。如果直接设0的话2秒会变成8帧