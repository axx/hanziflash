<template>
    <p>Current retries: {{retries}}</p>
    <p v-if="averageRetries !== null">Average retries for the last 10 hanzi: {{averageRetries}}</p>
</template>

<script>
export default {
    props: {
        retries: Number,
        latest: Object,
    },
    data() {
        return {
            averageRetries: null,
            history: []
        }
    },
    methods: {
        getAverageRetries() {
            if (this.history.length > 0) {
                let sum = 0
                this.history.forEach(h => { sum += h.retries })
                return Number((sum / this.history.length).toFixed(3))
            }
            return null
        }
    },
    watch: {
        latest: function(newValue, oldValue) {
            if (newValue) {
                this.history.push(newValue)
                if (this.history.length > 10) {
                    this.history.shift()
                }
                this.averageRetries = this.getAverageRetries()
                this.history[this.history.length-1].averageRetries = this.averageRetries
                localStorage.setItem('hanzi-history', JSON.stringify(this.history))
            }
        }
    },
    mounted() {
        if (localStorage.getItem('hanzi-history')) {
            this.history = JSON.parse(localStorage.getItem('hanzi-history'))
            this.averageRetries = this.getAverageRetries()
        }
    }
}
</script>