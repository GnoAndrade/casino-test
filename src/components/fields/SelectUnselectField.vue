<template>
    <div class="select-unselect-container form-group">
        <div class="container-options">
            <span>Available Options</span>
            <div class="column">
                <ul class="options-list">
                    <li v-for="option in availableOptions" :key="option.value" @click="switchOption(option, 'available')"
                        class="option-item">
                        {{ option.label }}
                    </li>
                </ul>
            </div>
        </div>
        <div class="container-options">

            <span>Disabled Options</span>
            <div class="column">
                <ul class="options-list">
                    <li v-for="option in disabledOptions" :key="option.value" @click="switchOption(option, 'disabled')"
                        class="option-item">
                        {{ option.label }}
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({
    field: {
        type: Object,
        required: true
    },
    modelValue: {
        type: Array,
        default: () => []
    }
})

const emit = defineEmits(['update'])

const availableOptions = ref([...props.field.options])
const disabledOptions = ref([])

watch(() => props.field.options, (newOptions) => {
    if (newOptions) {
        availableOptions.value = [...newOptions]
        disabledOptions.value = []
    }
}, { immediate: true })

const switchOption = (option, fromList) => {
    if (fromList === 'available') {
        availableOptions.value = availableOptions.value.filter(opt => opt.label !== option.label)
        disabledOptions.value.push(option)
    } else {
        disabledOptions.value = disabledOptions.value.filter(opt => opt.label !== option.label)
        availableOptions.value.push(option)
    }
    sendParent()
}

const sendParent = () => {
    emit('update', {
        id: props.field.id,
        value: disabledOptions.value.map(el => el.id)
        
    })
}
</script>

<style scoped>
.select-unselect-container {
    display: flex;
    align-items: center;
    gap: 20px;
    width: 100%;}

.container-options {
    display: flex;
    flex-direction: column;
    width: 50%;
}

.column {
    border: 1px solid #ccc;
    padding: 10px;
    border-radius: 4px;
    height: 150px;
    overflow-y: auto;
}

h3 {
    margin: 0;
    font-size: 16px;
}

.options-list {
    list-style: none;
    padding: 0;
    margin: 0;
    min-height: 100px;
    text-align: left;
}

.option-item {
    padding-bottom: 4px;
    border-radius: 4px;
    cursor: pointer;
}
</style>