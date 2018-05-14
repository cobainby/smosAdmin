<template>
  <div class="login-container">
    <div id="canvascontainer" ref='can'></div>
    <div class="text-content">
      <h2>深基坑
        <span>监测</span>
        综合管理
        <em>平台</em>
      </h2>
      <h4>Deep foundation pit monitoring integrated management platform.</h4>
    </div>
    <Form ref="loginForm" autoComplete="on" :model="loginForm" :rules="loginRules" class="card-box login-form">
      <Form-item prop="email">
        <Input type="text" v-model="loginForm.email" placeholder="Username" autoComplete="on">
        <Icon type="ios-person-outline" slot="prepend"></Icon>
        </Input>
      </Form-item>
      <Form-item prop="password">
        <Input type="password" v-model="loginForm.password" placeholder="Password" @keyup.enter.native="handleLogin">
        <Icon type="ios-locked-outline" slot="prepend"></Icon>
        </Input>
      </Form-item>
      <Form-item>
        <Button type="primary" @click="handleLogin('loginForm')" long>登录</Button>
      </Form-item>
      <!-- <div class='tips'>admin账号为:admin@wz.com 密码123456</div>
      <div class='tips'>editor账号:editor@wz.com 密码123456</div> -->
    </Form>
  </div>
</template>

<script>
import { isWscnEmail } from "utils/validate";

export default {
  name: "login",
  data() {
    const validateEmail = (rule, value, callback) => {
      if (!isWscnEmail(value)) {
        callback(new Error("请输入正确的合法邮箱"));
      } else {
        callback();
      }
    };
    const validatePass = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error("密码不能小于6位"));
      } else {
        callback();
      }
    };
    return {
      loginForm: {
        email: "admin@wz.com",
        password: ""
      },
      loginRules: {
        email: [{ required: true, trigger: "blur", validator: validateEmail }],
        password: [{ required: true, trigger: "blur", validator: validatePass }]
      },
      loading: false,
      showDialog: false
    };
  },
  mounted() {
    container = document.createElement("div");
    this.$refs.can.appendChild(container);

    camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      1,
      10000
    );
    camera.position.z = 1000;

    scene = new THREE.Scene();

    particles = new Array();

    var PI2 = Math.PI * 2;
    // 这里是动态动画中的小圆圈的大小颜色定义
    var material = new THREE.ParticleCanvasMaterial({
      color: 0x0078de,
      program: function(context) {
        context.beginPath();
        context.arc(0, 0, 0.6, 0, PI2, true);
        context.fill();
      }
    });

    var i = 0;

    for (var ix = 0; ix < AMOUNTX; ix++) {
      for (var iy = 0; iy < AMOUNTY; iy++) {
        particle = particles[i++] = new THREE.Particle(material);
        particle.position.x = ix * SEPARATION - AMOUNTX * SEPARATION / 2;
        particle.position.z = iy * SEPARATION - AMOUNTY * SEPARATION / 2;
        scene.add(particle);
      }
    }

    renderer = new THREE.CanvasRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    container.appendChild(renderer.domElement);

    document.addEventListener("mousemove", onDocumentMouseMove, false);
    //

    window.addEventListener("resize", onWindowResize, false);

    animate();
  },
  methods: {
    handleLogin() {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true;
          this.$store
            .dispatch("LoginByEmail", this.loginForm)
            .then(() => {
              this.$Message.success("登录成功");

              this.loading = false;
              this.$router.push({ path: "/" });
            })
            .catch(err => {
              this.$message.error(err);
              this.loading = false;
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    }
  }
};

var SEPARATION = 100,
  AMOUNTX = 50,
  AMOUNTY = 50;

var container;
var camera, scene, renderer;

var particles,
  particle,
  count = 0;

var mouseX = 0,
  mouseY = 0;

var windowHalfX = window.innerWidth / 2;
var windowHalfY = window.innerHeight / 2;

// animate();

function init() {}

function onWindowResize() {
  windowHalfX = window.innerWidth / 2;
  windowHalfY = window.innerHeight / 2;

  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();

  renderer.setSize(window.innerWidth, window.innerHeight);
}

//

function onDocumentMouseMove(event) {
  mouseX = event.clientX - windowHalfX;
  mouseY = event.clientY - windowHalfY;
}

function onDocumentTouchStart(event) {
  if (event.touches.length === 1) {
    event.preventDefault();

    mouseX = event.touches[0].pageX - windowHalfX;
    mouseY = event.touches[0].pageY - windowHalfY;
  }
}

function onDocumentTouchMove(event) {
  if (event.touches.length === 1) {
    event.preventDefault();

    mouseX = event.touches[0].pageX - windowHalfX;
    mouseY = event.touches[0].pageY - windowHalfY;
  }
}

//

function animate() {
  requestAnimationFrame(animate);

  render();
}

function render() {
  camera.position.x += (mouseX - camera.position.x) * 0.05;
  camera.position.y += (-mouseY - camera.position.y) * 0.05;
  camera.lookAt(scene.position);

  var i = 0;

  for (var ix = 0; ix < AMOUNTX; ix++) {
    for (var iy = 0; iy < AMOUNTY; iy++) {
      particle = particles[i++];
      particle.position.y =
        Math.sin((ix + count) * 0.3) * 50 + Math.sin((iy + count) * 0.5) * 50;
      particle.scale.x = particle.scale.y =
        (Math.sin((ix + count) * 0.3) + 1) * 2 +
        (Math.sin((iy + count) * 0.5) + 1) * 2;
    }
  }

  renderer.render(scene, camera);

  count += 0.1;
}
</script>
<style>
/* 参考2016云栖大会 */
.login-container {
  background-color: #193c6d;
  filter: progid: DXImageTransform.Microsoft.gradient(gradientType=1, startColorstr="#003073", endColorstr="#029797");
  background-image: url(//img.alicdn.com/tps/TB1d.u8MXXXXXXuXFXXXXXXXXXX-1900-790.jpg);
  background-size: 100%;
  background-image: -webkit-gradient(
    linear,
    0 0,
    100% 100%,
    color-stop(0, #003073),
    color-stop(100%, #029797)
  );
  background-image: -webkit-linear-gradient(135deg, #003073, #029797);
  background-image: -moz-linear-gradient(45deg, #003073, #029797);
  background-image: -ms-linear-gradient(45deg, #003073 0, #029797 100%);
  background-image: -o-linear-gradient(45deg, #003073, #029797);
  background-image: linear-gradient(135deg, #003073, #029797);
  text-align: center;
  margin: 0px;
  overflow: hidden;
}
.login-container a {
  color: #0078de;
}
#canvascontainer {
  position: absolute;
  top: 0px;
}
.wz-input-group-prepend {
  background-color: #141a48;
  border: 1px solid #2d8cf0;
  border-right: none;
  color: #2d8cf0;
}
</style>

<style rel="stylesheet/scss" lang="scss">
.tips {
  font-size: 14px;
  color: #fff;
  margin-bottom: 5px;
}
.text-content {
  width: 100%;
  padding-top: 15vh;
  text-align: center;
  vertical-align: middle;
  h2 {
    font-family: "HelveticaNeue", "Helvetica Neue", Helvetica, Arial, sans-serif;
    font-size: 40px;
    font-weight: 500;
    color: #fff;
  }
  h2 span {
    font-weight: 700;
    color: #33c1cf !important;
  }

  h2 em {
    font-style: normal;
    font-weight: 700;
    color: #f89624 !important;
  }
  h4 {
    font-size: 20px;
    color: #ffffff;
    margin-top: 10px;
  }
}
.login-container {
  height: 100vh;
  background-color: #2d3a4b;

  input:-webkit-autofill {
    -webkit-box-shadow: 0 0 0px 1000px #293444 inset !important;
    -webkit-text-fill-color: #fff !important;
  }
  input {
    background: transparent;
    border: 1px solid #2d8cf0;
    -webkit-appearance: none;
    border-radius: 3px;
    padding: 12px 5px 12px 15px;
    color: #eeeeee;
    height: 47px;
  }
  .el-input {
    display: inline-block;
    height: 47px;
    width: 85%;
  }
  .svg-container {
    padding: 6px 5px 6px 15px;
    color: #889aa4;
  }

  .title {
    font-size: 26px;
    font-weight: 400;
    color: #eeeeee;
    margin: 0px auto 40px auto;
    text-align: center;
    font-weight: bold;
  }

  .login-form {
    position: absolute;
    left: 0;
    right: 0;
    width: 400px;
    padding: 35px 35px 15px 35px;
    margin: 20px auto;
  }

  .el-form-item {
    border: 1px solid rgba(255, 255, 255, 0.1);
    background: rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    color: #454545;
  }

  .forget-pwd {
    color: #fff;
  }
}
</style>
