<template>
  <div class="tab">
        <div class="tab-item">
          <router-link :to="{name:'goods'}">
            <div>
              商品
            </div>
          </router-link>
        </div>
        <div class="tab-item">
          <router-link :to="{name:'ratings'}">
            <div>
              评论
            </div>
          </router-link>
        </div>
        <div class="tab-item">
          <router-link :to="{name:'seller'}">
            <div>
              商家
            </div>
          </router-link>
        </div>
      </div>

      <div class="footer">
        <h1>{{title}}</h1>
      </div>
      <h1 class="title">{{title}}</h1>
      <ul class="list">
        <li v-for='(todo,index) in todos' :key='todo.value' :class="{'checked': todo.done}">
          <label>{{index + 1}}. {{todo.value}}</label>
          <time>{{todo.created | date}}</time>
        </li>
      </ul>
</template>
<script>
import header from './components/header/header.vue'
import moment from 'moment'
import 'moment/locale/zh-cn'
moment.locale('zh-cn')

export default {
  components: {
    'v-header': header
  },
  filters: {
    date (val) {
      return moment(val).calendar()
    }
  },
  data () {
    return {
      title: 'vue-todos',
      todos: [
        {
          value: '阅读一本关于前端开发的书',
          done: false,
          created: Date.now()
        },
        {
          value: '补充范例代码',
          done: true,
          created: Date.now()
        },
        {
          value: '写心得',
          done: false,
          created: Date.now()
        }
      ]
    }
  }
}
</script>
<style>
</style>
