# Chapter 13 트랜지션 효과

## 13.1 트랜지션 컴포넌트 기초

뷰는 트랜지션 래퍼 컴포넌트(Transition wrapper component)를 지원합니다.<br>
모든 요소, 컴포넌트, router-view를 감싸주는 것만으로 효과를 손쉽게 지정할 수 있습니다.<br>
애니메이션 효과는 트랜지션 래퍼 컴포넌트로 감싸진 요소, 컴포넌트, 라우버튜가 삭제되거나 추가되거나 변경될 때 발생합니다.

```
<style>
.fade-enter-active,
.fade-leave-active{transition:opacity .5s;}
.fade-enter, .fade-leave-to{opacity:0;}
</style>

<div id="demo">
    <button v-on:click="show = !show">Toggle</button>

    <transition name="fade">
        <p v-if="show">hello</p>
    </transition>
</div>

<script>
new Vue({
    el : '#demo',
    data : {
        show : true
    }
});
</script>
```

- 트랜지션 클래스 : 진입/진출 트랜지션에는 4가지 클래스가 적용됩니다.

1. v-enter : 요소가 나타나기 시작할 때 적용되는 클래스
2. v-enter-active : 요소가 나타나는 트랜지션이 진행되는 동안 적용할 클래스
3. v-enter-to : 요소가 나타나는 트랜지션이 완료될 때 적용할 클래스
4. v-leave : 요사가 사라지기 시작할 때 적용할 클래스
5. v-leave-active : 요소가 사라지는 트랜지션이 진행되는 동안 적용할 클래스
6. v-leave-to : 요소가 사라지는 트랜지션이 완료될 때 적용할 클래스

![트랜지션 클래스 작동 원리](https://kr.vuejs.org/images/transition.png)

## 13.2 CSS 애니메이션 처리

CSS 애니메이션은 CSS 트랜지션과 같은 방식으로 적용됩니다.

```
<style>
.bounce-enter-active{animation:bounce-in .5s;}
.bounce-leave-active{animation:bounce-in .5s reverse;}
@keyframes bounce-in{
    0%{transform:scale(0);}
    50%{transform:scale(1.5);}
    100%{transform:scale(1);}
}
</style>

<div id="example-2">
    <button @click="show = !show">Toggle show</button>
    
    <transition name="bounce">
        <p v-if="show">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris facilisis enim libero, at lacinia diam fermentum id. Pellentesque habitant morbi tristique senectus et netus.</p>
    </transition>
</div>

<script>
new Vue({
    el : '#example-2',
    data : {
        show : true
    }
})
</script>
```

## 13.3 트랜지션 이벤트 훅

트랜지션 효과가 시작되거나 종료될 때의 이벤트 훅을 이용해 프로그래밍 수준에서 트랜지션 효과를 제어 할 수 있습니다.

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
<style>
    .box { margin:10px; }
    .ball { width:100px; height:100px; background:black; }
</style>
<div id="app">
    <div class="box">
        <button v-on:click="changeVisible">
            보여주기 토글
        </button>
    </div>
    <div class="box">
        <transition appear
                    v-on:before-enter="beforeEnter"
                    v-on:enter="enter"
                    v-on:leave="leave">
            <div class="ball" v-if="visible"></div>
        </transition>
    </div>
</div>

<script type="text/javascript">
    Vue.config.devtools = true;
    var v = new Vue({
        el : '#app',
        data : function() {
            return {
                visible:true
            }
        },
        methods : {
            changeVisible : function() {
                this.visible = !this.visible;
            },
            beforeEnter: function (el) {
                el.style.opacity = 0
            },
            enter: function (el, done) {
                Velocity(el, { opacity: 0, scale:0.2 }, { duration: 200 })
                Velocity(el, { opacity: 0.7, scale:1.2 }, { duration: 200 })
                Velocity(el, { opacity: 1, scale:1 }, { complete: done })
            },
            leave: function (el, done) {
                Velocity(el, { translateX: '0px', opacity: 1 }, { duration: 100 })
                Velocity(el, { translateX: '20px', opacity: 1 }, { duration: 100, loop:2 })
                Velocity(el, { translateX: '0px', opacity: 1 }, { duration: 200 })
                Velocity(el, { translateX: '100px', opacity: 0 }, { complete:done })
            }
        }
    })
</script>
```

## 13.4 트랜지션 리스트

v-for를 사용하여 동시에 렌더링 하고자 하는 항목은 `<transition-gorup>`를 사용합니다.
엘리먼트 내부 구현은 항상 필요하고, 고유한 key 속성을 가집니다.

```
<style>
.list-item {
    display: inline-block;
    margin-right: 10px;
}
.list-enter-active, .list-leave-active {
    transition: all 1s;
}
.list-enter, .list-leave-to /* .list-leave-active below version 2.1.8 */ {
    opacity: 0;
    transform: translateY(30px);
}
</style>

<div id="list-demo">
    <button v-on:click="add">Add</button>
    <button v-on:click="remove">Remove</button>
    <transition-group name="list" tag="ul">
        <li v-for="item in items" v-bind:key="item" class="list-item">
        {{ item }}
        </li>
    </transition-group>
</div>

<script>
new Vue({
    el: '#list-demo',
    data: {
        items: [1,2,3,4,5,6,7,8,9],
        nextNum: 10
    },
    methods: {
        randomIndex: function () {
            return Math.floor(Math.random() * this.items.length)
        },
        add: function () {
            this.items.splice(this.randomIndex(), 0, this.nextNum++)
        },
        remove: function () {
            this.items.splice(this.randomIndex(), 1)
        },
    }
})
</script>
```