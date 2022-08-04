<template>

<div class="login">
    <div class="login-box">
        <div class="logo">
            <img class="logo_symbol" src="../assets/symbol.svg">
            <img class="logo_text" src="../assets/logo.svg">
        </div>
        <button @click="loginClick" class="login-btn">학교 계정으로 로그인</button>
    </div>

</div>
</template>
<script>

import { mapActions } from "vuex"
//import axios from "axios"

export default {
    name : "Login",
    setup(){
    },
    data(){return{

    }},
    methods : {
        ...mapActions(["login", "getToken"]),

        loginClick(){
            const params = new URLSearchParams({
                redirect: window.location.origin + (import.meta.env.PUBLIC_PATH || "")
            });

            window.location.href = `${import.meta.env.VITE_API_URL}/auth/google?${params.toString()}`;

            //this.login({ "email" : this.inputEmail, "password" : this.inputPassword})    
        }
    },
    mounted(){
        
    },
    watch : {
    }
}
</script>

<style lang="scss">
    @import "src/assets/style/login/login.scss";
    @import "src/assets/style/mainPage_v2/style.css";
</style>