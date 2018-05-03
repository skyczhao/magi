title: VS Code入手
date: 2018-04-27 11:05:26
desc: 
tags: [配置,前端] 
---

扎心了，多少年没写的前端今天要捡起来。项目来了人员不够，女人当男人用，男人当畜生用了。😂

开玩笑，不过前端发展之快也出乎意料。11年初次接触，还是裸写html、css、javascript的时候，那是javascript也没有那么重视。不过传说的V8出现，似乎渐渐改变了业界对javascript的看法。而伴随着智能手机(iphone 3GS往后的)带来的移动互联网发展，响应式、渐进式、单页面应用等等的思路与概念不断出现，推陈出新。

今天就回顾一下自己怎么捡回前端的coding手感的。

<!-- more -->

## vscode

> 说实话，这个工具可是比以前常用的Notepad++好用多了，而且自带Emmet - 写前端神器之一。

#### plugin

* ESLint
* Emment
	- https://code.visualstudio.com/docs/editor/emmet
* Auto Close Tag
* Auto Rename Tag
* Path Intellisense
* Project Manager
* beautify
* minify
* Git History

#### config
``` json
{
    "terminal.integrated.shell.osx": "/bin/zsh",
    "editor.fontSize": 14,
    "workbench.colorTheme": "Solarized Dark",
    "editor.quickSuggestions": {
        "other": true,
        "comments": true,
        "strings": true
    },
    "emmet.triggerExpansionOnTab": true
}
```

## 代码
1. 上手即看代码
    * 由多年开发以及公司内部协作经验，最新最准的逻辑都是在代码里面。文档、指引一类一般维护第一版就没有再更新过了。
    * 代码用了vue做脚手架，echarts做图表，其他还不懂的没有更新在此
    
2. 接手简单的功能
    > 这里也说明，代码是需要实践的。
    
    * 功能需求：为原结构中加入两条分类别统计的曲线图
    * 代码样例：因为是javascript，代码还挺容易的，这里贴一下
    
    ```javascript
    // 原代码
    handleTrendChartsData: (volumeTrend) => {
        let xAxis = [],
          volumeTrendData = [],
          dataArray = [],
          max = 0;
        for (let i in volumeTrend) {
          if (!R.contains(fn.getLocalTime(volumeTrend[i].key, 'yyyyMMdd'))(xAxis)) {
            xAxis.push(fn.getLocalTime(volumeTrend[i].key, 'yyyyMMdd'));
          }
        }
        for (let i in xAxis) {
          let t = false;
          for (let j in volumeTrend) {
            if (xAxis[i] == fn.getLocalTime(volumeTrend[j].key, 'yyyyMMdd')) {
              t = true;
              volumeTrendData.push({
                proportion: (volumeTrend[j].proportion * 100).toFixed(2),
                value: volumeTrend[j].count
              });
              // dataArray.push(parseInt(volumeTrend[j].proportion * 100));
              break;
            }
          }
          if (!t) {
            volumeTrendData.push({
              value: 0,
              count: 0
            })
          }
        }
        // max = fn.getMaxNum(dataArray);
        return {
          volumeTrend: volumeTrendData,
          xAxis: xAxis,
          // max: max
        }
      }
    ```
    ```javascript
       // 修改后
      handleTrendChartsMultiData: (data) => {
        let result = {
          xAxis: [],
          volumeTrend: [],
          volumeTrend0: [],
          volumeTrend1: []
        };
    
        // 对齐时间
        let dateArray = [];
        for (let i in data.trend) {
          dateArray.push(data.trend[i].key)
        }
        for (let i in data.trend0) {
          dateArray.push(data.trend0[i].key)
        }
        for (let i in data.trend1) {
          dateArray.push(data.trend1[i].key)
        }
        dateArray = [...new Set(dateArray)].sort(function (a, b) {
          return a - b;
        });
        // 填充数据
        dateArray.forEach(item => {
          if (item) {
            let trendObj = R.find(R.propEq('key', item))(data.trend);
            result.volumeTrend.push(trendObj ? trendObj.count : 0);
    
            result.xAxis.push(fn.getLocalTime(item, 'yyyyMMdd'));
          }
        });
        if (data.trend0 && data.trend0.length > 0) {
          dateArray.forEach(item => {
            if (item) {
              let trend0Obj = R.find(R.propEq('key', item))(data.trend0);
              result.volumeTrend0.push(trend0Obj ? trend0Obj.count : 0);
            }
          });
        }
        if (data.trend1 && data.trend1.length > 0) {
          dateArray.forEach(item => {
            if (item) {
              let trend1Obj = R.find(R.propEq('key', item))(data.trend1);
              result.volumeTrend1.push(trend1Obj ? trend1Obj.count : 0);
            }
          });
        }
    
        return result;
      }
    ```
    * 按需进行修改
    
