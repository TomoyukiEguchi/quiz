<template>
	<div class="question-box-container paper">
		<b-jumbotron>
			<template v-slot:lead>
				<!--
				<Menu
					:fetchApi="fetchApi"
				/>
				-->
				<div class="inset checktext-container">
					<b>{{ currentQuestion.question | replace }}</b>
				</div>
			</template>

			<hr class="my-4">

		<label
			v-for="(answer, index) in shuffledAnswers"
			:key="index"
			@click="selectAnswer(index)"
		>
			<input type="radio" name="radio"
				:class="[
					!answered && selectedIndex === index ? 'selected' :
					answered && correctIndex === index ? 'correct' :
					answered && selectedIndex === index && correctIndex !== index ? 'incorrect' : ''
				]"
			>
			<font style="vertical-align: inherit;">
				{{ answer | replace }}
			</font>
		</label>
		
			<br>
			<br>

			<div style="text-align:right">
				<!--
				<button
					@click="submitAnswer"
					:disabled="selectedIndex === null || answered"
					class = "blue-button"
				>
					Submit
				</button>
				-->
				<b-button 
					variant="primary"
					@click="submitAnswer"
					:disabled ="selectedIndex === null || answered"
					v-if ="countModalQuestion < 10"
				>
					Submit
				</b-button>

				<b-button
					variant ="success"
					@click ="next"
					:disabled ="answered==false"
					v-if ="countModalQuestion < 10"
				>
					Next
				</b-button>						
					<button variant="info" v-b-modal.modal-1 v-else>Result</button>
						
					<b-modal id="modal-1" title="Result" ok-only @ok="resetQuestions">
						<p class="my-4">Correct: {{ countCorrect }}</p>
						<p class="my-4">Question: {{ countModalQuestion }}</p>
					</b-modal>					
				
			</div>
		</b-jumbotron>
	</div>
</template>

<script>
import _ from 'lodash'

export default {
	/*
	name: 'App',
  components: {
    Menu
	},
	*/
	props: {
		currentQuestion: Object,
		next: Function,
		increment: Function,
		resetQuestion: Function
	},
	data() {
		return {
			selectedIndex: null,
			correctIndex: null,
			shuffledAnswers: [],
			answered: false,
			countModalQuestion: 0,
			countCorrect: 0,
		}
	},
	computed: {
		answers() {
			let answers = [...this.currentQuestion.incorrect_answers]
			answers.push(this.currentQuestion.correct_answer)
			return answers
		}
	},
	watch: {
		currentQuestion: {
			immediate: true,
			handler() {
				this.selectedIndex = null
				this.answered = false
				this.shuffleAnswers()
			}
		}
	},
	methods: {
		selectAnswer(index) {
			this.selectedIndex = index
		},
		submitAnswer() {
			let isCorrect = false

			if (this.selectedIndex === this.correctIndex) {
				isCorrect = true
				this.countCorrect++
			}
			this.answered = true
			this.countModalQuestion++
			this.increment(isCorrect)
		},
		resetQuestions() {
			this.countCorrect = 0
			this.countModalQuestion = 0
			this.resetQuestion()
		},
		shuffleAnswers() {
			let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
			this.shuffledAnswers = _.shuffle(answers)
			this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
		},
		modal() {
      if (this.countModalQuestion == 10) {
        return this.$bvModal.msgBoxOk("#modal-1",false);
			}
		}
	},
	filters: {
		replace: function decodeHtml(html) {
			let areaElement = document.createElement("textarea");
			areaElement.innerHTML = html;

			return areaElement.value;
		}
	}
}
</script>

<style scoped>
.paper {
	padding-bottom: 10px;
}
.jumbotron {
	margin-bottom: 0rem;
	padding-bottom: 1rem;
}
.paper > .jumbotron > label {
    width: 50%;
		text-align: left;
		padding-left: 1rem;
}
.checktext-container {
	padding: 40px 7px;
	font-size: 1.1em;
	color: rgb(34, 37, 53);
}
button {
	margin: 0 7px;
}
.selected {
  border-width: .1em;
	border-color: #115fe2;
	box-shadow: 0 0 0.5em rgba(54, 123, 240, 0.7), inset 0 -0.1em 0 rgba(0, 0, 0, 0.2), inset 0 0.1em 0.1em rgba(255, 255, 255, 0.2); 
	background-color: rgb(54, 123, 240)
}
.btn-primary {
	font-weight: bold;
	padding: 14px 0;
	width: 8em;
	font-size: 1.1rem;
	color: rgb(255, 255, 255);
	text-shadow: 0 0.1em 0.1em rgba(0, 0, 0, 0.7);
	background: linear-gradient(to top, #276add 30%, #115fe2 80%);
	box-shadow: 3px 4px 1em rgba(54, 123, 240, 0.7), 0 0 transparent, inset 0 -5px 0 rgba(0, 0, 0, 0.05), inset 0 2px 0 rgba(255, 255, 255, 0.2);
	border-radius: 6px 6px 8px 8px;
	transition-property: box-shadow, border-radius;
}
.btn-success {
	font-weight: bold;
	padding: 14px 0;
	width: 8em;
	font-size: 1.1rem;
	color: rgb(255, 255, 255);
	text-shadow: 0 0.1em 0.1em rgba(0, 0, 0, 0.7);
	background: linear-gradient(to top, #2cb15f 30%, #24af5a 80%);
	box-shadow: 3px 4px 1em rgba(25, 105, 61, 0.7), 0 0 transparent, inset 0 -5px 0 rgba(0, 0, 0, 0.05), inset 0 2px 0 rgba(255, 255, 255, 0.2);
	border-radius: 6px 6px 8px 8px;
	transition-property: box-shadow, border-radius;
}
.correct {
  border-width: .1em;
	border-color: #17af51;
	/*background-color: #1dbb9d;*/
	background: linear-gradient(to top, #1aa34f 30%, #1dbb9d 80%);
	box-shadow: 0 0 0.5em #17af51, 0 0 transparent, inset 0 -5px 0 rgba(0, 0, 0, 0.05), inset 0 2px 0 rgba(255, 255, 255, 0.2);
	transition-property: box-shadow, border-radius;
}
.incorrect {
  border-width: .1em;
	border-color: crimson;
	background: linear-gradient(to top, crimson 30%, #ee3d69 80%);
	box-shadow: 0 0 0.5em crimson, 0 0 transparent, inset 0 -5px 0 rgba(0, 0, 0, 0.05), inset 0 2px 0 rgba(255, 255, 255, 0.2);
	transition-property: box-shadow, border-radius;
}
button .close {
	margin: 0;
}
</style>