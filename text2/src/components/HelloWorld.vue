<template>
   	<div id="wrap">
			<h1>标准信息列表</h1>
			<div class="box">
				<div>
					<input type="text" v-model="inputtext" /><button>提交检索</button><button @click="insertarr('增加')">新增标准</button><button @click="deletearr">删除标准</button>
				</div>
				<table border="" cellspacing="" cellpadding="">
					<tr>
						<th><input type="checkbox" /></th>
						<th>标准号</th>
						<th>中文名称</th>
						<th>版本</th>
						<th>发布日期</th>
						<th>实施日期</th>
						<th>操作</th>
					</tr>
					<tr v-for="item,index in newarr">
						<td><input type="checkbox" v-model="item.istrue" /></td>
						<td>{{item.std_num}}</td>
						<td>{{item.zhname}}</td>
						<td>{{item.version}}</td>
						<td>{{item.release_data}}</td>
						<td>{{item.impl_data}}</td>
						<td>
							<a href="#">下载</a>
							<a @click="updatearr('修改',item,index)">修改</a>
						</td>
					</tr>
				</table>
				<div><span @click="shouye" :class="{active:isclass}">首页</span>
					|<span @click="prev" :class="{active:isclass}">上一页</span>|
					<span @click="next" :class="{active:!isclass}">
				下一页</span>|<span @click="moye" :class="{active:!isclass}">末页</span>|<span>
					<span>第{{dangqian}}页</span>/<span>共{{zongye}}页</span>
					</span>
				</div>
			</div>
			
			<div class="input-group" v-if="isbol">
				<h1>{{Normanomo}}标准</h1>
				<div>
					<ul>
						<li><span>*标准号</span><span><input type="text" v-model="obj.std_num" /></span></li>
						<li><span>*中文名称</span><span><input type="text" v-model="obj.zhname" /></span></li>
						<li><span>*版本</span><span><input type="text" v-model="obj.version"/></span></li>
						<li><span>*关键字/词</span><span><input type="text" v-model="obj.keys" /></span></li>
						<li><span>发布日期</span><span><input type="text"  v-model="obj.release_data"/></span></li>
						<li><span>实施日期</span><span><input type="text" v-model="obj.impl_data" /></span></li>
						<li><span>*附件</span><span><input type="file" @change="onchange" /></span></li>
						<li>
							<div style="margin: 0 auto;"><button @click="inseupda">确定</button><button @click="quxiao">取消</button></div></li>
		  		</ul>
		  	</div>
		  </div>
	</div>
</template>

<script>
	export default{
			  data(){
			  	
			  	return {
			  	arr:[],
			  	inputtext:"",
			  	Normanomo:"",
			  	isbol:false,
			  	obj:{
			  		id:0,
			  		std_num:"",
			  		zhname:"",
			  		version:"",
			  		keys:"",
			  		release_data:"",
			  		impl_data:"",
			  		package_path:"",
			  		istrue:false,
			  		indexs:0
			  	},
			  	zongye:0,
			  	dangqian:1,
			  	first1:0,
			  	last1:4,
			  	zonglie:0
			  }
			  
			  },
			  components:{
			  	"inputGroup":{
			  		template:"#inputGroup"
			  	}
			  }
			  ,
			  beforeMount(){
			  	this.ajaxs(`http://localhost:8080/Server/Zongyeshu?lie=4`,"zongye");
			  }
			  ,
			  mounted(){
			  	this.ajaxs(`http://localhost:8080/Server/Server?first=${this.first1}&last=${this.last1}`,"arr");
			  }
			  ,
			  methods:{
				  	ajaxs(url,tags){
				  		this.axios.get(url).then(
				  		res=>{
				  			if(tags=="arr"){
				  				this.arr=res.data
				  			}else if(tags=="zongye"){
				  				this.zongye=eval("("+res.data+")").zongye;
				  				this.zonglie=eval("("+res.data+")").zonglie;
				  			}
				  		     },
				  		error=>console.log(error)
				  	)
			  	}
			  	 ,
			  	deletearr(){
			  		this.arr=this.arr.filter(item=>item.istrue!=true);
			  	},
			  	insertarr(name){
			  		this.Normanomo=name;
			  		this.isbol=true;
			  	},
			  	inseupda(){
			  		//新增和修改
			  		if(this.Normanomo=="新增"){
			  			this.arr.push(this.obj);
			  		}else if(this.Normanomo=="修改"){
			  			this.arr[this.indexs]=this.obj;
			  		};
			  		this.obj={
				  		id:0,
				  		std_num:"",
				  		zhname:"",
				  		version:"",
				  		keys:"",
				  		release_data:"",
				  		impl_data:"",
				  		package_path:"",
				  		istrue:false
				  		}
			  		this.isbol=false
			  	},
			  	quxiao(){
			  		this.obj={
				  		id:0,
				  		std_num:"",
				  		zhname:"",
				  		version:"",
				  		keys:"",
				  		release_data:"",
				  		impl_data:"",
				  		package_path:"",
				  		istrue:false
			  		}
			  		this.isbol=false
			  	}
			  	,
			  	next(){
			  		if(this.dangqian===this.zongye){
			  			return;
			  		}else{
			  			this.dangqian+=1;
			  			this.first1+=4;
			  			this.last1+=4;
			  			console.log(this.first1)
			  			console.log(this.last1)
			  		    this.ajaxs(`http://localhost:8080/Server/Server?first=${this.first1}&last=${this.last1}`,"arr");
			  		}
			  	},
			  	prev(){
			  		if(this.dangqian===1){
			  			return;
			  		}else{
			  			this.dangqian-=1;
			  			this.first1-=4;
			  			this.last1-=4;
			  			this.ajaxs(`http://localhost:8080/Server/Server?first=${this.first1}&last=${this.last1}`,"arr");
			  		}
			  	}
			  	,
			  	shouye(){
			  			this.dangqian=1;
			  			this.first1=0;
			  			this.last1=4;
			  			this.ajaxs(`http://localhost:8080/Server/Server?first=${this.first1}&last=${this.last1}`,"arr");
			  	},
			  	moye(){
			  			this.dangqian=this.zongye;
			  			this.first1=4;
			  			this.last1=8;
			  			this.ajaxs(`http://localhost:8080/Server/Server?first=${this.first1}&last=${this.last1}`,"arr");
			  	}
			  	,
			  	onchange(event){
			  		this.obj.package_path=event.target.file;
			  	},
			  	updatearr(name,item,index){
			  		this.Normanomo=name;
			  		this.isbol=true;
			  		this.obj=item;
			  		this.indexs=index;
			  	}
			  },
			  computed:{
			    newarr(){
			    	if(this.inputtext==""){
			    		return this.arr;
			    	}else{
			    		return this.arr.filter(item=>item.keys.includes(this.inputtext))
			    	}
			    	
			    },
			    isclass(){
			    	if(this.dangqian===this.zongye){
			    		return true;
			    	}else{
			    		return false;
			    	}
			    }
			  }
		}
		
	</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
			#wrap {
				width: 1000px;
				margin: 0 auto;
				text-align: center;
			}
			
			table {
				margin: 10px 0;
			}
			
			.box {
				width: 700px;
				margin: 0 auto;
				border: 1px solid #000;
				padding: 10px 0;
			}
			
			td a {
				margin: 0 5px;
			}
			
			.input-group {
				margin-top: 40px;
				width: 700px;
				margin: 0 auto;
				border: 1px solid #ccc;
			}
			
			.input-group li {
				list-style: none;
				overflow: hidden;
			}
			
			.input-group li span:nth-of-type(1) {
				border: 1px solid #ccc;
				box-sizing: border-box;
				display: block;
				float: left;
				width: 40%;
			}
			
			.input-group li span:nth-of-type(2) {
				border: 1px solid #ccc;
				box-sizing: border-box;
				display: block;
				float: right;
				width: 60%;
			}
			#wrap .active{
				color: dodgerblue;
				text-decoration: underline;
			}
		</style>
