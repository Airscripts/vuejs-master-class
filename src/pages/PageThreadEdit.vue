<template>
  <div class="col-full push-top">

    <h1>Editing <i>{{thread.title}}</i></h1>

    <ThreadEditor
      :title="thread.title"
      :text="text"
      @save="save"
      @cancel="cancel"
    />
  </div>
</template>

<script>
  import ThreadEditor from '@/components/ThreadEditor'
  export default {
    components: {
      ThreadEditor
    },

    props: {
      id: {
        type: String,
        required: true
      }
    },

    computed: {
      thread () {
        return this.$store.state.threads.items[this.id]
      },

      text () {
        const post = this.$store.state.posts.items[this.thread.firstPostId]
        return post ? post.text : null
      },

      hasUnsavedChanges () {
        // this.saved is not required in this implementation because `this.thread.title` and `this.text` are reactive
        // Thus `hasUnsavedChanges` will automatically become false when the thread is updated
        return this.$refs.editor.form.title !== this.thread.title || this.$refs.editor.form.text !== this.text
      }
    },

    methods: {
      ...mapActions('threads', ['updateThread', 'fetchThread']),
      ...mapActions('posts', ['fetchPost']),

      save ({title, text}) {
        this.$store.dispatch('updateThread', {
          id: this.id,
          title,
          text
        }).then(thread => {
          this.$router.push({name: 'ThreadShow', params: {id: this.id}})
        })
      },

      cancel () {
        this.$router.push({name: 'ThreadShow', params: {id: this.id}})
      }
    }
  }
</script>

<style scoped>

</style>
