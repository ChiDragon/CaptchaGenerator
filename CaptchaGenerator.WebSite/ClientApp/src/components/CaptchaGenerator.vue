<script setup>
    import { ref, reactive, onMounted } from 'vue';

    import DrawingSVG from '../components/DrawingSVG.vue'
    //import DrawingSVG from '../components/DrawingSVG.vue'

    const generate = (number = 10) => {
        let uniquechar = "";

        const randomchar =
            //"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
            "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";

        for (let i = 1; i <= number; i++) {
            uniquechar += randomchar.charAt(
                Math.random() * randomchar.length)
        }

        return uniquechar;
    }

    const Captchas = reactive([])
    onMounted(() => {
        Captchas.push({
            text: generate(8)
        });
    })

    const inputCaptchaText = ref('');
    const AddCaptcha = () => {
        let msg = inputCaptchaText.value;
        if (!msg) {
            msg = '請輸入'; 
        }
        Captchas.push({
            text: msg
        });
    }

    const inputCaptchaNumber = ref(8);
    const AddRandomCaptcha = () => {
        Captchas.push({
            text: generate(inputCaptchaNumber.value)
        });
    }

    const DelCaptcha = (idx) => {
        Captchas[idx].isDel = true;
    }

</script>

<template>
    <div class="captcha_generator_content">
        <h1>建立驗證碼</h1>
        <div class="panel_body">
            <div>
                <div style="margin-bottom:10px;">
                    <button @click="AddRandomCaptcha">加入隨機驗證碼</button>
                    <input type="number" @focus="$event.target.select()"
                           v-model="inputCaptchaNumber" style="margin-left:10px;" />
                </div>
                <div style="margin-bottom:10px;">
                    <button @click="AddCaptcha">加入指定內容驗證碼</button>
                    <input placeholder="指定內容"
                           v-model="inputCaptchaText" style="margin-left:10px;"/>
                </div>
            </div>
            <hr />
            <div v-for="(item,i) in Captchas">
                <div v-if="!item.isDel">
                    <div>
                        <span>編號：{{i}}</span>
                        <button @click="DelCaptcha(i)" style="margin-left:10px;">刪除</button>
                    </div>
                    <DrawingSVG :text="item.text" />
                    <hr />
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
    .captcha_generator_content {
        width: 100%;
        /*height: 600px;*/
        padding: 4px;
        border: 1px solid red;
    }

        .captcha_generator_content pre {
            border: 1px solid #222;
            padding: 4px;
        }
</style>
