<template>
  <q-layout view="hHh lpR fFf">

    <q-drawer
      v-model="drawerOpen"
      :width="300"
      bordered
    >
      <ListCreate @listAdded="fetchList"></ListCreate>

      <q-list bordered separator>
        <q-item clickable v-ripple v-for="item in listData" @click.prevent="$router.push('/'+item.id)" :active="isActiveLink(item.id)" active-class="c-active">
          <q-item-section>{{ item.title }}</q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref, computed } from 'vue'
import {useRoute} from "vue-router"
import ListCreate from "components/ListCreate.vue";

export default defineComponent({
  name: 'MainLayout',
  components: {ListCreate},
  setup () {
    const drawerOpen = ref(true)
    const listData = ref([])
    const route = useRoute();

    const isActiveLink = (id) => {
      return route.params.id == id
    }

    const fetchList = () => {
      fetch('http://todo-api/api/lists', {
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
          listData.value = data.data
        })
        .catch(error => {
          console.log(error)
        })
    }

    return {
      drawerOpen,
      listData,
      fetchList,
      isActiveLink
    }
  },
  mounted() {
    this.fetchList()
    this.drawerOpen = true
  }
})
</script>

<style scoped>
.c-list-input{
  margin-left: 1%;
}
.c-active{
  background: rgba(9, 255, 0, 0.14);
}
</style>
