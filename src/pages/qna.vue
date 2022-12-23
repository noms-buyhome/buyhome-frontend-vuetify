<template>
  <VCard>
    <VTable>
      <template #default>
        <thead>
          <tr>
            <th class="text-left">no</th>
            <th class="text-left">title</th>
            <th class="text-left">author</th>
            <th class="text-left">created time</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in qnaList" :key="item.id" @click="openQnaDetailDialog(item.id)">
            <td>{{ item.id }}</td>
            <td>{{ item.title }}</td>
            <td>{{ item.author.nickname }}</td>
            <td>{{ item.createdTime }}</td>
          </tr>
        </tbody>
      </template>
    </VTable>

    <VDialog v-model="dialog">
      <VCard class="modal">
        <h3>Qna Detail</h3>
        <Question :question="qna" />
        <!-- <div v-if="qna.answers != null"> -->
        <span>답변</span>
        <Answer v-for="answer in qna.answers" :key="answer.id" :answer="answer" />
        <!-- </div> -->
        <VCardActions>
          <VBtn color="primary" block @click="dialog = false"> 닫기 </VBtn>
        </VCardActions>
      </VCard>
    </VDialog>
  </VCard>
</template>

<script>
import http from '@/api/http'
import Answer from '@/components/Answer.vue'
import Question from '@/components/Question.vue'

export default {
  components: {
    Question,
    Answer,
  },
  data() {
    return {
      dialog: false,
      qna: {},
      qnaList: [
        {
          id: 1,
          title: 'test title',
          author: 'test author',
        },
      ],
    }
  },
  created() {
    http.get('/board/qna').then(({ data }) => (this.qnaList = data))
  },
  methods: {
    openQnaDetailDialog(qnaId) {
      this.dialog = true
      http
        .get(`/board/qna/${qnaId}`)
        .then(({ data }) => {
          this.qna = data
          console.log(data)
        })
        .catch(error => console.log(error))
    },
  },
}
</script>
