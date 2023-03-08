<template>
    <div>
        <a-card :style=" $breakpoints.xs ? 'margin-bottom: 30px' : 'margin-bottom: 30px; padding: 20px'">
            <span :style=" $breakpoints.md ? 'font-size: 25px;' : $breakpoints.xs ? 'font-size: 20px' : 'font-size: 36px'">Profile</span>
            {{ $breakpoints }}
        </a-card>

        <a-card class="card-height" :style=" $breakpoints.sSm ? 'padding: 0px' : 'padding: 30px'">
            <a-row type="flex" :gutter="[20, 20]">
                <a-col :span="$breakpoints.md ? '24' : $breakpoints.sMd ? '24' : '12'">
                    <a-row type="flex" justify="center">
                        <a-col :span=" $breakpoints.xs ? '20' : $breakpoints.sSm ? '20' : $breakpoints.sm ? '18' : '24'">
                            <h1>Do you want to update your Profile? <span style="color: #1f67dc">Update here <a-icon type="arrow-down" /></span></h1>
                                <a-form-model :form="profile" :rules="rules" ref="ruleForm">
                                    <a-row :gutter="[10]">
                                        <a-col :span="12">
                                            <a-form-model-item label="Firt Name" prop="fname">
                                                <a-input v-model="profile.fname"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="12">
                                            <a-form-model-item label="Middle Name" prop="mname">
                                                <a-input v-model="profile.mname"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="12">
                                            <a-form-model-item label="Last Name"  prop="lname">
                                                <a-input v-model="profile.lname"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="12">
                                            <a-form-model-item label="Suffix"  prop="suffix">
                                                <a-input v-model="profile.suffix"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="8">
                                            <a-form-model-item label="Course"  prop="course">
                                                <a-input v-model="profile.course"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="8">
                                            <a-form-model-item label="Year"  prop="year">
                                                <a-input v-model="profile.year"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="8">
                                            <a-form-model-item label="Section"  prop="section">
                                                <a-input v-model="profile.section"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                    </a-row>
                                   
                                    
                                    
                                   
                                    
                                   
                                   
                                </a-form-model>
                            
                            <a-row type="flex" justify="center">
                            <a-button @click="saveProfile">Save</a-button>
                            </a-row>
                        </a-col>
                    </a-row>
                    
                </a-col>
                <a-col :span=" $breakpoints.md  ? '24' : $breakpoints.sMd ? '24' : '12'">
                    <a-card :style=" $breakpoints.xs ? 'width: 100%; padding: 10px' : 'padding: 30px'">
                        <div>
                            <h1>Booked List</h1>
                        </div>
                        <a-row>
                            <a-col :span="24">
                                <a-table :data-source="appointments" :columns="columns" >
                                    <span slot="name" slot-scope="rec">{{ `${ rec.teacher&&rec.teacher.profile&&rec.teacher.profile.fname} ${rec.teacher&&rec.teacher.profile&&rec.teacher.profile.lname}` }}</span>
                                    <span slot="date" slot-scope="rec">{{ dateFormatter(rec.date)}}</span>
                                    <span slot="action" slot-scope="rec"><a style="font-size: 16px; color: red;" @click="cancel(rec)">Cancel</a></span>
                                </a-table>
                            </a-col>
                        </a-row>
                        
                    </a-card>
                </a-col>
            </a-row>
           
        </a-card>
       
    </div>
</template>

<script>
import Cookies from 'js-cookie';
export default {
    layout: 'header',
    data(){
        return{
            profile: {},
            students: [],
            appointments: [],
            columns: [
                {
                    title: 'Professor',
                    key: 'name',
                    scopedSlots: { customRender: 'name'}
                },
                {
                    title: 'Date',
                    key: 'date',
                    scopedSlots: { customRender: 'date'}
                },
                {
                    title: 'Action',
                    key: 'action',
                    scopedSlots: { customRender: 'action'}
                },
                
            ],
            rules: {
                fname: { required: true, message: 'Please fill up this fields', trigger: 'change'},
                lname: { required: true, message: 'Please fill up this fields', trigger: 'change'},

                course: { required: true, message: 'Please fill up this fields', trigger: 'change'},
                year: { required: true, message: 'Please fill up this fields', trigger: 'change'},
                section: { required: true, message: 'Please fill up this fields', trigger: 'change'},
            }
        }
    },
    computed: {
        
    },
    async fetch(){
        await this.getStudent();
        await this.getApointment();
    },
    methods: {
        async getStudent(){
           try {
             let token = Cookies.get('token');
             let {data} = await this.$axios.get("/users/profile", { headers: { "token": token}});
             let studentData = await this.$axios.get(`/students/${data._id}`);

             this.students = studentData.data
           } catch (error) {
            console.log(error);
           }
            // let {data} 
        },
        async getApointment(){
            let {data} = await this.$axios.get("/schedules");
            let result = data.filter( e => e.studentId == this.students._id)
            this.appointments = result;
            console.log(this.appointments);
        },
        async saveProfile(){
            try {
                console.log("result: >>", this.profile);
            } catch (error) {
                console.log(error);
            }
        },
        dateFormatter(date){
            let month = new Date(date).toString().split(' ')[1];
            let splitter = new Date(date).toLocaleDateString().split('/');
            return `${month} ${splitter[1]}, ${splitter[2]}`;
        }

    }
}
</script>

<style>
.ant-card-body{
    padding: 0;
}
</style>