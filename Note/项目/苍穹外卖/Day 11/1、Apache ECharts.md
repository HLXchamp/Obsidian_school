### 介绍

![[Pasted image 20240531160939.png]]

![[Pasted image 20240531161028.png]]

![[Pasted image 20240531161045.png]]

![[Pasted image 20240531161101.png]]

### 入门案例

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>ECharts</title>
    <!-- 引入刚刚下载的 ECharts 文件 -->
    <script src="echarts.js"></script>
  </head>
  <body>
    <!-- 为 ECharts 准备一个定义了宽高的 DOM -->
    <div id="main" style="width: 600px;height:400px;"></div>
    <script type="text/javascript">
      // 基于准备好的dom，初始化echarts实例
      var myChart = echarts.init(document.getElementById('main'));
      // 指定图表的配置项和数据
      var option = {
        title: {
          text: 'ECharts 入门示例'   //标题
        },
        tooltip: {},
        legend: { 
          data: ['销量']   //用例
        },
        xAxis: {
          data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']   //x轴
        },
        yAxis: {},
        series: [
          {
            name: '销量',
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20]   //具体数据
          }
        ]
      };
      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option);
    </script>
  </body>
</html>
```

![[Pasted image 20240531161347.png|550]]

![[Pasted image 20240531161424.png]]