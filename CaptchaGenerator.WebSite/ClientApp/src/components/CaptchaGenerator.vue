<script setup>
    import { ref, onMounted } from 'vue';

    import DrawingSVG from '../components/DrawingSVG.vue'
    //import DrawingSVG from '../components/DrawingSVG.vue'

    const msg = ref('TestMsg')
    onMounted(() => {
        //generate();
    })

    let captcha;
    const generate = () => {

        // Clear old input
        document.getElementById("submit").value = "";

        // Access the element to store
        // the generated captcha
        captcha = document.getElementById("image");
        let uniquechar = "";

        const randomchar =
            "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

        // Generate captcha for length of
        // 5 with random character
        for (let i = 1; i <= 5; i++) {
            uniquechar += randomchar.charAt(
                Math.random() * randomchar.length)
        }

        // Store generated input
        captcha.innerHTML = uniquechar;
    }

</script>

<template>
    <div class="captcha_generator_content">
        <h1>建立驗證碼圖</h1>
        <div class="panel_body">
            <div>
                <span>Test：</span>
                <pre>{{ msg }}</pre>
            </div>
            <hr />
            <DrawingSVG :text="'Dark'" />
            <!--<hr />
            <DrawingSVG :text="'Test'" />
            <hr />
            <DrawingSVG :text="'YA!!'" />-->
        </div>
    </div>
</template>

<style scoped>
    .captcha_generator_content {
        width: 100%;
        height: 600px;
        padding: 4px;
        border: 1px solid red;
    }

        .captcha_generator_content pre {
            border: 1px solid #222;
            padding: 4px;
        }

</style>
