<template>
    <div>
        <h2>ユーザ一覧</h2>
        <button @click="router.push('/user/form/0')">新規</button>
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
    </div>
</template>

<script setup>
const router = useRouter();
const { data: users, refresh } = await useFetch('http://localhost:8080/api/users', { initialData: [] });

const deleteUser = async (id) => {
    if (confirm('削除しますか？')) {
        await $fetch(`http://localhost:8080/api/users/${id}`, { method: 'DELETE' });
        refresh();
    }
};
</script>