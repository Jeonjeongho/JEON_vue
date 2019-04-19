<template>
    <div>
        <h1>
            연락처
        </h1>
        <div class="wrapper">
            <div class="box" v-for="c in contacts" :key="c.no">
                <!--<router-link :to="'/contacts/'+c.no">-->
                <!--<router-link :to="{name : 'contactbyno', params: {no: c.no}}">-->
                   <!--{{c.name}}-->
               <!--</router-link>-->
                <span @click="navigate(c.no)" class="point">
                    [{{c.name}}]
                </span>
           </div>
           <router-view></router-view>
       </div>
   </div>
</template>
<script>
   import contactlist from "../ContactList";
   //import VueRouter from "vue-router";
   export default {
       name : "contacts",
       data: function() {
           return {
               contacts : contactlist.contacts
           }
       },
       methods: {
           navigate(no) {
               if(confirm("상세 정보를 보시겠습니까?")) {
                   //console.log("/contacts/"+ no +"")
                   this.$router.push({
                       name: 'contactbyno',
                       params: {no: no}
                   }, function() {
                      console.log("/contacts/"+ no + "로 이동 완료!")
                   });
               }

               return false;
           }
       }
   }
</script>
<style>
   .wrapper {
       display: table;
       background: #fff;
       clear: both;
   }
   .box {
       float: left;
       width: 100px;
       background: aqua;
       border-radius: 5px;
       margin: 3px;
       padding: 10px;
       font-size: 120%;
       text-align: center;
   }

   a:link, a:visited {
       display: inline-block;
       text-align: center;
       text-decoration: none;

   }

    .point {
        cursor: pointer;
    }
</style>