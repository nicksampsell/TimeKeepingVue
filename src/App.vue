<script setup>
import axios from 'axios';
import FormItem from './components/FormItem.vue';
import { reactive } from 'vue';

const state = reactive({
  entries: [
  {
    id: '',
    date: '',
    hours: '',
    notes: '',
    departmentId: ''
  }],
  allDepartments: [
    { 
      label: 'Department',
      options: [
        { departmentId: 1, title: 'County Clerk' },
        { departmentId: 2, title: 'District Attorney' },
        { departmentId: 3, title: 'City of Elmira' }
      ]
    },
    { 
      label: 'PTO/Holidays',
      options: [
        { departmentId: 99, title: 'Sick Day' },
        { departmentId: 88, title: 'Holiday' }
      ]
    }
  ],
  deletedEntries: [],
  error: '',
  success: ''
});


function doUpdateEntry(index, column, value) {
  if (index >= 0 && index < state.entries.length) {
    // Clone the array
    const updatedEntries = [...state.entries];

    // Clone the object at the specified index
    const updatedObject = { ...updatedEntries[index] };

    // Update the property of the cloned object
    updatedObject[column] = value;

    // Replace the object at the specified index with the updated object
    updatedEntries[index] = updatedObject;

    // Set the state with the new array
    state.entries = updatedEntries;
  }
}
function doAddEntry()
{
  state.entries = [...state.entries, { 
      id: '',
      date: '',
      hours: '',
      notes: '',
      departmentId: ''
      }
    ]
}

function doRemoveEntry(index)
{
  if (index >= 0 && index < state.entries.length) {
    state.entries.splice(index,1);
  }
}

function doClearEntry(index)
{
  state.entries = [{ 
      id: '',
      date: '',
      hours: '',
      notes: '',
      departmentId: ''
      }
    ]
}

function doHandleSubmit()
{
  axios({
    url: '/url/to/handle/request',
    method: 'post',
    data: {
      entries: [...state.entries]
    },
    withCredentials: true
  })
  .then(response => {
    state.success = 'Your entries have been saved.';
    doClearEntry(0);
  })
  .catch(error => {
    state.error = 'There was a problem saving your entries. Please try again later.';
  });
}
</script>

<template>

<div v-show="!!state.success" class="alert alert-success">
  {{ state.success }}
</div>

<div v-show="!!state.error" class="alert alert-danger">
  {{  state.error }}
</div>
<form>
<table class="table">
  <tr class="text-center">
    <th>Date</th>
    <th>Weekday</th>
    <th>Department</th>
    <th>Hours</th>
    <th>Notes</th>
    <th></th>
  </tr>

  <tbody v-for="(entry, index) in state.entries">
    <FormItem
      :total-entries="state.entries.length"
      :entry-index="index"
      :entry-id="entry.id"
      :entry-date="entry.date"
      :entry-hours="entry.hours"
      :entry-notes="entry.notes"
      :entry-department-id="entry.departmentId" 
      :all-departments="state.allDepartments"

      @update-entry="doUpdateEntry"
      @add-entry="doAddEntry"
      @delete-entry="doRemoveEntry"
      @clear-entry="doClearEntry"/>
  </tbody>
</table>

<button type="button" @click.prevent="doHandleSubmit" class="btn btn-primary">Submit Hours</button>
</form>
</template>

<style scoped>

</style>
