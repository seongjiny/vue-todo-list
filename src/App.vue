<template>
  <div v-if="showModal" @click="showModal = false" class="modal-mask">
    <div class="modal-wrapper">
      <div class="modal-card">
        <div class="modal-title"><h3>경고</h3></div>
        <div class="modal-body">
          할 일을 입력하세요<span class="material-icons">close</span>
        </div>
      </div>
    </div>
  </div>
  <h1>TODO it!</h1>
  <div class="capture-all">
    <div class="input-div">
      <input
        class="input-box"
        v-model="newItem"
        placeholder="Type what you have to do"
      />
      <button type="button" class="btn btn-primary" @click="addItem()">
        <span class="button-span material-icons">add</span>
      </button>
    </div>
    <div v-for="(item, i) in array" :key="item" class="card card-item">
      <div class="card-body">
        <span class="button-span material-icons">check</span>{{ item
        }} <span class="span-right material-icons" @click="deleteItem(i)"
          >delete</span
        >
      </div>
    </div>
  </div>
  <button
    type="button"
    class="btn btn-outline-danger mt-3"
    @click="clearItems()"
  >
    Clear All
  </button>
</template>

<script>
import { onMounted, ref } from "vue";
import firebase from "firebase/compat/app";
export default {
  setup() {
    const array = ref([]);
    const newItem = ref("");
    const showModal = ref(false);
    const todoRef = firebase.database().ref("data/").child("todo");
    onMounted(() => {
      todoRef.on("value", (sn) => {
        array.value = sn.val();
      });
    });
    const addItem = () => {
      if (newItem.value == "") {
        showModal.value = true;
      } else {
        todoRef.push(newItem.value);
        newItem.value = "";
      }
    };
    const clearItems = () => {
      todoRef.remove();
    };
    const deleteItem = (i) => {
      todoRef.child(i).remove();
    };

    return { addItem, array, newItem, clearItems, deleteItem, showModal };
  },
};
</script>

<style>
#app {
  --bs-font-sans-serif: system-ui, -apple-system, "Segoe UI", Roboto,
    "Helvetica Neue", "Noto Sans", "Liberation Sans", Arial, sans-serif,
    "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  font-weight: 500;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.input-div {
  display: flex;
  height: 50px;
}

.input-box {
  flex: 1;
}
.button-span {
  width: 50px;
}

input {
  text-align: center;
}

div.capture-all {
  margin-left: 10px;
  margin-right: 10px;
}
div.card-item {
  margin-top: 10px;
  text-align: left;
}
.span-right {
  float: right;
}

/* popup modal styling */
.modal-mask {
  position: fixed;
  z-index: 1;
  top: 0px;
  left: 0px;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;

  text-align: center;
}
.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}
.modal-card {
  background-color: white;
  width: 400px;
  padding: 30px 20px;
  float: center;
  margin: auto;
}
.modal-title {
  color: #62acde;
}

.modal-body {
  margin-top: 50px;
  line-height: 50px;
  font-size: 15px;
}
</style>
