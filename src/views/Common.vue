<template>
  <div class="common">
     <div class="wrap">
       <transition name="slide-fade">
       <Opening class="box"
                :cate="cate"
                v-on:information="fnInformation"
                v-if="viewOpening" />
       </transition>
       <transition name="slide-fade">
       <Information class="box"
                v-on:message="fnMessage"
                v-on:quizStart="fnQuizStart"
                v-if="viewInformation"  />
       </transition>
       <transition name="slide-fade">
       <Question class="box"
              :question="question"
              v-on:quizResult="fnQuizResult"
              v-if="viewQuestion"  />
       </transition>
       <transition name="slide-fade">
       <QuestionRight class="box"
              :question="question"
              v-on:quizPromote="fnQuizPromote"
              v-if="viewQuestionRight" />
       </transition>
       <transition name="slide-fade">
       <QuestionWrong class="box"
              :question="question"
              v-on:quizPromote="fnQuizPromote"
              v-if="viewQuestionWrong" />
       </transition>
       <transition name="slide-fade">
       <QuestionPromote class="box"
              :question="question"
              v-on:quizNext="fnQuizNext"
              v-if="viewQuestionPromote" />
       </transition>
       <transition name="slide-fade">
       <Point class="box"
              v-on:gift="fnGift"
              v-if="viewPoint" />
       </transition>
       <transition name="slide-fade">
       <Gift class="box"
              :cate="cate"
              v-on:close="fnClose"
              v-if="viewGift" />
       </transition>
       <transition name="slide-fade">
       <Close class="box"
              :cate="cate"
              v-if="viewClose" />
       </transition>
       <div class="yuyu"><img :src="left_down_image" alt="YES" width="70%"></div>
    </div>

    <audio
        style="display:none"
        ref="player"
        id="audio-player"
        loop="true"
    >
        <source src="http://www.emds.co.kr/media/bgm.mp3" type="audio/mpeg" />
    </audio>

    <base-alert-message :message="alertMessage" v-on:btnClose="alertMessage = ''"/>
    <base-loading-bar v-if="loadingbar" v-on:loadingBarClose="loadingbar=false" />
  </div>
</template>

<script>
import Opening from '@/components/Opening'
import Information from '@/components/Information'
import Question from '@/components/Question'
import QuestionRight from '@/components/QuestionRight'
import QuestionWrong from '@/components/QuestionWrong'
import QuestionPromote from '@/components/QuestionPromote'
import Point from '@/components/Point'
import Gift from '@/components/Gift'
import Close from '@/components/Close'

export default {
  name: 'Common',
  components: {
    Opening,
    Information,
    Question,
    QuestionRight,
    QuestionWrong,
    QuestionPromote,
    Point,
    Gift,
    Close
  },
  data() {
    return {
      url: '',
      viewOpening: false,
      viewInformation: false,
      viewQuestion: false,
      viewQuestionRight: false,
      viewQuestionWrong: false,
      viewQuestionPromote: false,
      viewPoint: false,
      viewGift: false,
      viewClose: false,
      point: 0,
      cate: {},
      arrQuestion: [],
      question: {},
      nowNum: 0,
      common_back_image: '',
      left_down_image: '',

      loadingbar: false,
      alertMessage: ''
    }
  },
  mounted() {
    this.$store.state.answer.score = 0
    this.point = this.$store.state.point

    var audio = document.getElementById("audio-player");
    audio.play();

    this.url = this.$store.state.url
    this.point = this.$store.state.point
    this.cate = this.$store.state.cate

    this.common_back_image = this.url+'file/cate/'+this.cate.common_back_image
    this.left_down_image = this.url+'file/cate/'+this.cate.left_bottom_image
    document.querySelector('body').style.backgroundImage = "url("+this.common_back_image+")"
    document.querySelector('body').style.backgroundSize = "100% 100%"
    //document.getElementsByTagName('body').style.backgroundImage = "url("+this.common_back_image+")"
    
    
    //this.viewPoint = true;
    //this.viewGift = true;
    //this.viewClose = true;
    this.viewOpening = true;
  },
  methods: {
    fnInformation() {
      this.viewOpening = false
      setTimeout(() => {this.viewInformation = true}, 1000)
    },
    fnQuizStart() {
      this.viewInformation = false
      
      this.question = this.$store.state.arrQuestion[this.nowNum++]
      
      setTimeout(() => {
        this.viewQuestion = true    
      }, 1000)
    },
    fnQuizResult(obj) {
      this.$store.state.reply.push(obj)

      this.viewQuestion= false

      if(obj.correct_yn) this.$store.state.answer.score += Number(this.point)
      
      if(obj.correct_yn) setTimeout(() => {this.viewQuestionRight = true}, 1000)
      else setTimeout(() => {this.viewQuestionWrong = true}, 1000)
    },
    fnQuizPromote() {
        this.viewQuestionRight = false
        this.viewQuestionWrong = false

        setTimeout(() => {this.viewQuestionPromote = true}, 1000)
    },
    fnQuizNext() {
      this.viewQuestionPromote = false

      if(this.nowNum == this.$store.state.arrQuestion.length)
        setTimeout(() => {this.fnPoint()}, 1000)
      else setTimeout(() => {this.fnQuizStart()}, 1000)
      
    },
    fnPoint() {
      this.viewPoint = true
    },
    fnGift() {
      this.viewPoint = false
      setTimeout(() => {this.viewGift = true}, 1000)
    },
    fnClose() {
      this.viewGift = false
      setTimeout(() => {this.viewClose = true}, 1000)
    },
    fnMessage(msg) {
      this.alertMessage = msg
    }
  }
}
</script>
<style lang="scss" scoped>
.common {
  height: 100%;
  .wrap {
    position: relative;
    padding: 0;
    text-align: center;
    min-height: 500px;
    
    .box {
      display: inline-block;
    }

    .yuyu {position: absolute; top: 500px; text-align: left; padding-left: 2rem;}
  }
}

.slide-fade-enter-active {
      animation: move-in .5s;
  }
.slide-fade-leave-active {
    animation: move-in .5s reverse;
}
@keyframes move-in {
    0% {
        transform: translateY(-1000px);
    }
    100% {
        transform: translateY(0px);
    }
}
</style>