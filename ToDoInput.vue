<script setup>
import{ref}from"vue";
import{statuses} from"../const/statuses";
const input = ref("");
const inputDate = ref("");

const isErrMsg = ref(false);

function onSubmitForm(){
  if (input.value=="" || inputDate.value==""){
    //エラーメッセージを表示
    isErrMsg.value == true;
    event.preventDefault();
    return;
  }
  console.log(input.value)
  const items = JSON.parse(localStorage.gatItem("items")) || [];
}

const newItem = {
  id: items.length,
  content: input.value,
  limit: inputDate.value,
  state: statuses.NOT_START,
  onEdit: false,
};

items.push(newItem);

localStorage.setItem("items", JSON.stringify(items));

</script>

<template>
  <p v-if ="isErrMsg">タスク・期限を両方入力してください。</p>
  <div>
    <form @submit="onSubmitForm">
      <label>やること<input type="text" v-model="input"/></label> <!--閉めるときは/忘れない-->

      <label>期限<input type="date" v-model="inputDate"/></label>
      <input type="submit" value="登録!"/>
    </form>
  </div>
</template>

