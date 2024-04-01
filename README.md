
# 支持异步加载版本
**在线预览地址：https://lusz.top/picture/h5/#/**
## 基础版
插件地址：https://ext.dcloud.net.cn/plugin?id=9976
## 升级版（支持异步加载）
下载地址：https://github.com/LSZ579/level-tree
预览地址：https://lsz579.github.io/level-tree/
1.支持纯展示
2.支持单选（所有可选、只选子级俩种模式）
3.多选（所有可选、只选子级俩种模式）
4.多选联动
5.效果图
<img src="https://img-blog.csdnimg.cn/b456d4d2196d40c3adee84b291afe7d5.gif" width="40%" >

## 兼容问题
**实测支持小程序、H5、App**
**根据层级路径操作数据，无需每一次操作都遍历所有数据，所以优化良好，响应速度快，无卡顿**（测试的数据量1314条，多选响应速度快）
**总代码量300多行，无其它依赖，体积小**
### 使用说明：

1. 在页面引用组件

```bash
<LoadTree :list="list" ref="tree" :check_type="type"  :check_id="check_id" @checkRadio="checkRadio" :onlyChooseChild="onlyChooseChild" :isFirst="true" @change="change"></LoadTree>
```

### 参数说明
|参数   | 类型  | 是否必填 |  默认值  |  说明 |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| list   | Array   | 是  |  [ ] | 传入的树形数据，结构见下面的例子  |
|  check_type|  string | 否 |  ‘ ’  |radio 单选 multiple 多选
|  onlyChooseChild|  Boolean| 否 |  false | 是否只可选子级
|  isFirst|  Boolean | 是 |  true  | 必须传true
|  check_id| string|否   |' '  |单选时选中的值/默认选中的值   |
| checkRadio| Event|  否 | | 单选的模式下选中值得时候的回调  |
| change| Event| 否  |  |  选中，或者点击某项时的回调 |

## 数据格式

```bash
		[
			{
				name: '一级',
				id: '1',
				children: []
			}
		]
```

## 必看:
 tree数据需要带有checked字段
 数据的id是唯一的id
默认选中需要传checkList，并调用checks方法

## 获取选中的值
单选：check_id
多选：通过ref获取
```bash
	this.$refs.tree.getValue()
```
## 完整的示例代码

```bash
<template>
	<view class="content">
		<LoadTree :list="list" ref="tree" :check_type="type"  :check_id="check_id" @checkRadio="checkRadio" :onlyChooseChild="onlyChooseChild" :isFirst="true" @change="change"></LoadTree>
	</view>
</template>

<script>
	import LoadTree from '@/components/load-tree/index.vue'
	import {treeNode} from './city.js'
	export default {
		components: {
			LoadTree
		},
		data() {
			return {
				list: treeNode,
				check_id:"",
				type:"multiple",
				onlyChooseChild:true
			}
		},
		methods: {
			// 点击或选择回调
			change(val) {
				console.log(val)
			},
			/* 单选回调 */
			checkRadio(check_id){
				this.check_id = check_id
			},
			//多选时-获取选中的值
			getValue(){
				let result= this.$refs.tree.getValue()
			}
		}
	}
</script>

```

## 将继续持续维护和开发
## 获取升级版 vx:  122720267
