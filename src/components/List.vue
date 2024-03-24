<script setup>
import {ref} from "vue";

const props = defineProps({
    task: {
        type: Object,
        default: () => {}
    },
    itemValue: {
        type: Array,
    },
    modelValue: Number,

})

defineEmits(["modelValue","changeStatus","deleteList","deleteItem"])

const setNewTask = ref(true)

const newTaskValue = ref('')

const setCurrentTaskId = (id) => {
        localStorage.setItem('currentTaskId',id.toString())
}

const toggleAdditional = ref(false)

const updatedField = ref('')

const updatedTaskName = ref('')

const updatedDescription = ref('')

const singleEditing = ref(false)

const mainEditing = ref(false)

const currentSubTask = ref(localStorage.getItem('currentTaskId'))

const currentIdTask = (data) =>{

    return data == localStorage.getItem('currentTaskId');
}

</script>


<template>

    <div class="flex flex-col items-center h-auto">

        <div class="h-auto w-[400px]">

            <div class="relative rounded mt-[25px] px-4  cursor-pointer"

                 :class="props.task.Status === true ? 'bg-gradient-to-r from-blue-300 to-green-300' : 'bg-gradient-to-r from-yellow-300 to-red-300'"

                 >

                <div v-if="props.task.Description" class="flex flex-col pb-4">

                    <p class="font-bold text-[14px]">{{props.task.Task}}</p>

                    <p class="w-full h-[30px] text-[18px] font-bold">{{props.task.Description}}</p>

                </div>



                <div v-else class="flex py-4">

                    <p class="font-bold text-[22px]">{{props.task.Task}}</p>

                </div>

                <div v-show="mainEditing" class="h-[70px] absolute top-0 left-0 flex justify-center items-center z-10 bg-gray-200">

                    <div class="pt-[10px]">

                        <input v-model="updatedTaskName" type="text" class="w-[220px] mb-[5px] rounded pl-2" placeholder="Edit Task name">


                        <input v-model="updatedDescription" type="text" class="w-[220px] rounded pl-2" placeholder="Edit description">

                    </div>

                    <button class=" px-[12px] w-[140px] h-[40px] bg-white text-black hover:bg-gray-300 rounded" @click="$emit('updatedMainTask', updatedTaskName, updatedDescription, mainEditing = !mainEditing)">Edit</button>

                </div>

                <div v-if="!props.task.Status"

                     class="block absolute left-[-10px] top-[-10px] border border-black rounded-[50%] w-[25px] text-[15px] bg-black text-center text-white z-[12]"

                     @click="$emit('changeStatus')"

                ><img src="../assets/icons/close1.png" ></div>

                <div v-else
                     class="block absolute left-[-10px] top-[-10px] left-1 border rounded-[50%] w-[25px] text-[15px] text-center bg-white font-bold z-[12]"

                     @click="$emit('changeStatus')"

                ><img src="../assets/icons/checkmark.png" alt="" class=""></div>

                <button class="absolute top-[2px] right-6 font-normal text-[17px]"
                        @click="mainEditing = !mainEditing">

                    <img src="../assets/icons/pen.png" alt="" class="">

                </button>

                <button class="absolute top-[2px] right-1 font-normal text-[17px] w-[16px] h-[16px]"

                        @click="$emit('deleteList')">

                    <img src="../assets/icons/close-sub.png" alt="" class="">

                </button>

                <button class="absolute bottom-[-1px] font-bold right-0  text-[17px]   px-1"

                        :class="props.task.Subtasks.length ? 'cursor-pointer border-2 border-black text-black' : 'cursor-no-drop bg-gray-400 text-white'"

                        @click="toggleAdditional = !toggleAdditional">

                    Expand

                </button>

            </div>

            <div v-if="props.task.Subtasks.length && toggleAdditional" class="flex flex-col items-center">
                <ul  class="my-[15px] rounded flex flex-col">

                    <li class="flex justify-between relative cursor-pointer ml-[10px] w-[390px] h-auto whitespace-nowrap overflow-hidden overflow-ellipsis rounded pl-8 pr-2 py-4 my-[12px]"
                    :class="task.Status ? 'bg-gradient-to-r from-emerald-400 to-cyan-400': 'bg-gradient-to-r from-pink-500 to-amber-500'"
                    @mouseenter="setCurrentTaskId(task.id)"

                    v-for="task in itemValue" :key="task.id">

                        <p class="text-[16px] font-bold">{{task.Text}}</p>

                        <div class="absolute bottom-4 left-6 pl-2 flex" v-if="singleEditing &&  currentIdTask(task.id)">

                            <input v-model="updatedField"  class="w-[225px]" :placeholder="task.Text"
                               >

                            <button class="ml-4 px-[7px] bg-white text-black hover:bg-green-300 hover:duration-1000 rounded" @click="$emit('updatedValue', updatedField, singleEditing = !singleEditing)">

                                Edit

                            </button>
                        </div>

                        <div v-if="!task.Status"

                             class="block absolute left-1 border border-black rounded-[50%] w-[25px] text-[15px] bg-black text-center"

                             @click="task.Status = !task.Status"

                            ><img src="../assets/icons/close1.png"></div>

                        <div v-else

                             class="block absolute left-1 border rounded-[50%] w-[25px] text-[15px] text-center bg-white font-bold" @click="task.Status = !task.Status"

                             ><img src="../assets/icons/checkmark.png" ></div>

                        <button class="absolute top-[2px] right-6"

                                @click="singleEditing = !singleEditing">

                            <img src="../assets/icons/pen.png">

                        </button>

                        <button class="absolute top-[2px] right-1 w-[16px] h-[16px]"

                                @click="$emit('deleteItem')">

                            <img src="../assets/icons/close-sub.png">

                        </button>

                    </li>

                </ul>

            </div>

        </div>

        <div class="mb-4 flex flex-col justify-around items-center mt-[15px]">

            <div class="my-4" v-show="!setNewTask">

                <div class="flex">

                    <input type="text" class="pl-2 w-full h-[35px] rounded outline-none" placeholder="Push 'Enter' to add task"  @keydown="$emit(modelValue,$event.target.value)" v-model="newTaskValue">

                    <button class=" ml-4 font-bold" @click="setNewTask = !setNewTask">X</button>
                </div>

            </div>

            <button v-show="setNewTask" @click="setNewTask = !setNewTask" class="rounded-xl w-[50px] h-[40px] bg-white text-[22px] font-bold pb-1" >+</button>


        </div>

    </div>

</template>

<style scoped>

</style>