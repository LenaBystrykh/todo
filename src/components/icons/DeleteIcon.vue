<template>
    <svg width="20" height="20" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg" @click="deleteNote">
        <path d="M7.5 10L8.5 15.5M12.5 10L11.5 15.5M4 7L6 18H14L16 7H4ZM4 4.5H16H12.5L11 2H9L7.5 4.5H4Z" stroke="#B84D4D" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </svg>
</template>

<script>
export default {
	name: 'DeleteIcon',
    methods: {
        deleteNote(event) {
            this.$emit('deleteNote', event);
        }
    }
}
</script>