# vue-shop

## 1.项目初始化

### 1.1安装Vue脚手架

```
需要先安装node.js
npm install -g @vue/cli
```

### 1.2通过脚手架创建项目

```
vue ui  进入vue 项目管理界面
```

### 1.3创建项目，配置路由，css处理器，配置Element-ui ,配置axios

![60983463257](assets/1609834632574.png)

![60983486920](assets/1609834869206.png)

![60983496676](assets/1609834966767.png)



### 1.4初始化git仓库

```
git init
```

### 1.5将本地项目托管到github

## 2.实现登录功能

A.登录状态保持
如果服务器和客户端同源，建议可以使用cookie或者session来保持登录状态
如果客户端和服务器跨域了，建议使用token进行维持登录状态。

B.登录逻辑：
在登录页面输入账号和密码进行登录，将数据发送给服务器
服务器返回登录的结果，登录成功则返回数据中带有token
客户端得到token并进行保存，后续的请求都需要将此token发送给服务器，服务器会验证token以保证用户身份。

C.添加新分支login，在login分支中开发当前项目vue_shop：
打开vue_shop终端，使用git status确定当前项目状态。
确定当前工作目录是干净的之后，创建一个分支进行开发，开发完毕之后将其合并到master
git checkout -b login
然后查看新创建的分支：git branch
确定我们正在使用login分支进行开发

```
运行----------> 启动
```

![60983568412](assets/1609835684129.png)



### 2.1对项目的默认设置进行修改

打开项目的src目录，点击main.js文件（入口文件）

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import './plugins/element.js'

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App)
}).$mount('#app')

```

再打开App.vue(根组件)，将根组件的内容进行操作梳理(template中留下根节点，script中留下默认导出，去掉组件，style中去掉所有样式,添加router-view 标签)

```vue
<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>

export default {
  name: 'app'
}
</script>

<style>
</style>

```

再打开router.js(路由)，将routes数组中的路由规则清除，然后将views删除，将components中的helloworld.vue删除

```vue
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const routes = []

const router = new VueRouter({
  routes
})

export default router

```

在components文件夹中新建Login.vue组件,添加template，script，style标签,style标签中的scoped可以防止组件之间的样式冲突，没有scoped则样式是全局的

```vue
<template>
    <div class="login_container">
        
    </div>
</template>

<script>
export default {
  
}
</script>

<style lang="less" scoped>
.login_container {
  background-color: #2b5b6b;
  height: 100%;
}

</style>
```

在router.js中导入组件并设置规则
在App.vue中添加路由占位符

```
import Vue from 'vue'
import VueRouter from 'vue-router'
import Login from '../components/Login'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    redirect: '/login'
  },
  {
    path: '/login',
    component: Login
  }
]

const router = new VueRouter({
  routes
})

export default router
```

![60983680299](assets/1609836802996.png)

添加公共样式，在src/assets文件夹下面添加css文件夹，创建global.css文件,添加全局样式,在main.js中导入global.css，使得全局样式生效 import "./assets/global.css"

```css
/* 全局样式表 */
html,body,#app{
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
}

```

然后Login.vue中的根元素也需要设置撑满全屏（height:100%）,最终Login.vue文件中的代码如下

```vue
<template>
    <div class="login_container">
        <!-- 登录盒子  -->
        <div class="login_box">
            <!-- 头像 -->
            <div class="avatar_box">
                <img src="../assets/logo.png" alt="">
            </div>
            <!-- 登录表单 -->
            <el-form :model="loginForm" ref="LoginFormRef" :rules="loginFormRules" label-width="0px" class="login_form">
                <!-- 用户名 -->
                <el-form-item prop="username">
                    <el-input v-model="loginForm.username" prefix-icon="iconfont icon-user" ></el-input>
                </el-form-item> 
                <!-- 密码 -->
                <el-form-item prop="password">
                    <el-input type="password" v-model="loginForm.password" prefix-icon="iconfont icon-3702mima"></el-input>
                </el-form-item> 
                <!-- 按钮 -->
                <el-form-item class="btns">
                    <el-button type="primary" @click="login">登录</el-button>
                    <el-button type="info" @click="resetLoginForm">重置</el-button>
                </el-form-item> 
            </el-form>
        </div>
    </div>
</template>

<script>
export default {
  data() {
    return {
      //数据绑定
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      //表单验证规则
      loginFormRules: {
        username: [
          { required: true, message: '请输入登录名', trigger: 'blur' },
          {
            min: 3,
            max: 10,
            message: '登录名长度在 3 到 10 个字符',
            trigger: 'blur'
          }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          {
            min: 6,
            max: 15,
            message: '密码长度在 6 到 15 个字符',
            trigger: 'blur'
          }
        ]
      }
    }
  },
  //添加行为，
  methods: {
    //添加表单重置方法
    resetLoginForm() {
      //this=>当前组件对象，其中的属性$refs包含了设置的表单ref
      //   console.log(this)
      this.$refs.LoginFormRef.resetFields()
    },
    login() {
      //点击登录的时候先调用validate方法验证表单内容是否有误
      this.$refs.LoginFormRef.validate(async valid => {
        console.log(this.loginFormRules)
        //如果valid参数为true则验证通过
        if (!valid) {
          return
        }

        //发送请求进行登录
        const { data: res } = await this.$http.post('login', this.loginForm)
        //   console.log(res);
        if (res.meta.status !== 200) {
          return this.$message.error('登录失败:' + res.meta.msg) //console.log("登录失败:"+res.meta.msg)
        }

        this.$message.success('登录成功')
        console.log(res)
        //保存token
        window.sessionStorage.setItem('token', res.data.token)
        // 导航至/home
        this.$router.push('/home')
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login_container {
  background-color: #2b5b6b;
  height: 100%;
}
.login_box {
  width: 450px;
  height: 300px;
  background: #fff;
  border-radius: 3px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  .avatar_box {
    height: 130px;
    width: 130px;
    border: 1px solid #eee;
    border-radius: 50%;
    padding: 10px;
    box-shadow: 0 0 10px #ddd;
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    img {
      width: 100%;
      height: 100%;
      border-radius: 50%;
      background-color: #eee;
    }
  }
}
.login_form {
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 0 20px;
  box-sizing: border-box;
}
.btns {
  display: flex;
  justify-content: flex-end;
}
</style>

```

其中我们有用到一下内容，需要进行进一步处理：

A.添加element-ui的表单组件（**无需操作，已全部导入**）
在plugins文件夹中打开element.js文件，进行elementui的按需导入
import Vue from 'vue'
import { Button } from 'element-ui'
import { Form, FormItem } from 'element-ui'
import { Input } from 'element-ui'

Vue.use(Button)
Vue.use(Form)
Vue.use(FormItem)
Vue.use(Input)



B.添加第三方字体
复制素材中的fonts文件夹到assets中,在入口文件main.js中导入import './assets/fonts/iconfont.css'
然后直接在 <el-input prefix-icon="iconfont icon-3702mima"></el-input>
接着添加登录盒子

C.添加表单验证的步骤
1).给<el-form>添加属性:rules="rules"，rules是一堆验证规则，定义在script、中
2).在script中添加rules：export default{ data(){return{......, rules: {
          name: [
            { required: true, message: '请输入活动名称', trigger: 'blur' },
            { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
          region: [
            { required: true, message: '请选择活动区域', trigger: 'change' }
          ]
}......
3).通过<el-form-item>的prop属性设置验证规则<el-form-item label="活动名称" prop="name">

4.导入axios以发送ajax请求
打开main.js，import axios from 'axios';
设置请求的根路径：axios.defaults.baseURL = 'http://127.0.0.1:8888/api/private/v1/';
挂载axios：Vue.prototype.$http = axios;

5.配置弹窗提示：
在plugins文件夹中打开element.js文件，进行elementui的按需导入
import {Message} from 'element-ui'
进行全局挂载：Vue.prototype.$message = Message;
在login.vue组件中编写弹窗代码：this.$message.error('登录失败')

## 3.登录成功之后的操作









