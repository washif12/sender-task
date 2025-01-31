<template>

    <h2>Draggable Vuetify Table (Descending Order Check)</h2>

    <!-- Sorting Status Alert -->
    <v-alert :type="isSorted ? 'success' : 'error'" class="mb-4">
        {{ isSorted ? "Table is sorted correctly" : "Table is NOT sorted!" }}
    </v-alert>
    <v-data-table :headers="headers" :hide-default-footer="true" class="elevation-2">
        <template #tbody >
            <draggable v-model="people" tag="tbody" item-key="potatoes" @end="checkSorting">
                <template #item="{ element }">
                    <tr>
                        <td>{{ element.name }}</td>
                        <td>{{ element.email }}</td>
                        <td>{{ element.potatoes }}</td>
                        <!-- <td>{{ element.carbs }}</td>
                        <td>{{ element.protein }}</td>
                        <td>{{ element.iron }}</td> -->
                    </tr>
                </template>
            </draggable>
        </template>
    </v-data-table>
</template>
<script setup>
import { VDataTable } from 'vuetify/lib/components/index.mjs';
import { ref, watch } from "vue";
import draggable from "vuedraggable";

const headers = ref([
    {
        align: 'start',
        key: 'name',
        sortable: false,
        title: 'Name',
    },
    { key: 'email', title: 'Email', sortable: false },
    { key: 'potatoes', title: 'Potatoes', sortable: false },
    // { key: 'carbs', title: 'Carbs (g)', sortable: false },
    // { key: 'protein', title: 'Protein (g)', sortable: false },
    // { key: 'iron', title: 'Iron (%)', sortable: false },
]);
const desserts = ref([
    {
        name: 'Frozen Yogurt',
        calories: 159,
        fat: 6.0,
        carbs: 24,
        protein: 4.0,
        iron: 1,
    },
    {
        name: 'Ice cream sandwich',
        calories: 237,
        fat: 9.0,
        carbs: 37,
        protein: 4.3,
        iron: 1,
    },
    {
        name: 'Eclair',
        calories: 262,
        fat: 16.0,
        carbs: 23,
        protein: 6.0,
        iron: 7,
    },
    {
        name: 'Cupcake',
        calories: 305,
        fat: 3.7,
        carbs: 67,
        protein: 4.3,
        iron: 8,
    },
    {
        name: 'Gingerbread',
        calories: 356,
        fat: 16.0,
        carbs: 49,
        protein: 3.9,
        iron: 16,
    },
    {
        name: 'Jelly bean',
        calories: 375,
        fat: 0.0,
        carbs: 94,
        protein: 0.0,
        iron: 0,
    },
    {
        name: 'Lollipop',
        calories: 392,
        fat: 0.2,
        carbs: 98,
        protein: 0,
        iron: 2,
    },
    {
        name: 'Honeycomb',
        calories: 408,
        fat: 3.2,
        carbs: 87,
        protein: 6.5,
        iron: 45,
    },
    {
        name: 'Donut',
        calories: 452,
        fat: 25.0,
        carbs: 51,
        protein: 4.9,
        iron: 22,
    },
    {
        name: 'KitKat',
        calories: 518,
        fat: 26.0,
        carbs: 65,
        protein: 7,
        iron: 6,
    },
]);

// Function to generate a random email
const getRandomEmail = () => {
  const domains = ["gmail.com", "yahoo.com", "outlook.com", "hotmail.com"];
  const randomName = Math.random().toString(36).substring(2, 10); // Random string
  return `${randomName}@${domains[Math.floor(Math.random() * domains.length)]}`;
};

// Function to generate a random name
const getRandomName = () => {
  const names = ["Alice", "Bob", "Charlie", "David", "Eve", "Frank", "Grace", "Hank", "Ivy", "Jack",
                 "Kevin", "Laura", "Mike", "Nina", "Oliver", "Paula", "Quincy", "Rachel", "Steve", "Tina"];
  return names[Math.floor(Math.random() * names.length)];
};

// Function to generate unique potato numbers
const generateUniqueNumbers = (count, min, max) => {
  const numbers = new Set();
  while (numbers.size < count) {
    numbers.add(Math.floor(Math.random() * (max - min + 1)) + min);
  }
  return [...numbers];
};

// Generate initial random data
const people = ref([]);
const generateRandomData = () => {
  const uniquePotatoes = generateUniqueNumbers(5, 1, 100); // Ensure unique potato counts
  people.value = uniquePotatoes.map(potatoCount => ({
    name: getRandomName(),
    email: getRandomEmail(),
    potatoes: potatoCount
  }));
};

// Sorting validation state
const isSorted = ref(false);

// Function to check if the table is sorted in descending order
const checkSorting = () => {
    isSorted.value = people.value.every((item, index, arr) => {
        return index === 0 || item.potatoes <= arr[index - 1].potatoes;
    });
};

// Run sorting check on initial load
generateRandomData();
</script>

<style scoped>
/* Add cursor style for draggable rows */
tr {
  cursor: grab;
}
</style>