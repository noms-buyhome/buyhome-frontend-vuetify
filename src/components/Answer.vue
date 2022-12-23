<template>
  <VCard>
    <h4 v-if="!isClicked" class="card-title">
      {{ answer.title }}
    </h4>
    <input v-else v-model="answer.title" />
    <h6 class="card-subtitle mb-2 text-muted text-right">
      {{ answer.author.nickname }}
    </h6>
    <input v-if="isClicked" v-model="answer.content" />
    <p v-else class="card-text">
      {{ answer.content }}
    </p>

    <div v-if="!isClicked">
      <button class="card-link" @click="isClicked = true">수정</button>
      <button @click="deleteAnswer(answer.id)">삭제</button>
    </div>
    <div v-else>
      <button class="card-link" @click="updateAnswer">적용</button>
      <button @click="isClicked = false">취소</button>
    </div>
    <p class="text-right">
      {{ answer.createdTime }}
    </p>
  </VCard>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: {
    answer: Object,
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
