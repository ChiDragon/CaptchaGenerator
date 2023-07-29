<script>
    function downloadSVGAsPNG(e) {
        const canvas = document.createElement("canvas");
        const svg = document.querySelector('svg');
        const base64doc = btoa(unescape(encodeURIComponent(svg.outerHTML)));
        const w = parseInt(svg.getAttribute('width'));
        const h = parseInt(svg.getAttribute('height'));

        //const img_to_download = document.createElement('img');
        const img_to_download = document.querySelector("#myImg");
        console.log(w, h);
        img_to_download.onload = function () {
            debugger;

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
                a.download = 'download.png';
                a.href = dataURL;
                a.dispatchEvent(my_evt);
            }
            //canvas.parentNode.removeChild(canvas);
        }
        img_to_download.src = 'data:image/svg+xml;base64,' + base64doc;
    }

</script>

<script setup>
    import { ref, reactive, onMounted, computed } from 'vue';

    const props = defineProps({
        text: String
    });

    const CaptchaSvg = ref();
    const obj = reactive({
        props: {
            x: 50,
            y: 15,
            "dominant-baseline": "middle",
            "text-anchor": "middle"
        }
    });

    const SVG_NS = "http://www.w3.org/2000/svg";
    const drawText = () => {
        let o = obj;
        let svgNode = CaptchaSvg.value;

        let textNode = document.createElementNS(SVG_NS, "text");
        for (var name in o.props) {
            if (o.props.hasOwnProperty(name)) {
                textNode.setAttributeNS(null, name, o.props[name]);
            }
        }
        textNode.textContent = props.text
        svgNode.appendChild(textNode);
    }

    onMounted(() => {
        drawText();

        const downloadPNG = document.querySelector('#downloadPNG');
        downloadPNG.addEventListener('click', downloadSVGAsPNG);
    })

</script>

<template>
    <button id="downloadPNG">Download Image</button>
    <hr />
    <svg ref="CaptchaSvg" width="200" height="100"></svg>
    <!--<svg ref="CaptchaSvg" viewBox="0 0 100 30"></svg>-->
    <hr />
    <img id="myImg" />
</template>
