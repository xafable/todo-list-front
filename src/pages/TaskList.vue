<template>
  <q-page class="c-page">

    <div v-if="dataLoaded">
      <div style="margin-left: 20%">
      <q-scroll-area :style="'height:' + windowHeight*0.87 + 'px;'">
        <TaskItem class="c-task-item"  @taskDeleted="fetchTasks" v-for="item in tasksData" :task="item"></TaskItem>
      </q-scroll-area>
      </div>
    </div>

    <q-page-sticky position="bottom-right" :offset="[18, 18]">.
      <q-btn fab icon="add" color="positive" @click="saveDialogOpened=true" />
    </q-page-sticky>

    <q-dialog v-model="saveDialogOpened" style="width: auto" >
      <TaskSave mode="Create" :list-id="id" @taskSaved="onTaskSaved"></TaskSave>
    </q-dialog>

  </q-page>
</template>

<script>
import {defineComponent, ref, computed } from 'vue'
import TaskItem from "components/TaskItem.vue";
import TaskSave from "components/TaskSave.vue";

export default defineComponent({
  name: 'TaskList',
  components: {TaskSave, TaskItem},
  props: {
    id: Number
  },
  setup (props) {
    const saveDialogOpened = ref(false)
    const tasksData = ref([])
    const dataLoaded = ref(false)

    const windowHeight = computed(() => window.screen.height)

    const fetchTasks = () => {
      fetch('http://todo-api/api/lists/'+props.id+'/tasks', {
        method: 'GET',
        headers: {
          'Accept': 'application/json',
        },
      })
        .then(response => {
          if (response.ok) {
            return response.json();
          } else
            return response.json().then(data => {
              throw new Error(data.message)
            })
        })
        .then(data => {
          tasksData.value = data.data
          dataLoaded.value = true

        })
        .catch(error => {

        })
    }

    return {
      saveDialogOpened,
      tasksData,
      dataLoaded,
      fetchTasks,
      windowHeight
    }
  },
  methods: {
    onTaskSaved(data){
      this.fetchTasks()
      this.saveDialogOpened = false
    },
  },
  mounted() {
    this.fetchTasks()
  },
  watch: {
    id() {
      this.fetchTasks()
    }
  }
})
</script>


<style scoped>
.c-page{

  width: 98%;
  margin-right: auto;

}
.c-task-item{
  margin-left: 0.3%;
  margin-bottom: 0.3%;
}
</style>
