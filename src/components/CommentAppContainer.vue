<template lang="pug">
#CommentaryAppContainer
  .row
    .col-md-2
    .col-md-8.d-flex.flex-column
        .d-flex.flex-row.mb-1
          .d-flex.flex-row
            span.commentaryLabel.mr-2 Comentarios
            div.commentaryBox 
              span {{commentaryAccount}}
          div(style="margin-left:auto;").d-flex.flex-row
            span.mr-2 Ordenar por
            select( v-model="orderBy" class="select-input ml-2")
              option(value="fecha_asc") Fecha Asc
              option(value="fecha_des") Fecha Des
        
    .col-md-2
  .row
    .col-md-2
    .col-md-8
      div(v-for="commentary in commentariesList").mb-5
        CommentaryCard(:commentary="commentary" @editText="editingCommentary" @deleteComment="handleDelete")
    .col-md-2
  .row
    .col-md-2
    .col-md-8
      CommentaryTextArea(
        :reply="false" 
        @sendComment="handleComment"
      )
    .col-md-2
</template>

<script>
import CommentaryTextArea from './CommentaryTextArea.vue'
import CommentaryCard from './CommentaryCard.vue'

export default {
  name: 'CommentAppContainer',
  components: {
    CommentaryTextArea,
    CommentaryCard
  },
  data () {
    return {
      commentaries: [],
      orderBy: 'fecha_asc',
    }
  },
  computed: {
    commentaryAccount() {
      return this.commentaries.length 
    },
    commentariesList() {
      if(this.commentaries.length > 0 ) {
        if(this.orderBy == 'fecha_asc') {
          return [...this.commentaries].sort((a,b) => {
            return a.date-b.date 
          })
        } else {
          return [...this.commentaries].sort((a,b) => {
            return b.date-a.date 
          })
        }
      }
      return {}
    },
  },
  methods: {
    handleComment(data) {
      let index = 0
      if (this.commentaries.length > 0) index = this.commentaries.length - 1 
      this.commentaries.push({...data, id: index})
    },
    editingCommentary(value) {
      const index = this.commentaries.map(comment => comment.index).indexOf(value.index)
      if(index >-1) this.commentaries[index].comment = value.comment
    },
    handleDelete(value) {
      console.log(value)
      this.commentaries = this.commentaries.filter(comment => comment.id != value)
    },
  },
}
</script>