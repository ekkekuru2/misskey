<template>
<Sortable :modelValue="modelValue" tag="div" itemKey="id" handle=".drag-handle" :group="{ name: 'blocks' }" :animation="150" :swapThreshold="0.5" @update:modelValue="v => $emit('update:modelValue', v)">
	<template #item="{element}">
		<div :class="$style.item">
			<!-- divが無いとエラーになる https://github.com/SortableJS/vue.draggable.next/issues/189 -->
			<component :is="'x-' + element.type" :modelValue="element" @update:modelValue="updateItem" @remove="() => removeItem(element)"/>
		</div>
	</template>
</Sortable>
</template>

<script lang="ts" setup>
import { defineAsyncComponent } from 'vue';
import XSection from './els/page-editor.el.section.vue';
import XText from './els/page-editor.el.text.vue';
import XImage from './els/page-editor.el.image.vue';
import XNote from './els/page-editor.el.note.vue';

const Sortable = defineAsyncComponent(() => import('vuedraggable').then(x => x.default));

const props = defineProps<{
	modelValue: any[];
}>();

const emit = defineEmits<{
	(ev: 'update:modelValue', value: any[]): void;
}>();

function updateItem(v) {
	const i = props.modelValue.findIndex(x => x.id === v.id);
	const newValue = [
		...props.modelValue.slice(0, i),
		v,
		...props.modelValue.slice(i + 1),
	];
	emit('update:modelValue', newValue);
}

function removeItem(el) {
	const i = props.modelValue.findIndex(x => x.id === el.id);
	const newValue = [
		...props.modelValue.slice(0, i),
		...props.modelValue.slice(i + 1),
	];
	emit('update:modelValue', newValue);
}
</script>

<style lang="scss" module>
.item {
	& + .item {
		margin-top: 16px;
	}
}
</style>
