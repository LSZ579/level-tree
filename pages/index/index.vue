<template>
	<view class="content">
			<view style="padding: 6px;box-sizing: border-box;">
					<LoadTree :list="list" ref="tree" :check_type="type"  :check_id="check_id" @checkRadio="checkRadio" :onlyChooseChild="onlyChooseChild" :isFirst="true" @change="change"></LoadTree>
					
			</view>
		<view style="height: 80px;">
		</view>
		<view class="fixed-btn">
			<scroll-view  scroll-x style="width: 100vw;">
				<view style="display: flex;align-items: center;">
						<button type="default" @click="show">打印值</button>
						<button type="default" @click="changeType('radio')">单选</button>
						<button type="default" @click="changeType('multiple')">多选</button>
						<button type="default" @click="changeType('')">仅展示</button>
						<button type="default" @click="changeSel(true)">只选子级</button>
						<button type="default" @click="changeSel(false)">选所有</button>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	import LoadTree from '@/components/load-tree/index.vue'
	import {
		treeNode
	} from './city.js'
	export default {
		components: {
			LoadTree
		},
		data() {
			return {
				title: 'Hello',
				list: treeNode,
				check_id:"",
				type:"multiple",
				onlyChooseChild:false
			}
		},
		onLoad() {

		},
		methods: {
			change(val) {
				console.log(val)
			},
			changeSel(value){
				this.onlyChooseChild = value
			},
			changeType(type){
				this.type = type
			},
			/* 单选回调 */
			checkRadio(check_id){
				this.check_id = check_id
			},
			show(){
				let result= this.$refs.tree.getValue()
				console.log(result)
			}
		}
	}
</script>

<style scoped lang="scss">
.fixed-btn{
	width: 100vw;
	
	position: fixed;
	bottom: 0;
	background-color: #FFFFFF;
	height: 60px;
	/* overflow-x: auto; */
	button{
		word-break: keep-all;
		min-width: 80px;
		margin-left: 15px;
	}
}

</style>
