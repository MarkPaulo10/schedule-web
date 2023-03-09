<template>
    <div >
        <a-card :style=" $breakpoints.xs ? 'margin-bottom: 30px' : 'margin-bottom: 30px; padding: 20px'">
            <span :style=" $breakpoints.md ? 'font-size: 25px;' : $breakpoints.xs ? 'font-size: 20px' : 'font-size: 36px'">Schedule</span>
        </a-card>
        <a-card>
            <a-row type="flex" justify="end" align="center" style="margin-bottom:10px">
                <a-col>
                    <a-row type="flex" align="center" :gutter="[50]">
                        <a-col span="12">
                            <a-date-picker v-model="date"></a-date-picker>
                        </a-col>
                        <a-col span="12">
                            <a-button type="primary" @click="addSchedule">Add schedule</a-button>
                        </a-col>
                    </a-row>

                </a-col>
            </a-row>
            <a-card class="">
                <a-row type="flex" justify="space-between" :gutter="[30, 20]">
                    <a-col :span="$breakpoints.sLg ? 24 : 12">
                       
                        <a-tabs default-active-key="1" type="card" @change="callback">
                          
                            <a-tab-pane key="1" tab="All appointment">
                                <a-card>
                                    <a-table :data-source="filterPending" :columns="columns" :scroll="{ x: 500, y: 300 }">
                                        <span slot="name" slot-scope="rec">{{ `${rec.student&&rec.student.profile&&rec.student.profile.fname} ${rec.student&&rec.student.profile&&rec.student.profile.lname}` }}</span>
                                        <span slot="yr&sec" slot-scope="rec">{{ `${rec.student&&rec.student.course} - ${rec.student&&rec.student.year}${rec.student&&rec.student.section}`  }} </span>
                                        <span slot="date" slot-scope="rec">{{ rec.date }}</span>
                                        <span slot="status" slot-scope="rec">
                                            <a-tag
                                                :color="rec.status == 'pending' ? 'orange' : rec.status == 'approved' ? '#52c41a' : 'volcano'"
                                            >
                                            {{ rec.status }}
                                            </a-tag>
                                        </span>
                                        <span slot="action" slot-scope="rec">
                                            <a-tooltip title="Accept">
                                                <a-button type="link" icon="check" style="font-size: 24px;" @click="acceptAppointment(rec)"></a-button>
                                            </a-tooltip>
                                            <a-tooltip title="Reject">
                                                <a-button type="link" icon="close" style="font-size: 24px; color: red;" @click="rejectAppoint(rec)"></a-button>                                    
                                            </a-tooltip>
                                        </span>
                                    </a-table>
                                </a-card>
                            </a-tab-pane>
                            <a-tab-pane key="2" tab="Accepted" force-render>
                                <a-table :data-source="filterApproved" :columns="columns" :scroll="{ x: 500, y: 300 }">
                                    <span slot="name" slot-scope="rec">{{ `${rec.student&&rec.student.profile&&rec.student.profile.fname} ${rec.student&&rec.student.profile&&rec.student.profile.lname}` }}</span>
                                    <span slot="yr&sec" slot-scope="rec">{{ `${rec.student&&rec.student.course} - ${rec.student&&rec.student.year}${rec.student&&rec.student.section}`  }} </span>
                                    <span slot="date" slot-scope="rec">{{ rec.date }}</span>
                                    <span slot="status" slot-scope="rec">
                                        <a-tag
                                            :color="rec.status == 'pending' ? 'warning' : rec.status == 'approved' ? 'green' : 'volcano'"
                                        >
                                        {{ rec.status }}
                                        </a-tag>
                                    </span>
                                    <span slot="action" slot-scope="rec">
                                        <a-tooltip title="Mark as Done">
                                            <a-button type="link" icon="check-circle" style="font-size: 24px;" @click="remove(rec)"></a-button>                                    
                                        </a-tooltip>
                                    </span>
                                </a-table>
                            </a-tab-pane>
                            <a-tab-pane key="3" tab="Rejected">
                                <a-table :data-source="filterRejected" :columns="columns" :scroll="{ x: 500, y: 300 }">
                                    <span slot="name" slot-scope="rec">{{ `${rec.student&&rec.student.profile&&rec.student.profile.fname} ${rec.student&&rec.student.profile&&rec.student.profile.lname}` }}</span>
                                    <span slot="yr&sec" slot-scope="rec">{{ `${rec.student&&rec.student.course} - ${rec.student&&rec.student.year}${rec.student&&rec.student.section}`  }} </span>
                                    <span slot="date" slot-scope="rec">{{ rec.date }}</span>
                                    <span slot="status" slot-scope="rec">
                                        <a-tag
                                            :color="rec.status == 'pending' ? '#fccc6c' : rec.status == 'approved' ? '#52c41a' : 'red'"
                                        >
                                        {{ rec.status }}
                                        </a-tag>
                                    </span>
                                    
                                    <span slot="action" slot-scope="rec">
                                        <a-tooltip title="Reject">
                                            <a-button type="link" style="color: red;" @click="remove(rec)">remove</a-button>                                    
                                        </a-tooltip>
                                    </span>
                                </a-table>
                            </a-tab-pane>
                        </a-tabs>          
                    </a-col>
                    <a-col :span="$breakpoints.sLg ? 24 : 12">
                        <a-card class="card-height">
                            <a-calendar @select="onSelect" class="card-height">
                                <template slot="dateCellRender" slot-scope="value">
                                    <div class="status">
                                        <div  v-for="item in schedules" :key="item._id">
                                            <div v-if="hasOpenSchedule(value, item)">
                                                <a-badge status="success" />
                                            </div>
                                            
                                        </div>
                                        <div  v-for="item in filterSchedules" :key="item._id">
                                            <div v-if="hasAppointment(value, item)">
                                                <a-badge status="warning" />
                                            </div>
                                        </div>
                                    </div>
                                   
                                </template>
                                <!-- <ul slot="dateCellRender">
                                    <li v-for="item in schedules" :key="item._id">
                                        <a-badge :status="!item.date ? '' : 'success'">{{ item.student.fname + ' ' + item.student.lname + ' booked this day' }}</a-badge>
                                    </li>
                                </ul> -->
                            </a-calendar>
                        </a-card>
                    </a-col>
                </a-row>
                
            </a-card>
        </a-card>
        <teacher-schedule-modal :visible="addModal" @close="closeModal"/>
    </div>
</template>

<script>
import Cookies from 'js-cookie';
import moment from 'moment';
export default {
    layout: 'header',
    data(){
        return{
            profInfo: [],
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
            addModal: false,
            appointmentDisplayed: false,
            date: '',
            time: '',
            searchData: '',
        }
        
    },  
    computed:{
        filterPending(value){
            return this.schedules.filter( e=> e.status == "pending")
        },
        filterApproved(){
            return this.schedules.filter( e => e.status == "approved")
        },
        filterRejected(){
            return this.schedules.filter( e => e.status == "rejected")
        },
        filterSchedules(){
            return this.schedules.filter( e => e.status == "pending" || e.status == "approved")
        },
    },
    async fetch(){
        await this.getUser();
        await this.getSchedules();
    },
    methods: {
        async getUser(){
            try {
                let token = Cookies.get('token')
                let {data} = await this.$axios.get("/users/profile", { headers: { "token": token}})
                let profData = await this.$axios.get(`/teachers/${data._id}`);
                this.profInfo = profData.data
                
            } catch (error) {
                console.log(error);
            }
        },
        async getSchedules(){
            try {
                let {data} = await this.$axios.get(`/schedules/${this.profInfo._id}`)
                this.schedules = data
                console.log('this.schedules :>> ', this.schedules);
                let holder = this.schedules.filter( e => e.status == "pending")
                // let dataDate = moment(this.schedules.date).format('YYYY-MM-DD');
                console.log("dateResult", holder);
                // let date = moment(this.schedules.date).format("YYYY-MM-DD");
            } catch (error) {
                console.log(error);
            }
        },
        hasOpenSchedule(value, item){
     
            // let scheduleDate = []
            // this.schedules.forEach( e => scheduleDate.push(moment(e.date).format('YYYY-MM-DD')))
            if(!item.studentId){
                let scheduleDate = moment(item.date).format("YYYY-MM-DD")
                let date = moment(value).format('YYYY-MM-DD')
                return scheduleDate === date
            }
            
        },
        hasAppointment(value, item){ 
            if(item.studentId){
                let scheduleDate = moment(item.date).format("YYYY-MM-DD")
                let date = moment(value).format('YYYY-MM-DD')
                this.appointmentDisplayed == true;
                return scheduleDate === date
            }
            
        },
        onSelect(value){
            this.visible = true

        },
        closeModal(){
            this.addModal = false;
        },
        async acceptAppointment(item){
            try {
                let {data} = await this.$axios.put(`/schedules/${item._id}`, {status: "approved"})
                console.log("acceptResult:>>", data);
                await this.$fetch();
            } catch (error) {
                console.log(error);
            }
        },
        async rejectAppoint(item){
            try {
                let {data} = await this.$axios.put(`/schedules/${item._id}`, { status: "rejected"})
                console.log("rejectResult: >>", data)
                await this.$fetch();
            } catch (error) {
                console.log(error);
            }
        },
        async remove (item){
            try {
                let {data}  = await this.$axios.put(`/schedules/${item._id}`, { status: "done"})
                console.log("result: >>", data);
                await this.$fetch();
            } catch (error) {
                
            }
        },
        async addSchedule(){
            try {
                if(!this.date){
                    this.$notification.error({
                        message: 'Error',
                        description: 'Please date and time fields!'
                    })
                }else {
                    let alreadyOpen = this.schedules.filter( e => moment(e.date).format("YYYY-MM-DD") == moment(this.date).format("YYYY-MM-DD"))
                    if(!alreadyOpen.length){
                        let result = await this.$axios.post('/schedules', {
                            date: this.date,
                            teacherId: this.profInfo._id
                        })
                        console.log(result);
                        await this.$fetch();
                        
                    } else {
                        this.$notification.warning({
                            message: "warning",
                            description: 'You already open this date'
                        })
                    }
                    // let result = await this.$axios.post('/schedules', {
                    //     date: this.date,
                    //     teacherId: this.profInfo._id
                    // })
                    // console.log("result: >>", result);
                }
                // console.log(this.profInfo);
               
            } catch (error) {
                
            }
        },
        
        
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
</style>