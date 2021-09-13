<template lang="pug">
  div(:style="levelWidth" :class="commentary.level === 1? 'card mt-2 mb-2 p-2 box-shadow-medium': 'mt-2 mb-2 p-2'")#CommentaryCard
    .row
      .col.d-flex.flex-row
        b-avatar
        .d-flex.flex-column
          span(style="color: orange;font-size: 0.9rem;font-weight:bold;") {{commentary.userName?commentary.userName : 'Anónimo'}}
          span(v-html="commentary.comment" v-show="show && !edit")
          span(style="font-size: 0.8rem;" v-show="!show && !edit") Comentario Oculto
          div(v-if="!edit").d-flex.flex-row
            b-icon.cursor-pointer(:style="like? 'margin-top:4px;color: red;margin-right:20px;': 'margin-top:4px;margin-right:20px;'" :icon="like? 'heart-fill': 'heart'" @click="like = !like")
            span(@click="show = !show").cursor-pointer.show-button {{show? 'ocultar': 'mostrar'}}
            b-icon.cursor-pointer(v-show="commentary.level <3" :icon="reply? 'reply-fill': 'reply'"  @click="replying()")
      .col
        .d-flex.flex-row
          b-icon.cursor-pointer.pencil-button(
            :style="edit? 'color: rgb(3, 3, 85);margin-left:auto;margin-right: 18px;':'margin-left:auto;margin-right: 18px;'" 
            icon="pencil-square"
            @click="setEdit()"  
          )
          b-icon.ml-2.cursor-pointer.x-button(
            icon="x" 
            @click="deleteComment"
          )
          span(style="margin-right:6px;margin-left: 18px;") {{dateFormat}}
      div(v-show="edit").mb-2
        quillEditor(
          :content="editText"
          :options="editorOption"
          @change="onEditorChange($event)"
        )
      div(v-show="reply && !edit")
        CommentaryTextArea(:reply="true" :replyLevel="commentary.level" @setReplay="getReply").mt-3
      div.show-replys
        commentary-card(
          v-if="repliesComment.length > 0" 
          :commentary="repliesComment[0]"
          :isReply="true"
          @eliminateReply="eliminateReply"
        )
        
</template>

<script>
import moment from 'moment'
import CommentaryTextArea from './CommentaryTextArea.vue'
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'

import { quillEditor } from 'vue-quill-editor'

export default {
  name:'commentary-card',
  props: ['commentary', 'isReply'],
  components: {
    quillEditor,
    CommentaryTextArea
  },
  data() {
    return {
      like: false,
      show: true,
      edit: false,
      reply: false,
      editText: '',
      editorOption: {
        modules: {toolbar:  ['bold', 'italic', 'underline', 'strike'],}
      },
      repliesComment: [],
    }
  },
  computed: {
    dateFormat() {
      return moment(this.commentary.date).format('d/D/DDD h:mm:ss a')
    },
    levelWidth() {
      if (this.commentary.level === 1) {
        return ''
      } else if (this.commentary.level == 2) {
        return 'margin-left:25px'
      } 
      return 'margin-left:35px'
    }
  },
  methods: {
    onEditorChange({ quill, html, text }) {
      console.log('editor change!', quill, html, text)
      this.editText = html
    },
    setEdit() {
      if (!this.edit) return this.edit = true
      const data = this.commentary
      data.comment = this.editText
      this.$emit('editText', data)
      this.edit = false 
    },
    replying() {
      if (this.repliesComment.length === 0) {
        this.reply= !this.reply
      }
    },
    getReply(data) {
      console.log('reply', data)
      this.repliesComment.push(data)
      this.reply = false
    },
    deleteComment() {
      if (this.isReply) {
        return this.$emit('eliminateReply')
      }
      const data = this.commentary.id
      console.log(data) 
      this.$emit('deleteComment', data)
    },
    eliminateReply() {
      this.repliesComment = []
    }
  },
  watch: {
    'commentary.comment'(value) {
      this.editText = value
    },
    'edit'(value) {
      if (value) this.reply = false
    }
  }
}
</script>

<style scoped lang="scss">
::v-deep .ql-toolbar.ql-snow {
    border: 1px solid #ccc;
    box-sizing: border-box;
    font-family: 'Helvetica Neue', 'Helvetica', 'Arial', sans-serif;
    display: flex;
    padding: 8px;
}
</style>