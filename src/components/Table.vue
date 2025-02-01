<template>
    <div class="inc-exp-container">
        <div class="header-text">
            <h2>Sorting Training System</h2>
        </div>
        <div class="header-button">
            <v-btn color="orange" class="mb-4 mt-5" @click="openDialog">Start Sorting</v-btn>
            <!-- <button class="btn-header">Start Sorting</button> -->
            <!-- Dialog for Setting Row Count -->
            <v-dialog v-model="dialog" max-width="400px">
                <v-card>
                    <v-card-title>Enter a number of how many people you want to add to the list</v-card-title>
                    <v-card-text>
                        <v-text-field v-model="rowCount" v-model.number="rowCount" label="Number of Rows"
                            :error-messages="rowError" min="1" max="50" step="1"></v-text-field>
                    </v-card-text>
                    <v-card-actions>
                        <v-btn color="" @click="dialog = false">Cancel</v-btn>
                        <v-btn color="orange" @click="generateRandomData">Start</v-btn>
                    </v-card-actions>
                </v-card>
            </v-dialog>
        </div>

    </div>
    <div class="container">

        <!-- Sorting Status Alert -->
        <v-alert v-if="people.length > 0" :type="isSorted ? 'success' : 'error'" class="mb-4">
            {{ isSorted ? `Table is sorted! Time Taken: ${elapsedTime} seconds` : "Table is NOT sorted!" }}
        </v-alert>
        <!-- Timer Display -->
        <p v-if="people.length > 0"><strong>Time Elapsed:</strong> {{ elapsedTime }} seconds</p>

        <v-data-table :headers="headers" :hide-default-footer="true" class="elevation-2">
            <template #tbody>
                <draggable v-model="people" tag="tbody" item-key="potatoes" @end="checkSorting"
                    :disabled="dragDisabled">
                    <template #item="{ element }">
                        <tr>
                            <td>{{ element.name }}</td>
                            <td>{{ element.email }}</td>
                            <td>{{ element.potatoes }}</td>
                            <td>{{ element.location }}</td>
                            <td><button class="pill">{{ element.tag }}</button></td>
                            <!-- <td>{{ element.iron }}</td> -->
                        </tr>
                    </template>
                </draggable>
            </template>
        </v-data-table>
    </div>
</template>
<script setup>
import { VDialog } from "vuetify/lib/components/index.mjs";
import { VBtn } from "vuetify/lib/components/index.mjs";
import { VDataTable } from "vuetify/lib/components/index.mjs";
import { VCard } from "vuetify/lib/components/index.mjs";
import { VCardTitle } from "vuetify/lib/components/index.mjs";
import { VCardText } from "vuetify/lib/components/index.mjs";
import { VTextField } from "vuetify/lib/components/index.mjs";
import { VCardActions } from "vuetify/lib/components/index.mjs";
import { VAlert } from "vuetify/lib/components/index.mjs";
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
    { key: 'location', title: 'Location', sortable: false },
    { key: 'tag', title: 'Tags', sortable: false },
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

// Dialog state
const dialog = ref(false);
const dragDisabled = ref("false");

// Number of rows to generate
const rowCount = ref(null);
const rowError = ref("");

const openDialog = () => {
    dialog.value = true;
    people.value = [];
}

// Timer variables
const startTime = ref(0);
const elapsedTime = ref(0);
let timerInterval = null;

// Function to start the timer
const startTimer = () => {
    elapsedTime.value = 0;
    startTime.value = Date.now();
    clearInterval(timerInterval);
    timerInterval = setInterval(() => {
        elapsedTime.value = Math.floor((Date.now() - startTime.value) / 1000);
    }, 1000);
};

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
    const count = Math.floor(rowCount.value);
    if (!count || count < 1 || count > 50 || rowCount.value % 1 !== 0) {
        rowError.value = "Please enter a  whole number between 1 and 50.";
    } else {
        rowError.value = "";
        const uniquePotatoes = generateUniqueNumbers(count, 1, 100); // Ensure unique potato counts
        people.value = uniquePotatoes.map(potatoCount => ({
            name: getRandomName(),
            email: getRandomEmail(),
            potatoes: potatoCount,
            location: 'Dhaka',
            tag: 'Customers'
        }));
        isSorted.value = people.value.every((item, index, arr) => {
            return index === 0 || item.potatoes <= arr[index - 1].potatoes;
        });
        dialog.value = false;

        startTimer();
        dragDisabled.value = false;
    }
};

// Sorting validation state
const isSorted = ref(false);

// Function to check if the table is sorted in descending order
const checkSorting = () => {
    isSorted.value = people.value.every((item, index, arr) => {
        return index === 0 || item.potatoes <= arr[index - 1].potatoes;
    });

    // Stop timer when sorted
    if (isSorted.value) {
        clearInterval(timerInterval);
        dragDisabled.value = true;
    }
};

// Run sorting check on initial load
//generateRandomData();
watch(dialog, (newValue, oldValue) => {
    if (!newValue && oldValue) {
        rowCount.value = '';
        rowError.value = '';
    }
});
</script>

<style scoped>
/* Add cursor style for draggable rows */
tr {
    cursor: grab;
}
</style>