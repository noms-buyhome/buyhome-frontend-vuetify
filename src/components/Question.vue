<template>
  <VCard>
    <h3>질문</h3>

    <h4 v-if="!isClicked">
      {{ question.title }}
    </h4>
    <input v-else v-model="question.title" />
    <h6 class="text-right">
      {{ question.author.nickname }}
    </h6>
    <input v-if="isClicked" v-model="question.content" />
    <p v-else>
      {{ question.content }}
    </p>

    <div v-if="!isClicked">
      <button @click="isClicked = true">수정</button>
      <button @click="deleteAnswer(question.id)">삭제</button>
    </div>
    <div v-else>
      <button @click="updateAnswer">적용</button>
      <button @click="isClicked = false">취소</button>
    </div>
    <p class="text-right">
      {{ question.createdTime }}
    </p>
  </VCard>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: {
    question: Object,
  },
  data() {
    return {
      isClicked: false,
    }
  },
  methods: {
    ...mapActions(['actionUpdateAnswer', 'actionDeleteAnswer']),
    updateAnswer() {
      console.log('updateAnswer in Answer.vue ', this.answer)
      this.actionUpdateAnswer(this.answer, this.answer.questionId)
      this.isClicked = false
    },

    deleteAnswer(answerId) {
      this.actionDeleteAnswer(answerId)
    },
  },
}
</script>
