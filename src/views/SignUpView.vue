<template>
  <div id="signUpPage" class="bg-yellow">
    <div class="conatiner signUpPage vhContainer">
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
          <h2 class="formControls_txt">註冊帳號</h2>
          <label class="formControls_label" for="email">Email</label>
          <input
            class="formControls_input"
            type="text"
            id="email"
            name="email"
            placeholder="請輸入 email"
            required
            v-model="signupField.email"
          />
          <span>{{ errorMsg.email }}</span>
          <label class="formControls_label" for="name">您的暱稱</label>
          <input
            class="formControls_input"
            type="text"
            name="name"
            id="name"
            placeholder="請輸入您的暱稱"
            required
            v-model="signupField.nickname"
          />
          <span>{{ errorMsg.nickname }}</span>
          <label class="formControls_label" for="pwd">密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="pwd"
            id="pwd"
            placeholder="請輸入密碼"
            required
            v-model="signupField.password"
          />
          <span>{{ errorMsg.password }}</span>
          <label class="formControls_label" for="pwd">再次輸入密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="pwd"
            id="pwd"
            placeholder="請再次輸入密碼"
            required
            v-model="signupField.passwordCheck"
          />
          <span>{{ errorMsg.passwordCheck }}</span>
          <input class="formControls_btnSubmit" type="button" @click="signup" value="註冊帳號" />
          <router-link class="formControls_btnLink" to="/">登入</router-link>
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

const signupField = ref({
  email: '',
  nickname: '',
  password: '',
  passwordCheck: ''
})

const validateEmail = (email) => {
  const re = /\S+@\S+\.\S+/
  return re.test(email)
}

const errorMsg = computed(() => {
  return {
    email: validateEmail(signupField.value.email) ? '' : 'Email 格式錯誤',
    nickname: signupField.value.nickname ? '' : '此欄位不可為空',
    password: signupField.value.password.length >= 6 ? '' : '密碼長度不足',
    passwordCheck:
      signupField.value.password === signupField.value.passwordCheck ? '' : '密碼不一致'
  }
})

const signup = async () => {
  try {
    const res = await axios.post(`${apiUrl}/users/sign_up`, signupField.value)
    if (res.data.status) {
      alert('註冊成功，請登入')
    }
    router.push('/')
  } catch (error) {
    console.log(error)
    alert('註冊失敗: ' + error.response.data.message)
  }
}
</script>