<template>
    <div class="h-96 w-full">
        <div class="h-full p-2 flex flex-col-reverse overflow-y-scroll scrollbar scrollbar-w-1 scrollbar-thumb-violet-200 ">
            <!-- <div v-if="lastMessage">
                <MessageItem :message="lastMessage" class="text-violet-600"/>
            </div> -->
            <div v-for="(messages,index) in messages" :key="index">
                <MessageItem :message="messages" :user_now="user_now" />
            </div>
        </div>
    </div>
</template>

<script>
import MessageItem from './messageItem.vue';
export default {
    props: ["messages","user_now", "lastMessage"],
    components: { MessageItem },
}
</script>