<template>
	<div class="detailBox markdown">

		<div class="wid">
			<div class="title">
				<textarea placeholder="请输入标题" v-model="title"></textarea>
			</div>

			<div class="markeditor">
				<mavon-editor class=".editor" v-model="content" ref="md" @imgAdd="$imgAdd" @change="change" :subfield="false" style="min-height: 640px;" :toolbars="toolbars" />
			</div>
			<div class="description">
				<el-input placeholder="描述" v-model="description">
				</el-input>
			</div>
			<div class="kind">
				<div class="block-wrap">
					<h3 class="block-title">请选择分类</h3>
					<div class="classify">
						<ul class="classify-list" >
							<li class="list-item" v-for="(item,index) in classListObj">
								<a :href="'/#/Publish?classtwoId='+item.classId" :class="classtwoId==item.classId?'active':''">{{item.name}}</a>
							</li>
						</ul>
					</div>
				</div>
			</div>
			<div class="sign">
				<h3 class="block-title">请添加标签:</h3>
				<el-input placeholder="vue" v-model="catename"></el-input>
			</div>
			<div class="btngroup">
				<div class="group1">
					<el-button class="btn" @click="submit" style="background-color: #37c8f7;;color:#fff">发布文章</el-button>
					<el-button class="btn">保存草稿</el-button>
				</div>
				<div class="group2">
					<el-button class="btn" @click="preview">预览文章</el-button>
					<el-button @click="cancel" class="btn" style="background-color: #37c8f7;;color:#fff">取消</el-button>
				</div>
			</div>

		</div>
	</div>
</template>

<script>
import editor, { mavonEditor } from 'mavon-editor'
import 'mavon-editor/dist/css/index.css'
import axios from 'axios'
import { publishArt, getCateList} from '../utils/server.js'
export default {
	components: {
		mavonEditor,
	},
	data() {
		return {
			title: '',
			image: '',
			description: '',
			classtwoId: 0,
			catename: '',
			content: '',
			html: '',
			classListObj:[],
			configs: {},
			toolbars: {
				bold: true, // 粗体
				italic: true, // 斜体
				header: true, // 标题
				underline: true, // 下划线
				strikethrough: true, // 中划线
				mark: true, // 标记
				superscript: true, // 上角标
				subscript: true, // 下角标
				quote: true, // 引用
				ol: true, // 有序列表
				ul: true, // 无序列表
				link: true, // 链接
				imagelink: true, // 图片链接
				code: false, // code
				table: true, // 表格
				fullscreen: true, // 全屏编辑
				readmodel: true, // 沉浸式阅读
				htmlcode: true, // 展示html源码
				help: true, // 帮助
				/* 1.3.5 */
				undo: true, // 上一步
				redo: true, // 下一步
				trash: true, // 清空
				save: true, // 保存（触发events中的save事件）
				/* 1.4.2 */
				navigation: true, // 导航目录
				/* 2.1.8 */
				alignleft: true, // 左对齐
				aligncenter: true, // 居中
				alignright: true, // 右对齐
				/* 2.2.1 */
				subfield: true, // 单双栏模式
				preview: true, // 预览
			},
		}
	},
	methods: {
		$imgAdd(pos, $file) {
			// 第一步.将图片上传到服务器.
			var formdata = new FormData()
			formdata.append('file', $file)
			axios({
				url: 'http://110.42.223.135:8888/Userinfo/UploadImg',
				method: 'post',
				data: formdata,
				headers: { 'Content-Type': 'multipart/form-data' },
			}).then((url) => {
				console.log(url)
				// 第二步.将返回的url替换到文本原位置![...](0) -> ![...](url)
				// $vm.$img2Url 详情见本页末尾
				this.image = url.data.image_name
				this.$refs.md.$img2Url(pos, url.data.image_name)
			})
		},
		// 所有操作都会被解析重新渲染
		change(value, render) {
			// render 为 markdown 解析后的结果[html]
			this.html = render
		},
		// 提交
		submit() {
			var that = this
			publishArt(that.title, that.image, that.description, that.html, that.classtwoId, that.catename, function (msg) {
				if (msg.code == 1001) {//存储
					console.log("1111")
					that.$message.success('发布成功')
					that.title = ''
					that.image = ''
					that.description = ''
					that.html = ''
					that.classtwoId = ''
					that.catename = ''
					that.content=''
				} else {
					that.$message.error('发布失败')
				}
			})
		},
		cancel() {
			var that = this
			that.title = ''
			that.image = ''
			that.description = ''
			that.html = ''
			that.classtwoId = ''
			that.catename = ''
			that.content=''
		},
		showInitDate: function (oldDate, full) {
			// console.log(oldDate,full);
			return initDate(oldDate, full)
		},
		showSearchShowList: function (initpage) {//展示数据
			var that = this
			that.classtwoId = that.$route.query.classtwoId == undefined ? '' : parseInt(that.$route.query.classtwoId)//获取传参的classtwoId

		},
		addMoreFun: function () {//查看更多
			this.showSearchShowList(false)
		},
		routeChange: function () {
			var that = this
			this.showSearchShowList(true)
		}

	},
	mounted() {
	},
	watch: {
		// 如果路由有变化，会再次执行该方法
		'$route': 'routeChange',
		'$store.state.keywords': 'routeChange'
	},
	created() { //生命周期函数
		// console.log(this.$route);
		var that=this;
	        getCateList(function(msg) { //文章分类
				that.classListObj = msg;
			})
		// var that = this
		// that.routeChange()
	}
}
</script>

<style>
.title textarea {
	width: 100%;
	height: 100%;
	line-height: 1.4;
	border: 0;
	background-color: transparent;
	outline: none;
	resize: none;
	text-align: justify;
	padding: 12px 50px 0px 0px;
	height: 40px;
	font-size: 28px;
	margin-top: 18px;
}
.description .el-input {
	width: 100%;
	height: 100%;
	line-height: 1.4;
	border: 0;
	background-color: transparent;
	outline: none;
	resize: none;
	text-align: justify;
	padding: 12px 50px 12px 0px;
	height: 18px;
	font-size: 18px;
}
.sign .el-input {
	width: 40%;
	height: 100%;
	line-height: 1.4;
	border: 0;
	background-color: transparent;
	outline: none;
	resize: none;
	text-align: justify;
	padding: 0px 50px 0px 0px;
	height: 18px;
	font-size: 18px;
}
.inp {
	width: 100%;
	height: 100%;
	line-height: 1.4;
	border: 0;
	background-color: transparent;
	outline: none;
	resize: none;
	text-align: justify;
}
.markeditor {
	height: auto;
	margin-top: 10px;
}
.editor {
	box-shadow: none;
	border: 1px solid #0c1f3b;
	border-radius: 4px;
}
h3 {
	font-size: 18px;
	font-weight: 400;
	line-height: 1;
	margin-bottom: 16px;
}
.sign {
	height: auto;
	float: left;
	margin: 10px 0px 20px auto;
}
.sign h3 {
	float: left;
	vertical-align: center;
	margin: 8px 12px 8px 0px;
}
.sign .el-input {
	float: left;
}
.kind {
	padding: 20px 0px 10px 0px;
	margin-top: 10px;
}
.classify .classify-list {
	margin-top: -12px;
}

.classify .classify-list .list-item {
	position: relative;
	display: inline-block;
	margin-right: 12px;
	margin-top: 12px;
}
.classify-list li a {
	display: inline-block;
	padding: 3px 7px;
	margin: 5px 10px;
	color: #fff;
	border-radius: 4px;
	background: #64609e;
	border: 1px solid #64609e;
	-webkit-transition: -webkit-transform 0.2s linear;
	transition: -webkit-transform 0.2s linear;
	transition: transform 0.2s linear;
	transition: transform 0.2s linear, -webkit-transform 0.2s linear;
	-webkit-transition: transform 0.2s linear;
}

.classify-list li a.active {
	background: #fff;
	color: #64609e;
}
.btngroup {
	margin-top: 15px;
	text-align: center;
	min-width: 80%;
}
.el-button {
	width: 40%;
	height: 42px;
	line-height: 42px;
	border-color: #37c8f7;
	color: #37c8f7;
	border-radius: 50px;
	margin: 0 5px 20px 5px;
	padding: 0;
}

.wid {
	width: 80%;
	height: 100%;
	/* text-align: center; */
	margin: 0 auto;
}
.markdown {
	/* width: auto; */
	/* height: auto;
	background-color: #fff;
	float: left; */
	white-space: normal;
	word-wrap: break-word;
	word-break: break-all;
	position: relative;
	background: #fff;
	padding: 15px;
	border-radius: 5px;
	margin-bottom: 40px;
	font-size: 15px;
	overflow: hidden;
}
.group1 {
	width: 50%;
	float: left;
}
.group2 {
	width: 50%;
	float: right;
}
</style>