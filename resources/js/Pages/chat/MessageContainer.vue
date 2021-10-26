<template>
    <div class="h-96 w-full">
        <div class="h-full p-2 flex flex-col-reverse overflow-scroll">
            <div v-for="message in messages" :key="message.id">
                <message-item :message="message" />
            </div>
        </div>
    </div>
</template>

<script>
import MessageItem from "./MessageItem";
const MessageContainer = {
    components: {
        "message-item": MessageItem,
    },
    props: ["messages"],
};
export default MessageContainer;
</script>
