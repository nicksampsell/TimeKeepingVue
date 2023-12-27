<script setup>

import { reactive, ref, onMounted, getCurrentInstance } from 'vue';

const emit = defineEmits(['update-entry', 'add-entry', 'delete-entry', 'clear-entry'])

const props = defineProps([
    'totalEntries',
    'entryIndex',
    'entryId',
    'entryDate',
    'departmentId',
    'entryHours',
    'entryNotes',
    'allDepartments'
]);

const instance = getCurrentInstance();


const inputValue = ref(props.entryHours);
let timeout;

const displayDayOfWeek = (selectedDate = null) =>
{
    if (selectedDate) {
        const date = new Date(selectedDate);
        const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
        const dayOfWeek = days[date.getDay()];
        return dayOfWeek;
    }
    
    return 'Required'
}


const roundToNearestQuarter = (value) => {
  if (!isNaN(value)) {
    const roundedValue = Math.round(value * 4) / 4;
    return roundedValue.toFixed(2);
  }
  return null;
};

const handleHoursInput = (event) => {
  clearTimeout(timeout); // Clear the existing timeout

  // Set a new timeout to execute the rounding function after 500 milliseconds
  timeout = setTimeout(() => {
    inputValue.value  = roundToNearestQuarter(event.target.value);
    emit('update-entry', props.entryIndex, 'hours', inputValue.value);
    
    //this is a hack, but I with the way I did things, I don't know of a better way at the moment.
    instance?.proxy?.$forceUpdate();
  }, 500);
};


</script>

<template>
<tr style="vertical-align: middle;">
    <td>
        <input type="date" :value="props.entryDate" @input="e => $emit('update-entry',props.entryIndex, 'date', e.target.value)" class="form-control" required/>
    </td>
    <td class="text-center">{{ displayDayOfWeek(props.entryDate) }}</td>
    <td>
        <select @change="e => $emit('update-entry',props.entryIndex, 'departmentId', e.target.value)" :value="props.departmentId" class="form-select" required>
            <option hidden disabled selected>Select a department</option>
            <optgroup :label="optgroup.label" v-for="optgroup in props.allDepartments">
                <template v-for="dept in optgroup.options">
                    <option :value="dept.departmentId">{{ dept.title  }}</option>
                </template>
            </optgroup>
        </select>
    </td>
    <td>
        <input type="number" min="0" max="24" step="0.25" :value="props.entryHours" @input="handleHoursInput" class="form-control" style="width: 120px;" required/>
    </td>
    <td>
        <textarea rows="2" cols="30" @input="e => $emit('update-entry',props.entryIndex, 'notes', e.target.value)" class="form-control">{{ props.entryNotes }}</textarea>
    </td>
    <td>
        <div class="btn-group">
        <button type="button" @click="$emit('add-entry')" class="btn btn-outline-success btn-sm">Add</button>

        <template v-if="totalEntries > 1">
            <button type="button" @click="$emit('delete-entry', props.entryIndex)" class="btn btn-outline-danger btn-sm">Remove</button>
        </template>
        <template v-else>
            <button type="button" @click="$emit('clear-entry', props.entryIndex)" class="btn btn-outline-danger btn-sm">Clear</button>
        </template>
    </div>
    </td>
</tr>
</template>

<style scoped>

</style>