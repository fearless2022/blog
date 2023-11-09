## 官方文档

* https://cn.vuejs.org/

## API风格

* 选项式API（vue2）
* 组合数API

## 创建项目

* ```bash
  # 初始化
  npm init vue@latest
  # 安装依赖
  npm install
  # 运行
  npm run dev
  ```

## 项目结构

* ```text
  your-project/
    |- public/
    |  |- index.html         # 应用程序的入口HTML文件
    |
    |- src/
    |  |- assets/            # 静态资源文件夹，如图片、样式等
    |  |- components/        # 组件文件夹
    |  |- views/             # 页面视图文件夹
    |  |- router/            # 路由配置文件夹
    |  |- store/             # Vuex状态管理文件夹
    |  |- services/          # 网络请求服务文件夹
    |  |- utils/             # 工具函数文件夹
    |  |- App.vue            # 根组件
    |  |- main.js            # 应用程序的入口文件
    |
    |- tests/                # 测试文件夹
    |
    |- .gitignore            # Git忽略文件配置
    |- babel.config.js       # Babel配置文件
    |- package.json          # 项目配置文件
    |- README.md             # 项目说明文件
  ```

## 模板语法

* ```vue
  <template>
    <h3>模板语法</h3>
    <p>{{ msg }}</p>
  </template>
  <script>
    export default {
      data() {
        return {
          msg: "hello vue3"
        }
      }
    }
  </script>
  ```

## 属性绑定

* ```vue
  <template>
    <h3>属性绑定</h3>
    <div v-bind:id="dynamicId" :class="dynamicClass">测试</div>
    <button :disabled="isButtonDisabled">按钮</button>
    <div v-bind="attrObj"></div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        dynamicClass: "dynamicClass",
        dynamicId: "dynamicId",
        isButtonDisabled: true,
        attrObj: {
          class: "dynamicClass",
          id: "dynamicId",
        },
      };
    },
  };
  </script>
  
  <style scoped>
  .dynamicClass {
    color: red;
  }
  </style>
  ```

* `v-bind`指令如果绑定的值是`null`或者`undefined`，那么改attribute将会从渲染的元素上移除

* `v-bind`简写`:`例如`v-bind:class`简写`:class`

## 条件渲染

* ```vue
  <template>
    <h3>条件渲染</h3>
    <div v-if="word === 'A'">A</div>
    <div v-else-if="word === 'B'">B</div>
    <div v-else>{{ word }}</div>
    <div v-show="flag">see</div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        word: "D",
        flag: false,
      };
    },
  };
  </script>
  ```

* `v-if`：`v-if`指令会根据表达式的真假有条件地渲染元素。当表达式为真时，元素将被渲染；否则，元素将不会被渲染。因此，`v-if`具有“惰性”特性，即只有当条件第一次满足时，元素才会被创建和插入DOM

* `v-show`：`v-show`指令也会根据表达式的真假有条件地渲染元素。然而，无论表达式为真或假，元素始终会被创建并被插入DOM。当表达式为假时，元素将不会被显示，但它仍然存在于DOM中，并占用空间。因此，`v-show`具有“惰性”和“始终存在”的特性

## 列表渲染

* ```vue
  <template>
    <h3>列表渲染</h3>
    <div v-for="(i, index) in list" v-bind:key="index">{{ i }} - {{ index }}</div>
    <div v-for="(i, index) of list" :key="index">{{ i }} - {{ index }}</div>
    <div v-for="(value, key, index) in userInfo" :key="index">
      {{ key }} - {{ value }} - {{ index }}
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        list: ["A", "B", "C"],
        userInfo: {
          id: 1,
          name: "fearless",
          sex: "男",
        },
      };
    },
  };
  </script>
  ```

## 事件处理器

* ```vue
  <template>
    <h3>内联事件处理器</h3>
    <button v-on:click="count++">add - {{ count }}</button>
    <h3>方法事件处理器</h3>
    <button @click="addCount">add - {{ num }}</button>
    <h3>event对象</h3>
    <button @click="addEventCount">add</button>
    <div>{{ eventNum }}</div>
    <h3>事件传参</h3>
    <p @click="getNum(item, $event)" v-for="(item, index) in nums" :key="index">
      {{ item }}
    </p>
    <h3>事件修饰符</h3>
    <a @click.prevent="clickHandler" href="https://www.baidu.com">百度一下</a>
    <div @click="clickDiv">
      <p @click.stop="clickP">点击</p>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        count: 0,
        num: 0,
        eventNum: 0,
        nums: [1, 2, 3],
      };
    },
    methods: {
      addCount(e) {
        this.num++;
      },
      addEventCount(e) {
        e.target.innerHTML = "add - " + this.eventNum;
        this.eventNum++;
      },
      getNum(item, e) {
        console.log(item, e);
      },
      clickHandler(e) {
        // 阻止默认事件
        // e.preventDefault();
        console.log("点击了！");
      },
      clickDiv() {
        console.log("clickDiv");
      },
      clickP(e) {
        // 阻止事件冒泡
        // e.stopPropagation();
        console.log("clickP");
      },
    },
  };
  </script>
  ```

* `v-on:`简写`@`例如`v-on:click`简写`@click`

## 数组侦听

* ```vue
  <template>
    <h3>数组监听</h3>
    <ul>
      <li v-for="(item, index) in nums" :key="index">
        {{ item }}
      </li>
    </ul>
    <button @click="addNum">添加数据</button>
  </template>
  
  <script>
  export default {
    data() {
      return {
        nums: [1, 2, 3, 4, 5],
      };
    },
    methods: {
      addNum() {
        // 变更方法：引起UI自动更新
        this.nums.push(6);
        // 替换一个数组：不会引起UI自动更新
        // this.nums = this.nums.concat(7);
      },
    },
  };
  </script>
  ```

## 计算属性

* ```vue
  <template>
    <h3>计算属性</h3>
    <p>{{ userInfo.name }}</p>
    <p>{{ getNum }}</p>
    <p>{{ getNums() }}</p>
  </template>
  
  <script>
  export default {
    data() {
      return {
        userInfo: {
          name: "fearless",
          nums: [1, 2, 3],
        },
      };
    },
    computed: {
      getNum() {
        return this.userInfo.nums.length > 0 ? "yes" : "no";
      },
    },
    methods: {
      getNums() {
        return this.userInfo.nums.length > 0 ? "yes" : "no";
      },
    },
  };
  </script>
  ```

* 计算属性：计算属性值会基于其响应式依赖被缓存，一个计算属性仅会在其响应式依赖更新时才会重新计算

* 方法：方法调用总是会在重新渲染发生时再次执行函数

## class绑定

* ```vue
  <template>
    <h3>class绑定</h3>
    <p :class="{ active: isActive, 'text-danger': isDanger }">class</p>
    <p :class="myClass">class</p>
    <p :class="[arrActive, arrDanger]">class</p>
    <p :class="[isActive ? 'active text-danger' : '']">class</p>
    <p :class="[isActive ? 'active' : '', { 'text-danger': isDanger }]">class</p>
  </template>
  
  <script>
  export default {
    data() {
      return {
        isActive: true,
        isDanger: true,
        myClass: {
          active: true,
          "text-danger": true,
        },
        arrActive: "active",
        arrDanger: "text-danger",
      };
    },
  };
  </script>
  
  <style>
  .active {
    font-size: 30px;
  }
  .text-danger {
    color: red;
  }
  </style>
  ```

* 数组和对象嵌套时，只能数组嵌套对象

## style绑定

* ```vue
  <template>
    <h3>style绑定</h3>
    <p :style="{ color: activeColor, fontSize: activeFont + 'px' }">style</p>
    <p :style="myStyle">style</p>
    <p :style="[myStyle]">style</p>
  </template>
  
  <script>
  export default {
    data() {
      return {
        activeColor: "green",
        activeFont: 30,
        myStyle: {
          color: "green",
          fontSize: "30px",
        },
      };
    },
  };
  </script>
  ```

## 侦听器

* ```vue
  <template>
    <h3>侦听器</h3>
    <p>{{ msg }}</p>
    <button @click="update">修改数据</button>
  </template>
  
  <script>
  export default {
    data() {
      return {
        msg: "hello",
      };
    },
    methods: {
      update() {
        this.msg = "world";
      },
    },
    watch: {
      // 函数名必须与侦听的数据对象保持一致
      // 数据发生变化，自动执行的函数
      msg(newValue, oldValue) {
        console.log(newValue, oldValue);
      },
    },
  };
  </script>
  ```

* 侦听器函数名必须与侦听的数据对象保持一致

## 表单数据绑定
