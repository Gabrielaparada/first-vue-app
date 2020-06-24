<template>
  <div>
    <b-jumbotron>

    <template v-slot:lead>
      {{ currentQuestion.question}}
    </template>

    <hr class="my-4">

    <b-list-group>
    <b-list-group-item 
      v-for="(answer, index) in answers" 
      :key='index'
      @click="selectAnswer(index)"
      :class="answerClass(index)"
      >
        {{ answer }}
    </b-list-group-item>
    
  </b-list-group>


    <b-button
        variant="primary" 
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      
      >Submit</b-button>
    <b-button @click="next" variant="success" >Next question</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'
// in order for you to use props within your component you will need to reference first
  export default {
    props: {
      currentQuestion: Object,
      next: Function,
      increment: Function
    },
    data(){
      //this acts like state
      return{
        selectedIndex: null,
        shuffledAnswers: [],
        answered: false,
        correctIndex: null,
      }
    },
    computed : {
      answers(){
        //if you need to access props on a another acomponent you will need t use the this keyword
        
        //creating a copy of the answers array
        //pushing a new array of all answers (correct and incorrent)
        let answers = [...this.currentQuestion.incorrect_answers]
        answers.push(this.currentQuestion.correct_answer)
        return answers
      }
    },
    //this will wacth for changes in your props
    watch: {
      currentQuestion : {
        //immediate will run the handler function once the props are passed and every time it updates after that! 
        immediate: true,
        handler () {
          this.selectedIndex = null
          this.answered = false
          this.shuffleAnswers()
        }
      }
    },
    methods : {
      selectAnswer(index){
        //this will select the clicked answer
        this.selectedIndex = index
      },
      submitAnswer() {
      let isCorrect = false
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.answered = true
      this.increment(isCorrect)
    },
      shuffleAnswers() {
        // create a copy of answers array
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      //shuffle the arrray and set it as a value of shuffledAnswers
      this.shuffledAnswers = _.shuffle(answers)
      //if the selected index from(shuffledarray) matches the index of the correct answer stored that into correct index variable.
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    answerClass(index){
      let answerClass = ''

      if(!this.answered && this.selectedIndex === index){
        answerClass = 'selected'
      } else if(this.answered && this.correctIndex === index){
        answerClass = 'correct'
      }else if(this.answered && this.selectedIndex === index &&  this.correctIndex !== index){
        answerClass = 'incorrect'
      }
      return answerClass
      }
    }
  }
  
</script>

<style scoped>
  .list-group{
    margin-bottom: 15px;
  }

  .btn-primary{
    margin: 0 10px;
  }

  .list-group-item:hover{
    border:1px solid black;
    cursor: pointer;
  }

  .selected{ 
    background-color: blue;
    color:black;
  }

  .correct{
    background-color: green;
  }

  .incorrect{
    background-color:red;
  }
</style>

