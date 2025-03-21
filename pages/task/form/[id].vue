<template>
    <div>
        <h2>{{ id ? 'タスク編集' : 'タスク登録' }}</h2>
        <form @submit.prevent="submitTask">
            <div>
                <label>タスク名*</label>
                <input v-model="task.task_name" required>
            </div>

            <div>
                <label>開始日</label>
                <input v-model="task.start_date" type="date">
            </div>

            <div>
                <label>期限日</label>
                <input v-model="task.deadline" type="date">
            </div>

            <div>
                <label>
                    <input v-model="task.complete_flag" type="checkbox"> 完了フラグ
                </label>
            </div>

            <div>
                <label>コメント</label>
                <textarea v-model="task.comment" />
            </div>

            <button type="button" @click="router.back()">戻る</button>
            <button type="submit">保存</button>
        </form>
    </div>
</template>

<script setup>
import { reactive } from 'vue';
import { useRouter, useRoute } from 'vue-router';

const router = useRouter();
const route = useRoute();
const id = Number(route.params.id);

// 編集時はAPIから既存データを取得、新規作成時は空のフォーム
const { data: taskData } = id
    ? await useFetch(`http://localhost:8080/api/tasks/${id}`)
    : { data: reactive({ task_name: '', start_date: '', deadline: '', complete_flag: false, comment: '' }) };

const task = reactive({ ...taskData.value });

const submitTask = async () => {
    await $fetch(`http://localhost:8080/api/tasks${id ? '/' + id : ''}`, {
        method: id ? 'PUT' : 'POST',
        body: task,
    });
    router.push('/');
};
</script>