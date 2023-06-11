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
</script>

<template>
	<div>
		<draggableComponent
			v-model="columns"
			group="columns"
			:animation="400"
			handle=".drag-handle"
			item-key="id"
			class="flex gap-4 overflow-x-auto items-start"
		>
			<template #item="{ element: column }: { element: Column }">
				<div class="bg-gray-200 p-5 rounded min-w-[250px]">
					<header class="font-bold mb-4">
						<DragHandle />
						{{ column.title }}
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
								<TrelloBoardTask :task="task" />
							</div>
						</template>
					</draggableComponent>

					<footer>
						<button class="text-gray-500">+ Add New Task</button>
					</footer>
				</div>
			</template>
		</draggableComponent>
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
