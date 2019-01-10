<template>
  <div id="app">    
    <h1>Can you guess the animal?</h1>
    <!-- use 'keydown' to prevent default submit behaviour -->
    <input v-model="animalInput" v-on:keydown.enter="enterAnimal" type="text" />
    <i v-on:click="enterAnimal" class="fas fa-paw"></i>
    <div id="correctness-div">
      <p v-show="correctness">{{ correctness }}</p>
    </div>
    <div id="list-div">
      <ul class="adjective-list" v-show="array1status">
        <li v-for="adjective in adjectiveArray1">{{ adjective }}</li>
      </ul>
      <ul class="adjective-list" v-show="array2status">
        <li v-for="adjective in adjectiveArray2">{{ adjective }}</li>
      </ul>
      <ul class="adjective-list" v-show="array3status">
        <li v-for="adjective in adjectiveArray3">{{ adjective }}</li>
      </ul>
  </div>
    <p v-show="showFirstLetter">It starts with: <span class="big-letter">{{ animal[0] }}</span></p>
    <p id="answer" v-show="showAnswer">{{ animal }}</p>
    <button v-on:click="buttonFunction">{{ buttonText }}</button>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      animalArray: [ "dog", "cat", "cow", "sheep", "rabbit", "donkey", "duck", "hen", "horse", "pig", "turkey", "chicken", "donkey", "goat", "llama", "squirrel", "snail", "mouse", "chameleon", "deer", "raccoon", "moose", "antelope", "beaver", "weasel", "hedgehog", "ferret", "koala", "wolf", "lynx", "groundhog", "anteater", "panda", "grizzly", "wombat", "panther", "mole", "snake", "bat", "tiger", "leopard", "parrot", "eagle", "cockatoo", "orangutan", "turtle", "octopus", "frog", "whale", "crab", "clam", "fish", "lobster", "shark", "seahorse", "hippopotamus", "squid", "shrimp", "swan", "dolphin", "starfish", "seagull", "alligator", "slug", "eel", "fox", "wilddog", "wildcat", "armadillo", "badger", "buffalo", "camel", "coyote", "lion", "elephant", "cheetah", "hyena", "gazelle", "ostrich", "weaver", "meerkat", "wallaby", "seal", "reindeer", "walrus", "orca", "wolverine", "penguin", "owl", "pigeon", "dove", "woodpecker", "crow", "sparrow", "hummingbird", "robin", "bee", "ant", "butterfly", "fly", "cockroach", "beetle", "cicada", "grasshopper", "moth", "wasp", "dragonfly", "cricket" ],
      adjectiveArray1: [],
      adjectiveArray2: [],
      adjectiveArray3: [],
      animal: '',
      animalInput: '',
      buttonText: 'Give me some adjectives!',
      buttonStatus: 'stage1',
      array1status: false,
      array2status: false,
      array3status: false,
      showFirstLetter: false,
      showAnswer: false,
      correctness: ''
    }
  },
  created () {
    
  },
  methods: {
    buttonFunction: function () {
      if (this.buttonStatus === 'stage0') {
        this.buttonText = 'New animal, please!';
        this.buttonStatus = 'stage1';
      } else if (this.buttonStatus === 'stage1') {
        this.randomAnimal();
        this.getAdjectives();
        this.animalInput = '';
        this.array1status = true;
        this.buttonStatus = 'stage2';
        this.buttonText = '5 more!';
        this.showAnswer = false;
        this.correctness = '';
      } else if (this.buttonStatus === 'stage2') {
        this.array2status = true;
        this.buttonStatus = 'stage3';
        this.buttonText = 'I need another 5!';
      } else if (this.buttonStatus === 'stage3') {
        this.array3status = true;
        this.buttonStatus = 'stage4';
        this.buttonText = 'Just one more hint!';
      } else if (this.buttonStatus === 'stage4') {
        this.showFirstLetter = true;
        this.buttonStatus = 'stage5';
        this.buttonText = 'I give up!';
      } else if (this.buttonStatus === 'stage5') {
        this.showAnswer = true;
        this.array1status = false;
        this.array2status = false;
        this.array3status = false;
        this.showFirstLetter = false;
        this.buttonStatus = 'stage0';
        this.buttonFunction();
      } 

    },
    randomAnimal: function () {
      let i = Math.floor(Math.random() * this.animalArray.length);
      // return this.nouns[i];
      this.animal = this.animalArray[i];
      // this.animal = this.nouns[i];
      console.log(this.animal)
    },
    getAdjectives: function () {
      this.adjectiveArray1 = [];
      this.adjectiveArray2 = [];
      this.adjectiveArray3 = [];
      this.$http.get('https://api.datamuse.com/words?rel_jjb=' + this.animal).then(function(data) {
      console.log(data);
      return data.json();
      
      }).then(function(data) {
        for (let i = 0; i < 5; i++) {
          this.adjectiveArray1.push(Object.values(data[i])[0]);
        };
        for (let i = 5; i < 10; i++) {
          this.adjectiveArray2.push(Object.values(data[i])[0]);
        };
        for (let i = 10; i < 15; i++) {
          this.adjectiveArray3.push(Object.values(data[i])[0]);
        };
      })
    },
    enterAnimal: function (event) {
      event.preventDefault();
      this.animalInput = this.animalInput.toLowerCase();

      if (this.animalInput === this.animal && this.animalInput !== '') {
        this.correctness = 'You\'re right!'
        this.array1status = false;
        this.array2status = false;
        this.array3status = false;
        this.showFirstLetter = false;
        this.buttonStatus = 'stage0';
        this.buttonFunction();
      } else if (this.animalInput === '') {
        this.correctness = '';
      } else {
        this.correctness = 'Sorry, that\'s not it.';
        this.animalInput = '';
      }
    },
  }
}
</script>

<style>
#app {
  font-family: 'Nunito';
  text-align: center;
  min-height: 700px;
  background: #e3ebf9;
}

input {
  width: 200px;
  border: none;
  border-bottom: 3px solid #000;
  font-family: 'Indie Flower';
  font-size: 30px;
  text-align: center;
  background: none;
}

.fas {
  font-size: 40px;
  cursor: pointer;
  color: #e2141b;
}

#correctness-div {
  height: 20px;
}

#list-div {
  margin-top: 20px;
}

.adjective-list {
  display: inline-block;
  list-style-type: none;
  text-align: left;
}

button {
  display: block;
  margin: 0 auto;
  margin-top: 50px;
  padding: 10px;
  border: none;
  border-radius: 15px;
  background-color: #375ba8;
  color: #fff;
  font-family: 'Nunito';
  font-size: 20px;
  cursor: pointer;
}

.big-letter {
  margin-left: 10px;
  font-size: 40px;
  color: #e2141b;
}

#answer {
  font-size: 30px;
}

</style>
