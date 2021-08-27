<template>
<div id="p1">
    <div id="circle"></div> 
    <nav class="navbar navbar-expand-lg">
        <div class="container-fluid"><a class="navbar-brand title1" id="topLogo">GoStudy</a>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav">
                    <li class="nav-item"><input class="login button outline-light" type="button" value="Se connecter" /></li>
                </ul>
            </div>
        </div>
    </nav>
    <div class="row" id="herobanner" style="width: 100%">
        <img id="herobIllu" src='./herobanilu.png' />
        <div id="pave">
            <p class="title1"> Réservez vos cours hebdomadaires de manière simple et efficace. </p>
            <p class="regular-spaced">Cliquez sur le bouton pour réserver vos premières heures </p>
        </div>
        <p style="width:100%;text-align:center">
            <input class="button fill-dark reg-nn-medium" id="herobStart" type="button" v-on:click="scrollTop" value="Réserver" />
        </p>
    </div>
</div>
    
</template>
<script>

let counter = 0
export default {
    data(){
        return{
            //herobanilu: require("./herobanilu.png"),
            //paypalfinal: require("../assets/paypal final.png"),
            //calendar: require("../assets/google-calendar.png"),
            //zoom: require("/assets/Zoom-Logo-650x366.png")
        }
    },
    methods: {
         scrollTop(){
            let y = document.getElementById("p2").offsetTop
            console.log(y)
            window.scrollTo(0,y)
        }
    }
}

</script>

<style>
#circle{
    width: 200%;
    margin-top: 30px;
    height: 100%;
    position: absolute;
    top: -50%;
    left: -100%; 
    margin-left: 50px;
    background-color: #6B4EFF;
    border-radius: 50%;
}
#p1{
    position: absolute;
    width: 100%;
    top: 0;
    bottom: 0;
    height: 100%;
    display: block;
    overflow: hidden;
}
#topLogo{
    margin-left: 0px;
    color: white !important;
}

#pave{
    margin-left: 20px;
    margin-right: 20px;
    margin-top: 60%;
    position: relative;
    z-index: 20;
}
#herobStart{
    height: 65px;
    font-size: 30px;
    width: 300px;
    font: "inter";
    font-weight: 600;
}
#herobStart:active{
    animation: mobile-click-anime 50ms
}
@keyframes mobile-click-anime{
    0%{
        transform: scale(1)
    }
    50%{
        transform: scale(0.98);
    }
    100%{
        transform: scale(1);
    }
}
#herobIllu{
    z-index: 0;
    position: absolute;
    right: -10px;
    max-width: 900px;
    z-index: 10;
    opacity: 1;
}
#technos{
    box-shadow: var(--shadow-large);
    max-width: 630px;
    border-radius: 16px;
    margin-left: 40px;
    max-height: 140px;
    z-index: 50;
    margin-top: 55px;
}
.techno{
    height: 160px;
    margin-top: -10px;
}
.techno.active{
    background-color: var(--purle-primary);
    border-radius: 48px;
    box-shadow: var(--shadow-medium);
}

.technoImg{
    position: relative;
    top: 50%;
    left: 50%;
    margin-top: -5px;
    transform: translateY(-50%) translateX(-50%);
}
.technoExplain{
    box-shadow: var(--shadow-large);
    background-color: var(--purle-primary);
    border-radius: 25px;
    margin-top: 10px;
    height: 80px;
    font: "inter";
    font-weight: 700;
    color: #6B4EFF;
    font-size: 16px;
    text-align: center;
    line-height: 24px;
    vertical-align: middle;
    padding: auto;
    display: none;
}
.technoExp{
    position: relative;
    left: 50%;
    top: 50%;
    transform: translateX(-50%) translateY(-50%);
}
.technoExplain.active{
    display:block !important;
}
#ppal,#zoom{
    width: 190px;
}
#gagenda{
    width: 110px;
}
@media screen and (min-device-width: 1200px) and (max-device-width: 1600px) and (-webkit-min-device-pixel-ratio: 1) {  

    #pave{
        margin-top: 10px;
    }
    #herobIllu{
        max-width: 850px;
    }
    #technos{
        visibility: hidden;
    }
    #herobStart{
        width: 260px;
        height: 70px;
        margin-top: -15px;
    }

}


</style>
