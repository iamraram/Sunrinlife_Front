<template>
    <template v-if="userInfo">
        <div class="panel panel-profile">
            <div class="user-profile">
                
                <div class="user-profile-background-item">
                    <img v-if="isEditable && editBackgroundImage" class="user-profile-background-items" :src="editBackgroundImage">
                    <img v-else-if="userInfo.backgroundImage" class="user-profile-background-items" :src="userInfo.backgroundImage">
                    <div v-else class="user-profile-background"></div>

                    <input v-if="isEditable" type="file" ref="image" @change="uploadImage" id="profile-background-choice">
                    <label v-if="isEditable" for="profile-background-choice">
                        <img src="../assets/user_profile_assets/add.svg">
                    </label>
                </div>
                

                <!-- 프로필 이미지 -->
                <div class="user-img-items">
                        <img v-if="isEditable && editProfileImage" class="user-img-item" :src="editProfileImage" />
                        <img v-else-if="userInfo.image" class="user-img-item" :src="userInfo.image" />
                        <img v-else class="user-img-item" src="../assets/user_profile_assets/basic_profile_img.svg" />

                        <input v-if="isEditable" type="file" ref="image" @change="uploadProfileImage" id="profile-img-choice">
                        <label v-if="isEditable" for="profile-img-choice">
                            <img src="../assets/user_profile_assets/correctionIcon_white.svg" alt="">
                        </label>
                </div>
                <!-- 정보 수정 버튼 -->
                <div v-if="isMyProfile" class="info-correcrion-button" @click="!isEditable?changeProfile():updateProfile()">
                    <img class="correction-button-img" src="../assets/user_profile_assets/correctionIcon.svg"
                        v-if="!isEditable"/>
                    <img class="correction-button-img" src="../assets/user_profile_assets/checkIcon.svg"
                        v-else/>
                </div>



                <!-- 유저 정보(동아리, 이메일, 소개 등) -->
                <div class="user-info-items">
                    <div class="user-info-wrapper">
                        <div class="user-info-top-wrapper">
                            <div class="user-title">
                                <div class="user-name">{{userInfo.username}}</div>
                                <div class="user-info-top-left">
                                    <div>{{ department_map[userInfo.department] }}</div>
                                    <div>{{ userInfo.grade }}학년 {{ userInfo.class }}반 {{ userInfo.number }}번</div>
                                </div>
                            </div>
                            <div class="user-info-group">
                                <div class="user-info-top-right">
                                    <div class="user-info-contents" v-if="userInfo.clubInfo?.name !== undefined">
                                        <div class="user-info-label">동아리</div>
                                        <div class="user-info-content">{{ userInfo.clubInfo?.name }}</div>
                                        <!-- <select v-else name="club" id="club-select" v-model="editClubData">
                                            <option v-for="(i, k) in loadedClubData" :value="i.id" :key="k">{{ i.id }}</option>
                                        </select> -->
                                    </div>
                                    <div class="user-info-contents">
                                        <div class="user-info-label">메일</div>
                                        <div class="user-info-content">{{ userInfo.email }}</div>
                                    </div>
                                    <div class="user-info-contents" v-if="userInfo?.githubLink || isEditable">
                                        <div class="user-info-label">GITHUB</div>
                                        <a :href="userInfo.githubLink" target="_blank" v-if="!isEditable" class="user-info-content">{{ userInfo.githubLink }}</a>
                                        <input class="github" v-else type="text" v-model="editGithubLink">
                                    </div>
                                </div>
                            </div>
                        </div>

                        <hr v-if="userInfo.description" :class="{ hr_after: isEditable || !userInfo.githubLink || !userInfo.clubInfo, hr_before: !isEditable }" color="#a9a9a9"> 


                        <div class="user-info-bottom-wrapper">
                            <div class="user-info-description">
                                <div class="user-info-label" v-if="userInfo.description || isEditable">소개</div>
                                <div class="user-info-content" v-if="!isEditable">{{ userInfo?.description }}</div>
                                <textarea v-else class="description-input" type="text" v-model="editDescription"></textarea>
                            </div>
                        </div>
                    </div>
                </div>

                <span v-if="!isEditable && isMyProfile" class="logout-btn" @click="logoutClick">로그아웃</span>
            </div>
        </div>
    </template>
</template>

<script>
import { mapState } from "vuex"
// import { getClubAll } from '../api.js'
// import store from '../store.js'
import { editProfileData, getUserDataById, logout } from "../api.js"
export default {
    data() {
        return {
            isEditable : false, //현재 수정 모드인지 아닌지를 판단

            editClubInfo : "",
            editSubClubInfo : "",
            editGithubLink : "",
            editDescription : "",
            editProfileImage : "",
            editBackgroundImage : "",

            userInfo: null
            // 수정을 입력받은 데이터
        } 
    },
    methods: {
        uploadImage(payload){
            const reader = new FileReader()

            reader.onload = () => {
                const key = {
                    'profile-img-choice': 'editProfileImage',
                    'profile-background-choice': 'editBackgroundImage'
                };
                this[key[payload.target.id]] = reader.result;
            }
            
            reader.readAsDataURL(payload.target.files[0]);
        },
        async updateProfile(){
            const update = {}
            if (this.editGithubLink !== this.userInfo.githubLink){
                if(this.editGithubLink !== ""){
                    if(this.editGithubLink.indexOf("https://github.com/") === -1){
                        alert("정확한 깃허브 링크를 입력해주세요.");
                        return;
                    }
                }
                this.userInfo.githubLink = update["githubLink"] = this.editGithubLink;
            }
            if (this.editProfileImage !== this.userInfo.image)
                this.userInfo.image = update["image"] = this.editProfileImage;
            if (this.editBackgroundImage !== this.userInfo.backgroundImage)
                this.userInfo.backgroundImage = update["backgroundImage"] = this.editBackgroundImage;
            if (this.editDescription !== this.userInfo.description)
                this.userInfo.description = update["description"] = this.editDescription;
            
            if (this.editClubInfo){
                if (!this.userInfo.clubInfo || this.editClubInfo != this.userInfo.clubInfo.id){
                    if (!this.userInfo.clubInfo) this.userInfo.clubInfo = {};
                    update["clubInfo"] = this.editClubInfo || 0;
                    this.userInfo.clubInfo.name = "로딩중...";
                }
            }
            if (this.editSubClubInfo){
                if (!this.userInfo.subClubInfo) this.userInfo.subClubInfo = [];
                update["subClubInfo"] = this.editSubClubInfo.split(',')
                    .map(x => parseInt(x))
                    .filter(x => x)
                this.userInfo.subClubInfo = [{name: "로딩중..."}]
            }
            this.isEditable = false

            if (Object.keys(update).length > 0){
                console.log(update)
                await editProfileData(update);
            }
        },
        changeProfile(){
            this.isEditable = true
            // getClubAll().then((data)=>{
            //     store.commit("setClubData", {id:"all",data})
            //     console.log(data)
            // })
        },
        logoutClick(){
            logout().then(res => {
                if(res === "success") this.$router.push({ name: 'login' })
            })
        },
        setEditData(val){
            if (!val) return;
            this.editClubInfo = val.clubInfo?.id;
            this.editGithubLink = val.githubLink;
            this.editDescription = val.description;
            this.editProfileImage = val.image;
            this.editBackgroundImage = val.backgroundImage;
            this.editSubClubInfo = val.subClubInfo?.map(x => x.id).join(',');
        },
        async loadData(){
            if (this.isMyProfile){
                this.userInfo = this.userData;
            }
            else
                this.userInfo = await getUserDataById(this.userId);
        }
    },
    computed:{
        getAuthToken() {
            return this.$store.getters.getAuthToken;
        },
        ...mapState(["userData", "department_map", "clubData"]),
        githubID: function() {
            return this.userInfo?.githubLink.split('/').filter(x=>x).pop();
        },
        userId() {
            return this.$route.params.profileId;
        },
        isMyProfile() {
            return this.userId === undefined;
        },
        loadedClubData(){
            return this.clubData["all"]
        }
    },
    watch: {
        getAuthToken() {
            this.loadData()
        },
        userData: function(){
            if (this.isMyProfile)
                this.userInfo = this.userData;
        },
        userInfo: function(val) {
            this.setEditData(val);
        },
        $route() {
            this.loadData();
        },
    },
    mounted() {
        this.loadData();
    }
}
</script>

<style lang="scss">
    @import "src/assets/style/profile/profile.scss";
    @import "src/assets/style/mainPage_v2/style.css";
</style>
