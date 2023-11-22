<template>
    <div class="modal-container" v-if="localVisible">
        <div class="modal-bg" @click="closeModal"></div>
        <div class="modal">
            <h2 class="modal__title title">Удаление заметки</h2>
            <p>Вы уверены, что хотите удалить заметку "{{ note.title }}"?</p>
            <div class="modal__delete-actions">
                <button class="modal__cancel" @click="closeModal">Отменить</button>
                <button class="modal__delete" @click="deleteNote">Удалить</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
	name: 'DeleteTodoModal',
    props: {
        visible: Boolean,
        note: Object,
    },
    computed: {
        localVisible: {
            get: function () {
                return this.visible
            },
            set: function (newValue) {
                this.$emit('update:visible', newValue);
            }
        }
    },
    methods: {
        deleteNote() {
            this.$emit('deleteNote', this.note);
            this.closeModal();
        },
        
        closeModal() {
            this.localVisible = false;
        }
    }
}
</script>

<style lang="scss">
.modal {
    &__delete-actions {
        display: flex;
        margin-top: 16px;
        gap: 16px;

        button {
            width: 50%;
            height: 40px;
            outline: none;
            border-radius: 8px;
            cursor: pointer;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
        }
    }

    &__delete {
        background: #B84D4D;
        color: white;
        font-size: 16px;
        font-weight: bold;
        border: none;
        transition: 0.4s;

        &:hover {
            color: #ffffffc2;
        }
    }

    
    &__cancel {
        background: none;
        color: #2d44c5;
        font-size: 16px;
        border: 1px solid #2d44c5;
        transition: 0.4s;

        &:hover {
            color: white;
            border: none;
            background: #2d44c5a6;
        }
    }
}
</style>