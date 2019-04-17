# Chapter 12 : vue-router란
최근 개발되는 웹애플리케이션은 SPA(단일 페이지 에플리케이션)구조입니다.<br>
여려 화면을 하나의 페이지 안에서 제공하면서도 화면을 별도로 로딩하지않습니다. 화면은 고유의 식별자를 기반으로 화면을 렌더링을 합니다. <br>
고유 식별자로 사용하기에 가장 적절한 정보가 바로 URL입니다.<br>
Vue.js 에서 사용자가 요청한 URL 경로에 따라 각각 다른 화면을 렌더링되도록 하려면 직접 코드로 구현해 사용할 수도 있지만 대부분 잘만들어진 라이브러러리를 사용해야합니다.<br>
Vue.js에서는 vue-router라는 것을 이용하면 됩니다. 
기능을 다은과 같습니다.

1. 중첩된 결로, 뷰를 매핑할 수 있습니다.

2. 컴포넌트 기반의 라우팅을 구현할 수 있습니다.
   
3. Vue.js의 전환 효과를 적용할 수 있습니다.

4. 쿼리스트링,파라미터, 와일드 카드를 사용하여 라우팅을 구현할 수 있습니다.


## 동적 라우트(Dynamic Route)
일정한 패턴의 URL경로를 하나의 컴포넌트에 연결하는 방법입니다.<br>
URL 경로의 일부에 실행에 필요한 파라미터 값이 포함된 경우에 유용합니다.

### 인스턴스 라이프사이클 훅
각 Vue 인스턴스는 생성될 때 일련의 초기화 단계를 거칩니다. 
모든 라이프사이클 훅은 this 컨텍스트가 호출하는 Vue 인스턴스를 가리키며 호출됩니다

```
created: function() {
    this.no = this.$route.params.no;
},

```

## 중첨 라우트
하나의 컴포넌트가 다시 하위 컴포넌트를 포함하는 경우에 라우팅도 중첩이 가능해야합니다.
중첩 라우트에서는 인스턴스 라이프사이클 훅이 한번 작동하므로 한번 이상 데이터 변환이 필요할 시 관찰 속성(watched property)을 이용할 수 있습니다.

```
created: function() {
  this.no = this.$route.params.no;
},
watch: {
  '$route' : function(to, from) {
      this.no = to.params.no;
  },
},

```



## 명명된 라우트(named Routes)
라우트 정보에 고유한 이름을 부여하는 것입니다.<br />
이것을 사용할 경우 URL 경로가 아닌 부여된 라우트 이름으로 네비게이션하도록 할 수 있습니다.
url은 단어로 함축할수 있으며 url get 값 또한 컨트롤 할 수 있습니다.
query, params 객체를 전달하는 방법은 아래와 같습니다. 
문자열이 아닌 객체를 바인딩하고있습니다. 따라서 반드시 v-bind 디렉티브를 이용해 바인딩합니다.


```
<router-link to="/home">
<router-link :to="{name: 'home'}">

params
/contacts/:no 
{name: 'contacts', params: {no : 1003}}

query
/contacts/?pageno=2
{name: 'contacts', params: {query : 1003}}
```
  
  
## 프로그래밍 방식의 라우팅 제어

### 라우터 객체의 push 메서드
router-link를 이용해 선언적으로 네이게이션하는 방법 이외에 프로그래밍 방식으로 네비게이션을 할수도 있습니다.
예를 들어 링크를 클릭하면 바로 이동하는 것이 아니라 사용자의확인을 받고 이동하는 이벤트 처리를 해보겠습니다.

```
push(location,[... completeCallback, abortcallback ])
```
1. location 필수 옵션입니다. 이동해야할 경로 정보

2. completeCallback 내비게이션이 완료되었을 때 호출되는 콜백 함수입니다. 

3. abortcallback 내비게이션이 취소되었을 때 콜백 함수입니다. 

2,3 번은 필수가 아니므로 필요하지 않는다면 생략 할 수 있습니다. <br>

```
//문자열 직접 전달
this.$router.push('/home');

//객체 정보로 전달
this.$router.push({path: "/about"});

//명명된 라우트 사용
this.$router.push({name: 'contacts', params: {no : 1002}});

// 쿼리 문자열 잔달 ex) /contacts?pageno=1&pagesize=5;
router.push({path: '/contacts', query: {pageno: 1, pagesize: 5 }});

avigate(no) {
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

```

### 네이게이션 보호
프로그래밍 방식으로 라우팅을 제어하는 방법으로는 내비게이션 보호(Navigation Guards)기능이 있습니다.<br>
이 기능은 다른 경로로 리디렉션하거나 네비게이션을 취소하여 애플리케이션의 내비게이션을 보호하는데 사용됩니다.<br /><br />
내비게이션 보호 기능은 전역 수준, 라우트 정보 수준, 컴포넌트 수준에서 사용할 수 있습니다. 

전연수준에서의 내비게이션 보호는 라우팅이 일어나기 전에 실행되는 beforeEach, 이후 실행되는 afterEach 메서드로 나눌 수 있습니다. 각 함수에 전달되는 인자 함수는 to, from, next의 파라미터를 사용할 수 있습니다.
to 파라미터로 전달되는 값은 이동하려는 대상의 Route 객체입니다. from은 이동하기 전의 route 객체입니다. /from에서 to 로 이동하는 상황이라고 생각하면됩니다. next는 함수형입니다. 


1. next(): 다음 이벤트 훅으로 이동시킵니다. 이 함수를 호출하지 않으면 다음 이벤트 훅으로 이동하지 않습니다.

1. next(flase): 현제 네비게이션을 중단합니다. ㄹfrom 라우트 객체의 url로 재설정됩니다.

1. next(경로): 지동된 경로로 디리렉션합니다. 현제의 내비게이션이 중단되고 새로운 내비게이션이 시작됩니다. 

1. next(error): next에 전달된 인자가 error 객체라면 내비게이션이 중단되고 router.onError()를 이용해 등록된 콜백에 에러가 전달됩니다.


```
전역 수전

const router = new VueRouter({...})
router.beforeEach((to, from, next) => {
    //...
})
router.afterEach((to, from) => {
    //...
})
router.beforeEach((to, from, next) => {
      //next()
  });


```

컴포넌트 내부에서는 Vue 인스턴스 라이프 사이클에 살펴보았던 라이프 사이클 훅(이벤트 훅)과 동일한 방법으로 네비게이션 보호 기능을 사용할 수 있습니다.

```
beforeRouteEnter(to, from, next) {

},

beforeRouteLeave(to, from, next) {

}

beforeRouteUpdata(to, from, next) {

}
```
1. beforeRouteEnter 렌더링하는 라우트 이전에 호출되는 훅입니다. 이훅이 호출되는 시점에는 Vue 인스턴스가 만들어지지않았기 때문에 this 를 이용할 수 없단점을 주의합니다.

2. beforeRouteLeave 현제 경로에서 다른경로로 빠져나갈 때 호출되는 훅입니다.

3. beforeRouteUpdata 이미 렌더링된 컴포넌트의 경로가 변경될 때 호출되는 훅입니다. 이미 현재 컴포넌트의 Vue 인스턴스가 만들어져 있어서 재사용될 때는 beforeRouteEnter 훅은 호출되지 않고 beforeRouteUpdata 훅이 호출됩니다. 이 훅은 대신에 $route 옵션에 관찰속성을 사용할 수 있습니다.

```
beforeRouteUpdate(to, from, next) {
    console.log("** beforeRouteUpdate");
    this.no = to.params.no;
    next();
},
```

sample

```
  import Home from "./components/Home.vue";
  import About from "./components/About.vue";
  import Contacts from "./components/Contacts.vue";
  import ContackByNo from "./components/ContactByNo.vue";
  import NotFound from "./components/NotFound.vue";
  import VueRouter from "vue-router";

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
                     props: true
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


```
실행순서 

```
** beforeEach!!
App.vue?234e:58 @@ beforeEnter! : /contacts-->/contacts/1001
App.vue?234e:59 true
App.vue?234e:87 ** afterEach!!
Contacts.vue?833d:38 /contacts/1001로 이동 완료!
```

### 라우팅 모드
VueRouter 객체의 기본설정 도느는 해시 모드입니다. URL에서 해시(#)기호 다음의 경로는 경로가 페이지 내부의 이름으로 여겨지기 때문에 해시 이후의 경로가 바뀌더라고 페이지가 다시 로드되지 않습니다.

```
const router = new VueRouter({
     //mode : "history",

```
해시모드일경우 url 경로를 직접 바꿔도 네트워크환경에 변화가 없습니다. 하지만 VueRouter mode 를 history로 변경할 경우 url을 직접 입력하면 네트워크 환경이 변화가 되는걸 확인하실수 있습니다.


### 404 페이지 
프로젝트 환경이 Node.js + Express와 같은 서버를 사용하는 경우라면 서버쪽의 라우트를 이용해 라우트 정보가 일치하지 않은 경우 아래와 같은 처리가 가능합니다.

```
  import NotFound from "./components/NotFound.vue";
    {
        path: "*",
        component: NotFound
    }

```




### 라우트 정보를 속성으로 연결하기
컴포넌트를 라우트 객체에 의존적으로 사용하는 것은 재사용성 측면에서 바람직하지않습니다. 이전 까지 작성한 예제에서 ContactByNo.vue 컴포넌트는 this.$route 객체의 정보에 의존적이기 때문에 라우팅을 사용하지 않는 애플리케이션에서는 사용하기에 적합하지않습니다.
이 경우 속성을 정희하고 속성값으로 this.$route.params 정보를 할당하도록 하는것입니다.

```
App.vue

children: [
     {
         path: ":no",
         name : "contactbyno",
         component: ContackByNo,
         props: true
     }
 ]
 
 
ContactByNo.vue
 
import contactlist from '../ContactList';

export default {
 name : 'contactbyn',
 props: ["no"],
 data: function() {
   return {
       //no : 0,
       contacts: contactlist.contacts
   }
 },
//        data: function() {
//          return {
//              no: 0,
//              contacts: contactlist.contacts
//          }
//        },
//        created: function() {
//            this.no = this.$route.params.no;
//        },
//        watch: {
//            '$route' : function(to, from) {
//                this.no = to.params.no;
//            },
//        },
//        beforeRouteUpdate(to, from, next) {
//            console.log("** beforeRouteUpdate");
//            this.no = to.params.no;
//            next();
//        },
 computed: {
     contact: function() {
         var no = this.no;
         var arr = this.contacts.filter(function(item, index) {
             return item.no == no;
         });
         if(arr.length == 1) {
             return arr[0];
         } else {
             return {}
         }
     }
 }
}
```
props:true가 추가된것을 볼 수 있습니다. props:true인 경우 route.params 정보를 동일한 속성 (props)에 할당합니다. 
created 이벤트 훅(초기값)과 beforeRouteUpdate은 더이상 필요하지않습니다. 인스턴스 객체가 생성돼면서 자동으로 정보가 업데이트 되기 때문에 필요하지않습니다.

위 방법은 해쉬스타일에 라우트 방식에 사용돼며 query 정보 등이 속성에 부여되야 한다면 라우트 정보를 다음과 같은 형태로 변경할수있습니다. 

```
function connectQueryToProp(route) {
    return {
        no: route.query.no,
        path: route.path
    };
}

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
     props: connectQueryToProp
 }
]

```