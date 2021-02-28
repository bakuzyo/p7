<template>
    <div id="profil" class="d-flex justify-center">
        <top-header/>
        <v-card class="ma-12 myCard" id="card" min-width="300px" flat>
                <div class="flexible">
                    <h1 class="ma-5 cut-text">Mon profil</h1> <!--Affichage des informations de l'utilisateur -->
                </div>
            <v-card-title class="my-3" >
            </v-card-title>
            <v-card-text class="ml-2">
                <p>Prénom : {{ dataGet.firstName }}</p>
                <p>Nom : {{ dataGet.lastName }}</p>
                <p>Email : {{ dataGet.email }}</p>
               
            </v-card-text>
            <v-card-actions class="d-flex justify-space-between">
                <v-btn class="option" @click.stop="dialogUp=true" title="modifier mes informations">Modifier</v-btn>
                <v-btn class="option" @click.stop="dialogDel=true" title="supprimer mon profil">Supprimer</v-btn>
            </v-card-actions>
        </v-card>
        

        <v-dialog persistent v-model="dialogUp" max-width="600px">
            <v-card>
                <v-card-title>Modifier mon profil</v-card-title>
                <v-card-text>
                    <v-form ref="form" v-model="valid">
                        <v-text-field v-model="dataUp.firstName" :rules="nameRules" label="Prénom" prepend-icon="mdi-account-circle" required></v-text-field>
                        <v-text-field v-model="dataUp.lastName" :rules="nameRules" label="Nom" prepend-icon="mdi-account-circle" required></v-text-field>
                        <v-text-field v-model="dataUp.email" :rules="emailRules" label="e-mail" prepend-icon="mdi-at" required></v-text-field>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-btn text @click="dialogUp=false">Annuler</v-btn>
                    <v-btn text :disabled="!valid" @click="updateUser">Valider</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDel" max-width="350px">
            <v-card>
                <v-card-title>
                    Êtes-vous certain. e ?
                </v-card-title>
                <v-card-text>
                    <p>En supprimant votre profil, vous effacerez aussi tous vos posts ainsi que vos commentaires.</p>
                    <p>{{ msg }}</p>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text @click="dialogDel=false">
                        Annuler
                    </v-btn>
                    <v-btn text @click="deleteUser">
                        Supprimer
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </div>
</template>

<script>
import TopHeader from "./TopHeader"
import axios from "axios"
export default {
    name: "Profil",
    data() {
        return{
            dialogDel: false,
            dialogUp: false,
            msg: "",
            dataGet: { 
                firstName: "",
                lastName: "",
                email: "",
            },
            dataUp: {
                firstName: "",
                lastName: "",
                email: "",
               
            },
            dataUpS: "",
            valid: true,
            nameRules: [
                v => !!v || 'Nom requis',
            ],
            emailRules: [
                v => !!v || 'E-mail requis',
                v => /.+@.+\..+/.test(v) || 'E-mail invalide',
            ],
            
        }
    },
    methods: {
        deleteUser() {
            axios.delete("http://localhost:3000/api/auth/", {headers: {Authorization: 'Bearer ' + localStorage.token}})
            .then(response => {
                let rep = JSON.parse(response.data);
                console.log(rep);
                localStorage.clear();
                //localStorage.token = "";
                this.$router.push('/');  
            })
            .catch(error => {
                console.log(error);
                this.msg = error  
            })
        },
        updateUser() {
            this.dataUpS = JSON.stringify(this.dataUp);
            axios.put("http://localhost:3000/api/auth/", this.dataUpS, {headers: {'Content-Type': 'application/json', Authorization: 'Bearer ' + localStorage.token}})
            .then(response => {
                let rep = JSON.parse(response.data);
                console.log(rep);
                this.dialogUp = false;
                window.location.assign('http://localhost:8080/Accueil/Profil');
            })
            .catch(error => {
                console.log(error);
                this.msg = error  
            })
        }
    },
    mounted() { 
        axios.get("http://localhost:3000/api/auth/", {headers: {Authorization: 'Bearer ' + localStorage.token}})
            .then(response => {
                let profil = JSON.parse(response.data);
                this.dataGet.email = profil[0].email;
                this.dataGet.firstName = profil[0].firstName;
                this.dataGet.lastName = profil[0].lastName;
                
            })
            .catch(error => {
                console.log(error);
            });    
    },
    
    components: {
        "top-header": TopHeader,
    }
}
</script>

<style lang="scss" scoped>
#card {
    padding:15px;
    background-color: grey;
    border:2px solid grey;
    border-radius: 20px;
    width: 80%;
    margin: auto;
    margin-top:20px;
    box-shadow: 0px 0px 60px grey;
   
    background-image: url("../../assets/icon.png");
    background-size: 100%;
    background-position-x: 50%;
}
    .myCard {
        margin-right: 5.5em!important;
    }
    .flexible {
        display: flex;
        justify-content: center;
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
        padding-left: 5em;
        padding-right: 5em;
    }
.option {
    background-color: black!important;
    color: white;
    :hover {
        color: #ffb700;
    }
}
p{
    text-transform: uppercase;
    border: 3px solid #40444a;
    border-radius: 15px;
    padding: 2em;
    color: black;
    font-weight: bold;
}
    @media all and (max-width:500px)
    {
        .cut-text {
            flex: none;
            padding-right: 3em;
            padding-left: 3em;
        }
    }
</style>