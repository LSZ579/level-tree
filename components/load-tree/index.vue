<template>
	<view class="load-tree">
		<view v-for="(item,index) in renderList" :key="index">
			<view class="leval" @click.stop="handleClick(item,index)"
				style="box-sizing: border-box;display: flex;justify-content: space-between;">
				<view class="leval">
					<view @click.stop="showLeval(item,index)" :class="[item.open?'down':'arrow-right']"
						v-if="((item.children&&item.children.length>0)||item.hasChildren)">
						<view class="arrow-down"></view>
					</view>
					<view class="label">
						{{item.name}}
					</view>
				</view>
				<view v-if="item.loading" class="__loading"></view>
				<view class="check" v-if="check_type=='radio'">
					<Icon :type="check_type" v-if="onlyChooseChild?!item.children||item.children.length==0:true"
						:value="item.id==check_id"></Icon>
				</view>
				<view class="check" @click.stop="checkMultiple(item,index)" v-if="check_type=='multiple'">
					<template v-if="onlyChooseChild?!item.children||item.children.length==0:true">
						<Icon v-if="item.half" :value="item.half" type="half"></Icon>
						<Icon v-else :type="check_type" :value="item.check"></Icon>
					</template>
				</view>
			</view>
			<view style="margin-left: 40px;"
				v-if="item.open&&((item.children&&item.children.length>0)||item.hasChildren)">
				<loadTree :key="item.uid" ref="load" :uid="item.uid" @halfChange="halfChange" @checkRadio="checkRadio"
					:onlyChooseChild="onlyChooseChild" :pitem="item" :check_id="check_id" :check_type="check_type"
					@change="change" :list="item.children" :current="index" :isFirst="false">
				</loadTree>
			</view>
		</view>
	</view>
</template>

<script>
	import Icon from '@/components/load-tree/icon.vue'
	import loadTree from '@/components/load-tree/index.vue'
	export default {
		name: "loadTree",
		components: {
			Icon,
			loadTree
		},
		data() {
			return {
				renderList: deepClone(this.list)
			}
		},
		props: {
			// 一次性渲染的数据
			list: {
				type: Array,
				default: () => {
					return []
				}
			},
			/* 单选选中的值 */
			check_id: {
				type: [String, Number],
				default: ''
			},
			current: {
				type: Number,
				default: () => {
					return 0
				}
			},
			/* 组件类型 */
			/* radio 单选 multiple 多选 */
			check_type: {
				type: String,
				default: ''
			},
			/* 是否只选择底层子级 */
			onlyChooseChild: {
				type: Boolean,
				default: () => {
					return true
				}
			},
			pitem: {
				type: [Object, String],
				default: () => {
					return ''
				}
			},
			isFirst: {
				type: Boolean,
				default: () => {
					return true
				}
			},
			uid: {
				type: String,
				default: ''
			}
		},
		watch: {
			uid(val) {
				if (JSON.stringify(this.renderList) != JSON.stringify(this.list)) {
					this.$set(this, 'renderList', deepClone(this.list))
				}
			}
		},
		methods: {
			showLeval(item, index) {
				if (item.children && item.children.length > 0) {
					if (this.isFirst) {
						this.$set(this.renderList[index], 'open', !this.renderList[index].open)
					} else {
						this.$emit('change', 'showLeval', this.current, index)
					}
				}
			},
			halfChange() {
				let stack = Array.prototype.slice.call(arguments)
				if (this.isFirst) {
					let origin = deepClone(this.renderList);
					let stack_index = stack.length - 1;
					var stackData
					if (this.onlyChooseChild) {
						stackData = origin
						stack.forEach((v, index) => {
							if (stack_index >= index) {
								if (index == 0) {
									stackData = stackData[v]
									stackData.uid = guid()
								} else {
									stackData.uid = guid()
									stackData = stackData.children[v]
								}
							}
						})
						stackData.check = !stackData.check
						stackData.uid = guid()
						stackData.half = false
						console.log(origin)
					} else {
						while (stack_index >= 0) {
							stackData = origin
							stack.forEach((v, index) => {
								if (stack_index >= index) {
									if (index == 0) {
										stackData = stackData[v]
									} else {
										stackData = stackData.children[v]
									}
								}
							})
							let check_count = 0,
								half_status = false;
							if (stack_index == (stack.length - 1)) {
								stackData.check = !(!!stackData.check)
								stackData.half = false
								stackData.uid = guid()
								this.$emit('change', stackData)
								if (stackData.children && stackData.children.length > 0) {
									stackData.children = this.deepResolve(deepClone(stackData.children), stackData.check,
										false)
								}
							} else if (stackData.children && stackData.children.length > 0) {
								stackData.children.forEach(row => {
									if (row.check) {
										check_count++
									}
									if (row.half) {
										half_status = true
									}
								})
								stackData.uid = guid()
								if (check_count == stackData.children.length) {
									stackData.half = false
									stackData.check = true
								} else if (check_count != 0 || half_status) {
									stackData.half = true
									stackData.check = false
								} else {
									stackData.half = false
									stackData.check = false
								}
							}
							stack_index -= 1
						}
					}
					this.$set(this, 'renderList', origin)
				}
				if (!this.isFirst) {
					this.$emit('halfChange', this.current, ...arguments)
				}
			},
			checkMultiple(item, index) {
				let check = !item.check;
				if (this.isFirst) {
					this.$set(this.renderList[index], 'check', check)
					this.$set(this.renderList[index], 'half', false)
					this.$set(this.renderList[index], 'uid', guid())
					if (item.children && item.children.length > 0) {
						let arr = this.deepResolve(item.children, check, false)
						this.$set(this.renderList[index], 'children', deepClone(arr))
					}
					this.$emit('change', item)
				}
				this.$emit('halfChange', this.current, index)
			},

			deepResolve(arr, check, half) {
				arr.forEach(row => {
					row.check = check
					row.half = half
					row.uid = guid()
					if (row.children && row.children.length > 0) {
						this.deepResolve(row.children, check, half)
					}
				})
				return arr
			},
			// 点击item
			handleClick(item, index) {
				if (this.check_type == 'radio') {
					this.$emit('checkRadio', item.id)
					return
				} else if (this.check_type == 'multiple') {
					if (this.onlyChooseChild && (!item.children || item.children.length == 0)) {
						this.checkMultiple(item, index)
						return
					}
				}
				if (item.children && item.children.length > 0) {
					this.showLeval(item, index)
				}
			},
			checkRadio(value) {
				this.$emit('checkRadio', value)
				if (this.isFirst) {
					this.$emit('change', value)
				}
			},
			getValue() {
				if (this.check_type == 'multiple') {
					let result = {
						resultItem: [],
						resultIds: [],
						resultHalf: []
					};
					this.searchCheck(this.renderList, result)
					return result
				}
			},
			searchCheck(arr, result) {
				arr.forEach(row => {
					if (row.check) {
						result.resultItem.push(row)
						result.resultIds.push(row.id)
					} else if (row.half) {
						result.resultHalf.push(row)
					}
					if (row.children && row.children.length > 0) {
						this.searchCheck(row.children, result)
					}
				})
				return result
			},
			change() {
				if (this.isFirst) {
					let stack = Array.prototype.slice.call(arguments),
						operate_type = stack[0];
					if (operate_type != 'showLeval') {
						this.$emit('change', operate_type)
					}
					stack.splice(0, 1)
					let origin = deepClone(this.renderList);
					var stackData = origin;
					stack.forEach((v, index) => {
						if (index == 0) {
							stackData = stackData[v]
						} else {
							stackData = stackData.children[v]
						}
						stackData.uid = guid()
					})
					if (operate_type == 'showLeval') {
						stackData.uid = guid()
						stackData.open = !stackData.open
					}
					this.$set(this, 'renderList', origin)
					this.$emit('change', stackData)
				} else {
					let stack = Array.prototype.slice.call(arguments),
						operate_type = stack[0];
					stack[0] = this.current
					stack.unshift(operate_type);
					this.$emit('change', ...stack)
				}
			},
		}
	}

	function guid() {
		function S4() {
			return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
		}
		return (S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4() + "-" + S4() + S4() + S4());
	}

	function deepClone(arr) {
		return JSON.parse(JSON.stringify(arr))
	}
</script>

<style lang="scss" scoped>
	$grey: #909090;

	.load-tree {
		// margin: 5px;

		.leval {
			display: flex;
			align-items: center;
			height: 55rpx;

			.label {
				max-width: 100px;
				overflow: hidden;
				-webkit-box-orient: vertical;
				margin-left: 10rpx;
				-webkit-line-clamp: 1;
				overflow: hidden;
				white-space: nowrap;
				text-overflow: ellipsis;
			}

			.check {
				// margin-left: 10px;
				display: flex;
				align-items: center;
			}
		}

	}

	.__loading {
		width: 25rpx;
		height: 25rpx;
		display: inline-block;
		border: 5rpx solid #ececec;
		border-radius: 50%;
		border-top: 5rpx solid $grey;
		animation: loadingAnimation 0.6s infinite linear;
	}

	@keyframes loadingAnimation {

		/* 动画开始旋转0度 */
		0% {
			transform: rotate(0deg);
		}

		/* 动画结束旋转360度 */
		100% {
			transform: rotate(360deg);
		}
	}

	.arrow-down {
		width: 0;
		height: 0;
		position: relative;
		border: 15rpx solid;
		margin-top: 15rpx;
		border-color: $grey transparent transparent transparent;

		&::after {
			content: '';
			position: absolute;
			// top: -19rpx;
			// left: -15rpx;
			// border: 15rpx solid;
			// border-color: white transparent transparent transparent;
		}
	}

	.arrow-right {
		transform: rotate(-90deg);
		margin-top: -5rpx;
		transition: all 0.3s ease;

		&::after {
			content: '';
			position: absolute;
			// top: -19rpx;
			// left: -15rpx;
			// border: 15rpx solid;
			// border-color: white transparent transparent transparent;
		}
	}

	.down {
		transition: all 0.3s ease;
	}
</style>