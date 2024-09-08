<template>
  <div id="todoListPage" class="bg-half">
    <nav>
      <h1><a href="#">ONLINE TODO LIST</a></h1>
      <ul>
        <li class="todo_sm">
          <a href="#todolist"
            ><span>{{ user.nickname }}的代辦</span></a
          >
        </li>
        <li><a href="" @click.prevent="signOut">登出</a></li>
      </ul>
    </nav>
    <div class="conatiner todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input type="text" placeholder="請輸入待辦事項" v-model="addTodoField.content" required />
          <a href="#" @click.prevent="addTodo">
            <i class="fa fa-plus"></i>
          </a>
        </div>
        <div class="todoList_list">
          <ul class="todoList_tab">
            <li>
              <a href="#" :class="{ active: filter === 'all' }" @click.prevent="setFilter('all')"
                >全部</a
              >
            </li>
            <li>
              <a
                href="#"
                :class="{ active: filter === 'active' }"
                @click.prevent="setFilter('active')"
                >待完成</a
              >
            </li>
            <li>
              <a
                href="#"
                :class="{ active: filter === 'completed' }"
                @click.prevent="setFilter('completed')"
                >已完成</a
              >
            </li>
          </ul>
          <div class="todoList_items">
            <ul class="todoList_item">
              <li v-for="item in filteredTodos" :key="item.id">
                <label class="todoList_label">
                  <input
                    class="todoList_input"
                    type="checkbox"
                    v-model="item.status"
                    @change="toggleStatus(item.id)"
                  />
                  <span>{{ item.content }}</span>
                </label>
                <a href="#" @click.prevent="deleteTodo(item.id)">
                  <i class="fa fa-times"></i>
                </a>
              </li>
            </ul>
            <div v-if="todos.length === 0" class="todoList_empty">
              <p>目前尚無待辦事項</p>
            </div>
            <div v-if="todos.length > 0" class="todoList_statistics">
              <p>{{ activeCount }}個待完成項目</p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-if="isLoading" class="loading-overlay">
      <div class="loading-spinner"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const router = useRouter()

const token = ref('')
const apiUrl = 'https://todolist-api.hexschool.io'
const user = ref({
  nickname: '',
  uid: ''
})
const todos = ref([])
const addTodoField = ref({
  content: ''
})
const filter = ref('all')
const isLoading = ref(false)

const activeCount = computed(() => {
  return todos.value.filter((todo) => !todo.status).length
})

const filteredTodos = computed(() => {
  switch (filter.value) {
    case 'all':
      return todos.value
    case 'active':
      return todos.value.filter((todo) => !todo.status)
    case 'completed':
      return todos.value.filter((todo) => todo.status)
    default:
      return []
  }
})

const setFilter = (newFilter) => {
  if (isLoading.value) return
  filter.value = newFilter
}

const getTodos = async () => {
  try {
    isLoading.value = true
    const res = await axios.get(`${apiUrl}/todos/`, {
      headers: {
        authorization: token.value
      }
    })
    todos.value = res.data.data
  } catch (err) {
    console.log('err', err)
  } finally {
    isLoading.value = false
  }
}

const addTodo = async () => {
  try {
    isLoading.value = true
    await axios.post(`${apiUrl}/todos/`, addTodoField.value, {
      headers: {
        authorization: token.value
      }
    })
    addTodoField.value.content = ''
    await getTodos()
  } catch (err) {
    console.log('err', err)
  } finally {
    isLoading.value = false
  }
}

const signOut = async () => {
  try {
    isLoading.value = true
    await axios.post(`${apiUrl}/users/sign_out`, null, {
      headers: {
        authorization: token.value
      }
    })
    document.cookie = 'customTodoToken=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;'
    router.push('/')
  } catch (err) {
    console.log('err', err)
  } finally {
    isLoading.value = false
  }
}

const toggleStatus = async (id) => {
  try {
    isLoading.value = true
    await axios.patch(`${apiUrl}/todos/${id}/toggle`, null, {
      headers: {
        authorization: token.value
      }
    })
    await getTodos()
  } catch (err) {
    console.log('err', err)
  } finally {
    isLoading.value = false
  }
}

const deleteTodo = async (id) => {
  try {
    isLoading.value = true
    await axios.delete(`${apiUrl}/todos/${id}`, {
      headers: {
        authorization: token.value
      }
    })
    await getTodos()
  } catch (err) {
    console.log('err', err)
  } finally {
    isLoading.value = false
  }
}

onMounted(async () => {
  token.value = document.cookie.replace(
    /(?:(?:^|.*;\s*)customTodoToken\s*=\s*([^;]*).*$)|^.*$/,
    '$1'
  )
  console.log('token', token.value)
  if (!token.value) {
    alert('沒有token')
    router.push('/')
  }
  try {
    isLoading.value = true
    const res = await axios.get(`${apiUrl}/users/checkout`, {
      headers: {
        authorization: token.value
      }
    })
    user.value = res.data
    await getTodos()
  } catch (err) {
    console.log('err', err)
    alert('請重新登入')
    router.push('/')
  } finally {
    isLoading.value = false
  }
})
</script>

<style scoped>
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.loading-spinner {
  border: 16px solid #f3f3f3;
  border-top: 16px solid #3498db;
  border-radius: 50%;
  width: 120px;
  height: 120px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>