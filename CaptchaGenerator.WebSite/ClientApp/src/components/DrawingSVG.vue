<script>
    class PathPoint {
        constructor(type, points = []) {
            this.Type = type;
            this.Points = points;
        }
    }

    function getRandomId(number = 10) {
        let rs = '';
        let alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz'.split('');
        for (var i = 0; i < number; i++) {
            rs += alphabet[getRandomInt(alphabet.length)];
        }
        return rs;
    }

    function getRandomInt(max) {
        return Math.floor(Math.random() * max);
    }

    function getNy(sy = 0, minY = 20, maxY = 80, dySize = 50, dyRange = 20) {
        let ny = sy;
        let dy = dySize - [...Array(dyRange).keys()][getRandomInt(dyRange)];
        let tof = [true, false][getRandomInt(2)];
        if ((sy < (minY + 10))) {
            ny += dy;
            if (ny > maxY) ny = maxY;
            //console.log('往下')
        } else if ((sy > (maxY - 10))) {
            ny -= dy;
            if (ny < minY) ny = minY;
            //console.log('往上')
        } else if (tof) {
            ny += dy;
            if (ny > maxY) ny = maxY;
            //console.log('往下')
        } else {
            ny -= dy;
            if (ny < minY) ny = minY;
            //console.log('往上')
        }
        return ny;
    }
</script>

<script setup>
    import { ref, reactive, onMounted, computed } from 'vue';
    import * as htmlToImage from 'html-to-image';
    import { toPng, toJpeg, toBlob, toPixelData, toSvg } from 'html-to-image';

    const myProps = defineProps({
        text: String
    });
    const CaptchaText = reactive(myProps.text)

    const myId = ref('');
    onMounted(() => {
        myId.value = getRandomId();

        generatorSvgPath([20, 40, 60, 80][getRandomInt(4)]);
        setPathLength();
        setFont();

        setSvgToImg();
    })

    const CaptchaImg = ref();
    const SVG_NS = "http://www.w3.org/2000/svg";
    const CaptchaSvg = ref();
    const showCaptchaSvg = ref(true);

    const DarkPath = ref();
    const DarkTextPath = ref();
    const generatorSvgPath = (startY = 30) => {
        let pathPoints = [];
        let xNodeCount = [3, 4][getRandomInt(2)];
        let define_types = ['L', 'Q'];
        //let define_types = ['C', 'Q'];
        //let define_types = ['L', 'C', 'Q'];

        let sx = 20;
        let sy = startY - [...Array(15).keys()][getRandomInt(15)];
        let nx = sx;
        let ny = sy;
        for (var i = 1; i <= xNodeCount; i++) {
            if (i == 1) {
                pathPoints.push(new PathPoint('M', [[sx, sy]]));
            } else {

                let dx = (250 - nx) * Math.random();

                let minDx = 30;
                if (dx < minDx) { dx = minDx; }
                let maxDx = (250 / (xNodeCount - 1));
                if (dx > maxDx) { dx = maxDx; }

                if (i == xNodeCount) {
                    dx = (250 - nx);
                }

                let rt = define_types[getRandomInt(2)];
                if (rt == 'L') {
                    nx += dx;
                    ny = getNy(ny);
                    pathPoints.push(new PathPoint(rt, [[nx, ny]]));
                } else if (rt == 'C') {
                    let arr = [];

                    let r = Math.random();
                    let ddx = dx * r;
                    let nnx = nx + ddx;
                    ny = getNy(ny);
                    arr.push([nnx, ny])

                    r = Math.random();
                    let dddx = (dx - ddx) * r;
                    let nnnx = nnx + dddx;
                    ny = getNy(ny);
                    arr.push([nnnx, ny])

                    nx += dx;
                    ny = getNy(ny);
                    arr.push([nx, ny])
                    pathPoints.push(new PathPoint(rt, arr));
                } else if (rt == 'Q') {
                    let arr = [];

                    let r = Math.random();
                    let ddx = dx * r;
                    let nnx = nx + ddx;
                    ny = getNy(ny);
                    arr.push([nnx, ny])

                    nx += dx;
                    ny = getNy(ny);
                    arr.push([nx, ny])
                    pathPoints.push(new PathPoint(rt, arr));
                }
            }
        }
        //console.log(`pathPoints:${pathPoints}`);
        //console.log(pathPoints);

        let d = '';
        pathPoints.forEach((e) => {
            d += ` ${e.Type}`;

            e.Points.forEach((e2) => {
                d += ` ${e2.toString()}`
            });
        });

        //console.log(`d:${d}`);
        let path = DarkPath.value;
        path.setAttribute('d', d);
    }

    const setPathLength = () => {
        let path = DarkPath.value;
        let len = path.getTotalLength() - 20;
        let textPath = DarkTextPath.value;
        textPath.setAttribute('textLength', len);
    };

    const myFont = ref();
    const myFont_20 = computed(() => { return `${myFont.value}`.replaceAll(" ", "%20"); });
    /*
    ** 字形來源
    ** https://fonts.google.com/?hl=zh-tw&preview.text=earhrss&preview.text_type=custom
    */
    const myFonts = ['Roboto', 'Borel', 'Oswald', 'Handjet',
        'Edu SA Beginner', 'Lumanosimo', 'Tektur', 'Dancing Script',
        'Maven Pro', 'EB Garamond', 'Teko', 'Lobster', 'Lugrasimo',
        'Pacifico', 'Rajdhani', 'Caveat', 'Abril Fatface', 'Bungee Spice', 'Shadows Into Light',
        'Indie Flower', 'Lilita One', 'Satisfy', 'Permanent Marker', 'Great Vibes', 'Yellowtail',
        'Kaushan Script', 'Gloria Hallelujah', 'Sacramento', 'Big Shoulders Inline Text', 'Press Start 2P', 'Kablammo',
        'Special Elite', 'Zeyada', 'Advent Pro', 'Tangerine', 'Bagel Fat One', 'Orbit',
        'Moirai One', 'Marck Script', 'Bangers', 'Bungee', 'Allura', 'Neucha',
        'Tsukimi Rounded', 'Playball', 'Monoton', 'Cherry Bomb One', 'Chokokutai', 'Homemade Apple',
        'Six Caps', 'Rock Salt', 'Alex Brush', 'Black Ops One', 'Leckerli One', 'Julee',
        'Covered By Your Grace', 'Rubik Moonrocks', 'Yesteryear', 'Rye', 'Henny Penny', 'Rubik Pixels'];
    const setFont = () => {
        myFont.value = myFonts[getRandomInt(myFonts.length)];
    };

    const downloadSVGAsText = () => {
        const svg = CaptchaSvg.value;
        const base64doc = btoa(unescape(encodeURIComponent(svg.outerHTML)));
        const a = document.createElement('a');
        const e = new MouseEvent('click');
        a.download = `${CaptchaText}.svg`;
        a.href = 'data:image/svg+xml;base64,' + base64doc;
        a.dispatchEvent(e);
    }

    const downloadSVGAsPNG = () => {
        const svg = CaptchaSvg.value;
        const canvas = document.createElement("canvas");
        const base64doc = btoa(unescape(encodeURIComponent(svg.outerHTML)));
        const w = parseInt(svg.getAttribute('width'));
        const h = parseInt(svg.getAttribute('height'));
        const img_to_download = document.createElement('img');
        img_to_download.src = 'data:image/svg+xml;base64,' + base64doc;
        console.log(w, h);
        img_to_download.onload = function () {
            console.log('img loaded');
            canvas.setAttribute('width', w);
            canvas.setAttribute('height', h);
            const context = canvas.getContext("2d");
            //context.clearRect(0, 0, w, h);
            context.drawImage(img_to_download, 0, 0, w, h);
            const dataURL = canvas.toDataURL('image/png');
            if (window.navigator.msSaveBlob) {
                window.navigator.msSaveBlob(canvas.msToBlob(), "download.png");
            } else {
                const a = document.createElement('a');
                const my_evt = new MouseEvent('click');
                a.download = `${CaptchaText}.png`;
                a.href = dataURL;
                a.dispatchEvent(my_evt);
            }
            //canvas.parentNode.removeChild(canvas);
        }
    }

    const downloadSVGAsPNG2 = () => {
        var node = CaptchaSvg.value;

        htmlToImage.toPng(node)
            .then(function (dataUrl) {
                showCaptchaSvg.value = true;

                const a = document.createElement('a');
                const my_evt = new MouseEvent('click');
                a.download = `${CaptchaText}.png`;
                a.href = dataUrl;
                a.dispatchEvent(my_evt);
            })
            .catch(function (error) {
                console.error('oops, something went wrong!', error);
            });
    };

    const setSvgToImg = () => {
        var svg = CaptchaSvg.value;
        var img = CaptchaImg.value;

        htmlToImage.toPng(svg)
            .then(function (dataUrl) {
                img.src = dataUrl;
                showCaptchaSvg.value = false;
            })
            .catch(function (error) {
                console.error('oops, something went wrong!', error);
            });
    };

    const downloadCaptchaPNG = (fileName) => {
        var img = CaptchaImg.value;
        var fn = fileName;
        if (!fn) {
            fn = CaptchaText;
        }

        const a = document.createElement('a');
        const my_evt = new MouseEvent('click');
        a.download = `${fn}.png`;
        a.href = img.src;
        a.dispatchEvent(my_evt);
    };

    const inputCaptchaText = ref('');
    const verifyRs = ref('');
    const verifySpan = ref();
    const verify = () => {
        console.log(inputCaptchaText);
        if (inputCaptchaText.value == CaptchaText) {
            verifyRs.value = '驗證成功';
            verifySpan.value.style.color = 'green';
        } else {
            verifyRs.value = '驗證失敗';
            verifySpan.value.style.color = 'red';
        }
    };

    const copyCaptchaText = () => {
        navigator.clipboard.writeText(CaptchaText)
            .then(() => {
                let msg = '複製成功，請使用Ctrl + V';
                verifyRs.value = msg;
                //alert(msg);
            })
    }
</script>

<template>
    <svg ref="CaptchaSvg" v-if="showCaptchaSvg"
         :xmlns="SVG_NS"
         x="0px" y="0px" width="600px" height="200px"
         viewBox="0 0 300 100">

        <component :is="'style'">
            @import url(https://fonts.googleapis.com/css?family={{myFont_20}});
        </component>

        <path ref="DarkPath" :id="myId" stroke="red" fill="none"
              d="M0,100 L100,0 L200,100 L300,0" />

        <text>
            <textPath ref="DarkTextPath"
                      :class="`CaptchaText_`+myId"
                      :href="`#`+myId"
                      href="#DarkPath"
                      startOffset="10">
                {{CaptchaText}}
            </textPath>
        </text>

        <component :is="'style'">
            .CaptchaText_{{myId}} {
            fill: blue;
            font-family: '{{myFont}}', serif;
            <!--font-size: 24px;-->
            }
        </component>
    </svg>
    <div>
        <img ref="CaptchaImg" />
    </div>
    <br />
    <div style="margin-bottom: 10px;">
        <span style="margin-left:10px;">輸入驗證碼</span>
        <input v-model="inputCaptchaText" @focus="$event.target.select()"
               v-on:change="verify" style="margin-left:10px;" />
        <span ref="verifySpan" style="margin-left: 10px;">{{verifyRs}}</span>
    </div>
    <!--<button @click="downloadSVGAsPNG2">下載圖片</button>-->
    <!--<button @click="downloadSVGAsPNG2">下載圖片(檔名不同步)</button>-->
    <button @click.prevent="downloadCaptchaPNG()" style="margin-left: 10px;">下載圖片</button>
    <button @click="downloadCaptchaPNG('驗證碼')" style="margin-left: 10px;">下載圖片(檔名不同步)</button>
    <button @click="copyCaptchaText" style="margin-left: 10px;">複製驗證碼</button>
    <br />
</template>
