<template>
    <div>{{ message.user.name }}: {{ message.message}}</div>
</template>

<script>
const MessageItem = {
    props: ["message"],
};
export default MessageItem;
</script>
