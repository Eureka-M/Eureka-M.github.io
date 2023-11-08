---
layout:     post
title:      iview table 加载更多
subtitle:  
date:       2023-11-08
author:     
header-img: 
catalog: true
tags:
  - 工作
typora-root-url: ..
---

### 需求

表格需要的功能如下：

- 拖拽排序 draggable="true"
- 加载更多
- 全选 / 单选

设计大致如下：

![image-20231108115732376](/../img/postImage/image-20231108115732376.png)

### 实现方案

- 监听表格是否滚动到底部，加载更多
- 表格尾部插入一条假数据，Intersection observer 监听假数据 dom，加载更多

由于第一种方案也是需要在表格底部插一条假数据，显示一些加载信息的，如：下拉加载，加载中... 暂无更多~，直接采用第二种方案。

### 实现

1. 插入假数据

    ```
    getData().then((res) => {
    	this.tableData = res.data.list
    	this.total = res.data.total
    	// 插入假数据
    	this.tableData.push({ tip: '加载中...'})
    })
    ```

2. 改最后一行假数据的样式

    - 有一个 check，不好改 render 函数，不然可以使用行合并，合并成一个单元格，这样默认文字可以居中。这样的话，只好用 样式 把数据隐藏了，但是由于要显示 tip，就需要有一列显示，图方便选择了第三列

        ```
        emptyRowHandler(h, params) {
        	let emptyTip = params.row.tip
            if (emptyTip) {
            	return h('div', emptyTip)
            }
            else {
                return h('div', params.row.category)
            }
        }
        ```

    - 假数据行样式

        ```
        rowClassName(row, index) {
        	if (index === this.videoList.length - 1) {
            	return 'empty-row'
            }
        }
        
        .ivu-table .empty-row {
            height: 48px;
            pointer-events: none; // ！！！屏蔽 dragger 事件
            position: relative;
            background: #fff;
        
            > td {
                &:not(:nth-child(3)) {
                	display: none;
            	}
        
                &:nth-child(3) {
                    position: absolute;
                    left: 50%;
                    display: flex;
                    align-items: center;
                }
            }
        }
        ```

3. intersectionObserver 监听假数据行的出现

    ```
    await this.getData()
    
    if (this.videoList.length) {
    	this.$nextTick(() => {
         	this.bindEvent()
    	})
    }
    
    bindEvent() {
        let ele = document.getElementsByClassName('empty-row')[0]
        let io = new IntersectionObserver((entries) => {
            if (entries[0].intersectionRatio > 0 && hasMore) {
            	this.page++
                this.getData()
            }
        })
        io.observe(ele)
    }
    ```

4. getData 里设置话术

    ```
    getData().then((res) => {
    	if (page == 1) {
    		this.tableData = res.data.list
    		this.total = res.data.total
    	}
    	else {
    		// 去掉假数据
    		this.tableData = this.tableData.pop()
    		this.tableData = this.tableData.push(...res.data.list)
    	}
    	
    	// 插入假数据
    	if (this.total > this.tableData.length) {
    		this.tableData.push({ tip: '加载中...'})
    	}
    	else {
    		this.tableData.push({ tip: '暂无更多～'})
    	}
    })
    ```

5. 一些处理
    - 上移下移中的 “下移” 禁用需要判断
    - 全选 selection 剔除掉假数据