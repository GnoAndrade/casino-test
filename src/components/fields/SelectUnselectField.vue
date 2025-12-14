<template>
    <div class="select-unselect-container">
        <div class="options-column">
            <h3>Available Options</h3>
            <div class="options-box">
                <div 
                    v-for="option in availableOptions" 
                    :key="option.id"
                    class="option-item"
                    @click="moveToDisabled(option)"
                >
                    {{ option.label }}
                </div>
            </div>
        </div>
        <div class="options-column">
            <h3>Disabled options</h3>
            <div class="options-box">
                <div 
                    v-for="option in disabledOptions" 
                    :key="option.id"
                    class="option-item"
                    @click="moveToAvailable(option)"
                >
                    {{ option.label }}
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';
import fieldMixin from '../FieldMixin';

const emit = defineEmits(['update']);

const props = defineProps({
    field: {
        type: Object,
        required: true,
    },
    modelValue: {
        type: [String, Number, Boolean, Object, Array],
        default: () => [],
    },
});

const availableOptions = ref([]);
const disabledOptions = ref([]);

let { handleChange } = fieldMixin.setup(props, { emit });

// Initialize options
const initializeOptions = () => {
    const allOptions = props.field.options || [];
    const selectedIds = Array.isArray(props.modelValue) ? props.modelValue : [];
    
    availableOptions.value = allOptions.filter(opt => !selectedIds.includes(opt.id));
    disabledOptions.value = allOptions.filter(opt => selectedIds.includes(opt.id));
};

onMounted(() => {
    initializeOptions();
});

watch(() => props.modelValue, () => {
    initializeOptions();
});

const moveToDisabled = (option) => {
    availableOptions.value = availableOptions.value.filter(opt => opt.id !== option.id);
    disabledOptions.value.push(option);
    updateValue();
};

const moveToAvailable = (option) => {
    disabledOptions.value = disabledOptions.value.filter(opt => opt.id !== option.id);
    availableOptions.value.push(option);
    updateValue();
};

const updateValue = () => {
    const selectedIds = disabledOptions.value.map(opt => opt.id);
    emit('update', {
        id: props.field.id,
        value: selectedIds
    });
};

const setSelected = (value) => {
    // This method is called by parent component if needed
    initializeOptions();
};

defineExpose({
    setSelected
});
</script>

<style scoped>
.select-unselect-container {
    display: flex;
    gap: 20px;
    width: 100%;
    justify-content: space-between;
}

.options-column {
    flex: 1;
    text-align: center;
}

.options-column h3 {
    font-size: 1.2em;
    margin-bottom: 10px;
    color: rgba(255, 255, 255, 0.87);
}

.options-box {
    border: 1px solid #444;
    background: #1a1a1a;
    min-height: 200px;
    padding: 10px;
    border-radius: 4px;
    text-align: left;
}

.option-item {
    padding: 8px 12px;
    margin-bottom: 5px;
    cursor: pointer;
    background: #2a2a2a;
    border-radius: 4px;
    transition: background-color 0.2s;
}

.option-item:hover {
    background: #3a3a3a;
}

.option-item:last-child {
    margin-bottom: 0;
}
</style>
