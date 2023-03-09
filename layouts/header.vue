<template>
    <div>
        <a-layout>
            <a-layout>
                <a-layout-header>
                    <a-row type="flex" justify="space-between">
                        <a-col>
                            <div class="header-title">
                                <h1 style="color: #fff">Scheduling System</h1>
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
                            <a-icon type="logout" style="margin-right: 30px; margin-top: 20px; font-size: 25px; color: #fff;" @click="logout"/>
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
            menus: [
                { icon: 'user', path: '/profile', name: 'Profile' },
                { icon: 'calendar', path: '/schedule', name: 'Schedule' },
            ],
            visible: false
        }
    },
    async fetch(){
        await this.getProfile();
        
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
        async getProfile(){
           try {
             let token = Cookies.get('token');
             let {data} = await this.$axios.get("/users/profile", { headers: { "token": token}});
             this.users = data

             let record = await this.loadData(data.role == 'professor' ? 'teachers' : 'students', data._id);
            console.log("record: >>", record);
             this.profile = record.data
             
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

</style>