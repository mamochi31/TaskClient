<template>
    <div>
        <h2>{{ id ? 'ユーザ編集' : 'ユーザ登録' }}</h2>
        <form @submit.prevent="submitUser">
            <div>
                <label>ユーザー名*</label>
                <input v-model="user.user_name" required>
            </div>
            <div>
                <label>メモ</label>
                <textarea v-model="user.memo" />
            </div>
            <button type="button" @click="router.back()">戻る</button>
            <button type="submit">保存</button>
        </form>
    </div>
</template>

<script setup>
const router = useRouter();
const route = useRoute();
const id = Number(route.params.id);

const { data: userData } = id !== 'new'
    ? await useFetch(`http://localhost:8080/api/users/${id}`)
    : { data: ref({ user_name: '', memo: '' }) };

const user = reactive({ ...userData.value });

const submitUser = async () => {
    await $fetch(`http://localhost:8080/api/users${id ? '/' + id : ''}`, {
        method: id ? 'PUT' : 'POST',
        body: user,
    });
    router.push('/user');
};
</script>