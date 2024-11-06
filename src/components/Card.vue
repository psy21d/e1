<template>
    <div class="user-card">
        <div class="user-avatar">
            <img 
                class="avatar-img"
                :src="user.picture.thumbnail"
            >
        </div>
        <div class="user-info">
            <div class="user-credentials">
                {{ user.name.title }}
                {{ user.name.first }}
                {{ user.name.last }}
            </div>
            <div class="user-email">
                {{ user.email }}
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
    props: {
        user: {
            /** @type {User} */
            type: Object,
            required: true,
        },
    }
})
</script>

<style scoped>
.user-card {
    width: 400px;
    min-width: 320px;
    height: 60px;
    border-radius: 6px;
    padding: 12px;
    border: 1px solid #ccc;
    display: flex;
    justify-content: flex-start;
    gap: 12px;
    font-family: Arial, Helvetica, sans-serif;
    color: #666;
}
.user-avatar {
    border-radius: 50%;
    width: 60px;
    height: 60px;
    min-width: 60px;
    min-height: 60px;
    overflow: hidden;
}
.avatar-img {
    width: 60px;
    height: 60px;
}
.user-info {
    gap: 6px;
    display: flex;
    flex-flow: column;
    justify-content: space-around;
}
</style>