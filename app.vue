<script setup lang="ts">
import { createUrlSearchParams, debounce } from "@acdh-oeaw/lib";
import Checkbox from "./components/ui/checkbox/Checkbox.vue";

const languages = {
	en: "English",
	de: "German",
	it: "Italian",
};

const router = useRouter();
const route = useRoute();

const setSearchParams = debounce(function setSearchParams(element: HTMLFormElement) {
	const formData = new FormData(element);
	const q = formData.get("q") as string;
	const language = formData.getAll("language") as string[];

	void router.push({ query: { q, language } });
}, 150);

function onSubmit(event: Event) {
	/**
	 * Note that `event.currentTarget` will be set to `null` once the event has finished propagating,
	 * so we cannot debounce `onSubmit` itself.
	 */
	const element = event.currentTarget as HTMLFormElement;
	setSearchParams(element);
}

const searchParams = computed(() => {
	const searchParams = createUrlSearchParams(route.query);
	const q = searchParams.get("q") ?? "";
	const language = searchParams.getAll("language");
	return { q, language };
});
</script>

<template>
	<main class="p-8">
		<form class="grid gap-y-6" role="search" @input="onSubmit" @submit.prevent="onSubmit">
			<label>
				<span>Search</span>
				<input name="q" type="search" :value="searchParams.q" />
			</label>

			<fieldset class="grid gap-y-2">
				<legend>Language (native)</legend>
				<label
					v-for="(label, language) of languages"
					:key="language"
					class="flex gap-x-1 items-center"
				>
					<input
						:checked="searchParams.language.includes(language)"
						name="language"
						type="checkbox"
						:value="language"
					/>
					<span>{{ label }}</span>
				</label>
			</fieldset>

			<fieldset class="grid gap-y-2">
				<legend>Language (shadcn-vue)</legend>
				<label
					v-for="(label, language) of languages"
					:key="language"
					class="flex gap-x-1 items-center"
				>
					<Checkbox
						:default-checked="searchParams.language.includes(language)"
						name="language"
						:value="language"
					/>
					<span>{{ label }}</span>
				</label>
			</fieldset>

			<div>
				<button type="submit">Submit</button>
			</div>
		</form>
	</main>
</template>
