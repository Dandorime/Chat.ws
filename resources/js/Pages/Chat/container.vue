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

        <div class="py-12" v-bind:class="{ 'animate-pulse': request}">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg ">
                    <div>
                        <MessageContainer :messages="messages" :user_now="user_now" :lastMessage="lastMessage"/>
                        <inputMessage 
                        :room="currentRoom"
                        v-on:messagesent="getLastMessage()"
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
            request: false,
            user_now: this.$attrs.user,
            chatRooms: [],
            currentRoom: [],
            messages: [],
            lastMessage: null
        }
    },
       // data()  {
    //     return {  }
    // },
    // created() {
    //     
    // },
    // methods: {
    //     coloring() {
    //         if(this.user_now.id == this.message.user_id){
    //         this.isActive = true;
    //         }
    //     }
    // }
    created() {
        this.getRooms();
        this.request = true;
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
        // coloring() {
        //     if(this.user_now.id == this.messages.user_id){
        //     this.isActive = true;
        //     }
        // },
        connect() {
            if(this.currentRoom.id) {
                let vm = this;
                window.Echo.private("chat." + this.currentRoom.id)
                .listen('.message.new', () =>{  
                    vm.getLastMessage();
                });
                this.getMessage();  
 
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
                // this.coloring();
                this.request = false;
            })
            .catch(err =>{
                console.log(err);
            })
        },
        getLastMessage() {
            let newObject = {};
            axios.get("/chat/room/" + this.currentRoom.id + "/message")
            .then(res => {
                this.lastMessage = res.data;
                var object = res.data;
                this.messages.unshift(object);
            })
            .catch(err =>{
                console.log(err);
            })
        }
    }
}
</script>