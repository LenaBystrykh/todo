<template>
	<div class="todo-app">
		<button class="base-button add-note-btn" @click="openAddModal">
			<PlusIcon />
			Добавить заметку
		</button>
		<div class="notes-container">
			<div v-for="note in notesNewFirst" :key="note.id">
				<!-- при нажатии на иконку всплывает подтверждение удаления заметки, при нажатии в любом другом месте карточки открывается модальное окно редактирования заметки -->
				<TodoItem :note="note" @editNote="openEditModal" @deleteNote="openDeleteModal" />
			</div>
		</div>
		<!-- модальные окна по умолчанию не видны и отображаются только при изменении соответствующих перменных visibility -->
		<!-- добавление новой заметки и редактирование существующей схожи, поэтому объединены в один компонент -->
		<TodoModal :visible="modalVisibility" :note="chosenNote" @createTodo="addNote" @changeTodo="editNote" @update:visible="updateModalVisibility" />
		<DeleteTodoModal :visible="deleteModalVisibility" :note="chosenNote"  @deleteNote="deleteNote" @update:visible="updateDeleteModalVisibility" />
	</div>
</template>

<script>
import TodoItem from './components/TodoItem.vue';
import TodoModal from './components/TodoModal.vue';
import DeleteTodoModal from './components/DeleteTodoModal.vue';
import PlusIcon from "@/components/icons/PlusIcon.vue";

export default {
	name: 'App',
	components: {
		TodoItem, TodoModal, DeleteTodoModal, PlusIcon
	},
	data() {
		return {
			notes: [
				{
					id: 1,
					title: 'Сделать MVP тудушки',
					todos: [
						{id: 1, done: true, task: 'Сверстать карточку'},
						{id: 2, done: true, task: 'Сверстать страницу'},
					]
				},
				{
					id: 2,
					title: 'Реализовать создание заметок',
					todos: [
						{id: 3, done: true, task: 'Сверстать кнопку создания'},
						{id: 4, done: true, task: 'Сверстать модалку'},
						{id: 5, done: true, task: 'Реализовать открытие/закрытие модалки'},
						{id: 6, done: true, task: 'Реализовать добавление тудушки в список'},
					]
				},
				{
					id: 3,
					title: 'Реализовать изменение заметок',
					todos: [
						{id: 7, done: true, task: 'Сверстать модалку'},
						{id: 8, done: true, task: 'Реализовать открытие модалки по нажатию на тудушку'},
						{id: 9, done: true, task: 'Реализовать обновление тудушки'},
					]
				},
				{
					id: 4,
					title: 'Реализовать удаление заметок',
					todos: [
						{id: 10, done: true, task: 'Сверстать модалку подтверждения'},
						{id: 11, done: true, task: 'Реализовать открытие/закрытие модалки'},
						{id: 12, done: true, task: 'Реализовать удаление тудушки из списка'},
					]
				},
				{
					id: 5,
					title: 'Реализовать трудоустройство было бы круто!',
					todos: [
						{id: 13, done: true, task: 'Выполнить тестовое'},
						{id: 14, done: false, task: 'Пройти собеседование'},
						{id: 15, done: false, task: 'Порадоваться'},
					]
				},
				{
					id: 6,
					title: 'Реализовать изменение заметок',
					todos: [
						{id: 7, done: true, task: 'Сверстать модалку'},
						{id: 8, done: true, task: 'Реализовать открытие модалки по нажатию на тудушку'},
						{id: 9, done: true, task: 'Реализовать обновление тудушки'},
					]
				},
				{
					id: 7,
					title: 'Реализовать удаление заметок',
					todos: [
						{id: 10, done: true, task: 'Сверстать модалку подтверждения'},
						{id: 11, done: true, task: 'Реализовать открытие/закрытие модалки'},
						{id: 12, done: true, task: 'Реализовать удаление тудушки из списка'},
					]
				},
				{
					id: 8,
					title: 'Реализовать трудоустройство было бы круто!',
					todos: [
						{id: 13, done: true, task: 'Выполнить тестовое'},
						{id: 14, done: false, task: 'Пройти собеседование'},
						{id: 15, done: false, task: 'Порадоваться'},
					]
				}
			],
			modalVisibility: false,
			deleteModalVisibility: false,
			// переменная для хранения выбранной заметки для реализации редактирования и удаления
			chosenNote: null
		}
	},
	computed: {
		/* сортировка списка заметок таким образом, чтобы последние добавленные были вверху
		здесь копируются объекты массива, а не ссылки на них */
		notesNewFirst() {
			let newNotes = this.notes.map(note => {return {...note, todos: note.todos.map(todo => {return {...todo}}) }})
            return newNotes.reverse();
        }
	},
	methods: {
		addNote(note) {
			this.notes.push(note);
		},

		updateModalVisibility(value) {
			this.modalVisibility = value;
			this.chosenNote = null;
		},

		updateDeleteModalVisibility(value) {
			this.deleteModalVisibility = value;
		},

		openAddModal() {
			this.modalVisibility = true;
		},

		openEditModal(note) {
			/* так как нужно открыть модальное окно редактирования заметки, выбранная заметка запоминается и передается как проп в компонент модалки
			аналогично строка 114, нужно запомнить, какую заметку необходимо удалить
			в случае с открытием модального окна создания заметки chosenNote остается пустой, но тоже передается в модалку
			на основании заполненности chosenNote совершается определение действия (создание/редактирование) внутри компонента
			поэтому важно своевременно очищать перменную после реализации или отмены редактирования/удаления (91, 126 строки) */
			this.chosenNote = note;
			this.modalVisibility = true;
		},

		openDeleteModal(note) {
			this.deleteModalVisibility = true;
			this.chosenNote = note;
		},

		editNote(note) {
			// для редактирования заметки выполняется её поиск в списке по идентификатору, и вся заметка заменяется на новое значение в списке по индексу
			let index = this.notes.findIndex(el => el.id === note.id);
			this.notes[index] = note;
		},

		deleteNote(note) {
			// для удаления заметки список перезаписывается и состоит из всех заметок, идентификаторы которых не совпадают с id удаляемой заметки
			this.notes = this.notes.filter((el) => { return el.id !== note.id });
			this.chosenNote = null;
		}
	}
}
</script>

<style>
@import '@/assets/main.scss';
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

#app {
	font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
	color: #2c3e50;
}

.todo-app {
	width: 100%;
	height: 100vh;
	background-color: rgb(241, 241, 241);
	padding: 20px 15%;
	position: relative;
	display: flex;
	flex-direction: column;

	.notes-container {
		flex: 1;
		/* скролл только внутри контейнера, скроллбар скрыт */
		overflow-y: auto;
		-ms-overflow-style: none;
		scrollbar-width: none;
	}

	.notes-container::-webkit-scrollbar {
		display: none;
	}
}
</style>
