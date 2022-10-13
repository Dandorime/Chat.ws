<template>
    <div class="grid grid-cols-2">
        <div class="font-bold text-xl">
            {{selected.name}} Chat
        </div>
        <select 
            v-model="selected"
            @change="$emit('roomchanged', selected)"
            class="w-48 col-end-4 form-select text-sm font-medium text-gray-900 bg-white rounded-lg border border-gray-200 dark:bg-gray-700 dark:border-gray-600 dark:text-white">
            <option 
                v-for="(room, index) in rooms"
                :key="index"
                :value="room">
                {{room.name}}
            </option>
        </select>
        
    </div>
</template>
 
<script>
export default {
    props: ['rooms', 'currentRoom'],
    data: function() {
        return {
            selected: ''
        }
    },
    created() {
        this.selected = this.currentRoom;
    }
}
</script>