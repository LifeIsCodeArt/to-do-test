<script setup>
import { ref, computed, inject } from 'vue'
import List from '@/components/List.vue'

    const taskList = ref([
        {
            id:1,
            Task:'Task 1',
            Description: '',
            Status:true,
            Subtasks:[]

        },

        {
            id:2,
            Task:'Task 2',
            Description: 'We have it here',
            Status:false,
            Subtasks:[{id:1,Text:'subtask 1', Status:true}, {id:2,Text:'subtask 2', Status:false}, {id:3,Text:'subtask 3', Status:true}, {id:4,Text:'subtask 4', Status:true}, {id:5,Text:'subtask 5', Status:true}]
        },

        {
            id:3,
            Task:'Task 3',
            Description: 'We have it here',
            Status:false,
            Subtasks:[{id:1,Text:'subtask 1', Status:true}, {id:2,Text:'subtask 2', Status:true}]
        },

    ])

    const newTaskName = ref('')

    const Description = ref('')

    const addNewTask = (value) => {
    if(newTaskName.value){
    taskList.value.push({
        id:taskList.value.length + 1,
        Description:Description.value,
        Task: value,
        Subtasks:temporaryArray.value
    })
        Description.value = ''
        newTaskName.value = ''
        subTask.value = ''
        temporaryArray.value = []
    }
    else{
        Description.value = ''
        newTaskName.value = ''
        console.log('Task field is empty')
    }
}



const setCurrentTaskBoardId = (id) => {
    if(localStorage.getItem('currentTaskBoardId') == id) {
        console.log('This id currently used')
    }
    else{
        localStorage.setItem('currentTaskBoardId',id.toString())
        console.log(id)
    }
}

const Input = (e) =>{

    addNewTask.value = e.target.value

    if(e.code === 'Enter' && e.target.value) {
        taskList.value[localStorage.getItem('currentTaskBoardId')-1].Subtasks.push(

                {
                    id:taskList.value[localStorage.getItem('currentTaskBoardId')-1].Subtasks.length+1,

                    Text:`${e.target.value}`
                }
        )

    }
}

const updatedValue = (updatedValueTest, fieldClose) =>{

    taskList.value[localStorage.getItem('currentTaskBoardId')-1].Subtasks[localStorage.getItem('currentTaskId')-1].Text = updatedValueTest
    fieldClose.value = !fieldClose
}

const updatedMainTask = (updatedTaskName, updatedDescription) =>{

    taskList.value[localStorage.getItem('currentTaskBoardId')-1].Task = updatedTaskName

    taskList.value[localStorage.getItem('currentTaskBoardId')-1].Description = updatedDescription
}

const changeStatus = () =>{

    if(taskList.value[localStorage.getItem('currentTaskBoardId') - 1].Subtasks){
        taskList.value[localStorage.getItem('currentTaskBoardId')-1].Status = !taskList.value[localStorage.getItem('currentTaskBoardId')-1].Status

        for(let i = 0; i<taskList.value[localStorage.getItem('currentTaskBoardId')-1].Subtasks.length; i++){

            taskList.value[localStorage.getItem('currentTaskBoardId') - 1].Subtasks[i].Status = taskList.value[localStorage.getItem('currentTaskBoardId') - 1].Status;


        }

    }

    else taskList.value[localStorage.getItem('currentTaskBoardId')-1].Status = !taskList.value[localStorage.getItem('currentTaskBoardId')-1].Status

}


const deleteList = () =>{



        taskList.value.splice(taskList.value[localStorage.getItem('currentTaskBoardId')-1].id-1,1)

        for(let i = localStorage.getItem('currentTaskBoardId')-1; i <= taskList.value.length; i++){

            taskList.value[i].id = Number(i)+1

        }


}


const deleteItem = () =>{

        taskList.value[localStorage.getItem('currentTaskBoardId')-1].Subtasks.splice(Number(localStorage.getItem('currentTaskId'))-1,1)
        for(let i = localStorage.getItem('currentTaskId')-1;
            i <= taskList.value[localStorage.getItem('currentTaskBoardId')-1].Subtasks.length;
            i++){

            taskList.value[localStorage.getItem('currentTaskBoardId')-1].Subtasks[i].id = Number(i)+1

        }

}

const subTask = ref()

const addSubTask = () =>{

    if(subTask.value){

        temporaryArray.value.push({
            id:temporaryArray.value.length+1,
            Text:subTask.value,
            Status:false
        })

    }

    subTask.value = ''
}

const temporaryArray = ref([])

</script>

<template>

<div class="bg-[#DCE8F2] h-auto">

    <div class="text-center font-bold pt-[50px]">

        <div class="">
            <input v-model="newTaskName" type="text" class="border border-2 border-gray-100 mr-[10px] font-normal placeholder:text-[15px] " placeholder="Add new task">

            <input v-model="Description" type="text" class="border border-2 border-gray-100 mr-[10px] font-normal placeholder:text-[15px] " placeholder="Add description">

            <button class="px-[7px] py-[5px] bg-white text-black hover:bg-green-300 hover:duration-1000 rounded" @click="addNewTask(newTaskName)">Add</button>

        </div>

        <div class="mt-4 flex flex-col items-center h-full">

            <ul class="flex flex-col mb-4 w-[430px] list-decimal " v-show="temporaryArray.length">

                <li v-for="item in temporaryArray" :key="item.index"  class=" border-black text-left pl-2  h-[25px] rounded mb-[20px] bg-white">{{item.Text}}</li>

            </ul>

            <div class="flex justify-between items-center mt-4 w-[430px] ">

                <input v-model="subTask" type="text" class="pl-2  font-normal rounded-xl h-[30px] w-[290px]">

                <button class="h-auto py-1 px-2 border-2 border-black bg-white rounded-xl ml-[15px]" @click="addSubTask">

                    Add SubTask

                </button>

            </div>

        </div>

    </div>

    <div class="w-full h-auto flex items-center justify-center mt-[100px] pb-[200px]">

        <div class="w-[800px] p-[60px] h-auto border-2 border-gray-400 rounded-xl">

            <h1 v-if="taskList.length" class="font-bold text-[22px] block flex justify-center">Current To Do List</h1>

            <h1 v-else class="font-bold text-[22px] block flex justify-center">Current To Do List is empty</h1>

            <List v-for="task in taskList"
                :key="task.id"
                :task="task"
                :item-value="task.Subtasks"
                @mouseenter="setCurrentTaskBoardId(task.id)"
                v-model="addNewTask"
                @keydown="Input"
                @changeStatus="changeStatus"
                @deleteList="deleteList"
                @deleteItem="deleteItem"
                @updatedValue="updatedValue"
                @updatedMainTask="updatedMainTask"
                />

        </div>

    </div>

</div>

</template>
