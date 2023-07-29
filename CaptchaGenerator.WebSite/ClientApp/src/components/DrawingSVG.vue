<script setup>
    import { ref, reactive, onMounted, computed } from 'vue';

    const myProps = defineProps({
        text: String
    });
    const CaptchaText = reactive(myProps.text)     

    onMounted(() => {
        //const downloadSVG = document.querySelector('#downloadSVG');
        //downloadSVG.addEventListener('click', downloadSVGAsText);
        //const downloadPNG = document.querySelector('#downloadPNG');
        //downloadPNG.addEventListener('click', downloadSVGAsPNG);

        drawText();
    })

    const CaptchaSvg = ref();
    const obj = reactive({
        props: {
            x: 50,
            y: 80,
            "class": "CaptchaText",
        }
    });

    const SVG_NS = reactive("http://www.w3.org/2000/svg");
    const drawText = () => {
        let o = obj;
        let svgNode = CaptchaSvg.value;

        let textNode = document.createElementNS(SVG_NS, "text");
        for (var name in o.props) {
            if (o.props.hasOwnProperty(name)) {
                textNode.setAttributeNS(null, name, o.props[name]);
            }
        }
        textNode.textContent = CaptchaText
        svgNode.appendChild(textNode);
    }

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
                e.preventDefault();
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

</script>

<template>
    <svg ref="CaptchaSvg"
         :xmlns="SVG_NS"
         x="0px" y="0px" width="300px" height="100px"
         viewBox="0 0 300 100">

        <path id="MyPath"
              fill="none"
              stroke="red"
              d="M10,90 Q90,90 90,45 Q90,10 50,10 Q10,10 10,40 Q10,70 45,70 Q70,70 75,50" />

        <component :is="'style'">
            .small {
            font: italic 13px sans-serif;
            }

            .heavy {
            font: bold 30px sans-serif;
            }

            .Rrrrr {
            font: italic 40px serif;
            fill: red;
            }

            .CaptchaText {
            font: italic 40px serif;
            fill: blue;
            }
        </component>

        <text>
            <textPath href="#MyPath">Quick brown fox jumps over the lazy dog.</textPath>
        </text>

        <text x="20" y="35" class="small">My</text>
        <text x="40" y="35" class="heavy">cat</text>
        <text x="55" y="55" class="small">is</text>
        <text x="65" y="55" class="Rrrrr">Grumpy!</text>
    </svg>
    <br />
    <button @click="downloadSVGAsPNG">Download .png</button>
    <button @click.prevent="downloadSVGAsText">Download .svg</button>
</template>
