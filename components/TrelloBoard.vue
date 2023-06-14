<script lang="ts" setup>
import { nanoid } from "nanoid";
import draggableComponent from "vuedraggable";

import type { Column, Task } from "@/types";

const columns = ref<Column[]>([
	{
		id: nanoid(),
		title: "Backlog",
		tasks: [
			{
				id: nanoid(),
				title: "Create marking landing page",
				created: new Date(),
			},
			{ title: "Develop cool new feature", created: new Date(), id: nanoid() },
			{ title: "Fix page nav bug", created: new Date(), id: nanoid() },
		],
	},
	{ title: "Selected for Dev", id: nanoid(), tasks: [] },
	{ title: "In Progress", id: nanoid(), tasks: [] },
	{ title: "QA", id: nanoid(), tasks: [] },
	{ title: "Complete", id: nanoid(), tasks: [] },
]);

const alt = useKeyModifier("Alt");

function createColumn() {
	const column: Column = {
		id: nanoid(),
		title: "",
		tasks: []
	};

	columns.value.push(column);

	nextTick(() => {
		(document.querySelector(".column:last-of-type .title-input") as HTMLInputElement).focus();
	});
}
</script>

<template>
	<div class="flex items-start overflow-x-auto gap-4">
		<draggableComponent
			v-model="columns"
			group="columns"
			:animation="400"
			handle=".drag-handle"
			item-key="id"
			class="flex gap-4 items-start"
		>
			<template #item="{ element: column }: { element: Column }">
				<div class="column bg-gray-200 p-5 rounded min-w-[250px]">
					<header class="font-bold mb-4">
						<DragHandle />
						<input
							class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
							@keyup.enter="($event.target as HTMLInputElement).blur()"
							@keydown.backspace="column.title === '' ? columns = columns.filter((c) => c.id !== column.id) : null"
							type="text"
							v-model="column.title"
						/>
					</header>

					<draggableComponent
						v-model="column.tasks"
						:group="{ name: 'tasks', pull: alt ? 'clone' : true }"
						:animation="400"
						handle=".drag-handle"
						item-key="id"
					>
						<template #item="{ element: task }: { element: Task }">
							<div>
								<TrelloBoardTask :task="task" @delete="column.tasks = column.tasks.filter((t) => t.id !== $event)" />
							</div>
						</template>
					</draggableComponent>

					<footer>
						<NewTask @add="column.tasks.push($event)" />
					</footer>
				</div>
			</template>
		</draggableComponent>

		<button
			@click="createColumn"
			class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
		>
			+ Add New Column
		</button>
	</div>
</template>

<style>
.sortable-drag .task {
	transform: rotate(5deg);
}

.sortable-ghost .task {
	position: relative;
}

.sortable-ghost .task::after {
	content: "";
	@apply absolute top-0 bottom-0 left-0 right-0 bg-slate-300 rounded;
}
</style>
