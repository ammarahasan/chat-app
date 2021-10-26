<template>
    <app-layout title="Dashboard">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                <chat-room-selection
                    v-if="currentRoom.id"
                    :rooms="chatRooms"
                    :currentRoom="currentRoom"
                    v-on:roomchanged="setRoom($event)"
                />
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
                    <message-container :messages="messages" />
                    <input-message
                        :room="currentRoom"
                        v-on:messagesent="getMessages()"
                    />
                </div>
            </div>
            <div></div>
        </div>
    </app-layout>
</template>

<script>
import { defineComponent } from "vue";
import AppLayout from "@/Layouts/AppLayout.vue";
import MessageContainer from "./MessageContainer";
import InputMessage from "./InputMessage";
import ChatRoomSelection from "./ChatRoomSelection";

export default defineComponent({
    data: function () {
        return {
            chatRooms: [],
            currentRoom: [],
            messages: [],
        };
    },
    components: {
        AppLayout,
        "message-container": MessageContainer,
        "input-message": InputMessage,
        "chat-room-selection": ChatRoomSelection,
    },
    watch: {
        currentRoom(val, oldVal) {
            if (oldVal.id) {
                this.disconnect(oldVal);
            }
            console.log("go to get messages");
            this.connect();
        },
    },
    methods: {
        connect() {
            console.log("connect");
            if (this.currentRoom.id) {
                console.log("in current room");
                let vm = this;
                this.getMessages();
                window.Echo.private("chat." + this.currentRoom.id).listen(
                    ".message.new",
                    (e) => {
                        console.log("e => vm.getMessages()");
                        vm.getMessages();
                    }
                );
            }
        },
        disconnect(room) {
            window.Echo.leave("chat." + room.id);
        },
        getRooms() {
            axios
                .get("/chat/rooms")
                .then((response) => {
                    this.chatRooms = response.data;
                    this.setRoom(response.data[0]);
                })
                .catch((error) => {
                    console.log("error");
                });
        },
        setRoom(room) {
            this.currentRoom = room;
            // this.getMessages();
        },
        getMessages() {
            axios("/chat/room/" + this.currentRoom.id + "/messages")
                .then((response) => {
                    this.messages = response.data;
                })
                .catch((error) => {
                    console.log(error);
                });
        },
    },
    created() {
        this.getRooms();
    },
});
</script>
