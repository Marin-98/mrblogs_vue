<!-- 用户喜欢和收藏列表 -->
<template>
	<div>
		<wbc-nav></wbc-nav>
		<div class="container">
			<el-row :gutter="30">
				<el-col :sm="24" :md="16" style="transition:all .5s ease-out;margin-bottom:30px;">
					<wbc-publish></wbc-publish>
				</el-col>
				<el-col :sm="24" :md="8">
					<wbc-rightlist></wbc-rightlist>
				</el-col>
			</el-row>
		</div>
		<wbc-footer></wbc-footer>
	</div>
</template>

<script>
import header from '../components/header.vue'
import footer from '../components/footer.vue'
import temRightlist from '../components/temRightlist.vue'
import temMessage from '../components/temMessage.vue'
import publish from '../components/temPublish.vue'
export default {
	name: 'Publish',
	data() { //选项 / 数据
		return {

		}
	},

	methods: { //事件处理器

	},
	components: { //定义组件
		'wbc-nav': header,
		'wbc-message': temMessage,
		'wbc-publish': publish,
		'wbc-rightlist': temRightlist,
		'wbc-footer': footer
	},
	watch: {
		// 如果路由有变化，会再次执行该方法

	},
	created() { //生命周期函数

	}
}
</script>

<style>
.likeBoxTitle {
	text-align: center;
	padding: 40px 0;
	font-size: 25px;
	font-weight: 700;
	margin-bottom: 40px;
	background: #fff;
	border-radius: 5px;
}
.likeBoxTitle h1 {
	font-weight: 700;
}
.likeBoxTitle h1 i {
	color: #df2050;
	margin-right: 10px;
	font-size: 30px;
}
</style>
