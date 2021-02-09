<template>
    <table class="table">
        <thead>
            <tr>
                <th scope="col" class="h3 hanzi-header">Hanzi</th>
                <th scope="col" class="h3">Meaning</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td class="display-1 align-middle">{{displayHanzi.hanzi}}</td>
                <td class="display-5 align-middle">{{displayMeaning}}</td>
            </tr>
            <tr>
                <td colspan="2"><PinyinChoices :choices="choices"
                                               :solution="correctPinyin"
                                               @wrong-choice="incrementRetries"
                                               @display-next-hanzi="displayNextHanzi"></PinyinChoices></td>
            </tr>
            <tr>
                <td colspan="2"><StatsDisplay :retries="currentRetries"
                                              :latest="previousDisplayInfo"></StatsDisplay></td>
            </tr>
        </tbody>
    </table>
</template>

<script setup>
import PinyinChoices from './PinyinChoices.vue'
import StatsDisplay from './StatsDisplay.vue'
import json from './data/simplified-hanzi-3000.json'
</script>

<script>
export default {
    data() {
        return {
            currentRetries: 0,
            previousDisplayInfo: null,
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
            this.previousDisplayInfo = {
                retries: this.currentRetries,
                hanzi: this.displayHanzi.hanzi,
                id: this.displayHanzi.id,
                pinyin: this.correctPinyin,
            }

            let newHanzi = this.selectHanzi()
            while (newHanzi.hanzi == this.displayHanzi.hanzi) {
                newHanzi = this.selectHanzi()
            }
            this.displayHanzi = this.selectHanzi()
            this.correctPinyin = this.displayHanzi.meanings[0].pinyin
            this.choices = this.buildPinyinChoices(this.correctPinyin)
            this.obfuscateMeaning()

            this.currentRetries = 0
        },
        obfuscateMeaning() {
            const bareMeaning = this.displayHanzi.meanings[0].meaning
            const hanzi = this.displayHanzi.hanzi
            const pinyin = this.correctPinyin
            if (bareMeaning.search(hanzi) > -1) {
                this.displayMeaning = bareMeaning.replaceAll(pinyin, '~')
            } else {
                this.displayMeaning = bareMeaning
            }
        },
        incrementRetries() {
            this.currentRetries += 1
        },
    },
    beforeMount() {
        this.hanziData = json.data
        this.uniquePinyins = Array.from(new Set(this.hanziData.map(hz => hz.meanings[0].pinyin)))
        this.displayHanzi = this.selectHanzi()
        this.correctPinyin = this.displayHanzi.meanings[0].pinyin
        this.choices = this.buildPinyinChoices(this.correctPinyin)
        this.obfuscateMeaning()
    },
}
</script>

<style scoped>
.hanzi-header {
    width: 33%;
}
</style>
