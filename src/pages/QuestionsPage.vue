<template>
  <q-page class="flex flex-center bg-dark">
    <div class="column items-center q-pa-md">
      <h2 class="q-my-xs">Vragen</h2>
      <h5 class="q-my-xs">{{ currentSet.title }}</h5>
      <div v-if="!showResult">
        <div v-for="(question, index) in questions" :key="index" class="q-my-md">
          <p class="q-mb-none">{{ question.text }}</p>
          <div class="row q-mt-none q-mb-sm" style="flex-wrap: wrap">
            <q-btn
              v-for="(choice, choiceIndex) in question.choices"
              :key="choiceIndex"
              :label="choice.text"
              @click="selectAnswer(index, choiceIndex)"
              :color="selectedAnswers[index] === choiceIndex ? 'primary' : 'secondary'"
              class="q-mx-xs q-my-xs"
            />
          </div>
        </div>
      </div>
      <q-btn
        v-if="!showResult"
        label="Indienen"
        @click="calculateResult"
        class="q-my-md"
        color="accent"
      />

      <q-dialog v-model="showResult">
        <q-card>
          <q-card-section class="q-pb-none">
            <h5 class="q-my-xs q-mb-md text-weight-bold">
              Je hebt {{ correctAnswers }} van de {{ questions.length }} vragen correct beantwoord.
            </h5>
            <div v-for="(question, index) in questions" :key="index" class="q-my-md">
              <q-card flat bordered class="q-my-sm" style="background-color: #f5f5f5">
                <q-card-section class="q-pa-sm">
                  <p class="q-my-xs"><strong>Vraag:</strong> {{ question.text }}</p>
                  <div class="row q-mt-none">
                    <div class="col-6">
                      <strong>Jouw Antwoord:</strong>
                    </div>
                    <div class="col-6">
                      <strong>Correct Antwoord(en):</strong>
                    </div>
                    <div class="col-6">
                      {{
                        question.choices[selectedAnswers[index]]?.text ||
                        'Geen antwoord geselecteerd'
                      }}
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
            <q-btn flat label="Sluiten" @click="closeDialog" />
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
    title: 'Verband tussen vuur en water',
    questions: [
      {
        text: 'Wat gebeurt er met water wanneer het door vuur wordt verwarmd?',
        choices: [
          { text: 'Het verandert in ijs', isCorrect: false },
          { text: 'Het verdampt en wordt gas', isCorrect: true },
          { text: 'Het wordt zuur', isCorrect: false },
          { text: 'Het blijft gewoon water', isCorrect: false },
        ],
      },
      {
        text: 'Bij welke temperatuur begint water te koken en te verdampen?',
        choices: [
          { text: '0°C', isCorrect: false },
          { text: '50°C', isCorrect: false },
          { text: '100°C', isCorrect: true },
          { text: '200°C', isCorrect: false },
        ],
      },
      {
        text: 'Wat is waterdamp volgens de tekst?',
        choices: [
          { text: 'Een doorzichtig vloeistof', isCorrect: false },
          { text: 'Een gas dat je goed kunt zien', isCorrect: false },
          { text: 'Kleine ijsdeeltjes', isCorrect: false },
          { text: 'Een onzichtbaar gas', isCorrect: true },
        ],
      },
      {
        text: 'Wat is het gevolg van warmte op watermoleculen?',
        choices: [
          { text: 'Ze bevriezen', isCorrect: false },
          { text: 'Ze stoppen met bewegen', isCorrect: false },
          { text: 'Ze bewegen sneller en komen los', isCorrect: true },
          { text: 'Ze veranderen in zuurstof', isCorrect: false },
        ],
      },
      {
        text: 'Wat voor soort proces is het veranderen van vloeibaar water in waterdamp?',
        choices: [
          { text: 'Een chemische reactie', isCorrect: false },
          { text: 'Een fysisch proces', isCorrect: true },
          { text: 'Een biologische verandering', isCorrect: false },
          { text: 'Een explosie', isCorrect: false },
        ],
      },
    ],
  },
  {
    title: 'Fotosynthese',
    questions: [
      {
        text: 'Wat hebben planten nodig om fotosynthese te doen?',
        choices: [
          { text: 'Alleen water', isCorrect: false },
          { text: 'Alleen zonlicht', isCorrect: false },
          { text: 'Zonlicht, water en koolstofdioxide', isCorrect: true },
          { text: 'Zonlicht en suiker', isCorrect: false },
        ],
      },
      {
        text: 'Wat maken planten tijdens fotosynthese?',
        choices: [
          { text: 'Alleen zuurstof', isCorrect: false },
          { text: 'Alleen suiker', isCorrect: false },
          { text: 'Suiker en zuurstof', isCorrect: true },
          { text: 'Water en koolstofdioxide', isCorrect: false },
        ],
      },
      {
        text: 'Waar vindt fotosynthese plaats in de plant?',
        choices: [
          { text: 'In de wortels', isCorrect: false },
          { text: 'In de bloemen', isCorrect: false },
          { text: 'In de stengel', isCorrect: false },
          { text: 'In de bladeren', isCorrect: true },
        ],
      },
      {
        text: 'Wat is een belangrijk gevolg van fotosynthese voor mensen en dieren?',
        choices: [
          { text: 'Ze kunnen groeien', isCorrect: false },
          { text: 'Er komt zuurstof in de lucht', isCorrect: true },
          { text: 'Ze krijgen regen', isCorrect: false },
          { text: 'Er is minder koolstofdioxide', isCorrect: false },
        ],
      },
      {
        text: 'Hoe noemen we het groene stofje in bladeren dat zonlicht opvangt?',
        choices: [
          { text: 'Zuurstof', isCorrect: false },
          { text: 'Water', isCorrect: false },
          { text: 'Bladgroenkorrels (chlorofyl)', isCorrect: true },
          { text: 'Glucose', isCorrect: false },
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
