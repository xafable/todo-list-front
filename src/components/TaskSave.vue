<template>
    <q-card style="width: 700px; max-width: 80vw;">
      <q-card-section class="row items-center">
        <q-select style="width: 100%" color="positive" v-model="status" :options="options" label="Status" outlined label-color="black"  />
        <br><br><br><br>
        <q-input style="width: 100%" outlined v-model="title" label="ToDo Title" />
        <br><br><br><br>
        <q-input style="width: 100%" outlined v-model="description" label="ToDo Description" />
      </q-card-section>

      <q-card-actions align="right">
        <q-btn flat label="Cancel" color="deep-orange-14" v-close-popup />
        <q-btn flat label="Save" color="positive"  @click="sendTask"/>
      </q-card-actions>
    </q-card>
</template>

<script>
import {defineComponent, ref} from 'vue'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'TaskSave',
  props: {
    task: Object,
    mode: String,
    listId: Number
  },
  setup (props, ctx) {
    const options = ['Started', 'Finished']
    const status = ref('Started')
    const title = ref('')
    const description = ref('')
    const $q = useQuasar()

    if(props.mode === 'Edit'){
      title.value = props.task.title
      description.value = props.task.description
      status.value = props.task.status
    }

    const sendTask = () => {

      const data = new FormData()
      data.append('taskTitle', title.value)
      data.append('taskDescription', description.value)
      data.append('taskStatus', status.value)

      if(props.mode === 'Create')
       data.append('listId', props.listId.toString())

      const url = props.mode === 'Edit' ? 'http://todo-api/api/tasks/'+props.task.id : 'http://todo-api/api/tasks'

      fetch(url,{
        method: 'POST',
        headers: {
          'Accept': 'application/json',
        },
        body: data
      })
        .then(response => {
          if (response.ok) {
            return response.json();
          }
          else
            return response.json().then(data => { throw new Error(data.message) })
        })
        .then(data => {
          ctx.emit("taskSaved",data.data)

          $q.notify({
            message: data.message,
            color: 'green'
          })
        })
        .catch( error =>  {
          $q.notify({
            message: error.message,
            color: 'red'
          })
        })
    }

    return {
      options,
      status,
      title,
      description,
      sendTask
    }
  }

})
</script>

<style scoped>

</style>
