<template>
    <div class="container">
        <v-alert class="d-flex align-center justify-center" color="orange" icon="$info" text="Press Start to load data or restart"></v-alert>
        <div class="inc-exp-container">
            <div class="text-left">
                <h2>Sorting Training System</h2>
            </div>
            <div class="text-right">
                <v-btn color="orange" class="sort-button" @click="openDialog">Start Sorting!</v-btn>
                <!-- <button class="btn-header">Start Sorting</button> -->
                <!-- Dialog for Setting Row Count -->
                <v-dialog v-model="dialog" max-width="400px" persistent transition="dialog-bottom-transition">
                    <v-card title="How many people?"
                    text="Enter a number of how many people you want to add to the
                            list">
                        <!-- <v-card-title class="text-wrap"></v-card-title> -->
                        <v-card-text>
                            <v-text-field v-model="rowCount" v-model.number="rowCount" label="Number of Rows"
                                variant="outlined" :error-messages="rowError" min="1" max="50" step="1"></v-text-field>
                        </v-card-text>
                        <v-card-actions>
                            <v-btn color="" variant="tonal" @click="dialog = false">Cancel</v-btn>
                            <v-btn color="orange" variant="flat" class="sort-button"
                                @click="generateRandomData">Start</v-btn>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </div>

        </div>


        <!-- Sorting Status Alert -->
        <v-alert v-if="people.length > 0" :type="isSorted ? 'success' : 'error'"
            class="mb-4 d-flex align-center justify-center">
            {{ isSorted ? `Table is sorted! Time Taken: ${elapsedTime} seconds` : "Table is NOT sorted!" }}
        </v-alert>
        <!-- Timer Display -->
        <p class="text-center" v-if="people.length > 0"><strong>Time Elapsed:</strong> {{ elapsedTime }} seconds</p>

        <v-data-table :headers="headers" :hide-default-footer="true" class="elevation-2 mt-4" hide-no-data>
            <template #tbody>
                <draggable v-model="people" tag="tbody" item-key="potatoes" @end="checkSorting"
                    :disabled="dragDisabled">
                    <template #item="{ element }">
                        <tr>
                            <td>{{ element.email }}</td>
                            <td>{{ element.potatoes }}</td>
                            <!-- <td>{{ element.location }}</td> -->
                            <td><v-chip>{{ element.tag }}</v-chip></td>
                            <td>{{ element.name }}</td>
                            <!-- <td>{{ element.iron }}</td> -->
                        </tr>
                    </template>
                </draggable>
            </template>
        </v-data-table>

        <v-snackbar v-model="snackbar" :timeout="5000" color="green">
            Congrats! Sorting completed in {{ elapsedTime }} seconds!
      </v-snackbar>
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
import { VChip } from "vuetify/lib/components/index.mjs";
import { VSnackbar } from "vuetify/lib/components/index.mjs";
import { ref, watch } from "vue";
import draggable from "vuedraggable";

const headers = ref([
    { align: 'start', key: 'email', title: 'Email', sortable: false },
    { key: 'potatoes', title: 'Potatoes', sortable: false },
    // { key: 'location', title: 'Location', sortable: false },
    { key: 'tag', title: 'Tags', sortable: false },
    { key: 'name', sortable: false, title: 'Name' },
    // { key: 'iron', title: 'Iron (%)', sortable: false },
]);

// Dialog state
const dialog = ref(false);
const dragDisabled = ref("false");

// Number of rows to generate
const rowCount = ref(null);
const rowError = ref("");
const snackbar = ref(false);

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
    if (!count || count < 20 || count > 100 || rowCount.value % 1 !== 0) {
        rowError.value = "Please enter a  whole number between 20 and 100.";
    } else {
        rowError.value = "";
        const uniquePotatoes = generateUniqueNumbers(count, 1, 100); // Ensure unique potato counts
        people.value = uniquePotatoes.map(potatoCount => ({
            name: getRandomName(),
            email: getRandomEmail(),
            potatoes: potatoCount,
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
        snackbar.value = true;
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

.sort-button {
    color: white !important;
}

.v-label .v-field-label .v-field-label--floating {
    top: 3px !important;
}

@media only screen and (max-width: 600px) {
    body {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }

    .container {
        width: 100%;
    }

    .v-alert {
        width: 100%;
    }
}
</style>