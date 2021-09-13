<template lang="pug">
div(
  :style="!reply? '-webkit-box-shadow: 5px 5px 12px 0px rgba(97,97,97,0.64);box-shadow: 5px 5px 12px 0px rgba(97,97,97,0.64);' : 'border: 0px;'"
  ).card.p-3.d-flex
  .d-flex.flex-row
    span.mb-1 Tu Comentario
  .d-flex.flex-column.mb-2
    span#userNameLabel Nombre de usuario
    input.form-input(v-model="userName")
  .card.d-flex.justify-content-center
    quillEditor(
      :content="comment"
      :options="editorOption"
      @change="onEditorChange($event)"
    )
  .mt-3.d-flex.flex-row
    b-button(style="margin-right:6px" variant="primary" @click="closeCommentaryBox()") Cancelar
    b-button(variant="warning" @click="sendComment()") Guardar
</template>

<script>
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'

import { quillEditor } from 'vue-quill-editor'


export default {
  name: 'CommentaryTextArea',
  props: ['reply', 'replyLevel'],
  components: {
    quillEditor
  },
  data() {
    return {
      comment: '',
      userName: '',
      editorOption: {
        modules: {toolbar:  ['bold', 'italic', 'underline', 'strike'],}
      }
    }
  },
  methods: {
    closeCommentaryBox() {
      this.userName = ''
      this.comment = ''
      if (this.reply) this.$emit('closeCommentary')
    },
    sendComment() {
      let data = {
        comment: this.comment,
        reply: this.reply,
        level: 1,
        userName: this.userName,
        date: new Date()
      }
      if (this.reply) {
        const replyLevel = this.replyLevel + 1
        console.log(replyLevel)
        data.level = replyLevel
        this.$emit('setReplay', data)
      }
      console.log(data)
      this.$emit('sendComment', data)
      this.closeCommentaryBox()
    },
    onEditorChange({ quill, html, text }) {
      console.log('editor change!', quill, html, text)
      this.comment = html
    }

  }
}
</script>

<style scoped lang="scss">
  ::v-deep .form-control:focus {
    color: #212529;
    background-color: #fff;
    /* border-color: #86b7fe; */
    outline: 0;
    /* box-shadow: 0 0 0 0.25rem rgb(13 110 253 / 25%); */
  }
  ::v-deep .form-control {
    display: block;
    width: 100%;
    height: 80px;
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: #212529;
    background-color: #fff;
    background-clip: padding-box;
    border: 1px solid white;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    /* border-radius: 0.25rem; */
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
  }
  ::v-deep .form-control .form-control:focus {
    color: #212529;
    background-color: #fff;
    /* border-color: #86b7fe; */
    outline: 0;
    /* box-shadow: 0 0 0 0.25rem rgb(13 110 253 / 25%);*/
  }
  ::v-deep .btn-warning {
    color: #fff;
    background-color: #ee7600;
    border-color: #ee7600;
  }
  ::v-deep .btn-primary {
    color: #fff;
    background-color: #23395d;
    border-color: #23395d;
  }
  ::v-deep .btn-warning:hover {
    color: #fff;
    background-color: #ffca2c;
    border-color: #ffc720;
  }

  ::v-deep .ql-toolbar.ql-snow {
    border: 1px solid #ccc;
    box-sizing: border-box;
    font-family: 'Helvetica Neue', 'Helvetica', 'Arial', sans-serif;
    display: flex;
    padding: 8px;
}
</style>