<script>
  import {onMount , onDestroy} from 'svelte';

  let questions = [
    {
    numb: 1,
    question: "What does HTML stand for?",
    answer: "Hyper Text Markup Language",
    options: [
      "Hyper Text Preprocessor",
      "Hyper Text Markup Language",
      "Hyper Text Multiple Language",
      "Hyper Tool Multi Language"
    ]
  },
    {
    numb: 2,
    question: "What does CSS stand for?",
    answer: "Cascading Style Sheet",
    options: [
      "Common Style Sheet",
      "Colorful Style Sheet",
      "Computer Style Sheet",
      "Cascading Style Sheet"
    ]
  },
    {
    numb: 3,
    question: "Which of the following is NOT a commonly used front-end framework/library?",
    answer: "Django",
    options: [
      "React",
      "Svelte",
      "Vue",
      "Django"
    ]
  },
    {
    numb: 4,
    question: "What does SQL stand for?",
    answer: "Structured Query Language",
    options: [
      "Stylish Question Language",
      "Stylesheet Query Language",
      "Statement Question Language",
      "Structured Query Language"
    ]
  },
    {
    numb: 5,
    question: "Which tag is used to define the main content of a webpage in HTML5?",
    answer: "main",
    options: [
      "body",
      "head",
      "div",
      "main"
    ]
  },
];

let info_box ="info_box"
let quiz_box ="quiz_box"
let next_btn ="next_btn"
let opttion = "option"
let timeleft;
let result_box = "result_box"
let score_text = ""

const start_quiz = () => {
  info_box = "info_box activeInfo"
}

const Exit_quiz = () => {
  info_box = "info_box"
}

const Countinue_quiz = () => {
  info_box = "info_box"
  quiz_box ="quiz_box activeQuiz"
  showQuestions(0)
  queCounter(1)
  StartTimer(14)
  startTimerLine(0)
}
const quit_quiz = () => {
  window.location.reload();
}

const Showresultbox = () => {
  quiz_box ="quiz_box"
  result_box ="result_box activeResult"
  if (UserScore > 3) {
      score_text = `and congrats! 🎉, You got ${UserScore} out of ${questions.length}`;
    } else if (UserScore > 1) {
      score_text = `and nice 😎, You got ${UserScore} out of ${questions.length}`;
    } else {
      score_text = `and sorry 😐, You got only ${UserScore} out of ${questions.length}`;
    }
}

let que_tag = '';
let options = [];
let index = 0;
let que_count = 0;
let que_numb = 1;
let counter;
let counterLine;
let timeLineWidth = "0px";
let UserScore = 0;

function showQuestions(index) {
    que_tag = `${questions[index].numb}. ${questions[index].question}`;
    options = questions[index].options.map((option) => ({
      text: option,
      correct: false,
      selected: false,
    }));
  }

onMount(() => {
    showQuestions(index);
});

let userAns
function optionSelected(answer) {
    clearInterval(counterLine)
    clearInterval(counter)
    userAns = answer;
    let correctAns = questions[que_count].answer;
    if (userAns.text == correctAns) {
      console.log('Correct Answer');
      answer.correct = true;
      userAns.correct = answer.correct;
      opttion = "option correct"
      UserScore++;
    } else {
      console.log('Wrong Answer');
      
    }
      
    options.forEach((option) => {
      option.selected = (option === answer);
      option.disabled = true;
    });
    
  
    next_btn ="next_btn show"
 }
  onMount(() => {
    showQuestions(que_count);
  });

  let totalQueCountTag = "";

function queCounter(index) {
    totalQueCountTag = `
      <span>
        <p>${index}</p> of <p>${questions.length}</p> Questions
      </span>
    `;
}
function StartTimer(time) {
  timeleft = time
  counter = setInterval(timer,1000)
  function timer(){
    timeleft = time; 
        time--; 
        if(time < 9){ 
            let addZero = timeleft; 
            timeleft = "0" + addZero; 
        }
        if(time < 0){ 
            clearInterval(counter); 
            options.forEach((option) => {
            option.selected = false;
            option.disabled = true;
            });
            next_button();
          }
      }
}

const next_button = () => {
  if(que_count < questions.length - 1){
    que_count++;
    que_numb++;
    showQuestions(que_count)
    queCounter(que_numb)
    next_btn = "next_btn"
    clearInterval(counter)
    StartTimer(15)
    clearInterval(counterLine)
    startTimerLine(0)
  }
  else{
    console.log("Submitting");
    Showresultbox()
  }
}
function startTimerLine(time) {
    counterLine = setInterval(() => {
      time += 1; 
      timeLineWidth = `${time}px`; 

      if (time > 549) {
        clearInterval(counterLine); 
      }
    }, 29);
  }

  onDestroy(() => {
    clearInterval(counterLine);
  });

  onMount(() => {
    startTimerLine();
  });
</script>

<body>
  <div class="start_btn"><button on:click={start_quiz}>Start Quiz</button></div>

  <div class={info_box}>
      <div class="info-title"><span>Some Rules of this Quiz</span></div>
      <div class="info-list">
          <div class="info">1. You will have only <span>15 seconds</span> per each question.</div>
          <div class="info">2. Once you select your answer, it can't be undone.</div>
          <div class="info">3. You can't select any option once time goes off.</div>
          <div class="info">4. You can't exit from the Quiz while you're playing.</div>
          <div class="info">5. You'll get points on the basis of your correct answers.</div>
      </div>
      <div class="buttons">
          <button class="quit" on:click={Exit_quiz}>Exit Quiz</button>
          <button class="restart" on:click={Countinue_quiz}>Continue</button>
      </div>
  </div>

  <div class={quiz_box}>
      <header>
          <div class="title">Quiz App with timer in Svelte</div>
          <div class="timer">
              <div class="time_left_txt">Time Left</div>
              <div class="timer_sec">{timeleft}</div>
          </div>
          <div class="time_line" style="width: {timeLineWidth}"></div>
      </header>
      <section>
        <div class="que_text">{que_tag}</div>
        <div class="option_list">
          {#each options as option}
          <div 
          class="option {option.selected && userAns.correct ? 'correct' : ''} {option.selected && !option.correct ? 'incorrect' : ''} {option.disabled ? 'disabled' : ''}"
           on:click={() => optionSelected(option)} on:keyup>
          <span>{option.text}</span>
          </div>
        {/each}
        </div>
      </section>

      <footer>
        <div class="total_que">{@html totalQueCountTag}</div>
          <button class={next_btn} on:click={next_button}>Next Que</button>
      </footer>
  </div>
  <div class={result_box}>
      <div class="icon">
          <i class="fas fa-crown"></i>
      </div>
      <div class="complete_text">You've completed the Quiz!</div>
      <div class="score_text">
          {score_text}
      </div>
      <div class="buttons">
          <button class="restart" on:click={quit_quiz}>Replay Quiz</button>
          <button class="quit" on:click={quit_quiz}>Quit Quiz</button>
      </div>
  </div>
</body>