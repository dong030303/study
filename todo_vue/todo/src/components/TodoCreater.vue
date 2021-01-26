<template>
  <div>
    <input
      :value="title"
      :placeholder="placeholder"
      type="text"
      @input="title = $event.target.value"
      @keypress.enter="createTodo"
    /><!--입력창-->
    <button @click="createTodo" focus>추가</button
    ><!--추가 버튼-->
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: "",
      placeholder: "할 일을 추가하세요!"
    };
  },
  methods: {
    // 데이터 유효성 검사 후 데이터 삽입
    createTodo() {
      const validatedTitle = this.title && this.title.trim();
      if (!validatedTitle) {
        alert("유효하지 않은 제목입니다.");
        this.title = this.title.trim();
        return;
      }
      this.$emit("create-todo", this.title);
      this.title = validatedTitle;
      this.title = "";
    }
  }
};
</script>
