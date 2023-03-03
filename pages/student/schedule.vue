<template>
    <div >
        <a-card>
            <a-row type="flex" justify="space-between" align="center">
                <a-col>
                    <h1 style="font-size: 24px;">Schedule</h1>
                </a-col>
                <a-col>
                    <a-button type="primary" @click="addSchedule">Add schedule</a-button>
                </a-col>
            </a-row>
            <a-card class="main-card">
                <a-row type="flex" justify="space-between" gutter="30">
                    <a-col span="12">
                        <a-row type="flex" justify="end">
                            <a-col>
                                <a-input placeholder="Search here..." allow-clear @change="filterName"></a-input>
                            </a-col>
                        </a-row>
                        <a-tabs default-active-key="1" type="card" @change="callback">
                          
                            <a-tab-pane key="1" tab="All appointment">
                                <a-card>
                                    <a-table :data-source="schedules" :columns="columns">
                                        <span slot="name" slot-scope="rec">{{ `${rec.student.fname}  ${rec.student.lname}` }}</span>
                                        <span slot="yr&sec" slot-scope="rec">{{ `${rec.student.course} - ${rec.student.year}${rec.student.section.toUpperCase()}`  }} </span>
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
                                Content of Tab Pane 2
                            </a-tab-pane>
                            <a-tab-pane key="3" tab="Rejected">
                                Content of Tab Pane 3
                            </a-tab-pane>
                        </a-tabs>
                       
                        
                    </a-col>
                    <a-col span="12">
                        <a-card class="card-height">
                            <a-calendar @select="onSelect" class="card-height">
                                <template slot="dateCellRender" slot-scope="value">
                                    <div v-for="item in schedules" :key="item._id">
                                        <div v-if="hasOpenSchedule(value, item.date)">
                                            <a-badge status="success"></a-badge>
                                            <a-badge status="warning" v-if="item.studentId"></a-badge>
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
        <schedule-modal :visible="addModal" @close="closeModal"/>
    </div>
</template>

<script>
import moment from 'moment';
export default {
    layout: 'main',
    data(){
        return{
            schedules: [
                {
                    _id: 1,
                    date: '2023-03-03',
                    studentId: 1,
                    student: {
                        _id: 1,
                        fname: 'park',
                        lname: 'mruz',
                        course: 'BSIT',
                        year: 1,
                        section: 'a',
                        description: 'May ipapakiusap'
                    },
                    teacherId: 1,
                    teacher: {
                        _id: 1,
                        fname: 'Richard',
                        lname: 'Gonzales',
                        subject: 'Math'
                    }
                    
                },
                {
                    _id: 2,
                    date: '2023-03-05',
                    studentId: 1,
                    student: {
                        _id: 1,
                        fname: 'park',
                        lname: 'mruz',
                        course: 'BSIT',
                        year: 1,
                        section: 'a',
                        description: 'About sa grade'
                    },
                    teacherId: 1,
                    teacher: {
                        _id: 1,
                        fname: 'Richard',
                        lname: 'Gonzales'
                    }
                    
                }
            ],
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
            addModal: false,
        }
        
    },  
    methods: {
        hasOpenSchedule(value, dataDate){
            let date = moment(value).format('YYYY-MM-DD')

            return dataDate === date
        },
        onSelect(value){
            this.visible = true
        },
        closeModal(){
            this.visible = false
        },
        acceptAppointment(){
            console.log('clicked');
        },
        rejectAppoint(){
            console.log('clicked')
        },
        addSchedule(){
            this.addModal = true
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
    height: 40px !important;
    width: 80px;
}
.ant-tabs-bar{
    margin: 0;
}
</style>