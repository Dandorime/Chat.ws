<template>
    <AppLayout title="Dashboard">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                <ChatRoomSelection
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
                    <div>
                        <MessageContainer :messages="messages"/>
                        <inputMessage 
                        :room="currentRoom"
                        v-on:messagesent="getMessage()" 
                        />
                        
                    </div>
                </div>
            </div>
        </div>
    </AppLayout>
</template>

<script >
import AppLayout from '@/Layouts/AppLayout.vue';
import ChatRoomSelection from './chatRoomSelection.vue';
import MessageContainer from './messageContainer.vue';
import inputMessage from './inputMessage.vue';
import axios from 'axios';

export default {
    components: {
        AppLayout,
        MessageContainer,
        inputMessage,
        ChatRoomSelection
    }, 
    data() {
        return {
            chatRooms: [],
            currentRoom: [],
            messages: []
        }
    },
    created() {
        this.getRooms();
    },
    watch: {
        currentRoom(val, oldVal) {
            if (oldVal.id) {
                this.disconnect(oldVal);
            }
            this.connect();
        }
    },
    methods: {
        connect() {
            if(this.currentRoom.id) {
                let vm = this;
                this.getMessage();  
                window.Echo.private("chat." + this.currentRoom.id)
                .listen('.message.new', () =>{  
                    vm.getMessage();
                });
            }
        },
        disconnect(room) {
            window.Echo.leave('chat.' + room.id);
        },
        getRooms() {
            axios.get("/chat/rooms")
            .then(res => {
                this.chatRooms = res.data;
                this.setRoom(res.data[0]);
            })
            .catch(err =>{
                console.log(err);
            })
        },
        setRoom(room) {
            this.currentRoom = room;
        },
        getMessage() {
            axios.get("/chat/room/" + this.currentRoom.id + "/messages")
            .then(res => {
                this.messages = res.data;
            })
            .catch(err =>{
                console.log(err);
            })
        }
    }
}
</script>