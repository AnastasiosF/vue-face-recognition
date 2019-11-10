<template>
    <div class="container">
        <video id="video" width="720" height="560" autoplay muted style="position: absolute"></video>
    </div>
</template>

<script>
    import * as faceapi from 'face-api.js';
    export default {
        name: 'VideoPresenter',
        props: {
            msg: String
        },
        data() {
            return {
                video: null

            }
        },
        methods: {
            startVideo() {
                navigator.getUserMedia(
                    {video: {}},
                    stream => this.video.srcObject = stream,
                    err => err.toString()
                )
            }
        },
        mounted() {
            setTimeout(()=>{
                this.video = document.getElementById('video')
                this.canvas = document.getElementById('canvas')
                Promise.all([
                    faceapi.nets.tinyFaceDetector.loadFromUri('/models'),
                    faceapi.nets.faceLandmark68Net.loadFromUri('/models'),
                    faceapi.nets.faceRecognitionNet.loadFromUri('/models'),
                    faceapi.nets.faceExpressionNet.loadFromUri('/models')
                ]).then(this.startVideo)
                    .catch(x=>{
                        x.toString()
                    })
                this.video.addEventListener('play', () => {
                    const canvas = faceapi.createCanvasFromMedia(this.video)
                    canvas.setAttribute("style","position:relative;")
                    this.$el.append(canvas)
                    const displaySize = {width: this.video.width,height: this.video.height}
                    faceapi.matchDimensions(canvas, displaySize)
                    setInterval(async () => {
                        const detections = await faceapi.detectAllFaces(this.video, new faceapi.TinyFaceDetectorOptions())
                            .withFaceLandmarks()
                            .withFaceExpressions()
                        const resizedDetections = faceapi.resizeResults(detections, displaySize)
                        canvas.getContext('2d').clearRect(0, 0,canvas.width,canvas.height)
                        faceapi.draw.drawDetections(canvas, resizedDetections)
                        faceapi.draw.drawFaceLandmarks(canvas, resizedDetections)
                        faceapi.draw.drawFaceExpressions(canvas, resizedDetections)
                    }, 100)
                })
            },200)

        },
        created() {
            /*Promise.all([
                faceapi.nets.tinyFaceDetector.loadFromUri('../../models'),
                faceapi.nets.faceLandmark68Net.loadFromUri('../../models'),
                faceapi.nets.faceRecognitionNet.loadFromUri('../../models'),
                faceapi.nets.faceExpressionNet.loadFromUri('../../models')
            ]).then(this.startVideo)
                .catch(x=>{
                    x.toString()
                })*/


        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .container{
        display:inline-block;
        width:720px;
        height:560px;
        margin: 0 auto;
        background: black;
        position:relative;
        border:5px solid black;
        border-radius: 10px;
        box-shadow: 0 5px 50px #333;
    }
    canvas{
        position: relative;
    }
</style>
