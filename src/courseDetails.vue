<template>
    <div class="courseGroup">
        <div class="col">
            <p class="courseHour"> 
                <img  src="../assets/clock.svg" />
                {{ timeStart }} <br>
                <span class="courseDate"> {{ courseDate }} </span>
            </p>
        </div>
    </div>  
</template>
<script>

let weekdays = ["Dimanche","Lundi","Mardi","Mercredi","Jeudi","Vendredi", "Samedi"]

let months = ["Janvier","Fevrier","Mars","Avril","Mai","Juin","Juillet","Août","Septembre","Octobre","Novembre","Décembre"]

function setTime(date){
    date = new Date(date)
    let minutes = date.getMinutes()
    if(minutes == 0) minutes = "00"
    let startHour = date.getHours()
    let endHour = date.getHours()+ 1
    return (startHour + ":" + minutes + " - " + endHour + ":" + minutes)
}


function setDate(date){
    date = new Date(date)
    let jour = weekdays[Number(date.getDay())]
    let number = date.getDate()
    let month = months[Number(date.getMonth())]
    return (jour + " " + number +" " + month)
}

export default {
    name: "course-details",
    props: {
        info: Date,
    },
    data(){
        return{
            timeStart: setTime(this.info),
            courseDate: setDate(this.info)
        }
    }
}
</script>
<style scoped>
img{
    height:28px;
    width: 28px;
    margin-top: -3px;
}
.courseDate{
    color: #404446;
    margin-left: 35px;
    font-size: 20px;
    font-weight: 500;
    line-height: 15px;
}
.courseHour{
    font-family: 'Inter';
    font-weight: 400;
    font-size: 22px;
    line-height: 22px;
    color: #72777A;
}
.courseGroup{
    color: #CDCFD0;
    font-weight: 400;
}

</style> 
