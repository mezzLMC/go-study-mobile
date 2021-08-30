<template>
<div id="p2">
    <div id="reservationBody">
        <p id="reservationLabel">
            Réservation
        </p>
        <p id="reservationSub">
                Cliquez sur une heure pour réserver
        </p>
        <div id="weekHelper" class="row">
                <button class="col weekControlButton" id="backToWeek" v-bind:disabled="weekCounter == 0" v-on:click="selectedDate= new Date()">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-rotate-cw"><polyline points="23 4 23 10 17 10"></polyline><path d="M20.49 15a9 9 0 1 1-2.12-9.36L23 10"></path></svg>
                </button>
                <button class="col weekControlButton" v-bind:disabled="weekCounter == 0" v-on:click="previousWeek">
                    <svg class="weekControl" xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 0 24 24" width="24px"><rect fill="none" height="24" width="24"/><path d="M9,19l1.41-1.41L5.83,13H22V11H5.83l4.59-4.59L9,5l-7,7L9,19z"/></svg>
                </button>
                <button class="col weekControlButton" v-on:click="nextWeek" v-bind:disabled="!(weekCounter < 4)">
                    <svg class="weekControl" xmlns="http://www.w3.org/2000/svg" enable-background="new 0 0 24 24" height="24px" viewBox="0 0 24 24" width="24px"><rect fill="none" height="24" width="24"/><path d="M15,5l-1.41,1.41L18.17,11H2V13h16.17l-4.59,4.59L15,19l7-7L15,5z"/></svg>
                </button>
        </div>    
            <div id="calendar">
                <vue-cal ref="vuecal" style="height: 250px" id="vucal" v-bind:selected-date="selectedDate" v-bind:on-event-click="deleteHour" class="vuecal--full-height-delete" v-bind:editable-events="{ title: false, drag: false, resize: true, delete: true, create: false }" v-bind:hide-weekdays="[7]"  v-bind:time-step="30" v-bind:time-from="22 * 30" v-bind:time-to="31 * 30" v-bind:disable-views="['day']" :cell-click-hold="false" :drag-to-create-event="false" v-bind:events="events" @cell-click="selectHalf($event)" locale="fr"></vue-cal>
            </div>
        <div class="col-sm" id="recap">
            <div id="before">

            </div>
            <div id="after"></div>
        </div>
    </div>
</div>
</template>
<script>
import VueCal from "vue-cal"
//import CourseDetails from "./courseDetails.vue"
import 'vue-cal/dist/drag-and-drop.js'
import './css/vuecal.css'
import './css/payement.css'
import "vue-cal/dist/i18n/fr"




export default {
    data(){
        return {
            selectedDate: new Date().getDay() == 0 ? new Date(new Date().setDate(new Date().getDay()+1)) : new Date(),
            weekCounter: 0,
            selectedHours: [],
            hoursCounter: 0,
            events: [],
            paypalLoaded: false,
            paypalApproved: false,
        }
    },
    methods: {
        formatDate(){
            let months={Janvier:"01",Fevrier:"02",Mars:"03",Avril:"04",Mai:"05",Juin:"06",Juillet:"07","Août":"08",Septembre:"09",Octobre:"10",Novembre:"11","Décembre":"12"};
            let m = ""
            let month = document.getElementsByClassName("vuecal__title")[0].children[0].innerHTML.split("(")[1].split(" 2")[0].split(" - ")
            let weekDays = Array.from(document.getElementsByClassName("weekday-label"))
            weekDays.forEach((e,i)=>{
                let target = e.children[3]
                let previous = weekDays[i-1]!=undefined ? weekDays[i-1].children[3] : {innerHTML: "0"}
                if(!target.innerHTML.includes(".")){
                    if(month[1]) Number(target.innerHTML.split(".")[0]) < previous.innerHTML.split(".")[0] ? m=month[1] : m=month[0]
                    else m = month[0]
                    target.innerHTML = "<br/>" + target.innerHTML + "." + months[m]
                    target.classList.add("date")
                }
            })
        },
        watchSunday(){
            if(this.selectedDate.getDay() == 0){
                this.selectedDate = new Date(this.selectedDate.setDate(this.selectedDate.getDate()+1))
            } 
        },
        nextWeek(){
            this.weekCounter++ 
            this.$refs.vuecal.next()
        },
        previousWeek(){
            this.weekCounter-- 
            this.$refs.vuecal.previous()
        },
        getWeekNumber: (date) => Math.floor((date - new Date(new Date().getFullYear(),0,1)) / (24 * 60 * 60 * 1000)),
        dateIsPaste: (firstDate, secondDate) => firstDate <= secondDate,
        focusDetails(){
            document.getElementById("details").style.border="2px solid #CDCFD0"
        },
        blurDetails(){
            document.getElementById("details").style.border="none"
        },
        selectHalf(eventTime,target){
            let finalTime = new Date(eventTime)
            if(!this.dateIsPaste(finalTime,new Date())){
            let finalTime = new Date(eventTime)
            finalTime.getMinutes()>=30 ? finalTime.setMinutes(30) : finalTime.setMinutes(0)
            let endTime = new Date(finalTime)
            console.log("pushed 2")
            endTime.setHours(endTime.getHours()+1)
            console.log(finalTime +" | "+ endTime)
            this.events.push({
                start: finalTime,
                end: endTime,
                deletable: true,
                resizable: false,
                draggable: false,
                content: "<div class='selectedHour'> <div class='top-bar'> </div> <div class='bottom-bar'> </div>  </div>",
                class:"selHour"
            })
            this.hoursCounter++;
            this.selectedHours.push(finalTime)
            }
        },
        addBusyTime(startTime,endTime){
            let startDate = new Date(startTime)
            let endDate = new Date(endTime)
            console.log("startDate before: " + startDate)
            if(startDate.getMinutes() != 0) startDate.getMinutes()>30 ? startDate.setMinutes(30) : startDate.setMinutes(0)
            console.log("startDate after: " + startDate)
            console.log("endDate before: " + endDate)
            if(endDate.getMinutes() != 0){
                endDate.setMinutes(0)
                endDate.setHours(endDate.getHours()+1)
            }
            console.log("endDate after: " + endDate)
            this.events.push({
                start: startDate,
                end: endDate,
                deletable: true,
                resizable: false,
                draggable: false,
                content: '<div class="busyHour"> <svg x="0" y="0" width="204" height="204"><defs><pattern id="diagonalHatch" patternUnits="userSpaceOnUse" width="4" height="8" patternTransform="rotate(-45 2 2)"><path d="M -1,2 l 6,0" stroke="#000000" stroke-width=".5"/></pattern></defs><rect x="1" y="1" width="200" height="200" stroke="#000000" stroke-width="0" fill="url(#diagonalHatch)" /></svg> </div>',
                class:"busy"
            })
        },
        deleteHour(event,element){
            let index = this.events.findIndex(element => {return (element.start == event.start)})
            if(this.events[index].class == "selHour"){
                this.events.splice(index,1)
                this.hoursCounter--;
            }
        },
        getCalendarBusy(){
            axios.get('http://localhost:8000/course',{},{
                headers: {"Access-Control-Allow-Origin": "*"},  
            })
            .then(response=>{
                response.data.forEach(element=>{
                    this.addBusyTime(element.start,element.end)
                })
            })
        }
    },
    components: {
        VueCal
    },
    updated(){
        //setTimeout(()=>this.formatDate(),400)
        //console.log(this.events)
    },
    mounted(){
        //this.getCalendarBusy()
        //this.watchSunday()
        //setTimeout(()=>this.formatDate(),400)
        let cellArray = Array.from(document.getElementsByClassName("vuecal__time-cell"))
        let labelArr = Array.from(document.getElementsByClassName("vuecal__time-cell-label"))
        let lineArr = Array.from(document.getElementsByClassName("vuecal__time-cell-line"))
        lineArr.forEach(e=>{
        let n = lineArr.indexOf(e) 
        if(((n + 1) % 2 == 0)) labelArr[n].innerHTML=""
        })
        let hourArray = Array(document.getElementsByClassName("vuecal__time-cell-label"))
        //let x = document.evaluate('//*[@id="vucal"]/div[2]/div/div/div/div[1]/div[11]/span[2]',document, null, XPathResult.ANY_TYPE, null).iterateNext()
        //x.style.top="-20px"
        let first = document.evaluate('//*[@id="vucal"]/div[2]/div/div/div/div[1]/div[1]/span[2]',document, null, XPathResult.ANY_TYPE, null).iterateNext()
        first.style.top = "0px"
    }

}

</script>

<style>

.last{
    top: -20px !important
}

.vuecal__weekdays-headings{
     border-bottom: 1px solid #E3E5E5;
}

.vuecal__menu, .vuecal__title-bar{
    display: none !important;
}
#p2navbar{
    display: none;
}

*{
    --purple-dark: #6B4EFF;
    --purle-primary: #C6C4FF;
    --purple-light: #9990FF;
    --shadow-small: 0px 0px 1px rgba(20, 20, 20, 0.04), 0px 0px 8px rgba(20, 20, 20, 0.08);
    --shadow-medium: 0px 0px 1px rgba(20, 20, 20, 0.08), 0px 1px 8px 2px rgba(20, 20, 20, 0.08);
    --shadow-large: 0px 1px 24px 8px rgba(20, 20, 20, 0.08);
}
#p2{
    position: absolute;
    width: 100%;
    height: 100%;
    top: 100%;
    z-index: 30;
    background-color: white;
}
#reservationLabel{
    font: "Inter";
    font-weight: 700;
    font-size: 40px;
    margin-bottom: 0;
}
#reservationSub{
    font-size: 18px;
    font: "Inter";
    font-weight: 700;
    margin-top: 0;
    margin-bottom: 0;
    color: #979C9E;
    line-height: 18px;
}
#scrollTop{
    width: 60px;
    position: absolute;
    background-color: var(--purple-dark);
    border-radius: 50%;
    left: 50%;
    transform: translateX(-50%) ;
    z-index: 10;
}
#reservationBody{
    width: 100%;
}
#vucal{
    border-top: 1px solid #E3E5E5;
    width: 100%;
    position: absolute;
}

#recap{
    position: absolute;
    box-shadow: 0px 0px 80px rgba(0, 0, 0, 0.07), 0px 0px 46.8519px rgba(0, 0, 0, 0.0531481), 0px 0px 25.4815px rgba(0, 0, 0, 0.0425185), 0px 0px 13px rgba(0, 0, 0, 0.035), 0px 0px 6.51852px rgba(0, 0, 0, 0.0274815), 0px 0px 3.14815px rgba(0, 0, 0, 0.0168519);
    height: 75px;
    padding-right: 10px;
    left: 2px; right: 5px;
    bottom: 0;
    border-top-left-radius: 12px; border-top-right-radius: 12px;
    background-color: #F7F9FA;
}
#courseType{
    margin-top: 20px;
}
#calendar{
    position: relative;
    top: 50px;
    box-shadow: 0 14px 28px rgba(0,0,0,0.25), 0 10px 10px rgba(0,0,0,0.22);
    border-radius: 10px;
    background-color: white;
    width: 100%
}
#recapitulatif{
    color: #453369;
    font: 'IBM Plex Sans';
    font-weight: 600;
    font-size: 30px;
    margin-top: 5px;
    margin-left: 5px;
}
.attribut{
    font: 'IBM Plex Sans';
    font-size: 21px;
    font-weight: bold;
    line-height: 16px;
    color: #3A147B;
}
#details{
    border: 2px solid #CDCFD0;
    font-weight: 500;
    border-radius: 4px;
    margin-top: 5px;
    width: 90%;
    margin-left: 15px;
    box-shadow: var(--shadow-large) ;
}
#detailsArea{
    border: none;
    width: auto;
    font-size: 17px;
    margin-left: 5px;
    resize: none;
}
#alignLeft{
    position: relative;
    top: -35px;
    left: 5px;
    margin: 0;
}
#detailsArea::placeholder{
    font: 'Inter';
    font-weight: 500;
    color: #404446;
}
#validbut{
    width: 90%;
    height: 7%;
    position: relative;
    left: 50%;
    transform: translateX(-50%);
    bottom: 0;
}
#scheduleList{
    height: 140px;
}
.vuecal__event-content{
    height: 100%;
    border: 1px solid black,
}
.vuecal__no-event{
    display: none !important;
}
.selectedHour{
    background-color: var(--purple-light);
    position: relative;
    margin-top: 1px;
    left: 50%;
    transform: translateX(-50%);
    height: 100%;
    width: 80%;
    border-radius: 4px;
}
.busy{
    background-color: white ;
    position: relative;
    top: 50%;
    margin-top: 1px;
    margin-left: 1px;
    margin-right: 1px;
    transform: translateY(-50%) ;
    height: 100%-30px;
    width: 100%;
}
.top-bar, .bottom-bar{
    height: 3px; 
    width: 40px;
    position: relative;
    left: 50%;
    transform: translateX(-50%);
    border-radius: 50px;
    background-color: var(--purple-dark);
}
.top-bar{
    top: 3px
}
.bottom-bar{
    top: 67px
}
p{
    margin-left: 15px;
}
.value{
    font: 'Inter';
    font-weight: bold;
    font-size: 25px;
    line-height: 32px;
    color: #404446;
}

#weekHelper{
    margin-right: 0;
    margin-top: -5px;
    float: right;
    width: auto
}
#backToWeek{
    height: 55px;
    border-radius: 24px;    
    background-color: white;
    border: none;
}
#backToWeek:not(:disabled) svg{
    stroke: #6B4EFF;
}
#backToWeek:disabled svg{
    stroke: #C6C4FF;
}
.weekControl{
    height: 52.5px;
    width: auto;
    margin-top: 2px;
    fill: inherit
}

button:active:not(:disabled) {
    transform: scale(0.96)
}
.weekControlButton:disabled{
    fill: #C6C4FF;
}
.weekControlButton {
    fill: #6B4EFF;
    border: none;
    background: none;
}
#total{
    position: absolute;
    bottom: 80px;
    margin-left: 5px;

}
#total1{
    font: 'Inter';
    font-weight: 700;
    color: #3C3844;
    font-size: 38px;
    line-height: 38px;
    margin: none;
}
#total2{
    line-height: 24px;
    font-weight: 600;
    color: #787885;
    font-size: 25px;
}
@keyframes selectHour{
    from{
        transform: scale(0.95)
    }
    to{
        transform: scale(1);
    }
}

.vuecal__event-time{
    display: none !important;
}

#checkout{
    position: absolute;
    margin: auto;
    bottom: 20px;
    margin-left: -10px;
    width: 100%;
    text-align: center;
}
#checkout button{
    width: 80%;
    font-size: 25px;
    font-weight: 500;
    padding-bottom: 5px;
    font: "Inter";
    height: 50px;
}
#checkout button:hover{
    transform: scale(1.03);
}

</style>
