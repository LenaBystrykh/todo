<template>
    <div class="note" @click="editNote">
        <div class="note__header">
            <h2 class="title">{{ note.title }}</h2>
            <DeleteIcon class="note__icon" @deleteNote="deleteNote" />
        </div>
        <div v-for="todo in note.todos" :key="todo.id" class="todo">
            <div class="checker" :class="{'done': todo.done }" />
            <p class="task">{{ todo.task }}</p>
        </div>
    </div>
</template>

<script>
import DeleteIcon from "@/components/icons/DeleteIcon.vue";

export default {
	name: 'TodoItem',
    components: { DeleteIcon },
    props: {
        note: Object,
    },
    methods: {
        editNote() {
            // при клике по заметке отправляется эмит о необходимости открыть окно её редактирования
            this.$emit('editNote', this.note);
        },

        deleteNote(event) {
            /* но если клик был совершен именно по иконке удаления, то stopPropagation предотвратит всплытие клика, 
            и editNote не будет вызвана, отправится только эмит о необходимости открыть модалку удаления заметки */
            event.stopPropagation();
            this.$emit('deleteNote', this.note);
        }
    }
}
</script>

<style lang="scss">
@import '@/assets/main.scss';

.note {
    background-color: white;
    padding: 16px;
    border-radius: 16px;
    margin-bottom: 16px;

    &__header {
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
    }

    &__icon {
        cursor: pointer;
    }
}
</style>