// App.vue
<script setup>
// vue3 컴포지션 api 가져오기
import { reactive, computed } from 'vue';

// reactive함수: 객체를 반응성으로 만들어주며 객체의 값이 변경되면 리렌더링하여 화면 업데이트
const data = reactive({
  newItem: '',
  items: [],
});

// 할일 완료 요소의 배열 길이 구하기
// 랜더링간에 새로운 변수가 생성되며 변경된 값이 변수에 각각 할당됨
// 완료된 요소를 새로운 배열로 만들고 갯수를 구해줌
const isComplete = computed(
  () => data.items.filter((item) => item.completed).length
);

// computed함수: data.items가 변경될때만 함수 실행(계산결과를 캐싱하여 성능 최적화)
const totalItems = computed(() => data.items.length);

// 할일추가: data.newItem은 입력필드와 연결되있으므로 빈값이 아닐경우 추가
// 추가된 데이터 마다 고유 id 추가하여 데이터 구분 가능하도록 해줌
// 할일 추가후 입력필드 빈칸으로 초기화
function addItem() {
  if (data.newItem !== '') {
    data.items.push({
      id: data.items.length + 1,
      text: data.newItem,
      completed: false,
    });
    // 할일 추가뒤 입력필드와 연결된 데이터를 빈칸으로 초기화
    data.newItem = '';
  }
}

const deleteItem = (id) => {
  // 해당 id에 맞는 아이템을 찾아서 반환
  console.log(id);
  const itemToDelete = data.items.find((item) => item.id === id);

  // 해당 아이템의 인덱스를 찾아서 삭제
  const index = data.items.indexOf(itemToDelete);
  data.items.splice(index, 1);
};
</script>

<template>
  <main class="app">
    <h1>Simple to-do list</h1>
    <div class="todo_count">
      <!-- 템플릿 내부에 데이터 표현시 {{ }} 사용 -->
      완료: {{ isComplete }} / 할 일: {{ totalItems }}
    </div>
    <div class="todo_add">
      <!-- v-model 디렉티브는 폼요소와 데이터를 양방향으로 연결 -->
      <!-- v-on:이벤트명으로 이벤트 연결 -->
      <input
        type="text"
        title="할 일을 입력하세요"
        placeholder="할 일을 입력하세요"
        v-model="data.newItem"
        v-on:keyup.enter="addItem()"
      />
      <button type="button" class="add_btn" v-on:click="addItem()">Add</button>
    </div>
    <ul class="todo_list">
      <!-- <li v-for="(todo, i) in data.items" v-bind:key="todo.id"> -->
      <!-- todo.completed가 true면 completed문자열이 클래스명이 됨 -->
      <li
        v-for="(todo, i) in data.items"
        v-bind:key="todo.id"
        v-bind:class="{ completed: todo.completed }"
      >
        <!-- v-blind 디렉티브: 데이터 단방향 연결 -->
        <!-- 리스트는 고유값을 key속성에 연결하여 순서가 변경되도 구별가능하도록 -->

        <!-- 라벨클릭시 for로 연결된 체크박스가 클릭되어 true, false가 발생되며
        v-model="item.completed"에 의해 completed속성값이 true, false가됨 -->

        <!-- <input type="checkbox" v-bind:id="`check${todo.id}`" /> -->
        <input
          v-bind:id="`check${todo.id}`"
          type="checkbox"
          v-model="todo.completed"
        />
        <label v-bind:for="`check${todo.id}`">{{ todo.text }}</label>
        <button
          type="button"
          class="remove_btn"
          v-on:click="deleteItem(todo.id)"
        >
          Remove
        </button>
      </li>
    </ul>
  </main>
</template>

<style scoped>
.app {
  padding: 40px;
}
.app h1 {
  font-size: 30px;
  font-weight: 700;
  margin-bottom: 20px;
}

.todo_count {
  margin: 10px 0;
}

.todo_add {
  display: flex;
}
.todo_add input {
  height: 40px;
  padding: 0 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  flex-grow: 1;
}
.todo_add .add_btn {
  height: 40px;
  padding: 0 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  margin-left: 10px;
  color: #fff;
  background: #333;
  border: none;
}

.todo_list {
  margin-top: 20px;
}
.todo_list li {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
.todo_list label {
  flex-grow: 1;
}
.todo_list li.completed label {
  color: #ccc;
  text-decoration: line-through;
}
.todo_list .remove_btn {
  margin-left: auto;
  height: 32px;
  padding: 0 5px;
  background: none;
  border: 1px solid #b83030;
  color: #b83030;
  border-radius: 4px;
  margin-left: 20px;
}
</style>
