<template>
  <div class="home">
    <CreateComment v-model="commentText" v-on:eventClick="createComment"></CreateComment>

    <h3>Страница: {{ pageNumber }}</h3>
    <UserComment v-for="comment in paginatedData" :key="comment.id" :comment="comment" />

    <div class="btns-navigation mb-3">
      <button
        class="btn btn-outline-primary btn-previous"
        :disabled="pageNumber == 1"
        @click="previousPage"
      >
        Previous
      </button>
      <button
        class="btn btn-outline-primary btn-next"
        :disabled="pageNumber >= pageCount - 1"
        @click="nextPage"
      >
        Next
      </button>
    </div>
  </div>
</template>

<script>
import Swal from 'sweetalert2';
import axios from 'axios';
import dayjs from 'dayjs';
import UserComment from '../components/UserComment.vue';
import CreateComment from '../components/CreateComment.vue';

export default {
  name: 'HomeView',
  components: {
    UserComment,
    CreateComment,
  },

  data() {
    return {
      pageNumber: 1,
      size: 20,
      comments: [],
      commentText: '',
    };
  },

  methods: {
    async nextPage() {
      this.pageNumber++;

      const query = Object.assign({}, this.$route.query);
      query.page = this.pageNumber;
      query.limit = this.pageCount - 1;

      await this.$router.push({ query });
    },

    async previousPage() {
      this.pageNumber--;

      const query = Object.assign({}, this.$route.query);
      query.page = this.pageNumber;
      query.limit = this.pageCount - 1;

      await this.$router.push({ query });
    },

    createComment() {
      const date = new Date();

      const countWords = this.commentText.split(' ').length;
      const lengthString = this.commentText.replace(/\s/g, '').length;

      if (countWords >= 3 && lengthString < 1000) {
        this.comments.unshift({
          body: this.commentText,
          created_at: dayjs(date),
          author: {
            id: 16,
            name: 'John Doe',
            avatar: 'http://placeimg.com/640/480/business',
            company: 'BigCountry',
          },
        });
      } else {
        Swal.fire({
          title: 'Ошибка при создании комментария!',
          icon: 'error',
          text:
            countWords < 3
              ? 'В тексте комментария должно быть больше 3-ех слов'
              : 'В тексте комментарии должно быть меньше 1000 символов',
        });
      }
    },
  },

  computed: {
    pageCount() {
      const lengthArrayComments = this.comments.length;
      const sizeArrayComments = this.size;

      return Math.ceil(lengthArrayComments / sizeArrayComments);
    },

    paginatedData() {
      const start = (this.pageNumber - 1) * this.size;
      const end = start + this.size;

      return this.comments.slice(start, end);
    },
  },

  created: function () {
    axios.get('https://bigcountry-task.vercel.app/comments.json').then((comments) => {
      this.comments = comments.data;

      this.pageNumber = this.$route.query.page;
    });
  },
};
</script>
