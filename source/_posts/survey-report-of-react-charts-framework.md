title: React 图表框架的调查笔记
date: 2016-11-23 12:08:28
categories: js
tags: 
- react 
- chart
---

一些 react 图表绘制框架的调查
<!-- more -->

> 想到不久后 react 项目中可能将会遇到一些绘制图表的数据可视化的需要，让我来小小调查一番，面对疾风吧。

Google + Github 了一番，综合相关库的健康状态——维护和人气，我选出下面几个高富帅（人气高，图形丰富，~~我觉得帅~~）。并分别用他们来实现一个图表 demo。

## 1. [recharts](https://github.com/recharts/recharts)

#### 背景

此库应该是我目前所知道最火的 react 图形库之一，在写这篇笔记时，他的 star 已将近 4000。乍一看全是英文的文档，实际上他是 ALIBABA 的团队开源的（GOOD)。 他的核心库为著名的 D3，也就是说
这是由 SVG 绘制的图形，天然的矢量图形。

![rechart-desc](http://7jpqdg.com1.z0.glb.clouddn.com/rechart-3.jpg)


#### 特点

- API 组成形式是将图表的元素分解为灵活的组件，可以生成多种自定义图形
- 在图表组件中可以嵌入 SVG `<defs/>` ，来使用渐变、滤镜等 SVG 的特效 
- 文档比较完整

#### 缺点

- 实时数据下，频繁更新 DOM 的时候，可能会造成性能的问题

#### Demo

```javascript
const RechartsDemo = () => {
	const data = [
		{ name: 'uv1', uv: 20, pv: 40 },
		{ name: 'uv2', uv: 60, pv: 20 },
		{ name: 'uv3', uv: 30, pv: 80 },
		// { name: 'pv', value: 50 },
		// { name: 'pv', value: 20 },
	]
	return (
		<div style={{height: 300}}>
			<ResponsiveContainer >
			 	<ComposedChart width={500} height={300} data={data}>
			 		<defs>
					    <linearGradient id="colorUv" x1="0" y1="0" x2="0" y2="1">
					      <stop offset="5%" stopColor="#8884d8" stopOpacity={0.8}/>
					      <stop offset="95%" stopColor="#8884d8" stopOpacity={0}/>
					    </linearGradient>
					</defs>
				    <XAxis dataKey="name"/>
				    <YAxis/>
				    <CartesianGrid stroke="#eee" strokeDasharray="5 5"/>
				    <Tooltip />
				    <Legend />
				    <Area type="monotone" dataKey="uv" stroke="#8884d8" fillOpacity={1} fill="url(#colorUv)"/>
			    	<Line type="monotone" dataKey="pv" stroke="#ff7000"/>
			  	</ComposedChart>

			</ResponsiveContainer>

		</div>
	)
}

```
![rechart-demo](http://7jpqdg.com1.z0.glb.clouddn.com/rechart-demo.jpg)
<!-- <script async src="//jsfiddle.net/5j2799j4/2/embed/js,result/"></script> -->

## 2. [react-chartjs](https://github.com/reactjs/react-chartjs)

#### 背景

光看他的名字——reactjs/react-chartjs，就知此库背景不简单，出自的 react 官方社区之手，核心库为以轻量和漂亮著称的图形库大家 Chart.js。

![react-chartjs](http://7jpqdg.com1.z0.glb.clouddn.com/react-chartjs.jpg)

#### 特点

- 他的图表渲染由 Canvas 实现，继承了 Canvas 绘图的优点，在大数据量时性能比 SVG 绘图要好。
- 体积比 recharts 小
- 只把 Chart.js 的顶级图表，简单地封装为 react 组件，参数完全可照搬。（各种意义上的方便）

#### 缺点

- 更新活跃度不高（？最后看到是九月份时的更新，大神们不是挖完坑就跑了吧？）
- 依赖 Chart.js@1.1.1，而最新版为 Chart.js@2.4.0，并且官网上是 v2 的文档。
所以 Chart.js 官网推荐使用这两个包 [react-chartjs2](https://github.com/topdmc/react-chartjs2) & [react-chartjs-2](https://github.com/gor181/react-chartjs-2)
（为什么在我翻完这么长的文档才发现，鞠了一把老泪）

<div class="tip">
	此处有坑
</div>

#### Demo ( react-chartjs-2 )

```javascript
var doughnutData = {
    labels: [
        "Red",
        "Blue",
        "Yellow"
    ],
    datasets: [
        {
            data: [300, 50, 100],
            backgroundColor: [
                "#FF6384",
                "#36A2EB",
                "#FFCE56"
            ],
            hoverBackgroundColor: [
                "#FF6384",
                "#36A2EB",
                "#FFCE56"
            ]
        }]
};

const ChartJSDemo = () => {

	return(
		<div>
			<Doughnut data={doughnutData}/>
		</div>

	)
}
```
![react-chartjs-2](http://7jpqdg.com1.z0.glb.clouddn.com/react-chartjs-2.jpg)

## 3. [victory](https://github.com/FormidableLabs/victory)

#### 背景

Victory 为了构建可交互的数据可视化的一组 react 组件，他的组件类型是这三个库中最丰富的，由 SVG 实现底层图形。使用他也可以高度自定义化你的图表，他还有个兄弟库 victory-native，可以用在 react-native 项目中。

![victory-desc](http://7jpqdg.com1.z0.glb.clouddn.com/victory-1.jpg)

#### 特点

- 类型多，很强大
- 可灵活的定制化图形
- 支持 native
- 平滑的动态更新
- 文档全面

#### 缺点

- 寂寞

#### Demo

```javascript
const VictoryDemo = () => {
	const data = [
 		{quarter: 1, earnings: 13000},
  		{quarter: 2, earnings: 16500},
  		{quarter: 3, earnings: 14250},
  		{quarter: 4, earnings: 19000}		
	]
	return (
		<VictoryChart domainPadding={20}>
	        <VictoryAxis
	          // tickValues specifies both the number of ticks and where
	          // they are placed on the axis
	          tickValues={["Quarter 1", "Quarter 2", "Quarter 3", "Quarter 4"]}/>
	        <VictoryAxis
	          dependentAxis
	          // tickFormat specifies how ticks should be displayed
	          tickFormat={(x) => (`$${x / 1000}k`)} />
	        <VictoryBar
	        	data={data}
	        	x="quarter"
	        	y="earnings"/>
        </VictoryChart>
    )
}
```
![victory-demo](http://7jpqdg.com1.z0.glb.clouddn.com/QQ20161123-15.png)


## 总结
