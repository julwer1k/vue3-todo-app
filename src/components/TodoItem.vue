<script>
export default {
	name: 'TodoItem',
	props: {
		todo: Object,
	},
	emits: ['update', 'delete'],
	data() {
		return {
			editing: false,
			newTitle: this.todo.title
		};
	},
	
	methods: {
		toggle() {
			this.$emit('update', {
				...this.todo,
				completed: !this.todo.completed,
			});
		},
		rename() {
			if (!this.editing) {
				return;
			}
			
			this.editing = false;
			
			if (this.newTitle === this.todo.title) {
				return;
			}
			
			if (this.newTitle === '') {
				this.remove();
				
				return;
			}
			
			this.$emit('update', {
				...this.todo,
				title: this.newTitle,
			});
			
			this.newTitle = '';
		},
		remove() {
			this.$emit('delete');
		},
		edit() {
			this.newTitle = this.todo.title;
			
			this.editing = true;
			
			this.$nextTick(() => {
				this.$refs['title-field'].focus();
			})
		},
	},
};
</script>

<template>
	<div
		data-cy="Todo"
		class="todo"
		:class="{completed : todo.completed}"
	>
		<label class="todo__status-label">
			<input
				data-cy="TodoStatus"
				type="checkbox"
				class="todo__status"
				:checked="todo.completed"
				@change="toggle"
			/>
		</label>
		
		<form v-if="editing" @submit.prevent="rename">
			<input
				data-cy="TodoTitleField"
				type="text"
				class="todo__title-field"
				placeholder="Empty todo will be deleted"
				v-model.trim="newTitle"
				@keyup.esc="editing = false"
				@blur="rename"
				ref="title-field"
			/>
		</form>
		
		<template v-else>
			<span
				data-cy="TodoTitle"
				class="todo__title"
				@dblclick="edit"
			>
				{{ todo.title }}
			</span>
			
			<button
				@click="remove"
				type="button"
				class="todo__remove"
				data-cy="TodoDelete"
			>
				×
			</button>
		</template>
		
		
		<div
			data-cy="TodoLoader"
			class="modal overlay"
			:class="{ 'is-active': false}"
		>
			<div class="modal-background has-background-white-ter" />
			<div class="loader" />
		</div>
	</div>
</template>

<style scoped>

</style>