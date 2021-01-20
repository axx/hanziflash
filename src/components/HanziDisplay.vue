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
                <td class="display-4 align-middle">{{displayHanzi.meaning}}</td>
            </tr>
            <tr>
                <td colspan="2"><PinyinChoices :choices="choices" :solution="correctPinyin" @display-next-hanzi="displayNextHanzi"></PinyinChoices></td>
            </tr>
        </tbody>
    </table>
</template>

<script setup>
import PinyinChoices from './PinyinChoices.vue'
import json from './data/simplified-hanzi-1500.json'
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
            correctPinyin: null
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
            this.correctPinyin = this.displayHanzi.pinyin
            this.choices = this.buildPinyinChoices(this.correctPinyin)
        }
    },
    beforeMount() {
        this.hanziData = json.data
        this.uniquePinyins = Array.from(new Set(this.hanziData.map(hz => hz.pinyin)))
        this.displayHanzi = this.selectHanzi()
        this.correctPinyin = this.displayHanzi.pinyin
        this.choices = this.buildPinyinChoices(this.correctPinyin)
    }
}
</script>

<style scoped>
.hanzi-header {
    width: 33%;
}
</style>
