# React NBA

## Install
```
Nba web Setup
Step1: create nba-web project
create-react-app nba-web
cd nba-web
npm install --save nba@4.8
npm install --save d3-shotchart

npm start
```
* 安装ant design

1. ```npm install --save antd```
2. ```npm install -S lodash```




## Design

### component 划分

MVVM Model-View-ViewModel pattern

- 数据传递
  - profile
  - shot data

### 设计
- 两个component 分别拿
- 合并成2 child components; parent 去拿数据;通过props传给child
  - 任何数据的变化，只要parent去处理再分发

```
1. topbar
2. profile
3. shot

        APP
        / \
      topbar Main (fetch data from nba-client)
              / \ （by props）
          profile  shotchart (flexbox)
```

* NBA存在跨域访问限制所以自己用自己创建的server
  - 前端broswer向后端拿数据属于CORS
  - 但是node 属于后端，所以通过node 可以和NBA server进行通信
* 跨域
  * 端口不一致
  * IP不一致

## 如何通信

### 外部通信
  1. NBA web通过调用nba-client.js interface来获获取play 和他们的shot info


  ```
   NBA WEB  <--request/response [nba-client.js] -->   Node Server    <--node index.js-->     NBA server
                                                    Node API()                         playerInfo/shot data
                                      const SERVER_URL = 'http://35.235.84.235:5000';
  ```
### 内部数据通信

  ```
           p (define call back func回调) c的改变其实是父组件改变父组件
      /         \
    child       child          
  ```
  1. parent => child : props
    I. 在parent里面设置属性A
    II. 在child里面通过 props.A 拿到
  2. child => parent: callback
    - 在parent里面定义call back 函数，并且把这个callback 函数通过props传给child
    - 在child使用props的值时，它是通过调用parent传下来的这个callback函数
    - child到parent的数据修改，实际是parent修改parent
  3. child => child
    - 必须通过parent component
    - 先调用2.再执行1

  * setstates 变化=> render变化
  * UI 变化=> data 变化

## 各个函数

### nba-client.js

1. 向外暴露nba库 API
2. 暴露统计states属性

### Main.js

1. 获取数据
- fetch nba from nba-client.js
2. 数据初始化定义
  - playerInfo(personal data & shot data) 属于main component的私有属性，用 state={ playerInfo: {}} 来装变动的球员信息
3. 调用和使用数据
  - componentDidMount() (lifecycle)
    - 像node server（nba-client） 发送http request
    - connect with server
4. 通过props将state 传给profile 和 shotchart

###  Profile.js

1. 解构从main里面通过props传下来的playerInfo
2. 展示在web上{`${variable}`}

### ShotChart.js

1. import third party library d3-hexbin & d3-shotchart into public->index.html
2. 获取投球数据
  - fetch nba from nba-client.js
3. 在componentDidMount()里调用API
```
nba.stats.shots({
    PlayerID: this.props.playerId
})
```
4. 拿到投球数据存在response

### shotcut

* 创建component
```
rcc + tab
```

### filter
```
filter       ShotChart  <--------|
|------------------------------>main

```
* 新的层级关系
  - 便于数据管理
```
                    Main
                 /      \ (props) playerId
            Profile   dataview
                     /      \ (props) playerId
                  ShotChart filter （sibling 之间的通信必须通过parent)
                             minCount/chartType/Tooltips
```

- reat UI library with lots of components
- slider

## CounterSlider.js
* control components 双向绑定
* 用stats 记录私有属性

* filter data change ====call back====> dataview ========> ShotChart

## TOOLTIPS

```
Tooltips      

```
## React 其他知识点


### react lifecycle

[lifecycle](https://docs.google.com/document/d/15VHlygOAsuG1P9YOkhBR3MMhzI1psHfWMBLUVwoVb-g/edit)

### fetch ... then 通fetch向后端获取promise,创建一个request 请求

* then 什么时候被调用？
  - 首先，then是通过promise进行链式调用
  - 当拿到信息是then才会被调用，在调用之前，promise有3种状态

### promise 的状态

* pending
* resolve
* reject

### Main 传 info 给profile 通过props


### margin 塌陷

```
margin A botton 10
                   ===> margin 10 Effect
margin B top 10
````

### react 解构赋值 distructure

* nba.findPlayer('Stephen Curry').playerId 解构用法
* playerID: xxx 赋值用法

```
{ PlayerID: nba.findPlayer('Stephen Curry').playerId}
```

### prop-types 校验


### debounce
* import lodash

防抖：防止用户在短时间滑动造成阻塞

### componentDidMount vs. componentDidUpdata


componentDidMount:只渲染一次
componentDidUpdata：多次渲染
