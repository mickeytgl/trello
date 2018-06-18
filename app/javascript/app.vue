<template>
  <div class="board">
    <draggable v-model="lists" :options="{group: 'lists'}" class="d-inline-block dragArea" @end="listMoved">
      <list v-for="(list, index) in lists" :list="list" :key="list.id"></list>
    </draggable>

      <div class="list">
        <a v-if="!editing" v-on:click="startEditing">Add a List</a>
        <textarea v-if="editing" ref="message" class="form-control mb-1" v-model="message"></textarea>
        <button v-if="editing" v-on:click="createList" class="btn btn-secondary">Add</button>
        <a v-if="editing" v-on:click="editing=false">Cancel</a>
      </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'
import list from 'components/list'

export default {
  components: { draggable, list },

  data: function() {
    return {
      editing: false,
      message: "",
    }
  },

  computed: {
    lists: {
      get() {
        return this.$store.state.lists
      },
      set(value) {
        this.$store.state.lists
      },
    },
  },

  methods: {
    startEditing: function(){
      this.editing = true
      this.$nextTick(() => { this.$refs.message.focus() })
    },
    listMoved: function(event)  {
      console.log("MOVE IT!")
      var data = new FormData
      data.append("list[position]", event.newIndex + 1)

      Rails.ajax({
        url: `/lists/${this.lists[event.newIndex].id}/move`,
        type: "PATCH",
        data: data,
        dataType: "json",
      })
    },
    createList: function() {
      var data = new FormData
      data.append("list[name]", this.message)

      Rails.ajax({
        url: "/lists",
        type: "POST",
        data: data,
        dataType: "json",
        success: (data) => {
          this.message = ""
          this.editing = false
        }
      })
    },
  }
}
</script>

<style scoped>
.dragArea {
  min-height: 20px;
}
.board {
  overflow-x: auto;
   white-space: nowrap;
}
.list {
  background: #E2E4E6;
  border-radius: 3px;
  display: inline-block;
  margin-right: 20px;
  padding: 10px;
  width: 270px;
  vertical-align: top;
}
</style>
