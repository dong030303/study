<template>
  <div :class="{ done }" class="todo-item">
    <div v-if="isEditMode" class="item__inner item--edit">
      <input
        ref="titleInput"
        :value="editedTitle"
        type="text"
        @input="editedTitle = $event.target.value"
        @keydown.enter="editedTodo"
        @keydown.esc="offEditMode"
      />
      <!--수정 입력창 -->
      <div class="item__actions">
        <button key="complete" @click="editedTodo">완료</button>
        <button key="cancel" @click="offEditMode">취소</button>
      </div>
      <!--수정 완료와 취소 버튼-->
    </div>
    <!--수정 상태-->

    <div v-else class="item__inner item--normal">
      <input v-model="done" type="checkbox" />
      <div class="item__title-wrap">
        <div class="item__title" @dblclick="onEditMode">{{ todo.title }}</div>
        <div class="item__date">{{ date }}</div>
      </div>
      <!--할 일과 생성 및 수정 시간 표시-->
      <div class="item__actions">
        <button key="update" @click="onEditMode">수정</button>
        <button key="delete" @click="deleteTodo">삭제</button>
      </div>
      <!--수정 삭제 버튼 묶음-->
    </div>
    <!--v-if else 구문을 활용한 일반 상태와 수정 상태 구분-->
    <!--일반 상태-->
  </div>
  <!--추가되는 리스트(할 일 목록) 완료 여부를 체크하는 클래스-->
</template>
<script>
import dayjs from "dayjs";

export default {
  name: "TodoItem",
  props: {
    todo: Object
  },
  data() {
    return {
      isEditMode: false,
      editedTitle: this.todo.title
    };
  },
  computed: {
    done: {
      get() {
        return this.todo.done;
      },
      set(done) {
        this.updateTodo({
          done
        });
      }
    },
    date() {
      const date = dayjs(this.todo.createdAt);
      const isSame = date.isSame(this.todo.updatedAt);
      if (isSame) {
        return date.format("YYYY년 MM월 DD일");
      } else {
        return `${date.format("YYYY년 MM월 DD일")} (edited)`;
      }
    } // 생성된 시간과 수정된 시간을 체크하여 서로 다를 경우 (edited) 문구를 추가
  },
  methods: {
    onEditMode() {
      this.editedTitle = this.todo.title;
      this.isEditMode = true;
      this.$nextTick(() => {
        //$nextTick: 
        this.$refs.titleInput.focus();
      });
    }, //
    offEditMode() {
      this.isEditMode = false;
    }, //수정 상태 off
    editedTodo() {
      if (this.todo.title !== this.editedTitle) {
        this.updateTodo({ title: this.editedTitle, updatedAt: new Date() });
      }
      this.offEditMode();
    }, //title이 변경되었을 시 updateTodo 실행
    updateTodo(value) {
      this.$emit("update-todo", this.todo, value);
    },
    deleteTodo() {
      this.$emit("delete-todo", this.todo);
    }
  }
};
</script>

<style lang="scss">
.todo-item {
  margin-bottom: 10px;
  .item__inner {
    display: flex;
  }
  .item__date {
    font-size: 12px;
  }
  &.done {
    .item__title {
      text-decoration: line-through;
    }
  }
}
</style>