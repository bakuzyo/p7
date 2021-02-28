<template>
    <v-app id="moderation" class="moderation">
        <top-header/>
        <div class="ml-12">
            <div class="flexible">
                <h1 class="ma-4 cut-text">Modération</h1>
            </div>
            <v-container class="center">
                <v-btn class="ma-3 ma3-3 option" @click="clickPosts">Posts</v-btn>
                <v-btn class="ma-3 ma3-3 option" @click="clickComments">Com</v-btn>
                <v-btn class="ma-3 ma3-3 option" @click="clickGifs">Gifs</v-btn>
            
                <div v-if="showPosts"><!--Affichage des posts suite à action sur le bouton  -->
                    <v-card class="mur__post ma-3 mt-6 flex" color="grey" v-for="(post, index) in allPosts" v-bind:key="index">
                        <v-card-title>
                            <h2 class="mur__post__title">{{ post.title }}</h2>
                        </v-card-title>

                        <v-card-subtitle class=" mur__post__name">
                            Par {{ post.firstName }} {{ post.lastName }}, le {{ post.date }}
                        </v-card-subtitle>

                        <v-card-text class="v-card-text black--text mur__post__content" >
                            {{ post.content }}
                        </v-card-text>

                        <v-card-actions class="mur__post__manage">
                            <v-btn class=" butn mur__post__manage--btn" title="supprimer le post" @click="deletePost(post.id)" icon>
                                <v-icon>mdi-delete-outline</v-icon>
                            </v-btn> 
                        </v-card-actions>
                    </v-card>
                </div>
                <div v-if="showComments"><!--Affichages des commentaires suite à action sur bouton-->
                    <v-card class="mur__comments--ind ma-3 mt-6 flex" color="grey" v-for="(comment, index) in allComments" v-bind:key="index" outlined>
                        <v-card-subtitle class=" pb-0 mur__comments__name">
                            Le {{ comment.date }}, {{ comment.firstName }} {{ comment.lastName }} commente :
                        </v-card-subtitle>
                        
                        <v-card-text class="text--primary mur__comments__content ">
                            {{ comment.comContent }}
                        </v-card-text>

                        <v-card-actions class="mur__comments__manage">
                            <v-btn title="supprimer le commentaire" class="butn mur__comments__manage--btn" @click="deleteComment(comment.id)" icon>
                                <v-icon >mdi-delete-outline</v-icon>
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </div>
                <div v-if="showGifs"> <!--Affichage des gifs suite action sur bouton-->
                    <v-card class="mur__post ma-3 mt-6 flex" color="grey" v-for="(gif, index) in allGifs" v-bind:key="index">
                         <v-card-title>
                            <h2 class="mur__post__title">{{ gif.title }}</h2>
                        </v-card-title>
                        <v-card-subtitle class=" mur__post__name">
                            Le {{ gif.date }} à {{ gif.time }} par :  {{ gif.name }}
                        </v-card-subtitle>
                    <!-- insertion image-->
                        <div class="image">
                          <v-img
                          max-width="400"
                          max-height="400"
                          :src= "gif.url"
                          alt="une image gif"></v-img>
                        </div>
                         <v-card-actions class="mur__post__manage">
                            <v-btn class="mur__post__manage--btn butn" title="supprimer le gif" @click="deleteGif(gif.id)" icon>
                                <v-icon>mdi-delete-outline</v-icon>
                            </v-btn> 
                        </v-card-actions>
                    
                    </v-card>
                </div>
                
            </v-container>
        </div>
    </v-app>
</template>


<script>
import TopHeader from "./TopHeader"
import axios from "axios"
export default {
    name: "Moderation",
    data(){
        return{
            allPosts: [],
            allComments: [],
            allGifs: [],
            showPosts: true,
            showComments: false,
            showGifs: false
        }
    },
    components: {
        "top-header": TopHeader,
    },
    methods: {//Switch affichage des pages suite à action sur bouton
        clickPosts(){
            this.showPosts = true;
            this.showComments = false;
            this.showGifs = false;
            this.getAllPosts();
        },
        clickComments(){
            this.showPosts = false;
            this.showComments = true;
            this.showGifs = false;
            this.getAllComments();
        },
        clickGifs(){
            this.showPosts = false;
            this.showComments = false;
            this.showGifs = true;
            this.getAllGifs();
        },
                getAllGifs(){
            axios.get("http://localhost:3000/api/moderation/gifs", {headers: {'Content-Type': 'application/json',Authorization: 'Bearer ' + localStorage.token}})
            .then(response => {
                let gifs = JSON.parse(response.data);
                this.allGifs = gifs;
                this.gifId = this.allGifs.id;
            })
            .catch(error => {
            console.log(error); 
            });
        },
         getAllPosts(){
            axios.get("http://localhost:3000/api/moderation/posts", {headers: {'Content-Type': 'application/json',Authorization: 'Bearer ' + localStorage.token}})
            .then(response => {
                let posts = JSON.parse(response.data);
                this.allPosts = posts;
            })
            .catch(error => {
            console.log(error); 
            });
        },
        getAllComments(){
            axios.get("http://localhost:3000/api/moderation/comments", {headers: {'Content-Type': 'application/json',Authorization: 'Bearer ' + localStorage.token}})
                .then(response => {
                    let com = JSON.parse(response.data);
                    this.allComments = com;
                })
                .catch(error => {
                console.log(error);
                });
        },
        deletePost(pId){
            let self = this;
            axios.delete("http://localhost:3000/api/moderation/post/" + pId, {headers: {'Content-Type': 'application/json',Authorization: 'Bearer ' + localStorage.token}})
                .then(response => {
                    let rep = JSON.parse(response.data);
                    console.log(rep.message);
                    self.getAllPosts();//Envoie la page qui affiche les posts après suppression
                })
                .catch(error => {
                    console.log(error);    
                })
        },
              deleteComment(cId){
            let self = this;
            axios.delete("http://localhost:3000/api/moderation/comment/" + cId, {headers: {'Content-Type': 'application/json',Authorization: 'Bearer ' + localStorage.token}})
                .then(response => {
                    let rep = JSON.parse(response.data);
                    console.log(rep.message);
                    self.getAllComments();//Affiche la page des commentaires après suppression
                })
                .catch(error => {
                    console.log(error);
                })
        },
        deleteGif(gId){
            let self = this;
            this.gifId = gId
            axios.delete("http://localhost:3000/api/moderation/gif/" + gId,  {headers: {'Content-Type': 'application/json',Authorization: 'Bearer ' + localStorage.token}})
                .then(response => {
                    let rep = JSON.parse(response.data);
                    console.log(rep.message);
                    self.getAllGifs();
                })
                .catch(error => {
                    console.log(error);    
                })
        },
    },
    mounted(){
      //Quand la page est montée, je veux que par défault il affiche d'abord les POSTS (le premier onglet)
        // Je simule donc un appuie sur l'onglet posts
        this.clickPosts();  
    }
}
</script>

<style lang="scss" scoped>
#moderation{
    background-image: url("../../assets/icon.png");
    background-size: 50%;
    background-position-x: 50%;
    background-attachment: fixed;
}
.butn {
    :hover {
        color: #ffb700;
    }
}
.center {
    text-align: center;
    text-align: -webkit-center;
}

.option {
    background-color: black!important;
    color: white;
    :hover{
        color: #ffb700;
    }
}

.flexible {
        display: flex;
        justify-content: center;
}
.cut-text {
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
        padding-left: 5em;
        padding-right: 5em;
    }
@media all and (max-width:600px)
{
    h2 {
        font-size: 1rem;
    }
    .flexible {
        display: none;
    }
    .center {
        margin-left: -1.5em;
    }
    .flex {
        margin-right: 2em!important;
    }
    .v-card{
           &__title{
          font-size:0.8rem; 
          }
       }
       .cut-text {
        visibility: hidden;
    }   
}
</style>