<template>
    <div >
        <a-card :style=" $breakpoints.xs ? 'margin-bottom: 30px' : 'margin-bottom: 30px; padding: 10px'">
            <span :style=" $breakpoints.md ? 'font-size: 25px;' : $breakpoints.xs ? 'font-size: 20px' : 'font-size: 36px'">Schedule</span>
            <!-- {{ $breakpoints}} -->
        </a-card>
        <a-card>
            <a-row type="flex" justify="space-between" align="center">
                <a-col>
                    <h1 style="font-size: 24px;">Set Appointment</h1>
                </a-col>
            </a-row>
        
            <a-row type="flex" justify="space-between" :gutter="[30,20]">
                <a-col :span="$breakpoints.sLg ? 24 : 12">
                    <a-row type="flex" justify="center" :gutter="[0,20]">
                        <a-col :span="24">
                            <a-card class="appointment-card">
                                <a-form-model ref="ruleForm" :rules="rules" :model="form" >
                                    <a-row type="flex" :gutter="[10]">
                                        <a-col :span="12">
                                            <a-form-model-item label="Date" prop="date">
                                                <a-date-picker v-model="form.date" @change="searchDate" />
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="12">
                                            <a-form-model-item label="Professor">
                                                <a-select v-model="form.teacherId">
                                                    <a-select-option v-for="item in filterTeacher" :key="item._id" :value="item.teacherId">
                                                        {{  nameFormater(item) }}
                                                    </a-select-option>
                                                </a-select>
                                            </a-form-model-item>
                                        </a-col>
                                    </a-row>
                                </a-form-model>
                                <a-row :gutter="[0,20]">
                                    <a-col>
                                        <a-row type="flex" justify="center" >
                                            <a-button @click="submit">Submit</a-button>
                                        </a-row>
                                    </a-col>
                                    <a-col :span="24">
                                        <a-table :data-source="appointments" :columns="columns" :scroll="$breakpoints.xs ? {x: 500} : {}">
                                            <span slot="name" slot-scope="rec">{{ nameFormater(rec) }}</span>
                                            <span slot="status" slot-scope="rec">
                                                <a-tag
                                                    :color="rec.status == 'pending' ? 'orange' : rec.status == 'approved' || rec.status == 'done' ? 'green' : rec.status == 'rejected' ? 'red' : 'volcano' "
                                                >
                                                    {{ rec.status }}
                                                </a-tag>
                                            </span>
                                            <span slot="date" slot-scope="rec">{{ dateFormatter(rec.date)}}</span>
                                            <span slot="action" slot-scope="rec" style="display: flex; justify-content: center;">
                                                <a 
                                                :style="rec.status == 'done' || rec.status == 'rejected' ? 'color: green; font-size: 16px;' : rec.status == 'approved' || rec.status == 'pending' ? 'color: red;  font-size: 16px;' : '  font-size: 16px;'" 
                                                @click="
                                                    rec.status == 'approved' || rec.status == 'pending' ? cancel(rec): 
                                                    rec.status == 'cancelled' ? setAppointment(rec) :
                                                    remove(rec)
                                                    "
                                                >
                                                    {{ rec.status == 'cancelled' ? "Submit" : rec.status == 'approved' || rec.status == 'pending' ? 'Cancel' : '' }}
                                                    <a-icon type="check" v-if="rec.status == 'done' || rec.status == 'rejected'" />
                                                </a>
                                            </span>
                                        </a-table>
                                    </a-col>
                                </a-row>
                            </a-card>
                        </a-col> 
                    </a-row>
                </a-col>
                <a-col :span="$breakpoints.sLg ? '24' : '12'">
                    <a-card class="card-height">
                        <a-calendar class="card-height">
                            <template slot="dateCellRender" slot-scope="value">
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
    </div>
</template>

<script>
import moment from 'moment';
import Cookies from 'js-cookie';
export default {
    layout: 'header',
    data(){
        return{
            form: {},
            students: [],
            schedules: [],
            appointments: [],
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
                    title: 'Name',
                    key: 'name',
                    scopedSlots: { customRender: 'name'}
                },
                { 
                    title: 'Date',
                    key: 'date',
                    scopedSlots: { customRender: 'date'}
                },
                { 
                    title: 'Status',
                    key: 'status',
                    scopedSlots: { customRender: 'status'}
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
        await this.getAppointment();
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
                let active = result.filter( e => e.isActive);
                this.schedules = active;
                console.log("result:>>", this.schedules);
            } catch (error) {
                console.log(error);
            }
            
        },
        async getAppointment(){
            let {data} = await this.$axios.get("/schedules");
            let result = data.filter( e => e.studentId == this.students._id)
            let active = result.filter(e => e.isActive)
            this.appointments = active;
            console.log(this.appointments);
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
            if(value){
                let result = this.schedules.filter( e => moment(e.date).format("YYYY-MM-DD") == moment(date).format("YYYY-MM-DD") && !e.studentId)
                console.log("result", result );
                this.filterTeacher = result;
                if(!result.length){
                    this.form.teacherId = '';
                }

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
                            let {data} = await this.$axios.get('/schedules');
                            let date = moment(this.form.date).format('YYYY-MM-DD');
                            let result = data.filter( e => e.studentId == this.form.studentId && e.teacherId == this.form.teacherId && moment(e.date).format("YYYY-MM-DD") == date)
                           console.log("schedules: >>", result);
                           if(result.length){
                            this.$notification.error({
                                message: 'Error',
                                description: 'You already set an appointment here!'
                            })
                           } else {
                                this.$notification.success({
                                    message: 'Success',
                                    description: 'Succcessfully set an appointment!'
                                })
                                console.log("this form", this.form);
                                // let result = await this.$axios.get("/schedules/appointment", this.form);
                                // console.log("appointment: >>", result);
                                console.log("this.form",this.form);
                                let appointment = await this.$axios.post("/schedules/appointment", this.form);
                                console.log("result: >>", appointment);
                                   
                                let form = {name: `${this.students.profile.fname} ${this.students.profile.lname}`, description: `Has booked this date '${this.dateFormatter(this.form.date)}'`, teacherId: this.form.teacherId, status: 'Unviewed'}
                                let notifications = await this.$axios.post("/notifications", form)
                                this.form.teacherId = "";
                                await this.$fetch();
                           }
                           
                            
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
        },
        dateFormatter(date){
            let month = new Date(date).toString().split(' ')[1];
            let splitter = new Date(date).toLocaleDateString().split('/');
            return `${month} ${splitter[1]}, ${splitter[2]}`;
        },
        nameFormater(item){

            return `${item.teacher&&item.teacher.profile&&item.teacher.profile.fname} ${item.teacher&&item.teacher.profile&&item.teacher.profile.lname} `
        },
        async cancel(item){
            try {
                console.log("cancel");
                console.log("cancel: >>", item);
                let {data} = await this.$axios.put(`/schedules/${item._id}`, { status: 'cancelled'})
                let form = {name: `${this.students.profile.fname} ${this.students.profile.lname}`, description: `Has cancel the appointment '${this.dateFormatter(this.form.date)}'`, teacherId: item.teacherId, status: 'Unviewed'}
                let result = await this.$axios.post("/notifications", form)
                await this.$fetch();
            } catch (error) {
                console.log(error);
            }
        },
        async setAppointment(item){
            try {
                let {data} = await this.$axios.put(`/schedules/${item._id}`, { status: 'pending'})
                let form = {name: `${this.students.profile.fname} ${this.students.profile.lname}`, description: `Has booked this date '${this.dateFormatter(this.form.date)}'`, teacherId: item.teacherId, status: 'Unviewed'}
                let result = await this.$axios.post("/notifications", form)
                await this.$fetch();

            } catch (error) {
                console.log(error);
            }
            console.log("submit");
        },
        async remove(item){
            try {
                let {data} = await this.$axios.put(`/schedules/${item._id}`, { isActive: false })
                await this.$fetch();
            } catch (error) {
                console.log(error);
            }
        }

        // openSchedule(value){

        // }
    }
}
</script>

<style>
/* .main-card{
    height: 70vh;
} */
/* .card-height{
    padding: 0;
    height: 450px;
} */
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

</style>