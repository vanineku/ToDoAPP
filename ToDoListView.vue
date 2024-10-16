<script>
    let items = ref(JSON.parse(localStorage.getItem("items")) || []);
    let inputContent = ref(); //タスクの内容
    let inputLimit = ref(); //タスクの期限
    let inputState = ref(); //タスクのステータス


    function onEdit(id){
        inputContent.value = items.value[id].content;
        inputLimit.value = items.value[id].limit;
        inputState.value = items.value[id].state;
        items.value[id].onEdit = true;
    }

    

    const newItem = {
        id: id,
        content: inputContent.value,
        limit: inputLimit.value,
        state: inputState.value,
        onEdit: false,
    };
    
    items.value.splice(id,1,newItem);
    localStorage.setItem("items", JSON.stringify(items.value));
    isErrMsg.value = false;

    let isErrMsg = ref(false);
    function onUpdate(id){ //タスクの上書き保存を行う 
        if(inputContent.value == "" || inputLimit.value == ""){
            errMsg.value = 'タスクの内容と期限を入力してください。'
            isErrMsg.value = true;
            return;
        }
    }

    let isShowModel = ref(false);

    function ShowDeteteModel(id){
        isShowModel.value = true;
        deleteItemId = id;
        deleteItemContent = items.value[id].content;
    }

    function onDeleteItem(){
        //タスクを削除する処理
        //対象はdeleteItemID
        items.value.splice(deleteItemId, 1);
        isShowModal.value = false;
    }

    function onHideModal(){
        isShowModal.value = false;
    }
    let deleteItemId = ''; //削除対象のItemのID
    let deleteItemContent = ref(); //削除対象のItemの内容

    //IDを振り直す
    items.value = items.value.map((item, index) =>({
        id: index,
        content: item.content,
        limit: item.limit,
        state: item.state,
        onEdit: item.onEdit,
    }));

    localStorage.setItem("items", JSON.stringify(items.value));

    let isOnEditOther = false;

    items.value.map((item) =>{
        if(item.onEdit){
            isOnEditOther = true;
            return;
        }
    });

    /*if(isOnEditOther){
        //エラーメッセージを表示
        return;
    }
    */
    

    /*let errMsg = ref(''); //エラーメッセージの内容
    if (isOnEditOther){
        errMsg.value = "他に編集中のタスクがあります";
        isErrMsg.value = true;
        return;
    }
    */
</script>

<template>
    <div>
        <table>
            <thread>
                <tr>
                    <th class="th-id">ID</th>
                    <th class="th-value">やること</th>
                    <th class="th-limit">期限</th>
                    <th class="th-state">状態</th>
                    <th class="th-edit">編集</th>
                    <th class="th-delete">削除</th>
                </tr>
            </thread>

            <!--タスク件数分表示-->
            <tr v-for = "item in items" :key="item.id">
                <td>{{item.id}}</td>
                <td>{{item.content}}</td>
                <td>{{item.limit}}</td>
                <td>{{item.state.value}}</td>
                <td><button>編集</button></td>
                <td><button>削除</button></td>

                <td>
                    <span v-if="!item.onEdit">{{item.content}}</span>
                    <input v-else v-model="inputContent" type="text"/>
                </td>

                <td>
                    <span v-if="!item.onEdit">{{item.limit}}</span>
                    <input v-else v-model="inputLimit" type="date"/>
                </td>

                <td>
                    <span v-if="!item.onEdit">{{item.state.value}}</span>
                    <select v-else v-model="inputState">
                        <option>
                            v-for="state in statuses"
                            :key="state.id"
                            :value="state"
                            :selected="state.id == item.state.id"
                        </option>
                    </select>
                    <input v-else type="text"/>
                </td>

                <td><button @click="onEdit(item.id)">編集</button></td>
                <td><button>削除</button></td>

                <td>
                    <button v-if="!item.onEdit" @click="onEdit(item.id)">編集</button>
                    <button v-else @click"onUpdate(item.id)">完了</button>
                </td>
                <p v-if="isErrMsg">{{errMsg}}</p>
            </tr>
        </table> 
    </div>

    <div v-if= "isShowModel" class="model">
        <div class = "model-content">
            <p>{{deleteItemContent}}削除してもよろしいですか？</p>
            <button @click="onDeleteItem()">はい</button>
            <button @click="onHideModal()">キャンセル</button>

            <td><button @click ="ShowDeteteModel(item.id)">削除</button></td>

        </div>
    </div>
</template>