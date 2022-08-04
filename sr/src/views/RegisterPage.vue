<template>

<div class="register">
    <div class="register_panel">
        <div class="logo">
            <img class="logo_symbol" src="../assets/symbol.svg">
            <img class="logo_text" src="../assets/logo.svg">
        </div>
        <div class="profile">
            <img class="profile_img" src="../assets/user_profile_assets/basic_profile_img.svg">
        </div>
        <div class="user_info_panel">
            <div class="input_panel">
                <p>이름</p>
                <input class="user_name" placeholder="이름" :value="userData?.username" readonly>
            </div>
            <div class="input_panel">
                <p>학과</p>
                <input class="user_department" placeholder="학과" :value="department_map[userData?.department]" readonly>
            </div>
            <div class="input_panel">
                <p>학번</p>
                <input class="user_number" placeholder="학번" :value="studentID" readonly>
            </div>
            <div class="input_panel">
                <p>Github</p>
                <input class="user_github" placeholder="Github" v-model="editGithubLink">
            </div>
            <div class="input_panel introduce">
                <p>소개</p>
                <textarea class="user_introduce" placeholder="소개" v-model="editDescription"></textarea>
            </div>
        </div>

        <p class="rull">아래 단추를 눌러 계속하면 선린라이프에서 제공하는 <a href="https://legal.sunrint.life/privacy" target="_blank">개인정보처리방침</a>에 동의하는 것으로 간주됩니다.</p>

        <button class="register" @click="updateProfile">선린 라이프 시작하기</button>
    </div>

</div>
</template>
<script>
import { mapState } from 'vuex'
import { department_map } from '../store.js'
import { editProfileData, getUserDataAndCommit } from '../api.js'

export default {
    name:"register",
    data(){
        return {
            editGithubLink : "",
            editDescription : "",
        } 
    },
    setup() {
        
    },
    updated(){

    },
    computed:{
        ...mapState(["userData"]),
        department_map: function() {
            return department_map;
        },
        studentID: function() {
            if (!this.userData) return null;
            return this.userData?.grade
                + this.fillZero(2, this.userData?.class)
                + this.fillZero(2, this.userData?.number)
        }
    },
    methods:{
        fillZero(width, str){
            if (!str) return null;
            if (typeof str != "string") str = str.toString();
            return '0'.repeat(Math.max(0, width-str?.length)) + str;//남는 길이만큼 0으로 채움
        },
        async updateProfile(){
            editProfileData({
                "githubLink": this.editGithubLink,
                "description": this.editDescription
            }).then(() => getUserDataAndCommit())
            .then(() => this.$router.push({ name: "main" }));
        }
    }
}
</script>

<style lang="scss">
    @import "src/assets/style/register/register.scss";
    @import "src/assets/style/mainPage_v2/style.css";
</style>
