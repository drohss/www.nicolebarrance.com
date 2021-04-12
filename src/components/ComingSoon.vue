<template>
    <div class="coming-soon">
        <vue-p5
                @preload="preload"
                @setup="setup"
                @draw="draw"
        ></vue-p5>
    </div>
</template>

<script>
    import VueP5 from 'vue-p5';
    import p5 from 'p5';
    import logo from '../assets/logo_image.svg'


    export default {
        name: 'ComingSoon',
        components: {
            'vue-p5': VueP5
        },
        data() {
            return {
                position: null,
                velocity: null,
                image: null,
                test: null,
                colors: [
                    '#9b5de5',
                    '#f15bb5',
                    '#fee440',
                    '#00bbf9',
                    '#00f5d4'
                ],
                collisionIndex: 0
            }
        },
        computed: {
            logoDimensions() {
                return this.isMobile()
                    ? {width: this.image.width/3, height: this.image.height/3}
                    : {width: this.image.width, height: this.image.height}
            }
        },
        methods: {
            isMobile() {
                try {document.createEvent("TouchEvent");return true;}
                catch(e){return false;}
            },
            preload(sketch) {
                this.position = sketch.createVector(0,0);
                this.velocity = new p5.Vector.fromAngle(45);
                this.velocity.mult(5);
                this.image = sketch.loadImage(logo);
                sketch.frameRate(50);
            },
            setup(sketch) {
                sketch.createCanvas(window.innerWidth, window.innerHeight);
                sketch.tint(this.colors[this.collisionIndex])
            },
            draw(sketch) {
                sketch.background('#111');

                // Do something when image hits boundary
                let hasCollision = this.checkBoundaryCollision(sketch)
                if (hasCollision) {
                    this.collisionIndex >= this.colors.length-1 ? this.collisionIndex = 0  : this.collisionIndex++
                    sketch.tint(this.colors[this.collisionIndex])
                }

                this.position.add(this.velocity.x, this.velocity.y, this.velocity.z)

                sketch.image(this.image, this.position.x, this.position.y, this.logoDimensions.width, this.logoDimensions.height)

            },
            checkBoundaryCollision(sketch) {
                let hasCollision = false;

                // left or right collision
                if (this.position.x < 0 || this.position.x + this.logoDimensions.width > sketch.width) {
                    this.velocity.x *= -1;
                    hasCollision = true;
                }

                // top or bottom collision
                if (this.position.y < 0 || this.position.y + this.logoDimensions.height > sketch.height)  {
                    this.velocity.y *= -1;
                    hasCollision = true;
                }

                return hasCollision
            }
        },
        mounted() {

        }
    }
</script>

<style lang="scss">
    .coming-soon {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: #111;
    }
</style>
