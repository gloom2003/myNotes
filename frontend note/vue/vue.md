# Vue的使用

**会查看官方文档完成需求即可。**

## 1 三更springBoot课程的案例

### 1.1请求后台接口  把接受到的数据渲染展示在页面中 vue : el、data、methods、created()

~~~html
<div id="app">
    <table class="table table-striped table-bordered table-hover">
    <thead>
        <tr>
            <th>学号</th>
            <th>姓名</th>
            <th>年龄</th>
            <th>地址</th>
        </tr>
    </thead>
    <tbody>
        <!-- 遍历users数组-->
        <tr v-for="user in users">
            <td>{{user.id}}</td>
            <td>{{user.username}}</td>
            <td>{{user.age}}</td>
            <td>{{user.address}}</td>
        </tr>

    </tbody>
</table>
</div>

	<!-- 导入vue与axios-->
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>

   <script>
      var v = new Vue({
          <!-- 对id=app的标签进行控制-->
          el:"#app",
          <!-- 创建一个存放数据的数组-->
          data:{
              users:[]
          },
          <!-- id=app的标签创建时就会调用created()方法-->
          created(){
              this.findAll();
          },
          methods:{
              <!-- 定义方法-->
              findAll(){
                  //请求后台接口  把接受到的数据命名为res，渲染展示在页面中
                  axios.get("http://localhost/user/findAll").then((res)=>{
                      console.log(res);
                      //判断是否成功
                      if(res.data.code==200){
                          //如果成功，把数据赋值给this.users
                          this.users = res.data.data;
                      }

                  })
              }
          }
      });
   </script>
~~~

### 1.2 登录页面

~~~html
<form>
    <!-- 使用v-model把输入框的数据绑定给user.username -->
    <input id="exampleInputEmail1" v-model="user.username" type="email" placeholder="请输入用户名" class="form-control">
    <input id="exampleInputPassword1" v-model="user.password"  type="password" placeholder="请输入密码" class="form-control">
    <!-- 点击登录按钮时调用handleLogin()方法 -->
    <button type="submit" class="btn btn-block btn-primary" @click="handleLogin()">登录</button>
</form>

	<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
   <script>
       var v = new Vue({
           el:"#app",
           data:{
               // 定义一个空的user对象
               user:{}
           },
           methods:{
               handleLogin(){
                   //请求后台登录接口  发起一个异步的post请求，把this.user对象写入请求体中
                   
                   axios.post("http://localhost/sys_user/login",this.user).then((res)=>{
                       // console.log(res);
                       //判断是否成功
                       if(res.data.code==200){
                           //登录成功
                           // alert("登录成功");
                           //存储token
                           localStorage.setItem("token",res.data.data.token);
                           //跳转页面到index.html
                           location.href = "/index.html";
                       }else{
                           //登录失败
                           alert(res.data.msg);
                       }

                   })
               }
           }
       });
   </script>
~~~

