<template>
<section id="main-container">
    <div id="box-container" class="shadow">
        <div class="card">
            <div class="card-header shadow-sm">Typing Speed Checker</div>
            <div class="card-body">
                <div class="container-fluid">
                    <div class="row align-items-center">
                        <div class="col-sm-8">
                            <div class="border p-2">
                                <span id="kewords" :class="{ correct: keyword.correct, wrong: keyword.wrong, pending:keyword.pending, active: keyword.active}" v-for="keyword in keywords" v-bind:key="keyword.text">
                                    {{keyword.text}}
                                </span>
                            </div>
                        </div>
                        <div class="col-sm-4 align-items-self">
                            <button class="btn btn-primary" v-on:click="resetKeywords()">Reset Keywords</button>
                        </div>
                    </div>
                    <br>
                    <div class="form-group">
                        <input type="text" :value="inputValue" @keyup.space="processInput($event)" class="form-control" placeholder="type above words" :disabled="start == false || finish == true">
                        <small class="text-success">Type space after every word typed</small>
                    </div>
                    <hr>
                    <p v-if="!start" class="text-center"><b>your time start in {{ countDown }} seconds</b></p>
                    <div class="text-center">
                        <button class="btn btn-success" :disabled="finish == false" v-on:click="getScore()">Check Score</button>
                    </div>
                    <br>
                    <div class="text-center" v-if="showScoreCard">
                        <h3><span class="text-success">{{ keywords.filter((keyword) => keyword.correct).length }}</span> / {{ keywordsArrayLength }}</h3>
                    </div>
                </div>
            </div>
        </div>
    </div>
</section>
</template>

<script>
var randomWords = require("random-words");

export default {
    name: "App",
    data() {
        return {
            keywordsArrayLength: 15,
            keywords: null,
            index: 0,
            inputValue: "",
            start: false,
            finish: false,
            countDown: 5,
            score: null,
            showScoreCard: false,
        };
    },
    methods: {
        generateWords: (arrLength) => {
            return randomWords(arrLength).map((keyword) => {
                return {
                    text: keyword,
                    active: false,
                    correct: false,
                    wrong: false,
                    pending: true,
                };
            });
        },
        countDownTimer() {
            if (this.countDown > 0) {
                setTimeout(() => {
                    this.countDown -= 1
                    this.countDownTimer()
                }, 1000)
            } else {
                this.start = true;
            }
        },
        processInput(e) {
            const value = e.target.value.trim();
            if (value === "") {
                return
            }
            if (this.keywords[this.index].text === value) {
                this.keywords[this.index].correct = true;
                this.keywords[this.index].wrong = false;
                this.keywords[this.index].active = false;
                this.keywords[this.index].pending = false;
            } else {
                this.keywords[this.index].correct = false;
                this.keywords[this.index].wrong = true;
                this.keywords[this.index].active = false;
                this.keywords[this.index].pending = false;
            }

            if (this.index == this.keywordsArrayLength - 1) {
                this.finish = true
                this.start = true
                return;
            }

            this.index++;
            this.keywords[this.index].active = true;
            this.inputValue = "";
        },
        getScore() {
            this.showScoreCard = true;
        },
        resetKeywords() {
            this.keywords = this.generateWords(this.keywordsArrayLength);
            this.index = 0;
            this.keywords[this.index].active = true;
            this.inputValue = "";
            this.start = false;
            this.finish = false;
            this.countDown = 5;
            this.score = null;
            this.showScoreCard = false;
            this.countDownTimer();
        }
    },
    mounted() {
        this.keywords = this.generateWords(this.keywordsArrayLength);
        this.keywords[this.index].active = true;
        this.countDownTimer();
    },
};
</script>

<style>
@import "https://stackpath.bootstrapcdn.com/bootstrap/4.1.2/css/bootstrap.min.css";

#main-container {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-image: url("./assets/bg.jpg");
}

#kewords {
    display: inline-block;
    margin: 2px;
    text-align: justify;
}

.pending.active {
    font-weight: bold;
    font-size: 18px;
    color: #000;
    text-decoration: underline;
}

.pending {
    font-weight: bold;
    font-size: 18px;
    color: #a7a4a4;
}

.correct {
    font-weight: bold;
    font-size: 18px;
    color: #00ff00;
}

.wrong {
    font-weight: bold;
    font-size: 18px;
    color: #ff0000;
}

#box-container {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: #fff;
}

@media (min-width: 992px) {
    #box-container {
        width: 40%;
    }
}

@media (max-width: 992px) {
    #box-container {
        width: 60%;
    }
}

@media (max-width: 768px) {
    #box-container {
        width: 80%;
    }
}

@media (max-width: 400px) {
    #box-container {
        width: 90%;
    }
}
</style>
