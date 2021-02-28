<template>
    <v-app id="accueil" class="text-center">
        <top-header/>
        <div class="ml-12">
           <div class="flexible">
                <h1 class="ma-4 cut-text">Gifs Groupomania</h1>
            </div>
            <router-view></router-view>
            <v-container class="maclass">
                <v-btn class="ma-3" @click="gifForm">Créer un gif</v-btn>
                <!--Affichage des Gifs-->
                <div class="Gifs" v-for="(gif, index) in allGifs" v-bind:key="index">
                    <div class="flex">
                        <div class="cardTitle">
                            <h2 class="title">{{ gif.title }}</h2>
                            <h2 class="Name">Par : {{ gif.name}}</h2>
                        </div>
                    <!-- insertion image-->
                        <div class="image">
                          <v-img
                          max-width="400"
                          max-height="400"
                          :src= "gif.url"
                          alt="une image gif"></v-img>
                        </div>
                                                <div class="date">
                           Le {{ gif.date }} à {{ gif.time }}
                        </div>
                    </div>
                </div>
            </v-container>
        </div>
    </v-app>
</template>
                
<script>
import TopHeader from "./Accueil/TopHeader";
import axios from "axios";
export default {
    name: "Accueil",
    data(){
        return{
            userId: "",
            allGifs: [],
        }
    },
    methods: {
        gifForm(){
            this.$router.push('/Accueil/Gifs')
        },
    },
    components: {
        "top-header": TopHeader, 
    },
    mounted(){ 
        this.userId = localStorage.userId;
        axios.get("http://localhost:3000/api/gifs/", {headers: {Authorization: 'Bearer ' + localStorage.token}})
            .then(response => {
                let gifs = JSON.parse(response.data);
                this.allGifs = gifs;
                //console.log(gifs) 
            })
            .catch(error => {
            console.log(error); 
            });
    },
}
</script>

<style lang="scss" scoped>
    #accueil{
    background-image: url("../assets/icon.png");
    background-attachment: fixed;
    background-size: 50%;
    background-position-x: 50%;    
    }
    .flexible {
        display: flex;
        justify-content: center;
        padding-right: 3.5em;
    }
    .cut-text{
        font:bold 24px Arial, Monaco, monospace;
        font-style:normal;
        color:#ffb700;
        background:#40444a;
        border:6px inset #cccccc;
        text-shadow:0px -1px 10px #222222;
        box-shadow:1px 1px 9px #000000;
        -moz-box-shadow:1px 1px 9px #000000;
        -webkit-box-shadow:1px 1px 9px #000000;
        border-radius:83px 0px 90px 40px;
        -moz-border-radius:83px 0px 90px 40px;
        -webkit-border-radius:83px 0px 90px 40px;
        padding-left: 3em;
        padding-right: 3em;
    }
    .maclass {
        display: flex;
        justify-content: flex-start;
        flex-direction: column;
        padding-right: 3.5em;
    }
    .ma-3 {
        background-color: black!important;
        color: white;
        :hover {
            color:#ffb700;
        }
    }
    h1{
        color: black;
    }
    .flex {
        border:2px solid grey;
        border-radius: 20px;
        margin: auto;
        margin-top:20px;
        box-shadow: 0px 0px 60px grey;
        background-color: grey;
    }
    .image{
        width:30%;
        margin:auto;
        margin-bottom: 30px;
    }
    .header{
        background-color: rgb(94, 85, 83);
        color: white;
    }
    .button{
        border:2px solid rgb(235, 135, 112);
        padding:3px;
        background-color: #091f43;
        color:white;
        margin-top: 5%;
    }
      @media all and (max-width:500px)
    {
        .maclass {
            margin-top: -5em;
            margin-left: -1.5em;
        }
       .cut-text{
            visibility: hidden;
            margin-bottom: -3em;
       }
        .flex{
            border:1px solid #6e6e6e;
            width: 100%;
        }
        #image{
            width:300px;
            margin-left:0;
        }
        img{
            margin:0;
        }
       
    }  
</style>