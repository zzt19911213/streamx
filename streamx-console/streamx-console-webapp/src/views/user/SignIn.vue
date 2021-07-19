<template>
  <div :class="['container-wrapper', device]">
    <div class="container">
      <div class="main">
        <div class="top">
          <div class="header">
            <a href="/">
              <img
                height="100"
                width="300"
                src="~@/assets/imgs/logo3.svg"
                class="logo"
                alt="logo">
            </a>
          </div>
        </div>
        <a-form
          id="formLogin"
          class="signin-form"
          ref="formLogin"
          :form="form"
          @submit="handleSubmit">
          <a-form-item>
            <a-input
              size="large"
              type="text"
              v-decorator="[
                'username',
                {rules: [{ required: true, message: '请输入账号' }], validateTrigger: 'change'}
              ]">
              <a-icon
                slot="prefix"
                type="user"
                class="icon"/>
            </a-input>
          </a-form-item>

          <a-form-item>
            <a-input
              size="large"
              type="password"
              autocomplete="false"
              v-decorator="[
                'password',
                {rules: [{ required: true, message: '请输入密码' }], validateTrigger: 'blur'}
              ]">
              <a-icon
                slot="prefix"
                type="lock"
                class="icon"/>
            </a-input>
          </a-form-item>

          <a-form-item
            style="margin-top:40px">
            <a-button
              size="large"
              type="primary"
              html-type="submit"
              class="signin-button"
              :loading="state.loginBtn"
              :disabled="state.loginBtn">
              登录
            </a-button>
          </a-form-item>
        </a-form>
      </div>

      <div class="footer">
        <div class="links">
          <a href="_self">帮助</a>
          <a href="_self">隐私</a>
          <a href="_self">条款</a>
        </div>
        <div
          class="copyright">
          Copyright &copy; 2021~{{ year }} 宁波港信息通信有限公司
        </div>
      </div>
    </div>
  </div>
</template>

<script type="application/ecmascript">
import {mapActions} from 'vuex'
import {mixinDevice} from '@/utils/mixin'

export default {
  mixins: [mixinDevice],
  data() {
    return {
      loginBtn: false,
      form: this.$form.createForm(this),
      state: {
        time: 60,
        loginBtn: false
      },
      year: new Date().getFullYear()
    }
  },

  mounted() {
    const index = this.randomNum(1, 4)
    $('.main').css({
      'background': index > 2 ? 'rgba(122, 200, 255, .2)' : 'rgba(0, 0, 0, .2)'
    })
    // const bgUrl = require('@assets/bg/' + index + '.png')
    //$('#particles-js').css('background-image', 'url(' + bgUrl + ')')
  },

  methods: {
    ...mapActions(['SignIn']),
    // handler
    handleRember() {

    },
    handleSubmit(e) {
      e.preventDefault()
      const {
        form: { validateFields },
        state,
        SignIn
      } = this
      state.loginBtn = true
      const validateFieldsKey = ['username', 'password']
      validateFields(validateFieldsKey, {force: true}, (err, values) => {
        if (!err) {
          const loginParams = {...values}
          SignIn(loginParams)
            .then(resp => {
              if (resp.code != null) {
                const message = 'SignIn failed,' + (resp.code === 0 ? ' authentication error' : ' current User is locked.')
                this.$message.error(message)
              } else {
                this.$router.push({path: '/flink/app'})
              }
            })
            .catch(err => console.log(err))
            .finally(() => {
              state.loginBtn = false
            })
        } else {
          setTimeout(() => {
            state.loginBtn = false
          }, 600)
        }
      })
    }
  }
}
</script>
<style lang="less">
@import "SignIn";
</style>
