<template>
    <div>
        <!-- ヘッダー -->
        <nav>
            <ul>
                <li>
                    <NuxtLink to="/">タスク一覧</NuxtLink>
                </li>
                <li>
                    <NuxtLink to="/user">ユーザー一覧</NuxtLink>
                </li>
            </ul>
        </nav>

        <!-- 登録・検索ボタン -->
        <div class="actions">
            <button @click="router.push('/task/form/0')">新規</button>
            <button @click="showModal = true">検索</button>
        </div>

        <!-- 一覧 -->
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>タスク名</th>
                    <th>開始日</th>
                    <th>期限日</th>
                    <th>完了フラグ</th>
                    <th>コメント</th>
                    <th>操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="task in tasks" :key="task.id">
                    <td>{{ task.id }}</td>
                    <td>{{ task.task_name }}</td>
                    <td>{{ task.start_date }}</td>
                    <td>{{ task.deadline }}</td>
                    <td>{{ task.complete_flag ? '完了' : '未完了' }}</td>
                    <td>{{ task.comment }}</td>
                    <td>
                        <button @click="router.push(`/task/form/${task.id}`)">更新</button>
                        <button @click="deleteTask(task.id)">削除</button>
                    </td>
                </tr>
                <tr v-if="!tasks.length">
                    <td colspan="7">データがありません。</td>
                </tr>
            </tbody>
        </table>

        <!-- 検索モーダル -->
        <div v-if="showModal" class="modal-overlay">
            <div class="modal">
                <h3>タスク検索</h3>
                <input v-model="searchKeyword" placeholder="タスク名で検索">
                <button @click="searchTasks">検索する</button>
                <button @click="closeModal">閉じる</button>
            </div>
        </div>

    </div>
</template>

<script setup>
const router = useRouter();

const { data: tasks, refresh } = await useFetch('http://localhost:8080/api/tasks', { initialData: [] });

const searchKeyword = ref('');

const closeModal = () => {
    showModal.value = false;
};

const searchTasks = async () => {
    const query = searchKeyword.value;
    const { data } = await useFetch(`http://localhost:8080/api/tasks/search?task_name=${encodeURIComponent(query)}`);
    tasks.value = data.value;
    closeModal();
};


const deleteTask = async (id) => {
    if (confirm('削除しますか？')) {
        await $fetch(`http://localhost:8080/api/tasks/${id}`, { method: 'DELETE' });
        refresh();
    }
};

const showModal = ref(false);
</script>

<style scoped>
nav ul {
    list-style: none;
    display: flex;
    gap: 15px;
}

.actions {
    margin: 10px 0;
    display: flex;
    gap: 10px;
}

table {
    width: 100%;
    border-collapse: collapse;
}

th,
td {
    border: 1px solid #ddd;
    padding: 8px;
}

th {
    background-color: #f2f2f2;
}

.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.modal {
    background: white;
    padding: 20px;
    border-radius: 5px;
}
</style>