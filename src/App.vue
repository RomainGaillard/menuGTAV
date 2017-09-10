<template>
  <div id="app">
    <div v-if="debug" v-show="showMenu" class="col-xs-4 bg-dark" style="background-image:url('./static/assets/22.jpg')!important;background-size:auto 90% !important">
      <my-menu :data="menu_json" style="width:365px;"></my-menu>
    </div>
    <my-menu v-else v-show="showMenu" :data="menu_json" style="width:365px;"></my-menu>
    <div class="col-xs-7 col-lg-6 col-xs-offset-1" style="" v-if="debug">
      <h3>Configuration du menu</h3> 
      <textarea style="width:100%" rows="20" v-model="menu"></textarea>
      <br>
      <a @click="generate()" class="btn btn-success">Générer le menu</a>
    </div>
  </div>
</template>

<script>
import MyMenu from './components/myMenu'
import toastr from 'toastr'

var config = require("./config/settings.json");

export default {
  name: 'app',
  components: {
    MyMenu
  },
  data(){
    return{
      menu:"",
      menu_json:{},
      showMenu:false,
      debug:config.debug
    }
  },
  created:function(){
    this.$on("generateMenu",function(data){
      console.info('GenerateMenu',data);
      this.menu_json = data; 
    })

    this.$on("showMenu",function(state){
      console.info("ShowMenu",state);
      this.showMenu = state; 
    })

  },
  beforeDestroy:function(){
    this.$off("generateMenu"); 
    this.$off("showMenu"); 
  },
  mounted:function(){
    this.menu = `
    {
      "style":{
        "header":{
          "div":"background-color:#4987a5!important;",
          "title":"font-size:18px",
          "right_box":"background-color:#7db9d6!important"
        }
      },
      "header":{
        "title":"Rom Burdi",
        "right_box":"<i class='fa fa-user'></i>"
      },
      "parts":[
        {
          "title":"Carte d'indentité",
          "color":"#38a0af",
          "items":[
            {
              "title":"Nom",
              "right_box":"Rom Burdi"
            },
            {
              "title":"Métier",
              "right_box":"Chômeur"
            },
            {
              "title":"Sexe",
              "right_box":"<span class='label label-sm label-success'>Homme</span>"
            },
            {
              "title":"Permis voiture",
              "left_box":"<i class='fa fa-automobile'></i>",
              "description":"Actions possibles",
              "right_box":"<span class='label label-sm label-success'>7 points</span>",
              "menu_action":{
                "keep_header":true,
                "parts":[
                  {
                    "title":"Carte d'indentité",
                    "color":"#353535",
                    "items":[
                      {
                        "title":"Retirer le permis",
                        "description":"Indisponible"
                      },
                      {
                        "title":"Retirer des points",
                        "description":"Indisponible"
                      }
                    ]
                  }
                ]
              }
            }
          ]
        },
        {
          "title":"Casier judiciaire",
          "color":"#d0a635",
          "rigth_box":"<span class='label label-sm label-danger'>Recherché</span>",
          "items":[
            {
              "title":"Amendes",
              "left_box":"<i class='fa fa-euro'></i>",
              "description":"Voir en détail",
              "right_box":"<span class='badge badge-default pull-left font-dark bold'>3</span>",
              "menu_action":{
                "keep_header":true,
                "parts":[
                  {
                    "title":"Casier judiciaire",
                    "color":"#d0a635",
                    "items":[
                      {
                        "title":"Excès de vitesse",
                        "description":"Agent Rachid Bouzelouf",
                        "left_box":"<span class='label label-sm label-warning'>24/09</span>",
                        "right_box":"<strong>500</strong><i class='fa fa-euro'></i>"
                      },
                      {
                        "title":"Amende personnalisée",
                        "description":"Capitaine Rom Burdi | A bord d'une ferrari volé au niveau de l'autoroute A1.",
                        "left_box":"<span class='label label-sm label-warning'>18/09</span>",
                        "right_box":"<strong>1500</strong><i class='fa fa-euro'></i>"
                      }
                    ]
                  }
                ]
              }
            },
            {
              "title":"Peine de prison",
              "left_box":"<i class='fa fa-legal'></i>",
              "right_box":"<span class='badge badge-default pull-left font-dark bold'>1</span>"
            }
          ]
        },
        {
          "title":"Actions",
          "color":"#d84242",
          "rigth_box":"<span class='badge badge-default pull-left font-dark bold'>3</span>",
          "resource":"actions",
          "items":[
            {
              "title":"Mettre une amende",
              "left_box":"<i class='fa fa-euro'></i>",
              "menu_action":{
                "keep_header":true,
                "parts":[
                  {
                    "title":"Mettre une amende",
                    "color":"#d84242",
                    "resource":"fine",
                    "items":[
                      {
                        "title":"Personnalisée",
                        "menu_action":{
                          "keep_header":true,
                          "parts":[
                            {
                              "title":"Amende personnalisée",
                              "color":"#d84242",
                              "action":"sendFine",
                              "name":"type",
                              "value":"custom",
                              "action_title":"VALIDER",
                              "resource":"fine",
                              "items":[
                                {
                                  "title":"Montant",
                                  "type":"number",
                                  "name":"account",
                                  "step":100,
                                  "value":"100"
                                },
                                {
                                  "title":"Raison",
                                  "name":"reason",
                                  "type":"textarea",
                                  "value":"Je test les amendes perso !"
                                }
                              ]
                            }
                          ]
                        }
                      },
                      {
                        "title":"Insulte à agent",
                        "right_box":"<strong>500</strong><i class='fa fa-euro'></i>",
                        "action":"sendFine",
                        "name":"type",
                        "value":"agent"
                      },
                      {
                        "title":"Excès de vitesse",
                        "right_box":"<strong>1 500</strong><i class='fa fa-euro'></i>",
                        "action":"sendFine",
                        "name":"type",
                        "value":"speeding"
                      }
                    ]
                  }
                ]
              }
            },
            {
              "title":"Ajouter au contact",
              "left_box":"<i class='fa fa-legal'></i>",
              "action":"addContact"
            },
            {
              "title":"Bannir",
              "left_box":"<i class='fa fa-legal'></i>",
              "action":"ban"
            }
          ]
        }
      ]
    }`

    if(this.debug){
      this.generate(); 
    }


    var parent = this; 
    $(document).on("generateMenu", function(menu) {
      console.log("generateMENU 2",menu); 
      parent.menu_json = menu; 
    });

    $(document).on("showMenu", function(state) {
      console.log("showMenu 2", state);
      parent.showMenu = state;
    });

  },
  methods:{
    generate(){
      toastr.options.closeButton = true;
      try{
        var data = JSON.parse(this.menu); 
        this.$emit("generateMenu",data); 
        this.$emit("showMenu",true); 
        this.debug ? toastr.success("Menu généré", "Succès") : ''; 
      }
      catch(e){
        console.error("ERREUR JSON PARSE",e)
        toastr.error("Erreur formatage JSON.","ERREUR"); 
      }
    }
  }
}
</script>

<style>

</style>
