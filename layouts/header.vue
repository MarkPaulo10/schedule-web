<template>
    <div>
        <a-layout>
            <a-layout>
                <a-layout-header>
                    <a-row type="flex" justify="space-between">
                        <a-col>
                            <div class="header-title">
                                <h1 style="color: #fff; font-size: 25px">Scheduling System</h1>
                            </div>
                        </a-col >
                        <a-col :style="$breakpoints.sSm ? 'display: block': 'display: none'">
                            <a-tooltip title="Menu">
                                <a-icon type="menu" style="margin-right: 30px; color: #fff;" @click="menuList"/>
                            </a-tooltip>
                            <a-drawer
                                title="Menu"
                                placement="right"
                                :visible="visible"
                                
                                @close="onClose"
                            >
                            <a-menu
                                style="border-right : none;"
                            >
                                <a-menu-item v-for="menu in menus" :key="menu.path" @click="navigate">
                                    <a-icon :type="menu.icon" />
                                    <span>{{ menu.name}}</span>
                                </a-menu-item>
                                <a-menu-item key="/" @click="navigate">
                                    <a-icon type="logout" />
                                </a-menu-item>
                            </a-menu>
                            </a-drawer>
                        </a-col>
                        <a-col :style="$breakpoints.sSm ? 'display: none': ''">
                            <a-row type="flex" justify="space-between" align="center">
                                <a-col>
                                    <a-tooltip title="Notifications">
                                        <a-badge :count="notifications.length">
                                            <a-icon type="bell" id="bell" class="icons" @click="notification"></a-icon>
                                        </a-badge>
                                    </a-tooltip>
                                    
                                </a-col>
                                <a-col>
                                    <a-tooltip title="Logout">
                                        <a-icon type="logout" class="icons"  @click="logout"/>
                                    </a-tooltip>
                                </a-col>
                            </a-row>
                            <div>

                                
                                
                            </div>
                          
                        </a-col>
                    </a-row>
                </a-layout-header>
            </a-layout>
            <a-layout >
                <a-layout-sider theme="light" :style=" $breakpoints.sSm ? 'display: none;' : 'display: block;'">
                    <a-row type="flex" justify="center">
                        <div style="margin-top: 20px;">
                            
                            
                            <h1>{{ users.role == 'student' ? studentName(profile) : teacherName(profile) }}</h1>
                            <span style="display: flex; justify-content: center;">
                                {{users.role == 'student' ? `${profile.course} - ${profile.year}${profile.section}` : profile.subject }}
                            </span>
                        </div>
                    </a-row>
                    <hr>
                    <a-menu
                        mode="inline"
                        :default-selected-keys="['/profile']"
                    >
                        <a-menu-item v-for="menu in menus" :key="menu.path" @click="navigate">
                            <a-icon :type="menu.icon" />
                            <span>{{ menu.name}}</span>
                        </a-menu-item>
                        <a-menu-item key="/" @click="navigate">
                            <a-icon type="logout" />
                        </a-menu-item>
                    
                    </a-menu>
                </a-layout-sider>
            
                <a-layout style=" height: 100vh;">
                    <!-- {{ $breakpoints }} -->
                    <a-layout-content>
                        <div :style=" $breakpoints.xs ? '' : 'padding: 30px;'">
                            
                            <nuxt/>
                        </div>
                        
                    </a-layout-content>
                </a-layout> 
            </a-layout>
        </a-layout>
    </div>
  
</template>

<script>
import Cookies from 'js-cookie';
export default {
    data(){
        return {
            users: [],
            profile: [],
            notifications: [],
            menus: [
                { icon: 'user', path: '/profile', name: 'Profile' },
                { icon: 'calendar', path: '/schedule', name: 'Schedule' },
            ],
            visible: false
        }
    },
    async fetch(){
        await this.getProfile();
        let token = Cookies.get('token')
        if(!token){
            this.$router.push('/')
        }
        
    },
    methods: {
        navigate(path){
            if(path.key == "/profile"){
                if(this.users.role == 'professor'){
                    this.$router.push('/teacher')
                } else {
                    this.$router.push('/student')
                }
            } else if( path.key == "/schedule") {
                if(this.users.role == 'professor'){
                    this.$router.push('/teacher/schedule')
                } else {
                this.$router.push('/student/schedule')

                }
            } else {
                this.$warning({
                    title: 'Confirmation',
                    content: 'Are you sure you want to log out?',
                    onOk: () => {
                        Cookies.remove('name')
                        this.$router.push('/');
                    }
                });
            }
        },
        menuList(){
            this.visible = true
        },
        onClose() {
            this.visible = false;
        },
        async loadData(role, id) {
            return await this.$axios.get(`/${role}/${id}`);
        },
        async getNotif(role, id){
            return await this.$axios.get(`/notifications/${role}/${id}`)
        },
        async getProfile(){
           try {
                let token = Cookies.get('token');
                let {data} = await this.$axios.get("/users/profile", { headers: { "token": token}});
                this.users = data
                let record = await this.loadData(data.role == 'professor' ? 'teachers' : 'students', data._id);
                console.log("record: >>", record);
                this.profile = record.data;
                console.log("data Role", data.role == "professor" ? 'teachers' : 'students');
                let notifs = await this.getNotif(data.role == 'professor' ? 'teachers' : 'students', this.profile._id);
                this.notifications = notifs.data;
                console.log("notifications: >>", notifs);
           } catch (error) {
            console.log(error);
           }
        },
        logout(){
            this.$warning({
                title: 'Confirmation',
                content: 'Are you sure you want to log out?',
                onOk: () => {
                    Cookies.remove('token')
                    this.$router.push('/');
                }
            });
        },
        teacherName(item){
            return `${item.profile&&item.profile.fname} ${item.profile&&item.profile.lname}`
        },
        studentName(item){
            return `${item.profile&&item.profile.fname} ${item.profile&&item.profile.lname}`
        },
        async notification(){

        }
        
    }
}
</script>

<style>
.ant-layout-header{
    background-color: #1f67dc !important;
    border-bottom: 1px solid #e8e8e8;
    padding: 0;
}
.header-title{
    padding-left: 30px;
}
.ant-layout-sider{
    max-width: 280px !important;
    min-width: 280px !important;
    box-shadow: 8px 0px 8px -10px lightblue;
}
.icons{
    margin-right: 30px; 
    margin-top: 20px; 
    font-size: 25px; 
    color: #fff;
}
.icons:hover{
    color: #cdc8c8;

}
.ant-scroll-number{
    position: absolute;
    top: 20px;
    right: 35px;
}

</style>