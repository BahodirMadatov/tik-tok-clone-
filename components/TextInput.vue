<template>
    <div>
        <input 
            :id="`input-${placeholder}`"
            :placeholder="placeholder"
            :type="inputType"
            v-model="inputComputed"
            autocomplete="off"
            :maxlength="max"
            class="block w-full bg-[#F1F1F2] text-gray-800 border border-gray-300 rounded-md py-2.5 px-3 focus:outline-none"
        >

        <span v-if="error" class="text-red-500 text-[14px] font-semibold">
        {{ error }}
        </span>
    </div> 
</template>

<script setup>
const emit = defineEmits(['update:input'])

const props = defineProps(['input', 'placeholder', 'inputType', 'error', 'autoFocus', 'max']) 
const { input, placeholder, inputType, error, autoFocus, max } = toRefs(props)

onMounted(() => {
    if (autoFocus.value) {
        document.getElementById(`input-${placeholder.value}`).focus()
    }
})

const inputComputed = computed({
    get: () => input.value,
    set: (val) => emit('update:input', val)
})
</script>
