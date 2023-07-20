<template>

  <q-input bottom-slots v-model="listTitle" label="List title" class="c-list-input" color="positive">
    <template v-slot:append>
      <q-btn round dense flat icon="add" @click="sendList"/>
    </template>
  </q-input>

</template>

<script>
import {defineComponent, ref} from 'vue'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'ListCreate',
  setup (props, ctx) {
    const listTitle = ref('')
    const $q = useQuasar()


    const sendList = () => {
      const data = new FormData()
      data.append('listTitle', listTitle.value)

      fetch('http://todo-api/api/lists',{
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
          ctx.emit("listAdded" )
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
      listTitle,
      sendList
    }
  }

})
</script>

<style scoped>

</style>
