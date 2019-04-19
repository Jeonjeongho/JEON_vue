<template>
  <div>
    <header class="header">
        <h1 class="headerTest">
          (주)OpenSG
        </h1>
        <nav>
          <ul>
            <li>
              <!--<router-link to="/home">-->
              <router-link :to="{name: 'home'}">
                Home
              </router-link>
            </li>
            <li>
              <router-link to="/about">
                About
              </router-link>
            </li>
            <li>
              <router-link to="/contacts">
                Contacts
              </router-link>
            </li>
          </ul>
        </nav>
    </header>
    <div class="container">
      <router-view></router-view>
    </div>
  </div>
</template>
<script>
  import Home from "./components/Home.vue";
  import About from "./components/About.vue";
  import Contacts from "./components/Contacts.vue";
  import ContackByNo from "./components/ContactByNo.vue";
  import NotFound from "./components/NotFound.vue";
  import VueRouter from "vue-router";

  function test(router) {
      console.log(router)
  }

  const router = new VueRouter({
     //mode : "history",
     routes : [
         {
             path: '/', component: Home
         },{
             path: '/home', name: "home" ,component: Home
         },{
             path: '/about', name: "about" ,component: About
         },{
             path: '/contacts', name: "contacts" ,component: Contacts,
             children: [
                 {
                     path: ":no",
                     name : "contactbyno",
                     component: ContackByNo,
                     beforeEnter : (to, from, next) => {
                         console.log("@@ beforeEnter! : " + from.path + "-->" + to.path);
                         console.log(from.path.startsWith("/contacts"))
                         if(from.path.startsWith("/contacts")) next();
                         else next("/home")
                     },
                     props: test
                 }
             ]
         }
         ,{
             path: '/contacts/:no',
             component: ContackByNo,
//             beforeEnter: (ro, from, next) => {
//
//             }
         },
         {
             path: "*",
             component: NotFound
         }
     ]
  });

  router.beforeEach((to, from, next) => {
      console.log("** beforeEach!!")
      next()
  });

  router.afterEach((to, from) => {
      console.log("** afterEach!!")
  });

  export default {
      name : "app",
      router
  }
</script>
<style>
  .header {
    background: aqua;
    padding: 10px 0 0 0;
  };
  .headerTest {
    padding: 0 20px;
  };
  ul {
    overflow: hidden;
    list-style: none;
    margin: 0;
    padding: 0;
    background: purple;
  }

  ul:after {
    display: block;
    clear: both;
    content: "";
  }

  li {
    float: left;
    list-style: none;
  }

  li a {
    display: block;
    padding: 14px 16px;
    color: #555;
    text-align: center;
    text-decoration: none;
  }

  a:hover {
    background: aqua;
    color: block;
  }

  .container {
    background: #ccc;
  }
</style>
