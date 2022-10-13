<template>
    <div v-bind:class="{'text-violet-600': isActive}">{{message.user.name}}: {{message.message}} </div>
</template>

<script>
export default {
    props: ['message','user_now'],
    data()  {
        return { isActive: false }
    },
    created() {
     this.coloring();
    },
    methods: {
        coloring() {
            if(this.user_now.id == this.message.user_id){
            return this.isActive = true;
            }
        }
    }
}
</script>