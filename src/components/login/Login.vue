<template>
  <div class="html body" id="teste">
    <img src="../../static/imgs/download.jpg">
  </div>
</template>

<script>

var image;
var imageCanvas;
var imageCanvasContext;
var lineCanvas;
var lineCanvasContext;
var pointLifetime = 1000;
var points = [];
var teste;

export default {
  data () {
    return {
      image: image,
      imageCanvas: imageCanvas,
      imageCanvasContext: imageCanvasContext,
      lineCanvas: lineCanvas,
      lineCanvasContext: lineCanvasContext,
      pointLifetime: pointLifetime,
      points: points
    }
  },
  mounted(){
    image = document.querySelector('img');
    imageCanvas = document.createElement('canvas');
    imageCanvasContext = imageCanvas.getContext('2d');
    lineCanvas = document.createElement('canvas');
    lineCanvasContext = lineCanvas.getContext('2d');
    teste = document.getElementById('teste');
    console.log(lineCanvas);
    console.log(teste);
    
    // if (image.complete) {
    //   this.start();
    // } else {
    //   image.onload = start;
    // }
    this.start();
  },
  methods:{
    start() {
      document.addEventListener('mousemove', this.onMouseMove);
      window.addEventListener('resize', this.resizeCanvases);
      teste.appendChild(imageCanvas);
      this.resizeCanvases();
      this.tick();
    },

    onMouseMove(event) {
      points.push({
        time: Date.now(),
        x: event.clientX,
        y: event.clientY
      });
    },

    resizeCanvases() {
      imageCanvas.width = lineCanvas.width = window.innerWidth;
      imageCanvas.height = lineCanvas.height = window.innerHeight;
    },
    tick() {
      // Remove old points
      // console.log("chamou");
      points = points.filter((point) => {
        var age = Date.now() - point.time;
        return age < pointLifetime;
      });

      this.drawLineCanvas();
      this.drawImageCanvas();
      requestAnimationFrame(this.tick);
    },
    drawLineCanvas() {
      var minimumLineWidth = 25;
      var maximumLineWidth = 100;
      var lineWidthRange = maximumLineWidth - minimumLineWidth;
      var maximumSpeed = 50;

      lineCanvasContext.clearRect(0, 0, lineCanvas.width, lineCanvas.height);
      lineCanvasContext.lineCap = 'round';
      lineCanvasContext.shadowBlur = 30;
      lineCanvasContext.shadowColor = '#000';
      
      for (var i = 1; i < points.length; i++) {
        var point = points[i];
        var previousPoint = points[i - 1];

        // Change line width based on speed
        var distance = this.getDistanceBetween(point, previousPoint);
        var speed = Math.max(0, Math.min(maximumSpeed, distance));
        var percentageLineWidth = (maximumSpeed - speed) / maximumSpeed;
        lineCanvasContext.lineWidth = minimumLineWidth + percentageLineWidth * lineWidthRange;

        // Fade points as they age
        var age = Date.now() - point.time;
        var opacity = (pointLifetime - age) / pointLifetime;
        lineCanvasContext.strokeStyle = 'rgba(0, 0, 0, ' + opacity + ')';
        
        lineCanvasContext.beginPath();
        lineCanvasContext.moveTo(previousPoint.x, previousPoint.y);
        lineCanvasContext.lineTo(point.x, point.y);
        lineCanvasContext.stroke();
      }
    },

    getDistanceBetween(a, b) {
      return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
    },

    drawImageCanvas() {
      // Emulate background-size: cover
      var width = imageCanvas.width;
      var height = imageCanvas.width / image.naturalWidth * image.naturalHeight;
      
      if (height < imageCanvas.height) {
        width = imageCanvas.height / image.naturalHeight * image.naturalWidth;
        height = imageCanvas.height;
      }

      imageCanvasContext.clearRect(0, 0, imageCanvas.width, imageCanvas.height);
      imageCanvasContext.globalCompositeOperation = 'source-over';
      imageCanvasContext.drawImage(image, 0, 0, width, height);
      imageCanvasContext.globalCompositeOperation = 'destination-in';
      imageCanvasContext.drawImage(lineCanvas, 0, 0);
    }
  }
}
</script>

<style scoped>
  .html,
.body {
  font-size: 0;
  height: 100vh;
  margin: 0;
  padding: 0;
  width: 100%;
}

.body {
  background: url(../../static/imgs/downloadMask.jpg) 0 0 / cover;
}
img{
  display: none;
  height: 100vh;
  width: 100%;
}
</style>
