<template>
    <form @submit.prevent="translateIt" class="well">
        <textarea
            v-model="translateText"
            cols="30"
            rows="5"
            class="form-control"
            placeholder="Çevirmek istediğiniz kelime/cümle yazınız."
        ></textarea>
        <select v-model="translateTo" class="form-control">
            <option v-for="(value, key) in languages" :value="key" :key="key">{{
                value
            }}</option>
        </select>
        <br />
        <div class="text-left" v-if="translatedLang">
            <strong>Tespit Edilen Dil : </strong>{{ detectedLang }}
        </div>
        <br />
        <button type="submit" class="btn btn-primary btn-block">
            Çevir Gelsin!
        </button>
    </form>
</template>

<script>
import axios from "axios";
export default {
    data() {
        return {
            translateText: "",
            translateTo: "",
            languages: {},
            translatedLang: "",
            detectedLang : ""
        };
    },
    methods: {
        translateIt() {
            let url =
                "https://translate.yandex.net/api/v1.5/tr.json/translate?key=<YOUR_YANDEX_TRANSLATE_API_KEY>&text=" +
                this.translateText +
                "&lang=" +
                this.translateTo;
            axios
                .get(url)
                .then((response) => {
                    this.translatedLang = this.languages[this.translateTo];
                    this.$emit("translatedEvent", response.data.text[0]);

                    this.detectedLang = this.languages[response.data.lang.split("-")[0]];

                    let history = {
                        from: this.languages[response.data.lang.split("-")[0]],
                        to: this.translatedLang,
                        translateText: this.translateText,
                        translatedText: response.data.text[0],
                    };

                    this.$emit("historyEvent", history);
                    setTimeout(() => {
                        this.translateTo = ''
                        this.translateText = ''    
                    }, 2000);
                })
                .catch((e) => {
                    console.log(e);
                });
        },
    },
    created() {
        axios
            .get(
                "https://translate.yandex.net/api/v1.5/tr.json/getLangs?key=<YOUR_YANDEX_TRANSLATE_API_KEY>&ui=tr"
            )
            .then((response) => {
                console.log(response);
                this.languages = response.data.langs;
            })
            .catch((e) => {
                console.log(e);
            });
    },
};
</script>

<style></style>
