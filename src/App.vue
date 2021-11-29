<template>
  <div id="app">
    <div class="header">
      <ul class="list">
        <a href="https://vk.com/alaboet" target="_blank"><li>Contact me</li></a>
        <a href="https://freetp.org/" target="_blank"><li>More games</li></a>
        <a href="https://www.google.com/" target="_blank"><li>Back to Google</li></a>
      </ul>
    </div>
    <div class="main">
      <app-button @action="rulesOpen=false?rulesOpen:!rulesOpen" text="Rules" id="rulesBtn"></app-button>
      <div v-if="rulesOpen" class="rules" >
      <p>Hello there! In this simple app will have your writing skills tested.</p>
      <p>The rules are very simple. All you have to do is type the word that appears on the screen and press ENTER</p>
      <p v-if="easyLvl">For each correct answer you will get additional 2 seconds.</p>
      <p v-else-if="hardLvl">For each correct answer you will get additional 1 second.</p>
      <p v-else>For each correct answer you will get additional (<strong>depends of your mode</strong>) seconds.</p>
      <p>Once you reach 10 points, there will be one less additional second.</p>
      <p>In case you are wondering, no, you will not have all the eternity to complete the task since there is a ticking timer.</p>
      <p>Click 'Start the game' whenever you are ready</p>
      </div>
      <div class="level">
        <div class="btns">
          <div v-if="!startedPlaying">
        <app-button @action="easyLvl=true, hardLvl=false" id="ezBtn" text="Easy Mode"></app-button>
        <app-button @action="hardLvl=true, easyLvl=false" id="hardBtn" text="Hard Mode"></app-button>
          </div>
        </div>
        <div v-if="easyLvl" style="color: lightgreen;">Easy mode chosen - you don't loose a point for an incorrect answer</div>
        <div v-else-if="hardLvl" style="color: red">Hard mode chosen - you loose a point for an incorrect answer</div>
        <div v-else-if="!hardLvl || !easyLvl" style="color: coral">Choose the difficulty first</div>
      </div>
      <div class="extra" v-if="!alreadyPlayed && !startedPlaying">
      <p v-if="groupChosen==''">You also need to choose which group of words you would like to have in your test.</p>
      <p v-if="groupChosen==''">There are Animals, Numbers and Countries</p>
        <p v-if="isAnimals">Animals chosen</p>
        <p v-if="isNumbers">Numbers chosen</p>
        <p v-if="isCountries">Countries chosen (type words with an uppercase if needed) </p>
        <div class="buttonsToChoose">
      <app-button @action="isAnimals=true, isNumbers=false, isCountries=false,groupChosen='animals'" text="Animals"></app-button>
      <app-button @action="isNumbers=true, isAnimals=false, isCountries=false,groupChosen='numbers'" text="Numbers"></app-button>
      <app-button @action="isCountries=true, isAnimals=false, isNumbers=false,groupChosen='countries'" text="Countries"></app-button>
        </div>
        <p v-if="error && !groupChosen" style="color: coral">Choose the group first</p>
      <app-button @action="showTheWord" class="Btn" text="Start the game"></app-button>
      </div>
      <app-button v-else-if="alreadyPlayed" @action="reloadPage" class="Btn" text="Play again"></app-button>
    </div>

    <div v-if="isPlaying && !alreadyPlayed"><div class="game">
      <div class="word">{{currentWord}}</div>
      <input class="input" type="text" v-model="enteredWord" @keyup.enter="checkTheWord">
    </div>
      <div class="footer">
        <p>Time left: {{time}} seconds</p>
        <p>Current score: {{score}}</p>
        <app-button @action="alreadyPlayed=true,gameStopped=true" text="Finish the game"></app-button>
      </div>
    </div>
    <p v-if="alreadyPlayed && !gameStopped">Time is up. Your final score is {{score}}<br>You made {{mistakeCounter}} mistake(s) along the way. Your % of right answers is {{Math.floor(100-(mistakeCounter/totalWords)*100)}}%.</p>
    <p v-if="gameStopped">Game was stopped. Your final score is {{score}}<br>You made {{mistakeCounter}} mistake(s) along the way</p>
    <div class="ending" v-if="alreadyPlayed && score>0">
    <div v-if="mistakeCounter==0 && alreadyPlayed">Good job! Looks like you didn't make a single error</div>
    <div v-else-if="3>mistakeCounter>0">A few mistakes doesn't make you anything less than you are. Good job anyways!</div>
    <div v-else>You made a reasonable number of mistakes, but I'm sure you were distracted by the amazement of this app! Better concentration next time.</div>
  </div>
    <div v-else-if=" alreadyPlayed && score==0 && mistakeCounter==0">No mistakes because you forgot to type something! Play again.</div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import appButton from "@/components/appButton";
export default {
  name: 'App',
  components: {
appButton
  },
  data(){
    return{
      easyLvl:false,
      hardLvl:false,
      isPlaying:false,
      alreadyPlayed:false,
      startedPlaying:false,
      isAnimals:false,
      isNumbers:false,
      isCountries:false,
      groupChosen:'',
      enteredWord:'',
      time:12,
      score:0,
      totalWords:0,
      gameStopped:false,
      error:false,
      mistakeCounter:0,
      rulesOpen:false,
      currentWord:'',
      animals:['cats','dogs', 'lions','fish','panthers', 'manatees','bears','sharks','pigeons','ravens','dolphins','parrots','ants','wolves','salmons','hamsters','mice','humans','dinosaurs','flies','leopards','eagles','hawks','woodpeckers'],
      numbers:['zero','one','two','three','four','five','six','seven','eight','nine','ten','eleven','twelve','thousand', 'hundred','million','billion','trillion'],
      countries:['Russia','Angola','Australia','Mexico','Belarus','Vietnam','Italy','Norway','France','Ukraine','Mongolia','Turkey','Ethiopia','Brazil','Latvia','Canada','USA']
    }
  },
  methods:{
    reloadPage()
    {
      window.location.reload();
    },
    showTheWord() {
      if(this.isAnimals||this.isNumbers||this.isCountries) {
        if (this.easyLvl || this.hardLvl) {
          if (this.alreadyPlayed) {
            this.time = 8;
            this.score = 0;
            setInterval(this.countdown, 0);
          }
          if (!this.alreadyPlayed) {
            setInterval(this.countdown, 1000);
          }
          this.startedPlaying = true;
          this.isPlaying = true;
          if (this.isAnimals) {
            const randomIndex1 = Math.floor(Math.random() * this.animals.length);//случайное слово
            this.currentWord = this.animals[randomIndex1];
          } else if (this.isNumbers) {
            const randomIndex2 = Math.floor(Math.random() * this.numbers.length);//случайное слово
            this.currentWord = this.numbers[randomIndex2];
          } else if (this.isCountries) {
            const randomIndex3 = Math.floor(Math.random() * this.countries.length);//случайное слово
            this.currentWord = this.countries[randomIndex3];
          }

        }
      }
      else
      {
        this.error = true
      }
    },
    countdown()
    {
      if(this.time>0)
      {
        this.time--;
      }
      else if(this.time<=0)
      {
        this.isPlaying = false;
        this.alreadyPlayed = true
      }
      else
      {
        alert('bruh, отсчета нет')
      }
    },
    checkTheWord()
    {
      this.totalWords++
      if(this.enteredWord === this.currentWord)
      {
        if(this.isAnimals)
        {
          const randomIndex = Math.floor(Math.random() * this.animals.length);//случайное слово
          this.currentWord = this.animals[randomIndex];
        }
        else if(this.isCountries)
        {
          const randomIndex = Math.floor(Math.random() * this.countries.length);//случайное слово
          this.currentWord = this.countries[randomIndex];
        }
        else if(this.isNumbers)
        {
          const randomIndex = Math.floor(Math.random() * this.numbers.length);//случайное слово
          this.currentWord = this.numbers[randomIndex];
        }
        this.score++;
        if(this.score<=10)
        {
          if(this.easyLvl===true)
          {this.time+=2;}
          if (this.hardLvl===true)
          {
            {this.time+=1;}
          }


        }
        else if(this.score>10 && this.score<=20)
        {
          if(this.easyLvl===true){
            this.time+=1;
          }
        }
        else
        {
          this.time+=0;
        }
      }

      else if(this.enteredWord !== this.currentWord)
      {
        if(this.hardLvl)
        {
          this.mistakeCounter++;
          this.score--;
          this.time--;
          if(this.isAnimals)
          {
          const randomIndex = Math.floor(Math.random() * this.animals.length);//случайное слово
          this.currentWord = this.animals[randomIndex];
          }
          else if(this.isCountries)
          {
            const randomIndex = Math.floor(Math.random() * this.countries.length);//случайное слово
            this.currentWord = this.countries[randomIndex];
          }
          else if(this.isNumbers)
          {
            const randomIndex = Math.floor(Math.random() * this.numbers.length);//случайное слово
            this.currentWord = this.numbers[randomIndex];
          }
        }
        else if(this.easyLvl)
        {
          this.mistakeCounter++;
          if(this.isAnimals)
          {
            const randomIndex = Math.floor(Math.random() * this.animals.length);//случайное слово
            this.currentWord = this.animals[randomIndex];
          }
          else if(this.isCountries)
          {
            const randomIndex = Math.floor(Math.random() * this.countries.length);//случайное слово
            this.currentWord = this.countries[randomIndex];
          }
          else if(this.isNumbers)
          {
            const randomIndex = Math.floor(Math.random() * this.numbers.length);//случайное слово
            this.currentWord = this.numbers[randomIndex];
          }
        }

      }
      else
      {
        alert('ошибка, слово не проверяется')
      }
      this.enteredWord =''
    }}
  }


</script>


<style>
  @import url('https://fonts.googleapis.com/css2?family=Sora&display=swap');
  body
  {
    background-image: url("photos/imgonline-com-ua-Blur-Ptl1A84bce1LoW.jpg");
    background-repeat: no-repeat;
    background-size: cover;
  }
#app {
  font-family: 'Sora', sans-serif;
  font-size: 36px;
  text-align: center;
  color: white;
}
a
{
  text-decoration: none;
}
.header
{
  width: 35%;
  margin: 1rem auto;
}
.list
{
  display: flex;
  justify-content: center;
  list-style-type:none;
  padding: 1rem 0rem;
  height: 100%;
  background-color: white;
  border: 1px solid black;
  box-shadow: 0 0 20px black;

}
  li
  {
    color:darkkhaki;
     margin: auto 1rem;
    cursor: pointer;
  }
  li:hover
  {
    color:mediumpurple;
  }
.level
{
  width:40%;
  margin:0 auto;

}
#ezBtn:hover
{
  color:lightgreen;
}
#hardBtn:hover
{
  color:red
}
.Btn:hover
{
  color:purple;
}
.Btn
{
  padding-top: 1rem;
}

.main
{
  width:50%;
  margin: 3rem auto;
  padding: 2rem 0rem;
}
.game
{
  width:10%;
  margin:0 auto;
}

button
{
  font-family: 'Sora', sans-serif;
  background-color: darkkhaki;
  border-radius: 3%;
  border: none;
  color: #fff;
  padding: .7rem 1.6rem;
  text-align: center;
  text-decoration: none;
  font-size: 24px;
  margin: .5rem 1rem;
  transition-duration:.7s;
  cursor: pointer;
  box-shadow: 0 3px 0px black;
}
.btns
{
  display: flex;
  justify-content: space-around;
}

.input
{
  width: 100%;
  padding: .7rem;
  background-color: white;
  color: black;
  border: none;
  font-size: 30px;
}
.buttonsToChoose
{
  display: flex;
  justify-content: center;
}
  .word
  {
width:100%;
font-size: 40px;
  }
  #rulesBtn
  {
    margin-bottom:3rem
  }
  .footer
  {
    width: 20%;
    margin:0 auto;
  }

  @media screen and (max-width : 1000px) {
    #app
    {
      padding-top:0rem;
    }
    .main,.game,.footer,.other,.level
    {
      width:70%;
      margin: 0rem auto;
    }
    .list
    {

      display: flex;
      justify-content: space-around;
    }
    .header{
      width:80%;

    }
  }
</style>
