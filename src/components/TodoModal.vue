<template>
    <div class="modal-container" v-if="localVisible">
        <!-- при нажатии в любом месте фона модальное окно закроется -->
        <div class="modal-bg" @click="closeModal"></div>
        <div class="modal">
            <h2 class="modal__title title">{{ modalTitle }}</h2>
            <input type="text" class="modal__note-title" v-model="localNote.title" placeholder="Введите заголовок заметки">
            <div v-for="todo in localNote.todos" :key="todo.id" class="modal__note-todo todo">
                <div class="modal__note-checker checker" @click="toggleChecker(todo)" :class="{'done': todo.done }" />
                <input type="text" class="modal__note-task task" v-model="todo.task" placeholder="Введите задачу">
            </div>
            <button class="base-button" @click="addTask">
                <PlusIcon />
                Добавить задачу
            </button>
            <button class="modal__submit-button" @click="submitAction">{{ buttonText }}</button>
        </div>
    </div>
</template>

<script>
import PlusIcon from "@/components/icons/PlusIcon.vue";

export default {
	name: 'EditTodoModal',
    components: { PlusIcon },
    props: {
        visible: Boolean,
        note: Object,
    },
	data() {
		return {
            localNote: {
                id: 0,
                title: "",
                todos: []
            }
        }
    },
    computed: {
        /* текст заголовка и кнопки определяется в зависимости от того, есть ли содержимое в переданной заметке
        если передана заметка - модалка изменения, если передано null - создания заметки */
        modalTitle() {
            if (this.note) {
                return 'Изменение заметки'
            } else {
                return 'Создание заметки'
            }
        },
        buttonText() {
            if (this.note) {
                return 'Изменить заметку'
            } else {
                return 'Создать заметку'
            }
        },
        localVisible: {
            /* для выбора, отображать или нет модальное окно, используется переданная переменная visible
            при изменении значения localVisible будет отправлен эмит, сообщающий о необходимости обновить видимость модалки
            потому что напрямую отсюда visible изменять нельзя - это приведет к мутации пропса и непредсказуемым последствиям !! */
            get: function () {
                return this.visible
            },
            set: function (newValue) {
                this.$emit('update:visible', newValue);
            }
        }
    },
    watch: {
        localVisible() {
            if (this.visible) {
                // как только модалка становится видна, нужно обновить значение localmodel
                this.resetLocalModel();
            }
        },
    },
    methods: {
        resetLocalModel() {
            /* если была передана заметка, то это её редактирование и нужно "скопировать" значение переданной заметки,
            чтобы отобразить его в модалке, но сохранить возможность изменять его в дальнейшем
            если не использовать спреды, то тудушки сохранятся как ссылки, а не как "копии",
            и при изменениях внутри модалки сразу будут изменяться и исходные значения внутри карточек на главной */
            if (this.note) {
                this.localNote = { ...this.note, todos: this.note.todos.map(todo => {return {...todo}}) }
            } else {
                this.localNote = {
                    id: 0,
                    title: "",
                    todos: []
                }
            }
        },

        addTask() {
            this.localNote.todos.push({id: new Date().valueOf(), done: false, task: ""});
        },

        toggleChecker(todo) {
            todo.done ? todo.done = false : todo.done = true;
        },

        submitAction() {
            // в зависимости от наличия переданной заметки определяется, какой именно эмит нужно отправить, и нужно ли создавать новый id
            const task = {
                id: this.note ? this.note.id : new Date().valueOf(),
                title: this.localNote.title,
                todos: this.localNote.todos
            };
            this.note ? this.$emit('changeTodo', task) : this.$emit('createTodo', task);
            this.closeModal();
        },

        closeModal() {
            this.localVisible = false;
        }
    }
}
</script>

<style lang="scss">
@import '@/assets/main.scss';

.modal {
    &__title {
        text-align: center;
    }

    &__note-title {
        margin-bottom: 16px;
        font-size: 20px;
        font-weight: bold;
        color: #2c3e50;

        &::placeholder {
            font-weight: normal;
            color: #5f6f80;
        }
    }

    &__note-checker {
        cursor: pointer;
    }

    &__note-todo:last-of-type {
        margin-bottom: 16px;
    }

    &__note-task {
        width: 100%;
    }

    &__submit-button {
        margin-top: 16px;
        height: 40px;
        color: white;
        font-size: 16px;
        font-weight: bold;
        border: none;
        background: #65CC69;
        border-radius: 8px;
        cursor: pointer;
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    .base-button {
        height: 40px;
        margin-bottom: 0;
    }
}

input[type="text"] {
    border: none;
    outline: none;
}
</style>