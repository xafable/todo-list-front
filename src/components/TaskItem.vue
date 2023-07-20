<template>
  <div>
  <q-card class="c-card bg-white text-black">
    <q-card-section>
      <div class="text-h6"> {{ taskData.title }}</div>
      <q-separator inset /><br>
      <q-badge rounded :color="taskData.status==='Finished' ? 'green' : 'orange'" :label="taskData.status" class="c-badge"/>
      <q-badge rounded color="deep-purple-3" :label="'Created At: '+taskData.createdAt" class="c-badge"/>
    </q-card-section>


    <q-card-section style="overflow-wrap: break-word;">
     {{ taskData.description }}
    </q-card-section>

    <q-separator dark />

    <q-card-actions>
      <q-btn outline @click="saveDialogOpened = true">Edit</q-btn>
      <q-btn color="deep-orange-14" @click="onDelete">Delete</q-btn>
    </q-card-actions>
  </q-card>

  <q-dialog v-model="saveDialogOpened" style="width: auto" >
    <TaskSave mode="Edit" :task="task" @taskSaved="onTaskSaved"></TaskSave>
  </q-dialog>
  </div>

</template>

<script>
import {defineComponent, ref, toRef} from 'vue'
import { useQuasar } from 'quasar'
import TaskSave from "components/TaskSave.vue";

export default defineComponent({
  name: 'TaskItem',
  components: {TaskSave},
  props: {
    task: Object
  },
  setup (props) {
    const options = ['Started', 'Finished']
    const status = ref('Started')
    const saveDialogOpened = ref(false)
    const taskData = toRef(props, 'task')
    const $q = useQuasar()


    return {
      options,
      status,
      saveDialogOpened,
      taskData,
      $q
    }
  },
  methods:{
    onTaskSaved(data){
      this.taskData.title = data.title
      this.taskData.description = data.description
      this.taskData.status = data.status
      this.saveDialogOpened = false
    },
    async onDelete(){
        fetch('http://todo-api/api/tasks/'+this.taskData.id, {
          method: 'DELETE',
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
            this.$emit("taskDeleted")
            this.$q.notify({
              message: data.message,
              color: 'green'
            })
          })
          .catch(error => {
            console.log(error)
          })

    }
  }

})
</script>

<style scoped>
.c-card{
  margin-top: 1%;
  width: 60%;
}
.c-badge{
  margin-left: 0.5%;
}
</style>
