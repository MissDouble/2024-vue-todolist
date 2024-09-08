<template>
    <div id="loginPage" class="bg-yellow">
      <div class="conatiner loginPage vhContainer">
        <div class="side">
          <a href="#"
            ><img
              class="logoImg"
              src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/logo.png"
              alt=""
          /></a>
          <img
            class="d-m-n"
            src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/img.png"
            alt="workImg"
          />
        </div>
        <div>
          <form class="formControls" action="index.html">
            <h2 class="formControls_txt">最實用的線上代辦事項服務</h2>
            <label class="formControls_label" for="email">Email</label>
            <input
              class="formControls_input"
              type="text"
              id="email"
              name="email"
              placeholder="請輸入 email"
              required
              v-model="signinField.email"
            />
            <span>{{ errorMsg.email }}</span>
            <label class="formControls_label" for="pwd">密碼</label>
            <input
              class="formControls_input"
              type="password"
              name="pwd"
              id="pwd"
              placeholder="請輸入密碼"
              required
              v-model="signinField.password"
            />
            <span>{{ errorMsg.password }}</span>
            <input class="formControls_btnSubmit" type="button" @click="signin" value="登入" />
            <router-link class="formControls_btnLink" to="/signup">註冊帳號</router-link>
          </form>
        </div>
      </div>
    </div>
  </template>
  <script setup>
  import { ref, computed } from 'vue'
  import { useRouter } from 'vue-router'
  import axios from 'axios'
  
  const router = useRouter()
  
  const apiUrl = 'https://todolist-api.hexschool.io'
  
  const signinField = ref({
    email: '',
    password: ''
  })
  const signinRes = ref('')
  const errorMsg = computed(() => {
    return {
      email: signinField.value.email ? '' : '此欄位不可為空',
      password: signinField.value.password ? '' : '此欄位不可為空'
    }
  })
  
  const signin = async () => {
    try {
      const res = await axios.post(`${apiUrl}/users/sign_in`, signinField.value)
      signinRes.value = res.data.token
  
      const expires = new Date()
      expires.setDate(expires.getDate() + 1)
      document.cookie = `customTodoToken=${res.data.token}; expires=${expires.toUTCString()};`
      router.push('/todolist')
    } catch (err) {
      errorMsg.value.response = '帳號或密碼錯誤'
      alert('帳號或密碼錯誤')
    }
  }
  </script>