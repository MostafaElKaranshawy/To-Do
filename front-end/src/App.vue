<template>
  <div class="app">
    <nav>
      <div class="important" @click="setImportant">
        <div class="name">
          <img src="./assets/unstar.png" class="filter" v-if="important.length">
          <img src="./assets/star.png" class="filter" v-else>
          Important
        </div>
        <div class="count">{{important.length || ''}}</div>
        
      </div>
      <div class="folders">
        <div class="folder" v-for="(folder,index) in folders" :key="folder" @click="setFolder(folder, index)">
          <div class="name">
            <img src="./assets/list.png" class="filter">
            {{folder.name}}
          </div>
          <div class="count">{{folder.size}}</div>
        </div>
      </div>
      <div class="addList">
        <img src="./assets/add.png" @click="showList">
        <div class="addlistInput">
          <input type="text"  placeholder="List Name">
          <img src="./assets/true.png" @click="addList">
        </div>
      </div>
    </nav>
    <main>
        <p class="list-title" v-if="!importantView">{{currentFolder.name}}</p>
        <p class="list-title" v-else>Important</p>
        <div class="tasks">
            <div class="importantTasks" v-if="importantView">
                <div class="task" v-for="task in important"
                :key="task"
                >
                    <div class="content">
                      <p>{{task.content}}</p>
                      <p class="source">{{task.source}}</p>
                    </div>
                    <div class="container">
                      <p class="complete" @click="completeImportant(task)">Complete</p>
                      <img src="./assets/unstar.png" class="star" @click="unstar(task)">
                    </div>
                </div>
            </div>

            <div class="uncompleted" v-if="!importantView">
                <div class="task" v-for="task in uncompleted"
                :key="task"
                >
                    <p class="content">{{task.content}}</p>
                    <div class="container">
                      <p class="complete" @click="complete(task)">Complete</p>
                      <img src="./assets/star.png" class="star" @click="star(task)" v-if="!task.star">
                      <img src="./assets/unstar.png" class="star" @click="unstar(task)" v-else>
                    </div>
                </div>
            </div>
            <div class="complete-button" @click="showC" v-if="!importantView">
                completed
                <img src="./assets/arrow-down.png">
            </div>
            <div class="completed" v-if="!importantView">
              <div class="task" v-for="task in completed"
              :key="task"
              >
                  <p class="content">{{task.content}}</p>
                  <p class="complete" @click="uncomplete(task)">UnComplete</p>
              </div>
            </div>
        </div>
        <div class="addTask" v-if="!importantView">
          <img src="./assets/true.png" @click="addTask">
          <input type="text" id="addTask" placeholder="Add Task">
        </div>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return{
      currentFolder: {},
      folders: [
        {
          name: 'Default',
          completed: [],
          uncompleted: [],
          size: ''
        },
        {
          name: 'Tasks',
          completed: [],
          uncompleted: [],
          size: ''
        },
      ],
      important: [],
      uncompleted: [],
      completed: [],
      showCompleted: true,
      importantView: false
    }
  },
  mounted(){
    // const defaultFolder = {
    //   name: 'Default',
    //   completed: [],
    //   uncompleted: [],
    //   size: 0
    // }
    // this.folders.push(defaultFolder);
    // this.currentFolder = this.folders[0]
    // this.uncompleted = this.folders[0].uncompleted;
    // this.completed = this.folders[0].completed;
    document.querySelector(".folders .folder:first-child").click();
  },
  methods: {
    setImportant(){
      this.importantView = true;
      for(let i = 0; i < document.querySelectorAll(".folder").length ;i++){
          // console.log(document.querySelectorAll(".folder")[i])
          document.querySelectorAll(".folder")[i].classList.remove("active");
        }
      document.querySelector(".important").classList.add("active");
      this.currentFolder = {}
      this.completed = []
      this.uncompleted = []
    },
    addTask(){
      let content = document.querySelector("#addTask").value
      const task = {
        content: content,
        id: this.uncompleted.length+1+this.completed.length,
        star: false,
        source: this.currentFolder.name
      }
      // console.log(task.id)
      if(content != '' && this.currentFolder != null)this.uncompleted.push(task)
      document.querySelector("#addTask").value = ''
      this.currentFolder.size = this.uncompleted.length + this.completed.length;
    },
    setFolder(folder, index){
      // console.log(folder)
      this.importantView = false;
      this.currentFolder = folder;
      this.uncompleted = folder.uncompleted;
      this.completed = folder.completed;
      // console.log(document.querySelectorAll(".folder").length)
      if(this.folders.length != 0){
        document.querySelector(".important").classList.remove("active");
        for(let i = 0; i < document.querySelectorAll(".folder").length ;i++){
          // console.log(document.querySelectorAll(".folder")[i])
          document.querySelectorAll(".folder")[i].classList.remove("active");
        }
        document.querySelectorAll(".folder")[index].classList.add("active")
      }
    },
    showList(){
      let label = document.querySelector(".addlistInput")
      if(label.style.visibility == "visible"){
        label.style.visibility = "hidden"
      }
      else{
        label.style.visibility = "visible";
      }
    },
    addList(){
      let name = document.querySelector(".addlistInput input").value;
      const folder = {
        name: name,
        completed: [],
        uncompleted: [],
        size: ''
      }
      if(name != ''){
        this.folders.push(folder)
      }
      document.querySelector(".addlistInput input").value = '';
    },
    completeImportant(task){
      this.unstar(task)
      let folder = this.folders.filter(f => f.name == task.source)[0]
      folder.uncompleted = folder.uncompleted.filter(t => t.id != task.id)
      folder.completed.push(task)
    },
    complete(task){
      this.unstar(task)
      console.log(this.currentFolder)
      this.currentFolder.uncompleted = this.currentFolder.uncompleted.filter(t => t.id != task.id)
      this.currentFolder.completed.push(task)
      this.uncompleted = this.currentFolder.uncompleted
      this.completed = this.currentFolder.completed
      this.currentFolder.size = this.uncompleted.length + this.completed.length;
    },
    uncomplete(task){
      this.currentFolder.completed = this.currentFolder.completed.filter(t => t.id != task.id)
      this.currentFolder.uncompleted.push(task)
      this.completed = this.currentFolder.completed
      this.uncompleted = this.currentFolder.uncompleted
      this.currentFolder.size = this.uncompleted.length + this.completed.length;
    },
    showC(){
      this.showCompleted = !this.showCompleted;
      if(this.showCompleted){
        // console.log("true")
        document.querySelector(".completed").style.visibility = "visible"
        document.querySelector(".complete-button img").style.transform = "rotate(180deg)"
      }
      else{
        // console.log("false")
        document.querySelector(".complete-button img").style.transform = "rotate(0deg)"
        document.querySelector(".completed").style.visibility = "hidden"
      }
    },
    star(task){
      task.star = true
      this.important.push(task);
    },
    unstar(task){
      task.star = false;
      this.important = this.important.filter(t => t.source != task.source || t.id != task.id );
    }
  }
}
</script>

<style scoped>
/* width */
::-webkit-scrollbar {
  width: 10px;
}

/* Track */
::-webkit-scrollbar-track {
  background: transparent;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: #373737;
  transition: 0.3s;
  cursor: pointer;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #737373;
}

img{
  width: 20px;
}
*{
  padding: 0;
  margin: 0;
}
.app {
  color: white;
  display: flex;
  height: 100vh;
  overflow: hidden;
}
nav{
  background-color: #313131;
  width: 200px;
  padding: 20px 10px;
  overflow-y:scroll;
}
.filter{
  filter: contrast(0);
}
.folders{
  padding-bottom: 50px;
}
.important{
  display: flex;
  font-size: 15px;
  color: white;
  align-items: center;
  justify-content: space-between;
  transition: 0.3s;
  cursor: pointer;
  padding: 5px;
}
.important img{
  margin-right: 5px;
}
.important .name{
  display: flex;
  align-items:  center;
}
.important .count{
  font-size: 10px;
}
.important:hover{
  background-color: #2e2e2e;
}
.folder{
  display: flex;
  font-size: 15px;
  color: white;
  align-items: center;
  justify-content: space-between;
  transition: 0.3s;
  cursor: pointer;
  padding: 5px;
}
.folder img{
  margin-right: 5px;
}
.folder .name{
  display: flex;
  align-items:  center;
}
.folder .count{
  font-size: 10px;
}
.folder:hover{
  background-color: #2e2e2e;
}
.addList{
  position: fixed;
  bottom: 0px;
  transition: 0.3s;
  display: flex;
  align-items: center;
  backdrop-filter: blur(5px);
  height: 50px;
}
.addList img{
  transition: 0.3s;
  cursor: pointer;
  padding: 5px;
  background: white;
  border-radius: 50%;
}
.addList img:hover{
  transform: rotate(360deg);
  background: #EEE;
}
.addlistInput{
  visibility: hidden;
  transition: 0.3s;
  display: flex;
  align-items: center;
}
.addlistInput img{
  width: 10px;
  margin-left: 5px;
  transition: 0.3s;
}
.addlistInput input{
  letter-spacing: 1.5px;
  color: #4f4c4c;
  font-weight: bold;
  width: 120px;
  margin-left: 10px;
  padding: 2px 3px;
  font-size: 12px;
  transition: 0.3s;
}
main{
  width: calc(100% - 200px);
  height: 100vh;
  /* overflow-y: scroll; */
  background: #272727;
  padding: 20px 0px 20px 10px;
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  flex-direction: column;
  position: relative;
}
.list-title{
  margin-bottom: 10px;
}
.tasks{
  height: 80%;
  width: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  overflow-y:scroll;
  padding-bottom: 30px;
}
.task{
  color: white;
  font-size: 12px;
  padding: 5px;
  background-color: #313131;
  margin-bottom: 1px;
  display: flex;
  justify-content: space-between;
}
.star{
  cursor: pointer;
}
.task .container {
  display: flex;
  align-items: center;
}
.task .complete{
  font-size: 10px;
  padding: 2px;
  background-color: #585858;
  cursor: pointer;
}
.task .complete:hover{
  background-color: #3e3e3e;
}
.task:hover{
  background-color: #464545;
}
.task .content .source{
  color: #a1a1a1;
  margin-top: 5px;
}
.complete-button{
  font-size: 12px;
  padding: 5px;
  background: #313131;
  margin: 5px;
  width: 70px;
  display: flex;
  align-items: center;
  cursor: pointer;
  transition: all 0.3s;
}
.complete-button:hover{
  background: #525151;
}
.complete-button img{
  transform: rotate(180deg);
  transition: all 0.3s;
  width: 10px;
  margin-left: 10px;
}
.addTask{
  position: fixed;
  bottom: 10px;
  width:50%;
  display: flex;
  align-items: center;
}
.addTask input{
  width:100% ;
  font-size: 12px;
  background-color: #33323238;
  backdrop-filter: blur(5px);
  border: none;
  padding: 5px;
  color: white;
  transition: all 0.3s;
}
.addTask input::placeholder{
  padding: 5px;
  font-size: 10px;
  color: white;
}
.addTask input::selection{
  background-color: rgba(128, 128, 128, 0.515);
  color: white;
}
.addTask input:hover{
  background-color: #464545;
}
.addTask img{
  background: white;
  border-radius: 50%;
  padding: 1px;
  cursor: pointer;
  margin-right: 5px;
  transition: 0.3s;
}
.addTask img:hover{
  transform: rotate(360deg);
  background: #eee;
}
.active{
  border-left: solid 4px grey;
  background-color: #464545;
}
</style>