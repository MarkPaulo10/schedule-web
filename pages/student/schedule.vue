<template>
    <div >
        <a-card>
            <a-row type="flex" justify="space-between" align="center">
                <a-col>
                    <h1 style="font-size: 24px;">Set Appointment</h1>
                </a-col>
            </a-row>
            <a-card class="main-card">
                <a-row type="flex" justify="space-between" gutter="30">
                    <a-col span="12">
                        <a-row type="flex" justify="center">
                            <a-card class="appointment-card">
                                <a-form-model ref="ruleForm" :rules="rules" :model="form" >
                                    <a-form-model-item label="Date" prop="date">
                                        <a-date-picker v-model="form.date" @change="searchDate">
                                        </a-date-picker>
                                    </a-form-model-item>
                                    <a-form-model-item label="Professor">
                                        <a-select v-model="form.teacherId">
                                            <a-select-option v-for="item in filterTeacher" :key="item._id" :value="item.teacherId">
                                                {{ `${item.teacher&&item.teacher.profile&&item.teacher.profile.fname} ${item.teacher&&item.teacher.profile&&item.teacher.profile.lname}` }}
                                            </a-select-option>
                                        </a-select>
                                    </a-form-model-item>
                                    <a-row type="flex" justify="center">
                                        <a-col>
                                            <a-button type="primary" @click="submit">Submit</a-button>
                                        </a-col>
                                    </a-row>
                                    
                                </a-form-model>
                            </a-card>
                        </a-row>
                    </a-col>
                    <a-col span="12">
                        <a-card class="card-height">
                            <a-calendar class="card-height">
                                <template slot="dateCellRender" slot-scope="value">
                                    <!-- <div v-if="hasOpenSchedule(value)">
                                        <a-badge status="succes" />
                                    </div> -->
                                    <div class="status">
                                        <div  v-for="item in schedules" :key="item._id">
                                            <div v-if="hasOpenSchedule(value, item)">
                                                <a-badge status="success" />
                                            </div>
                                            
                                        </div>
                                    </div>
                                </template>
                            </a-calendar>
                        </a-card>
                    </a-col>
                </a-row>
                
            </a-card>
        </a-card>
    </div>
</template>

<script>
import moment from 'moment';
import Cookies from 'js-cookie';
import { off } from 'process';
export default {
    layout: 'header',
    data(){
        return{
            form: {},
            students: [],
            schedules: [],
            // schedules: [
            //     {
            //         _id: 1,
            //         date: '2023-03-03',
            //         studentId: 1,
            //         student: {
            //             _id: 1,
            //             fname: 'park',
            //             lname: 'mruz',
            //             course: 'BSIT',
            //             year: 1,
            //             section: 'a',
            //             description: 'May ipapakiusap'
            //         },
            //         teacherId: 1,
            //         teacher: {
            //             _id: 1,
            //             fname: 'Richard',
            //             lname: 'Gonzales',
            //             subject: 'Math'
            //         }
                    
            //     },
            //     {
            //         _id: 2,
            //         date: '2023-03-05',
            //         studentId: 1,
            //         student: {
            //             _id: 1,
            //             fname: 'park',
            //             lname: 'mruz',
            //             course: 'BSIT',
            //             year: 1,
            //             section: 'a',
            //             description: 'About sa grade'
            //         },
            //         teacherId: 1,
            //         teacher: {
            //             _id: 1,
            //             fname: 'Richard',
            //             lname: 'Gonzales'
            //         }
                    
            //     }
            // ],
            columns: [
                { 
                    title: 'name',
                    key: 'name',
                    scopedSlots: { customRender: 'name'}
                },
                { 
                    title: 'Course - Yr&Sc',
                    key: 'yr&sec',
                    scopedSlots: { customRender: 'yr&sec'}
                },
                { 
                    title: 'Date',
                    dataIndex: 'date',
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
                date: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                teacher: { required: true, message: 'Please fill up this field!', trigger: 'change'},
            },
            addModal: false,
            showModal: false,
            filterTeacher: [],
            activeDate: '',
            professorData: ''
        }
        
    },
    async fetch(){
        await this.getStudent();
        await this.getSchedule();
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
        async getSchedule(){
            try {

                let {data} = await this.$axios.get('/schedules');
                let result = data.filter(e => !e.studentId)
                this.schedules = result;
                console.log("result:>>", this.schedules);
            } catch (error) {
                console.log(error);
            }
            
        },
        hasOpenSchedule(value, item){
            if(!item.studentId){
                let scheduleDate = moment(item.date).format("YYYY-MM-DD")
                let date = moment(value).format('YYYY-MM-DD')
                return scheduleDate === date
            }
        },
        searchDate(value){
            let date = moment(value).format("YYYY-MM-DD")
            console.log("date", date);
            let result = this.schedules.filter( e => moment(e.date).format("YYYY-MM-DD") == moment(date).format("YYYY-MM-DD") && !e.studentId)
            console.log("result", result );
            this.filterTeacher = result;
            if(!result.length){
                this.form.teacherId = '';
            }
        },
        async submit(){
            try {
                this.$refs.ruleForm.validate( async valid => {
                    if(valid){
                        this.form.studentId = this.students._id
                        this.form.status = "pending"
                        console.log("form: >>", this.form); 
                        if(!this.form.teacherId){
                            this.$notification.error({
                                message: 'Error',
                                description: 'Invalid'
                            })
                        } else {
                            let {data} = await this.$axios.post("/schedules/appointment", this.form);
                            console.log("result: >>", data);
                        }
                    } else {
                        this.$notification.error({
                            message: 'Error',
                            description: 'Invalid'
                        })
                    }
                })
            } catch (error) {
                console.log(error);
            }
            
        },
        value(value){
            console.log(value);
        }
        // openSchedule(value){

        // }
    }
}
</script>

<style>
.main-card{
    height: 70vh;
}
.card-height{
    padding: 0;
    height: 450px;
}
.card-height .ant-card-body{
    padding: 0;
}
.ant-fullcalendar{
    height: 50vh;
}
.ant-fullcalendar-date{
    height: 50px !important;
}
.ant-fullcalendar-cell{
    height: 20px;
    width: 40px;
}
.ant-fullcalendar-content{
    height: 30px !important;
    width: 40px;
}
.ant-tabs-bar{
    margin: 0;
}
.ant-fullcalendar-value{
    font-size: 11px;
    height: 15px;
    width: 20px;
}
.status{
    display: flex;
    justify-content: center;
}
.appointment-card{
    height: 46vh;
    width: 100%;
}
</style>