<template>
    <div>
        <a-layout>
            <a-layout-header theme="light">
                <a-row type="flex" justify="space-between">
                    <a-col>
                        <div class="header-title">
                            <h1>Pwede na logo ng app</h1>
                        </div>
                    </a-col>
                    <a-col>
                        <a-menu 
                            mode="horizontal"
                            :default-selected-keys="['/student']"
                            :style="{ lineHeight: '62px' }"
                        >
                            <a-menu-item v-for="menu in menus" :key="menu.path" @click="navigate">
                                {{menu.name}}
                            </a-menu-item>
                            <a-menu-item key="/" @click="navigate">
                                <a-icon type="logout" />
                            </a-menu-item>
                        
                        </a-menu>
                    </a-col>
                </a-row>
            
            </a-layout-header>
            <a-layout>
                <a-layout-content>
                    <div style="margin-top: 50px; height: 88vh;">
                        <nuxt/>
                    </div>
                    
                </a-layout-content>
            </a-layout>
        </a-layout>
    </div>
  
</template>

<script>
export default {
    data(){
        return {
            menus: [
                { path: '/student', name: 'Profile' },
                { path: '/schedule', name: 'Schedule' },
            ]
        }
    },
    methods: {
        navigate(path){
            if(path.key == "/student"){
                this.$router.push('/student')
            } else if( path.key == "/schedule") {
                this.$router.push('/student/schedule')
            } else {
                this.$warning({
                    title: 'Confirmation',
                    content: 'Are you sure you want to log out?',
                    onOk: () => {
                        this.$router.push('/');
                    }
                });
            }
        }
    }
}
</script>

<style>
.ant-layout-header{
    background-color: #fff;
    border-bottom: 1px solid #e8e8e8;
    padding: 0;
}
.header-title{
    padding-left: 30px;
}
</style>