<template>
    <div>
        <nav>
            <ul>
                <li>
                    <h2>ユーザ一覧</h2>
                </li>
                <li>
                    <NuxtLink to="/">タスク一覧</NuxtLink>
                </li>
                <li>
                    <NuxtLink to="/user">ユーザー一覧</NuxtLink>
                </li>
            </ul>
        </nav>

        <button @click="router.push('/user/form/0')">新規</button>
        <button @click="showModal = true">検索</button>
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>ユーザー名</th>
                    <th>メモ</th>
                    <th>操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="user in users" :key="user.id">
                    <td>{{ user.id }}</td>
                    <td>{{ user.user_name }}</td>
                    <td>{{ user.memo }}</td>
                    <td>
                        <button @click="router.push(`/user/form/${user.id}`)">更新</button>
                        <button @click="deleteUser(user.id)">削除</button>
                    </td>
                </tr>
                <tr v-if="!users.length">
                    <td colspan="4">データがありません。</td>
                </tr>
            </tbody>
        </table>

        <!-- 検索モーダル -->
        <div v-if="showModal" class="modal-overlay">
            <div class="modal">
                <h3>ユーザ検索</h3>
                <input v-model="searchKeyword" placeholder="ユーザー名で検索">
                <button @click="searchUsers">検索する</button>
                <button @click="showModal = false">閉じる</button>
            </div>
        </div>
    </div>
</template>

<script setup>
const router = useRouter();
const showModal = ref(false);
const searchKeyword = ref('');
const { data: users, refresh } = await useFetch('http://localhost:8080/api/users', { initialData: [] });

const searchUsers = async () => {
    const { data } = await useFetch(`http://localhost:8080/api/users/search?user_name=${encodeURIComponent(searchKeyword.value)}`);
    users.value = data.value;
    showModal.value = false;
};

const deleteUser = async (id) => {
    if (confirm('削除しますか？')) {
        await $fetch(`http://localhost:8080/api/users/${id}`, { method: 'DELETE' });
        refresh();
    }
};
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