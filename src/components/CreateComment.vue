<template>
  <div class="form-create-post">
    <div class="mb-3">
      <form>
        <label for="post-body" class="form-label">Добавить комментарий</label>
        <input
          type="text"
          class="form-control mb-3"
          id="post-body"
          placeholder="Введите сюда текст комментария"
          @input="$emit('input', $event.target.value)"
        />

        <button type="button" class="btn btn-success" v-on:click="$emit('eventClick')">
          Добавить
        </button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CreateComment',
  methods: {
    handleInput() {
      this.$emit('input', this.commentText);
    },
  },
};
</script>

<style>
.form-create-post {
  width: 300px;
  margin: auto;
}
</style>
