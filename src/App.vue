<template>
  <div id="app">
    <canvas id="canvas" style="border: 1px solid #000;" width="300" height="300" :style2="{transform: 'scale(' + 1 / ratio + ')'}"
      @touchstart="onTouchStart"
      @touchmove="onTouchMove"
      @touchend="onTouchEnd">
    </canvas>

    <br/>
    <!-- img: <img id="pic" style="width: 220px; height: 277px;" src="./assets/img_the_scream.jpg" @load="draw11" /> -->

    <!-- 离屏 -->
    <!-- <img id="pic" style="width: 220px; height: 277px;" src="./assets/img_the_scream.jpg" @load="draw15"/> -->

    <!-- 子像素渲染／抗锯齿 -->
    <!-- <p class="antialias">这个锯齿可还好</p> -->
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    // HelloWorld
  },
  computed: {
    ratio() {
      return window.devicePixelRatio || 1;
    }
  },
  methods: {
    // 嵌套矩形
    draw1() {
      var ctx = document.getElementById('canvas').getContext('2d');

      ctx.fillRect(0,0,150,150);   // 使用默认设置绘制一个矩形
      ctx.save();                  // 保存默认状态

      ctx.fillStyle = '#09F'       // 在原有配置基础上对颜色做改变
      ctx.fillRect(15,15,120,120); // 使用新的设置绘制一个矩形

      ctx.save();                  // 保存当前状态
      ctx.fillStyle = '#FFF'       // 再次改变颜色配置
      ctx.globalAlpha = 0.5;    
      ctx.fillRect(30,30,90,90);   // 使用新的配置绘制一个矩形

      ctx.restore();               // 重新加载之前的颜色状态
      ctx.fillRect(45,45,60,60);   // 使用上一次的配置绘制一个矩形

      ctx.restore();               // 加载默认颜色配置
      ctx.fillRect(60,60,30,30);   // 使用加载的配置绘制一个矩形
    },

    // save／restore
    draw2() {
      var ctx = document.getElementById('canvas').getContext('2d');
      ctx.translate(75,75);

      for (var i=1;i<6;i++){ // Loop through rings (from inside to out)
        ctx.save();
        ctx.fillStyle = 'rgb('+(51*i)+','+(255-51*i)+',255)';

        for (var j=0;j<i*6;j++){ // draw individual dots
          ctx.rotate(Math.PI*2/(i*6));
          ctx.beginPath();
          ctx.arc(0,i*12.5,5,0,Math.PI*2,true);
          ctx.fill();
        }

        ctx.restore();
      }
    },

    // 镜面效果
    draw3() {
      var ctx = document.getElementById('canvas').getContext('2d');

      // draw a simple rectangle, but scale it.
      ctx.save();
      ctx.scale(10, 3);
      ctx.fillRect(1, 10, 10, 10);
      ctx.restore();

      // mirror horizontally
      ctx.scale(-1, 1);
      ctx.font = '48px serif';
      ctx.fillText('MDN', -135, 120);
    },

    // 非零环绕 - 五角星
    draw4() {
      var canvas = document.getElementById("canvas");
      var ctx = canvas.getContext("2d");

      // ctx.beginPath();
      // ctx.moveTo(50, 50);
      // ctx.lineTo(250, 50);
      // ctx.lineTo(100, 250);
      // ctx.lineTo(150, 0);
      // ctx.lineTo(200, 250);
      // ctx.lineTo(50, 50);
      // ctx.closePath();
      // ctx.stroke();
      // ctx.fillStyle = "#058";
      // ctx.fill('evenodd');

      var cvs = document.getElementById('canvas');
      var ctx = cvs.getContext('2d');
      var w = cvs.width,
          h = cvs.height;

      var x = w / 2,
          y = h / 2,
          r = w / 2,
          start = -Math.PI / 2,
          end = Math.PI * 3 / 2;

      ctx.arc(x, y, r, start, end);
      ctx.fillStyle = '#D43D59';
      ctx.fill();

      ctx.beginPath();
      ctx.moveTo(x, y - r);

      // 顶点连下左
      ctx.lineTo(x - r * Math.sin(Math.PI / 5), y + r * Math.cos(Math.PI / 5));

      // 下左连上右
      ctx.lineTo(x + r * Math.cos(Math.PI / 10), y - r * Math.sin(Math.PI / 10));

      // 上右连上左
      ctx.lineTo(x - r * Math.cos(Math.PI / 10), y - r * Math.sin(Math.PI / 10));

      // 上左连下右
      ctx.lineTo(x + r * Math.sin(Math.PI / 5), y + r * Math.cos(Math.PI / 5));

      ctx.fillStyle = '#246AB2';
      ctx.fill('evenodd');
    },

    // 动画／合成 - 太阳系
    draw5() {
      var ctx = document.getElementById('canvas').getContext('2d');

      ctx.globalCompositeOperation = 'destination-over';
      ctx.clearRect(0,0,300,300);

      ctx.fillStyle = 'rgba(0,0,0,0.4)';
      ctx.strokeStyle = 'rgba(0,153,255,0.4)';
      ctx.save();
      ctx.translate(150,150);

      // Earth
      var time = new Date();
      ctx.rotate( ((2*Math.PI)/60)*time.getSeconds() + ((2*Math.PI)/60000)*time.getMilliseconds() );
      ctx.translate(100,0);
      ctx.drawImage(this.earth,-12,-12);

      // Moon
      ctx.save();
      ctx.rotate( ((2*Math.PI)/6)*(time.getSeconds() % 6) + ((2*Math.PI)/6000)*time.getMilliseconds() );
      ctx.translate(0,28.5);
      ctx.drawImage(this.moon,-3.5,-3.5);
      ctx.restore();

      ctx.restore();
      
      ctx.beginPath();
      ctx.arc(150,150,100,0,Math.PI*2,false); // Earth orbit
      ctx.stroke();
    
      // sun
      ctx.drawImage(this.sun,0,0,300,300);

      window.requestAnimationFrame(this.draw5);
    },

    // 动画 - 圆环进度条
    draw6() {
      var canvas = document.getElementById('canvas'),  //获取canvas元素
      context = canvas.getContext('2d'),           //获取画图环境，指明为2d
      centerX = canvas.width/2,                    //Canvas中心点x轴坐标
      centerY = canvas.height/2,                   //Canvas中心点y轴坐标
      rad = Math.PI*2/100,                         //将360度分成100份，那么每一份就是rad度
      speed = 0.1;                                  //加载的快慢就靠它了
              
      //绘制蓝色外圈
      function blueCircle(n){
          context.save();
          context.beginPath();
          context.strokeStyle = "#49f";
          context.lineWidth = 12;
          context.arc(centerX, centerY, 100 , -Math.PI/2, -Math.PI/2 + n*rad, false);
          context.stroke();
          context.restore();
      }
          
      //绘制白色外圈
      function whiteCircle(){
          context.save();
          context.beginPath();
          context.strokeStyle = "#A5DEF1";
          context.lineWidth = 12;
          context.arc(centerX, centerY, 100 , 0, Math.PI*2, false);
          context.stroke();
          context.closePath();
          context.restore();
      }
          
      //百分比文字绘制
      function text(n){
          context.save();
          context.fillStyle = "#F47C7C";
          context.font = "40px Arial";
          context.textAlign = "center";
          context.textBaseline = "middle";
          context.fillText(n.toFixed(0)+"%", centerX, centerY);
          context.restore();
      }
          
      //动画循环
      (function drawFrame(){
          window.requestAnimationFrame(drawFrame, canvas);
          context.clearRect(0, 0, canvas.width, canvas.height);
              
          whiteCircle();
          text(speed);
          blueCircle(speed);
                  
          if(speed > 100) speed = 0;
          speed += 0.1;
      }());
    },

    // 动画 - 黑客帝国
    draw7() {
      var canvas = document.querySelector('canvas'),
      context = canvas.getContext('2d'),
      w = canvas.width = window.innerWidth,
      h = canvas.height = window.innerHeight;
            
      //初始化
      var clearColor = 'rgba(0, 0, 0, .1)',             //用于绘制渐变阴影
          wordColor = "#33ff33",                         //文字颜色
          words = "0123456789qwertyuiopasdfghjklzxcvbnm,./;'\[]QWERTYUIOP{}ASDFGHJHJKL:ZXCVBBNM<>?",
          wordsArr = words.split(''),                 //将文字拆分进一个数组
          font_size = 16,  //字体大小
          clumns = w / font_size,                     //文字降落的列数
          drops = [];

      for(var i=0; i<clumns; i++){
          drops[i] = 1;
      }

      function draw(){
          context.save();
          context.fillStyle = wordColor;
          context.font = font_size + "px arial";
          //核心
          for (var i = 0; i < drops.length; i++){
              var text = wordsArr[Math.floor(Math.random() * wordsArr.length)];
              context.fillText(text, i * font_size, drops[i] * font_size);
              
              // if (drops[i] * font_size > h){
              //   drops[i] = 0;
              // }
              if (drops[i] * font_size > h && Math.random() > 0.98){
                drops[i] = 0;
              }
              drops[i]++;
          }
          context.restore();
      }
      //动画循环
      (function drawFrame(){
        window.requestAnimationFrame(drawFrame, canvas);
        context.fillStyle = clearColor;
        context.fillRect(0, 0, w, h);
        draw();

        // setTimeout(() => {
        //   drawFrame();
        //   context.fillStyle = clearColor;
        //   context.fillRect(0, 0, w, h);
        //   draw();
        // }, 150);
      }())
    },

    // 非零环绕 - 圆环
    draw8() {
      var canvas = document.getElementById("canvas");
        canvas.width = 300;
        canvas.height = 300;
        var context = canvas.getContext("2d");
        context.fillStyle = "#FFF";
        context.fillRect(0,0,800,600);

        context.shadowColor = "#545454";
        context.shadowOffsetX = 5;
        context.shadowOffsetY = 5;
        context.shadowBlur = 2;

        context.arc(150, 150, 100, 0, Math.PI * 2 ,false);
        context.arc(150, 150, 130, 0, Math.PI * 2 ,true);
        context.fillStyle = "#00AAAA";
        context.fill();
    },

    // 非零环绕 - 多边形
    draw9() {
      var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");
        context.fillStyle = "#FFF";
        context.fillRect(0,0,800,600);

        context.beginPath();
        context.rect(200,100,400,400);
        drawPathRect(context, 250, 150, 300, 150);
        drawPathTriangle(context, 345, 350, 420, 450, 270, 450);
        context.arc(500, 400, 50, 0, Math.PI * 2, true);
        context.closePath();

        context.fillStyle = "#058";
        context.shadowColor = "gray";
        context.shadowOffsetX = 10;
        context.shadowOffsetY = 10;
        context.shadowBlur = 10;
        context.fill();

        //逆时针绘制矩形
        function drawPathRect(cxt, x, y, w, h){
            /**
             * 这里不能使用beginPath和closePath，
             * 不然就不属于子路径而是另一个全新的路径，
             * 无法使用非零环绕原则
             */
            cxt.moveTo(x, y);
            cxt.lineTo(x, y + h);
            cxt.lineTo(x + w, y + h);
            cxt.lineTo(x + w, y);
            cxt.lineTo(x, y);
        }

        //逆时针绘制三角形
        function drawPathTriangle(cxt, x1, y1, x2, y2, x3, y3){
            cxt.moveTo(x1,y1);
            cxt.lineTo(x3,y3);
            cxt.lineTo(x2,y2);
            cxt.lineTo(x1,y1);
        }
    },

    // 子像素渲染
    draw10() {
      var canvas = document.getElementById("canvas");
      var ctx = canvas.getContext("2d");
      ctx.imageSmoothingEnabled = true;

      // var ratio = window.devicePixelRatio || 1;
      // canvas.width = canvas.width * ratio;
      // canvas.height = canvas.height * ratio;
      // ctx.scale(ratio, ratio);
      // ctx.translate(0.5, 0.5);
    
      ctx.lineWidth = 1;
      ctx.moveTo(100,100);
      ctx.lineTo(200,100);
      ctx.lineTo(200,200);
      ctx.lineTo(100,200);
      ctx.lineTo(100,100);
      ctx.lineTo(200,200);

      ctx.stroke();
    },

    // 涂抹
    draw11() {
      var canvas = document.getElementById("canvas");
      var ctx = canvas.getContext("2d");
      this.ctx = ctx;
      var img = document.getElementById("pic");
      ctx.drawImage(img, 10, 10);
    },
    onTouchStart(e) {
    },
    onTouchMove(e) {
      const moveX = e.touches[0].clientX;
      const moveY = e.touches[0].clientY;
      this.ctx.globalCompositeOperation = 'destination-out';
      this.ctx.beginPath();
      this.ctx.fillStyle = "any color it's ok";
      this.ctx.arc(moveX, moveY, 10, 0, 2 * Math.PI);
      this.ctx.fill();

      // 方法2
      // this.ctx.clearRect(moveX, moveY, 10, 10);
    },
    onTouchEnd(e) {
    },

    // 动画 - 虚线环绕
    draw12() {
      var canvas = document.getElementById('canvas');
      var context = canvas.getContext('2d');
      // 偏移大小
      var offset = 0;
      // 绘制
      var draw = function () {
        context.clearRect(0,0, canvas.width, canvas.height);
        context.setLineDash([8, 4]);
        context.lineDashOffset = offset;
        context.strokeRect(10, 10, 200, 100);
      };


      // context.strokeRect(10, 10, 200, 100);
      // context.setLineDash([10, 5]);
      // context.lineDashOffset = -15;
      // context.moveTo(10, 60);
      // context.lineTo(210, 60);
      // context.stroke();      

      var run = function () {
        offset += 1;
        if (offset > 48) {
          offset = 0;
        }
        draw();
        // 继续绘制
        requestAnimationFrame(run);
        // setTimeout(_ => {
        //   run();
        // }, 20);
      }

      run();
    },

    // 飞驰的小球
    draw13() {
      var canvas = document.getElementById('canvas');
      var ctx = canvas.getContext('2d');
      var raf;
      var running = false;

      var ball = {
        x: 100,
        y: 100,
        vx: 5,
        vy: 1,
        radius: 25,
        color: 'blue',
        draw: function() {
          ctx.beginPath();
          ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
          ctx.closePath();
          ctx.fillStyle = this.color;
          ctx.fill();
        }
      };

      function clear() {
        ctx.fillStyle = 'rgba(255,255,255,0.3)';
        ctx.fillRect(0,0,canvas.width,canvas.height);
      }

      function draw() {
        clear();
        ball.draw();
        ball.x += ball.vx;
        ball.y += ball.vy;

        if (ball.y + ball.vy > canvas.height || ball.y + ball.vy < 0) {
          ball.vy = -ball.vy;
        }
        if (ball.x + ball.vx > canvas.width || ball.x + ball.vx < 0) {
          ball.vx = -ball.vx;
        }

        raf = window.requestAnimationFrame(draw);
      }

      canvas.addEventListener('mousemove', function(e){
        if (!running) {
          clear();
          ball.x = e.clientX;
          ball.y = e.clientY;
          ball.draw();
        }
      });

      canvas.addEventListener('click',function(e){
        if (!running) {
          raf = window.requestAnimationFrame(draw);
          running = true;
        }
      });

      canvas.addEventListener('mouseout', function(e){
        window.cancelAnimationFrame(raf);
        running = false;
      });

      ball.draw();
    },

    // 蓝天白云
    draw14() {
      function drawSky(cxt){
        cxt.save();

        cxt.beginPath();
        cxt.moveTo(0, 420);
        cxt.bezierCurveTo(250, 300, 350, 550, 800, 400);
        cxt.lineTo(800,0);
        cxt.lineTo(0,0);
        cxt.closePath();

        var lineStyle = cxt.createRadialGradient(400, 0, 50, 400, 0, 200);
        lineStyle .addColorStop(0, "#42A9AA");
        lineStyle .addColorStop(1, "#2491AA");

        cxt.fillStyle = lineStyle;

        cxt.fill();

        cxt.restore();
      }

      function drawPrairie(cxt){
          cxt.save();

          cxt.beginPath();
          cxt.moveTo(0, 420);
          cxt.bezierCurveTo(250, 300, 350, 550, 800, 400);
          cxt.lineTo(800,600);
          cxt.lineTo(0,600);
          cxt.closePath();

          var lineStyle = cxt.createLinearGradient(0, 600, 600, 0);
          lineStyle .addColorStop(0, "#00AA58");
          lineStyle .addColorStop(0.3, "#63AA7B");
          lineStyle .addColorStop(1, "#04AA00");

          cxt.fillStyle = lineStyle;
          cxt.fill();

          cxt.restore();
      }

      /*渲染单个云朵
      context:  canvas.getContext("2d")对象
      cx: 云朵X轴位置
      cy: 云朵Y轴位置
      cw: 云朵宽度
      */
      function drawCloud(cxt, cx, cy, cw) {
        //云朵移动范围即画布宽度
        var maxWidth = 800;
        //如果超过边界从头开始绘制
        cx = cx % maxWidth;
        //云朵高度为宽度的60%
        var ch = cw * 0.6;
        //开始绘制云朵

        cxt.beginPath();
        cxt.fillStyle = "white";
        //创建渐变
        var grd = cxt.createLinearGradient(0, 0, 0, cy);
        grd.addColorStop(0, 'rgba(255,255,255,0.8)');
        grd.addColorStop(1, 'rgba(255,255,255,0.5)');
        cxt.fillStyle = grd;

        //在不同位置创建5个圆拼接成云朵现状
        cxt.arc(cx, cy, cw * 0.19, 0, 360, false);
        cxt.arc(cx + cw * 0.08, cy - ch * 0.3, cw * 0.11, 0, 360, false);
        cxt.arc(cx + cw * 0.3, cy - ch * 0.25, cw * 0.25, 0, 360, false);
        cxt.arc(cx + cw * 0.6, cy, cw * 0.21, 0, 360, false);
        cxt.arc(cx + cw * 0.3, cy - ch * 0.1, cw * 0.28, 0, 360, false);
        cxt.closePath();

        cxt.fill();
      }

      var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
      var context = canvas.getContext("2d");
      context.fillStyle = "#FFF";
      context.fillRect(0,0,800,600);

      drawPrairie(context);
      drawSky(context);
      for(var i=0; i <5; i++){
          var x0 = 500 * Math.random() + 50;
          var y0 = 200 * Math.random() + 50;
          var c0 = 100 * Math.random() + 50;
          drawCloud(context, x0, y0, c0);
      }
    },

    // 性能
    draw15() {
      // v - 赋值
      var canvas = document.getElementById("canvas");
      var context = canvas.getContext("2d");
      var img = document.getElementById("pic");

      var offcanvas = document.createElement('canvas');//document.getElementById('offcanvas');
      offcanvas.width = 100;
      offcanvas.height = 100;
      var offcontext = offcanvas.getContext('2d');
      
      var t1 = Date.now();
      var num = 10000;

      // 直接绘制
      for (let i = 0; i < num; i++) {
        context.drawImage(img, 0, 0);
      }

      var t2 = Date.now();
      console.log('t1=', t2 - t1);


      // 离屏绘制
      for (let i = 0; i < num; i++) {
        offcontext.drawImage(img, 0, 0);
      }
      context.drawImage(offcanvas, 0, 0);

      var t3 = Date.now();
      console.log('t2=', t3 - t2);
    }
  },

  mounted() {
    this.sun = new Image();
    this.moon = new Image();
    this.earth = new Image();
    const init = () => {
      this.sun.src = 'https://mdn.mozillademos.org/files/1456/Canvas_sun.png';
      this.moon.src = 'https://mdn.mozillademos.org/files/1443/Canvas_moon.png';
      this.earth.src = 'https://mdn.mozillademos.org/files/1429/Canvas_earth.png';
      this.draw5();
    }

    init();

    // this.draw7();
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
}

#app {
  width: 100%;
  height: 100%;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
canvas:hover {
  cursor: pointer;
}

.antialias {
  font-size: 50px;
  font-weight: bold;
  -webkit-font-smoothing: none;
  -webkit-font-smoothing: antialiased; /* 灰度平滑 */
  -webkit-font-smoothing: subpixel-antialiased; /* 次像素平滑 */
}
</style>
