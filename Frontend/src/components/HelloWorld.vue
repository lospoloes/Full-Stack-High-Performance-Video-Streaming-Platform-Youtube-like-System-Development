<template>
  <div class="user-login-form-container">
    <div class="user-login-form">
      <div class="user-login-form-title">
        Password Login - Home
      </div>
      <div class="user-login-form-item">
        <div class="user-login-form-item-title">
          Account
        </div>
        <input placeholder="Please enter your account" type="text" v-model="account">
      </div>
      <div class="user-login-form-item">
        <div class="user-login-form-item-title">
          Password
        </div>
        <input placeholder="Please enter your password" type="password" v-model="password">
        <div class="forgetPwd">
          Forgot Password?
        </div>
      </div>
      <div class="user-login-form-footer">
        <button v-on:click="register(account, password)" class="user-login-form-footer-btn">
          Register
        </button>
        <button @click="login" class="user-login-form-footer-btn">
          Login
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data(){
    return {
      account:'',
      password:''
    }
  },

  methods:{

    register(account, password){
      console.log('account', account);
      console.log('password', password);
      this.account++;
    },

    login(){
      if(this.isUserLoggedIn){
        console.log('User is already logged in');
      }else{
        console.log('User is not logged in');
      }
      this.$destroy();
    }
  },

  computed:{

    isUserLoggedIn(){
      return localStorage.getItem('token');
    }
  },

  beforeCreate() {
    console.log('beforeCreate');
  },

  created() {
    console.log('created');
  },

  beforeMount() {
    console.log('beforeMount');
  },

  mounted() {
    console.log('mounted');
  },

  beforeUpdate() {
    console.log(this.account);
    console.log('beforeUpdate');
  },

  updated() {
    console.log(this.account);
    console.log('updated');
  },

  beforeDestroy() {
    console.log(this.account);
    console.log('beforeDestroy');
  },

  destroyed() {
    console.log('destroyed');
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">

.user-login-form-container{
  margin-top: 10%;
  margin-left: 20%;
  margin-right: 20%;
  background-color: #ececec;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 10px;

  .user-login-form {
    padding: 40px;
    display: flex;
    flex-direction: column;

    .user-login-form-title{
      font-size: 24px;
      text-align: center;
      margin-bottom: 40px;
      font-weight: bold;
    }

    .user-login-form-item{
      display: flex;
      font-size: 20px;
      margin-bottom: 20px;
      align-items: center;
      border: darkgray solid 1px;
      background-color: white;
      border-radius: 5px;
      padding: 6px;

      .user-login-form-item-title{
        margin-right: 10px;
        margin-bottom: 5px;
      }

      input{
        font-size: 18px;
        margin-right: 10px;
        border: none;
        outline: none;
      }

      .forgetPwd{
        font-size: 16px;
        color: dodgerblue;
        font-weight: bold;
        cursor: pointer;
      }

    }

    .user-login-form-footer{
      display: flex;
      justify-content: space-between;
    }
  }
}

</style>