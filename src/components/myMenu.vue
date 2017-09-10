<template>
  <div>
    <div class="mt-element-list" style="background-color:rgba(255,255,255,0.4)" v-for="(menu,index) in menus">      
      <div class="mt-list-head list-news ext-1 font-white bg-yellow-crusta" v-if="(menu.header && menus[menus.length-1].keep_header != false)" :style="menu.style.header.div">
        <div class="list-head-title-container">
          <h3 class="list-title" v-if="menu.header && menu.header.title" :style="menu.style.header.title" v-html="menu.header.title"></h3>
        </div>
        <div class="list-count pull-right bg-yellow-saffron" :style="menu.style.header.right_box" v-html="menu.header.right_box" v-if="menu.header.right_box"></div>
      </div>
      <div v-if="index > 0 && index == menus.length-1">
        <a class="btn btn-back" @click="goBack()" style="width:100%">Retour</a>
      </div>
      <div class="mt-list-container list-default ext-1 group" v-if="menu && menu.parts && index == (menus.length-1)">
        <div v-for="section,index_section in menu.parts">
          <div>
            <a class="list-toggle-container" :data-toggle="menu.parts.length > 1 ? 'collapse' : ''" :href="'#completed'+index_section" aria-expanded="true" v-if="section.title">
              <div class="list-toggle done uppercase" :style="{'background-color':section.color}"> {{section.title}}
                <span style="float:right" v-html="section.rigth_box"></span>
              </div>
            </a>
          </div>
          <div class="panel-collapse collapse in" :id="'completed'+index_section" aria-expanded="true" style="">
            <ul style="background-color:rgba(255,255,255,0.6)">
              <li class="mt-list-item done" @click="goAction(item,section)" :class="{'section':item.menu_action,'action':item.action}" :style="{'border-left-color':section.color,'background-color':getBgColorItem(section.color,item),'padding':item.type ? '0px': ''}" v-for="item in section.items">
                <div v-if="item.left_box && !item.type && !item.type != 'number'" class="list-icon-container" v-html="item.left_box"></div>
                <div v-if="!item.type && item.type != 'number'" class="list-datetime" v-html="getHtmlRightBox(item)"></div>
                <div v-if="item.title" class="list-item-content" :style="{'padding':item.type ? '15px' : ''}">
                  <h3 style="font-weight:bold;">
                    {{item.title}}
                  </h3>
                  <p v-if="item.description">{{item.description}}</p>

                </div>
                <input v-if="item.type && item.type == 'number'" v-model="item.value" :placeholder="item.title" type='number' :step="item.step ? item.step : '1'" class='inputNumber form-control' min='0'/>
                <textarea v-if="item.type && item.type == 'textarea'" v-model="item.value" style="resize:vertical" rows="5" class='form-control'></textarea>
              </li>
              <li v-if="section.action" class="mt-list-item done action" @click="goAction(section)" :style="{'border-left-color':section.color,'background-color':getBgColorItem(section.color)}">
                <div class="list-datetime" v-html="getHtmlRightBox(section)"></div>
                <div class='list-item-content'>
                  <h3 style="font-weight:bold;"><span v-if="section.action_title">{{section.action_title}}</span><span v-else>Valider</span></h3>
                </div>
              </li> 
            </ul>
          </div>
        </div>
      </div>
      <div v-if="index > 0 && index == menus.length-1">
        <a class="btn btn-back" @click="goBack()" style="width:100%">Retour</a>
      </div>
    </div>
  </div>
</template>

<script>

var config = require("../config/settings.json");

export default {
  name: 'menu',
  props:['data'],
  data () {
    return {
     menus:[]
    }
  },
  watch:{
    data:function(){
      // toastr.warning("MENU WATCH");
      this.menus = [this.data];
    }
  },
  mounted:function(){
    this.menus = [this.data];

  },
  methods:{
    goAction(item,section){
      var data = {};
      if(item.action){
        // Emit action. 
        // Si c'est une section vérifie items pour récupérer les valeurs. 
        if(item.items){
          for(var i=0;i<item.items.length;i++){
            if(item.items[i].name){
              data[item.items[i].name] = item.items[i].value; 
            }
          }
        }
        if(item.name){
          data[item.name] = item.value; 
        }

        console.info("data",data); 
        var resource = item.resource ? item.resource : section && section.resource ? section.resource : null; 
        if(resource){
          var parent = this;
          this.$http.post(config.api_url + resource +"/" + item.action, data).then((response) => {
            // Success.
          }, (response) => {
            // Error.
          });
        }

      }
      else if(item.menu_action){
        this.menus.push(item.menu_action);
      }
    }, 
    goBack(){
      this.menus.pop(); 
    },
    getHtmlRightBox(item){
      var html = item.right_box ? item.right_box : ""; 
      if(item.menu_action){
        return html+"<i class='fa fa-chevron-right' style='position:relative;right:-10px'></i>";
      }
      else if(item.action){
        return html+"<i class='fa fa-check-circle' style='position:relative;right:-10px'></i>"
      }
      return html;
    },
    getBgColorItem(color,item){
        var rgb = color.indexOf("#") >= 0 ? this.hexToRgb(color) : color; 
        if(!rgb) return ''; 
        var rgba = rgb.replace(/rgb/i, "rgba");
        if(color && item && !item.menu_action && !item.action){
          return rgba.replace(/\)/i,',0.5)');
        }
        else{
          return rgba.replace(/\)/i,',0.3)');
        }
      return '';
    },
    hexToRgb(hex) {
      var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
      return result ? 'rgb('+parseInt(result[1], 16)+','+parseInt(result[2], 16)+','+parseInt(result[3], 16)+')' : null;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.mt-element-list .list-default.mt-list-container ul>.mt-list-item>.list-item-content{
  padding: 0px;
}


.mt-element-list .list-default.mt-list-container ul>.mt-list-item>.list-icon-container{
  padding: 0px;
  padding-right:5px;
  /* height: 35px; */
  width:auto;
}


.mt-element-list .list-default.ext-1.mt-list-container ul>.mt-list-item.done:hover{
  background-color:#eaeaea;
}


.section:hover {
  background-color: #eaeaea!important;
  cursor: pointer;
}

.action:hover{
  background-color: #eaeaea!important;
  cursor: pointer;
}



.mt-element-list .list-default.mt-list-container{
  border-left:0px;
  border-right:0px;
  padding-top:0px;
}


.mt-element-list .list-default.mt-list-container ul>.mt-list-item>.list-datetime{
  width:auto; 
}


.btn-back{
  font-weight:bold;
  font-size:17px;
  color:black;
  text-transform:uppercase;
}

.btn-back:hover{
  background-color:rgba(0,0,0,0.6);
  color:white;
}

.inputNumber{
  height:44px;
}

</style>
