<template>
    <table class="table">
        <thead>
            <tr>
                <th scope="col" class="h3 hanzi-header">Hanzi</th>
                <th scope="col" class="h3 meaning-header">Meaning</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td class="display-1 align-middle">{{displayHanzi.hanzi}}</td>
                <td class="display-4 align-middle">{{displayMeaning}}</td>
            </tr>
            <tr>
                <td colspan="2"><PinyinChoices :choices="choices" :solution="correctPinyin" @display-next-hanzi="displayNextHanzi"></PinyinChoices></td>
            </tr>
        </tbody>
    </table>
</template>

<script setup>
import PinyinChoices from './PinyinChoices.vue'
import json from './data/simplified-hanzi-3000.json'
</script>

<script>
export default {
    data() {
        return {
            hanziData: null,
            uniquePinyins: null,
            displayHanzi: null,
            choices: null,
            selectedPinyin: null,
            correctPinyin: null,
            displayMeaning: null,
        }
    },
    methods: {
        getRandom(max) {
            return Math.floor(Math.random() * max)
        },
        selectHanzi() {
            const index = this.getRandom(this.hanziData.length)
            return this.hanziData[index]
        },
        buildPinyinChoices(answer) {
            let choices = []
            while (choices.length < 5) {
                const index = this.getRandom(this.uniquePinyins.length)
                const pinyin = this.uniquePinyins[index]
                if ((choices.indexOf(pinyin) == -1) && (pinyin != answer)) {
                    choices.push(pinyin)
                }
            }
            const index = this.getRandom(6)
            choices.splice(index, 0, answer)
            return choices
        },
        displayNextHanzi() {
            let newHanzi = this.selectHanzi()
            while (newHanzi.hanzi == this.displayHanzi.hanzi) {
                newHanzi = this.selectHanzi()
            }
            this.displayHanzi = this.selectHanzi()
            this.correctPinyin = this.displayHanzi.meanings[0].pinyin
            this.choices = this.buildPinyinChoices(this.correctPinyin)
            this.obfuscateMeaning()
        },
        obfuscateMeaning() {
            let bareMeaning = this.displayHanzi.meanings[0].meaning
            const hanzi = this.displayHanzi.hanzi
            const pinyin = this.correctPinyin
            this.displayMeaning = bareMeaning
            if (this.displayMeaning.search(hanzi)) {
                this.displayMeaning = this.displayMeaning.replace(pinyin, '~')
            }
        }
    },
    beforeMount() {
        this.hanziData = json.data
        this.uniquePinyins = Array.from(new Set(this.hanziData.map(hz => hz.meanings[0].pinyin)))
        this.displayHanzi = this.selectHanzi()
        this.correctPinyin = this.displayHanzi.meanings[0].pinyin
        this.choices = this.buildPinyinChoices(this.correctPinyin)
        this.obfuscateMeaning()
    }
}
</script>

<style scoped>
.hanzi-header {
    width: 33%;
}
</style>
