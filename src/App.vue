<template>
	<h1>ToDo App</h1>
	<form @submit.prevent="addTodo()" class="form-todo">
		<label>New ToDo </label>
		<input
			v-model="newTodo"
			name="newTodo"
			autocomplete="off"
		>
		<button type="submit">Add ToDo</button>
	</form>
	<h2>ToDo List</h2>
	<ul>
		<li
			v-for="(todo, index) in todos"
			:key="index"
		>
			<div class="task pointer" >
								
				<span style="display:inline-block; width:50%"
				:class="{ done: todo.done }"
				@click="showChild(todo.id,todo.subs.length,todo.done)"
				
				>{{ todo.todo }}</span>
				<div>
					<button style="opacity:1 !important;" v-if="todo.subs.length>0">{{todo.subs.length}}</button>
					<button @click="addSubShow(todo.id)" :class="(showAddSub[todo.id] == true)? 'active' : ''">Add Sub</button>
					<button @click="removeTodo(todo.id)">Remove</button>
				</div>
				
			</div>
			<div class="child">
				<div  v-if="show[todo.id]">
					<div class="sub" v-for="(sub,index) in todo.subs" :key="index">
						<span
						style="display:inline-block; width:50%"
						:class="{ done: sub.done }"
						@click="doneSub(todo.id,sub._id,sub.done)"
						>{{ sub.todo }}</span>
						<button @click="removeSub(todo.id,sub._id)">Remove</button>
					</div>
				</div>
				<div class="add-sub" v-if="showAddSub[todo.id]">
				<form action="" @submit.prevent="addSub(todo.id)" style="width: 100%">
					<input type="text" v-model="newSub[todo.id]" name="newSub" class="form-control" aria-label="Large" placeholder="Add Sub Todolist" keyu="">
					
						<button type="submit" class="btn-add"> Add Sub</button>
	
				</form>
				</div>
				
			</div>
			
			
		</li>
	</ul>
	<h4 v-if="todos.length === 0">Empty list.</h4>
</template>

<script>
	
	import axios from "./plugins/axios";
	export default {
		name: 'App',
		data(){
			return{
				todos:{},
				newTodo:null,
				newSub:{},
				show:{},
				showAddSub:{}
			}
		},
		methods:{
			addSub(id){
				console.log(this.newSub[id])
				axios.post("/sub/"+id,{"todo" : this.newSub[id]}).then(() =>{
					this.getAll()
					this.newSub={}
					this.show[id] = true
					this.doneTodo(id,true)
				})
			},
			addTodo(){
			
				axios.post("/",{"todo" : this.newTodo}).then(() =>{
					this.getAll()
					this.newTodo=null
				})
			},
			showChild(id,subLength,done){
				if(subLength == 0)
					this.doneTodo(id,done)
				else{
					
					if(this.show[id] != true)
						this.show[id] = true
					else {
						this.show[id] = false
						}
				}
				this.showAddSub[id] = false
				
					
			},

			addSubShow(id){
				if(this.showAddSub[id] != true)
					this.showAddSub[id] = true
				else this.showAddSub[id] = false
			},
			getAll(){
				axios.get("/").then(response =>{
				this.todos = response.data
				})
			},
			removeTodo(id){
				axios.delete("/"+id).then(()=>{
					this.getAll()
				})
			},
			removeSub(parrent,id){
				console.log(parrent,id)
				axios.post("/sub/delete/"+parrent,{"id":id}).then(()=>{
					this.getAll()
				})
			},
			doneTodo(id,done){
				axios.put("/"+id,{"done": !done}).then(()=>{
					this.getAll()
				})
			},
			doneSub(parrent,id,done){
			
				axios.put("/sub/"+parrent,{"done": !done,"id":id}).then(()=>{
					this.getAll()
				})
			},
			
		},
		mounted:function(){axios.get("/").then(response =>{
				this.todos = response.data
				
			})
		},
		
	}
</script>

<style lang="scss">
$border: 2px solid
	rgba(
		$color: white,
		$alpha: 0.3,
	);
$size1: 6px;
$size2: 12px;
$size3: 18px;
$size4: 24px;
$size5: 48px;
$backgroundColor: #27292d;
$textColor: white;
$primaryColor: #a0a4d9;
$secondTextColor: #1f2023;
body {
	margin: 0;
	padding: 0;
	// font-family: Avenir, Helvetica, Arial, sans-serif;
	font-family: 'Prompt', sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	background-color: $backgroundColor;
	color: $textColor;
	#app {
		max-width: 600px;
		margin-left: auto;
		margin-right: auto;
		padding: 20px;
		h1 {
			font-weight: bold;
			font-size: 28px;
			text-align: center;
		}
		.form-todo {
			display: flex;
			flex-direction: column;
			width: 100%;
			label {
				font-size: 14px;
				font-weight: bold;
			}
			input,
			button {
				height: $size5;
				box-shadow: none;
				outline: none;
				padding-left: $size2;
				padding-right: $size2;
				border-radius: $size1;
				font-size: 18px;
				margin-top: $size1;
				margin-bottom: $size2;
			}
			input {
				background-color: transparent;
				border: $border;
				color: inherit;
			}
		}
		button {
			cursor: pointer;
			background-color: $primaryColor;
			border: 1px solid $primaryColor;
			color: $secondTextColor;
			font-weight: bold;
			outline: none;
			border-radius: $size1;
		}
		h2 {
			font-size: 22px;
			border-bottom: $border;
			padding-bottom: $size1;
		}
		ul {
			padding: 10px;list-style-type: none;
			li {
				margin-bottom: 20px;
				.task{
					display: flex;
					justify-content: space-between;
					align-items: center;
					border: $border;
					padding: $size2 $size4;
					border-radius: $size1;
					
					span {
						cursor: pointer;
					}
					.done {
						text-decoration: line-through;
						text-decoration-thickness: 3px;
					}
					button {
						font-size: $size2;
						padding: $size1;
						opacity: 0.5;
						margin-right: 10px;
						margin-left: 10px;

					}
					button:hover{
						opacity: 1;
					}
				}
				.child{
					.sub{
						display: flex;
						justify-content: space-between;
						align-items: center;
						border-bottom:2px solid rgba(
								$color: white,
								$alpha: 0.20,
							);
						
						padding: $size2 $size4;
						margin-left: 60px;
						border-radius: $size1;
						span {
							cursor: pointer;
						}
						.done {
							text-decoration: line-through;
							text-decoration-thickness: 3px;
						}
						button {
							font-size: $size2;
							padding: $size1;
							opacity: 0.5;
						}
						button:hover{
							opacity: 1;
						}
					}
					.add-sub{
						display: flex;
						justify-content: space-between;
						align-items: center;
						border-bottom:2px solid rgba(
								$color: white,
								$alpha: 0.20,
							);
						
						margin-left: 60px;
						border-radius: $size1;
						input{
							height: 40px;
							background-color: transparent;
							width: 70%;
							border:0;
							color: white;
							padding-left: 25px;

						}
						
						button {
							float: right;	
							margin-top: 7px;
							margin-right: 22px;
							font-size: $size2;
							padding: $size1;
							
							
					}
					}
					
				}
			}
		}
		h4 {
			text-align: center;
			opacity: 0.5;
			margin: 0;
			
		}
		.pointer{
			cursor: pointer;
		}
		.active{
			opacity: 1 !important;
		}

	}
}
</style>
