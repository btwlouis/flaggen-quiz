<template>
  <div class="screen">
    <div v-if="this.currentView === 'selection'">
      <div class="options">
        <button @click="this.fetchQuestionsEurope()">
          Europa
        </button>

        <button @click="this.fetchQuestionWorldwide()">
          Welt
        </button>
      </div>
    </div>

    <div v-if="this.currentView === 'options' && this.currentQuestion" class="options">
      <img :src="this.currentQuestion.image" id="country">

      <div class="divider"></div>

      <button v-for="(option, index) in this.options" :key="index" @click="checkAnswer(option)">
        {{ option.name }}
      </button>
    </div>

    <div v-if="this.currentView === 'correct'">
      <div class="options">
        <img :src="this.currentQuestion.image" id="country">

        <div class="question">{{ this.currentQuestion.question }}</div>

        <div id="text" class="success-background">
          <h2>RICHTIG</h2>
          {{ this.currentQuestion.correctAnswer }}
        </div>

        <button @click="this.nextQuestion()">
          Weiter
        </button>
      </div>
    </div>

    <div v-if="this.currentView === 'wrong'">
      <div class="options">
        <img :src="this.currentQuestion.image" id="country">

        <div class="question">{{ this.currentQuestion.question }}</div>

        <div id="text" class="failed-background">
          <h2>FALSCH</h2>

          {{ this.currentQuestion.correctAnswer }}
        </div>

        <div class="divider"></div>

        <button @click="this.nextQuestion()">
          Weiter
        </button>
      </div>
    </div>

    <div v-if="this.currentView !== 'selection'">
      <div class="divider"></div>

      <div class="stats">
        <div>Stats:</div>
        <div>{{ correctAnswers }} Richtig | {{ wrongAnswers }} Falsch</div>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'HomeScreen',
  data() {
    return {
      questions: [],
      options: [
        { name: 'Country A' },
        { name: 'Country B' },
        { name: 'Country C' },
        { name: 'Country D' },
      ],

      currentView: 'selection',

      currentQuestionIndex: 0,
      correctAnswers: 0,
      wrongAnswers: 0,

      countries: [
        { name: 'Albanien', code: 'al' },
        { name: 'Andorra', code: 'ad' },
        { name: 'Belgien', code: 'be' },
        { name: 'Bosnien und Herzegowina', code: 'ba' },
        { name: 'Bulgarien', code: 'bg' },
        { name: 'Dänemark', code: 'dk' },
        { name: 'Deutschland', code: 'de' },
        { name: 'Estland', code: 'ee' },
        { name: 'Finnland', code: 'fi' },
        { name: 'Frankreich', code: 'fr' },
        { name: 'Griechenland', code: 'gr' },
        { name: 'Irland', code: 'ie' },
        { name: 'Island', code: 'is' },
        { name: 'Italien', code: 'it' },
        { name: 'Kosovo', code: 'xk' },
        { name: 'Kroatien', code: 'hr' },
        { name: 'Lettland', code: 'lv' },
        { name: 'Liechtenstein', code: 'li' },
        { name: 'Litauen', code: 'lt' },
        { name: 'Luxemburg', code: 'lu' },
        { name: 'Malta', code: 'mt' },
        { name: 'Mazedonien', code: 'mk' },
        { name: 'Moldawien', code: 'md' },
        { name: 'Monaco', code: 'mc' },
        { name: 'Montenegro', code: 'me' },
        { name: 'Niederlande', code: 'nl' },
        { name: 'Norwegen', code: 'no' },
        { name: 'Österreich', code: 'at' },
        { name: 'Polen', code: 'pl' },
        { name: 'Portugal', code: 'pt' },
        { name: 'Rumänien', code: 'ro' },
        { name: 'Russland', code: 'ru' },
        { name: 'San Marino', code: 'sm' },
        { name: 'Schweden', code: 'se' },
        { name: 'Schweiz', code: 'ch' },
        { name: 'Serbien', code: 'rs' },
        { name: 'Slowakei', code: 'sk' },
        { name: 'Slowenien', code: 'si' },
        { name: 'Spanien', code: 'es' },
        { name: 'Tschechien', code: 'cz' },
        { name: 'Türkei', code: 'tr' },
        { name: 'Ukraine', code: 'ua' },
        { name: 'Ungarn', code: 'hu' },
        { name: 'Vatikanstadt', code: 'va' },
        { name: 'Vereinigtes Königreich', code: 'gb' },
        { name: 'Weißrussland', code: 'by' },
        { name: 'Zypern', code: 'cy' }
      ]
    }
  },
  computed: {
    currentQuestion() {
      return this.questions[this.currentQuestionIndex];
    }
  },
  created() {
  },
  methods: {
    fetchQuestionWorldwide() {
      // Anfrage an die API von flagcdn.com senden, um alle Flaggen abzurufen
      fetch('https://flagcdn.com/de/codes.json')
        .then(response => response.json())
        .then(data => {
          let index = 0;

          for (const code in data) {
            const name = data[code];
            const image = `https://flagcdn.com/${code}.svg`;

            this.questions[index] = {
              image: image,
              correctAnswer: name
            }

            index++;
          }

          this.nextQuestion();

          console.log(`Finished fetching ${index} questions`)
        })
        .catch(error => {
          console.error('Fehler beim Abrufen der Flaggen:', error);
        });
    },
    fetchQuestionsEurope() {
      // Anfrage an die API von flagcdn.com senden, um alle Flaggen abzurufen
      let index = 0;

      this.countries.forEach(item => {
        const name = item.name;
        const code = item.code;

        console.log(name, code)

        const image = `https://flagcdn.com/${code}.svg`;

        this.questions[index] = {
          image: image,
          correctAnswer: name
        }

        index++;
      })

      // randomize questions
      this.questions = this.questions.sort(() => Math.random() - 0.5);
      this.nextQuestion();

      console.log(`Finished fetching ${index} questions`)
    },
    checkAnswer(option) {
      if (this.currentQuestion.correctAnswer === option.name) {
        this.correctAnswers++;

        this.currentView = 'correct';
      } else {
        this.wrongAnswers++;

        this.currentView = 'wrong';
      }

    },
    nextQuestion() {
      this.questions = this.questions.sort(() => Math.random() - 0.5);

      this.currentQuestionIndex++;
      this.currentView = 'options';

      if (this.currentQuestionIndex >= this.questions.length) {
        this.currentQuestionIndex = 0;
      }

      this.setOptions();
      this.shuffleOptions();
    },
    setOptions() {
      const correctAnswer = this.currentQuestion.correctAnswer;
      const options = [correctAnswer];

      while (options.length < 4) {
        const randomQuestion = this.questions[Math.floor(Math.random() * this.questions.length)];
        const randomAnswer = randomQuestion.correctAnswer;

        if (!options.includes(randomAnswer)) {
          options.push(randomAnswer);
        }
      }

      this.options = options.map(name => ({ name }));
    },
    shuffleOptions() {
      this.options = this.options.sort(() => Math.random() - 0.5);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

* {
  font-family: 'Roboto', sans-serif;
  font-size: 400;
}

.screen {
  height: 100vh;
  background-color: #263238;

  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}

.options {
  display: flex;
  justify-content: center;
  flex-direction: column;
}

.success-background {
  background-color: #4caf50;
}

.failed-background {
  background-color: #f44336;
}

.question {
  margin-top: 16px;
  font-size: 20px;
  font-weight: 500;
}

#country {
  width: 50vh;
}

button {
  border-radius: 4px;
  margin-top: 16px;
  padding: 8px 16px;
  font-size: 16px;
  background-color: #2196f3;
  color: white;
  width: 50vh;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #1976d2;
}

#text h2 {
  font-size: 32px;
}

#text {
  border-radius: 4px;
  margin: 0;
  font-size: 16px;
  font-weight: 700;
  color: #fff;
  text-align: center;
  width: 50vh;
  padding-bottom: 1rem;
}

.divider {
  height: 2px;
  background-color: #bdbdbd;
  margin: 16px auto;
  width: 50vh;
}

.stats {
  text-align: center;
  margin-bottom: 16px;
  font-size: 16px;
  color: #757575;
}
</style>

