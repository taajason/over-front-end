 
入口文件main.js
import Vue from 'vue';
import App from './App.vue';

import Vuex from 'vuex';
Vue.use(Vuex);

let store = new Vuex.Store({
    state: {            //数据
        num: 1
    },
    mutations: {        //改变靠近的数据
        add (state) {
            state.num++;
        },
        add2 (state,obj) {
            state.num = obj.num + 10;
        }
    },
    actions: {         //执行动作
        addAction (store) {
            store.commit('add');
        },
        add2Action ({commit},obj){
            commit('add2',obj);
        }
    },
    getters: {
        getNum(state){
            return state.num;
        }
    }
});

new Vue({
    el: '#App',
    render:create=>create(App),
    store
});

App.vue
<script>
    export default {
        name: "App",
        created(){
            //注意：不建议下列方式修改状态
            // this.$store.commit('add');
            // this.$store.commit('add',{num:5});  //进化版
            //推荐做法：使用actions分发任务
            // this.$store.dispatch('addAction');
            this.$store.dispatch('add2Action',{
                num:10
            });
            // console.log(this.$store.state);  //不推荐该获取方式
        },
        computed:{
            getNum(){
                return this.$store.getters.getNum;
            }
        }
    }
</script>


 


https://segmentfault.com/a/1190000007020623


