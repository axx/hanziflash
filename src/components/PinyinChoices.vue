<template>
    <table class="table table-borderless">
        <tr>
            <th colspan="3" class="h3">Select the pinyin</th>
        </tr>
        <tr>
            <td><button id="choice0" class="btn btn-lg choice" @click="selectChoice(0)">{{ choices[0] }}</button></td>
            <td><button id="choice1" class="btn btn-lg choice" @click="selectChoice(1)">{{ choices[1] }}</button></td>
            <td><button id="choice2" class="btn btn-lg choice" @click="selectChoice(2)">{{ choices[2] }}</button></td>
        </tr>
        <tr>
            <td><button id="choice3" class="btn btn-lg choice" @click="selectChoice(3)">{{ choices[3] }}</button></td>
            <td><button id="choice4" class="btn btn-lg choice" @click="selectChoice(4)">{{ choices[4] }}</button></td>
            <td><button id="choice5" class="btn btn-lg choice" @click="selectChoice(5)">{{ choices[5] }}</button></td>
        </tr>
        <tr>
            <td colspan="3">
                <button id="checkBtn" :disabled="selectedPinyinId == null" type="button" class="btn btn-lg btn-primary" @click="checkSelectedPinyin">Check your answer</button>
                <button id="nextBtn" type="button" class="btn btn-lg btn-success" @click="displayNextHanzi">Correct! Next Hanzi?</button>
            </td>
        </tr>
    </table>
</template>

<style scoped>
button {
    width: 100%;
    border-radius: 10px;
}
button.choice {
    border-color: gray;
}
button.choice:hover {
    background-color: lightblue;
}
#nextBtn {
    display: none;
}
td {
    width: 33%;
}
</style>

<script>
export default {
    data() {
        return {
            selectedPinyinId: null,
            isChoiceCorrect: false,
        }
    },
    props: {
        choices: Array,
        solution: String
    },
    methods: {
        selectChoice(id) {
            if (!this.isChoiceCorrect)
            {
                if (this.selectedPinyinId != null) {
                    let previousSelectedBtn = document.getElementById('choice' + this.selectedPinyinId)
                    previousSelectedBtn.classList.remove('btn-primary', 'btn-danger', 'btn-success')
                }
                this.selectedPinyinId = id
                let selectedBtn = document.getElementById('choice' + id)
                selectedBtn.classList.add('btn-primary')
                let checkBtn = document.getElementById('checkBtn')
                checkBtn.textContent = 'Check your answer'
                checkBtn.classList.remove('btn-warning')
                checkBtn.classList.add('btn-primary')
            }
        },
        checkSelectedPinyin() {
            let selectedBtn = document.getElementById('choice' + this.selectedPinyinId)
            let checkBtn = document.getElementById('checkBtn')
            let nextBtn = document.getElementById('nextBtn')

            selectedBtn.classList.remove('btn-primary')
            
            if (this.choices[this.selectedPinyinId] == this.solution)
            {
                this.isChoiceCorrect = true
                selectedBtn.classList.add('btn-success')
                checkBtn.style.display = 'none'
                nextBtn.style.display = 'block'
            }
            else
            {
                selectedBtn.classList.add('btn-danger')
                checkBtn.textContent = 'Oops, try again'
                checkBtn.classList.remove('btn-primary')
                checkBtn.classList.add('btn-warning')
                this.$emit('wrong-choice')
            }
        },
        displayNextHanzi() {
            let choiceBtnList = document.querySelectorAll('.choice')
            choiceBtnList.forEach(btn => {
                btn.classList.remove('btn-danger', 'btn-success')
            })
            let checkBtn = document.getElementById('checkBtn')
            let nextBtn = document.getElementById('nextBtn')
            nextBtn.style.display = 'none'
            checkBtn.style.display = 'block'
            this.selectedPinyinId = null
            this.isChoiceCorrect = false
            this.$emit('display-next-hanzi')
        }
    }
}
</script>