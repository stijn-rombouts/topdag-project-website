<template>
  <q-page class="flex flex-center bg-dark">
    <div class="column items-center">
      <h2 class="q-my-xs">Questions</h2>
      <h5 class="q-my-xs">{{ currentSet.title }}</h5>
      <div v-if="!showResult">
        <div v-for="(question, index) in questions" :key="index" class="q-my-md">
          <p>{{ question.text }}</p>
          <div>
            <q-btn
              v-for="(choice, choiceIndex) in question.choices"
              :key="choiceIndex"
              :label="choice.text"
              @click="selectAnswer(index, choiceIndex)"
              :color="selectedAnswers[index] === choiceIndex ? 'primary' : 'secondary'"
              class="q-mx-xs"
            />
          </div>
        </div>
      </div>
      <q-btn
        v-if="!showResult"
        label="Submit"
        @click="calculateResult"
        class="q-my-md"
        color="accent"
      />

      <q-dialog v-model="showResult">
        <q-card>
          <q-card-section class="q-pb-none">
            <h5 class="q-my-xs q-mb-md text-weight-bold">
              You answered {{ correctAnswers }} out of {{ questions.length }} questions correctly.
            </h5>
            <div v-for="(question, index) in questions" :key="index" class="q-my-md">
              <q-card flat bordered class="q-my-sm" style="background-color: #f5f5f5">
                <q-card-section class="q-pa-sm">
                  <p class="q-my-xs"><strong>Question:</strong> {{ question.text }}</p>
                  <div class="row q-mt-none">
                    <div class="col-6">
                      <strong>Your Answer:</strong>
                    </div>
                    <div class="col-6">
                      <strong>Correct Answer(s):</strong>
                    </div>
                    <div class="col-6">
                      {{ question.choices[selectedAnswers[index]]?.text || 'No answer selected' }}
                    </div>
                    <div class="col-6">
                      {{
                        question.choices
                          .filter((choice) => choice.isCorrect)
                          .map((choice) => choice.text)
                          .join(', ')
                      }}
                    </div>
                  </div>
                  <p
                    class="q-my-none"
                    :class="{
                      'text-positive': isAnswerCorrect(index),
                      'text-negative': !isAnswerCorrect(index),
                    }"
                  >
                    {{ isAnswerCorrect(index) ? 'Correct' : 'Incorrect' }}
                  </p>
                </q-card-section>
              </q-card>
            </div>
          </q-card-section>
          <q-card-actions align="right" class="q-pt-none">
            <q-btn flat label="Close" @click="closeDialog" />
          </q-card-actions>
        </q-card>
      </q-dialog>
    </div>
  </q-page>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const number = route.params.number

// Define multiple question sets with titles
const questionSets = [
  {
    title: 'Basic Math and Geography',
    questions: [
      {
        text: 'What is 2 + 2?',
        choices: [
          { text: '3', isCorrect: false },
          { text: '4', isCorrect: true },
          { text: '5', isCorrect: false },
        ],
      },
      {
        text: 'What is the capital of France?',
        choices: [
          { text: 'Berlin', isCorrect: false },
          { text: 'Madrid', isCorrect: false },
          { text: 'Paris', isCorrect: true },
        ],
      },
    ],
  },
  {
    title: 'Multiplication and Capitals',
    questions: [
      {
        text: 'What is 5 * 3?',
        choices: [
          { text: '15', isCorrect: true },
          { text: '10', isCorrect: false },
          { text: '20', isCorrect: false },
        ],
      },
      {
        text: 'What is the capital of Germany?',
        choices: [
          { text: 'Berlin', isCorrect: true },
          { text: 'Vienna', isCorrect: false },
          { text: 'Zurich', isCorrect: false },
        ],
      },
    ],
  },
]

// Load the appropriate question set based on the route parameter
const currentSet = computed(() => {
  const index = parseInt(number, 10) - 1
  return questionSets[index] || { title: 'Unknown Set', questions: [] }
})

const questions = computed(() => currentSet.value.questions)
const selectedAnswers = ref(Array(questions.value.length).fill(null))
const correctAnswers = ref(0)
const showResult = ref(false)

function selectAnswer(questionIndex, choiceIndex) {
  selectedAnswers.value[questionIndex] = choiceIndex
}

function calculateResult() {
  correctAnswers.value = questions.value.reduce((total, question, index) => {
    return total + (isAnswerCorrect(index) ? 1 : 0)
  }, 0)
  showResult.value = true
}

function isAnswerCorrect(questionIndex) {
  const selectedChoice = selectedAnswers.value[questionIndex]
  return questions.value[questionIndex].choices[selectedChoice]?.isCorrect || false
}

function closeDialog() {
  showResult.value = false
}
</script>
