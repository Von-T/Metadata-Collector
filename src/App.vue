<template>
  <div>
    <div id="main-page">
      <!-- Title section with a button to add a new row -->
      <div class="title-container">
        <button class="ui-buttons" @click="addRow">Add Use Case</button>
        <h1 id="main-title">Fields to Discuss</h1>
      </div>

      <!-- Table to display all rows -->
      <table id="main-table">
        <thead>
          <tr>
            <!-- Render table headers dynamically from the headers array -->
            <th class="main-table-header" v-for="(header, index) in headers" :key="index">{{ header }}</th>
            <th class="main-table-header">Actions</th> <!-- Extra column for action buttons -->
          </tr>
        </thead>
        <tbody>
          <!-- Loop through each row and render its data -->
          <tr v-for="(row, rowIndex) in rows" :key="row.id">
            <!-- Loop through each cell of the row -->
            <td class="main-table-data" v-for="(cell, cellIndex) in row.data" :key="cellIndex">
              <!-- Render a dropdown for specific fields ('Field Status' and 'Definition Status') -->
              <template
                v-if="
                  headers[cellIndex] === 'Field Status' ||
                  headers[cellIndex] === 'Definition Status'
                "
              >
                <select v-model="row.data[cellIndex]">
                  <option value="Approved">Approved</option>
                  <option value="Needs More Discussion">Needs More Discussion</option>
                  <option value="Canceled">Canceled</option>
                </select>
              </template>
              <!-- Render plain text for other fields -->
              <template v-else>
                <span>{{ cell.length > 10 ? cell.slice(0, 10) + '...' : cell }}</span> <!-- Limit displayed text length -->
              </template>
            </td>
            <!-- Action buttons for editing and deleting rows -->
            <td class="main-table-data">
              <button class="ui-buttons" @click="editRow(row, rowIndex)">Edit</button>
              <button class="ui-buttons" @click="deleteRow(rowIndex)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Pop-up Modal for editing a row -->
    <div v-if="isEditing" class="modal">
      <div class="modal-content">
        <h3 id="modal-title">Edit Use Case</h3>
        <form class="form-container">
          <div class="form-column">
            <!-- Render input fields for the left section of the form -->
            <div v-for="(header, index) in leftHeaders" :key="index" class="form-field">
              <label>{{ header }}</label>
              <!-- Dropdown for 'Field Status' -->
              <select
                v-if="header === 'Field Status'"
                v-model="editedRow.data[headers.indexOf(header)]"
              >
                <option value="Approved">Approved</option>
                <option value="Needs More Discussion">Needs More Discussion</option>
                <option value="Canceled">Canceled</option>
              </select>
              <!-- Textarea for 'Definition' -->
              <textarea
                class="text-area-fields"
                v-else-if="header === 'Definition'"
                v-model="editedRow.data[headers.indexOf(header)]"
              ></textarea>
              <!-- Text input for other fields -->
              <input
                class="input-fields"
                v-else
                type="text"
                v-model="editedRow.data[headers.indexOf(header)]"
              />
            </div>
          </div>
          <div class="form-column">
            <!-- Render input fields for the right section of the form -->
            <div v-for="(header, index) in rightHeaders" :key="index" class="form-field">
              <label>{{ header }}</label>
              <!-- Dropdown for 'Definition Status' -->
              <select
                v-if="header === 'Definition Status'"
                v-model="editedRow.data[headers.indexOf(header)]"
              >
                <option value="Approved">Approved</option>
                <option value="Needs More Discussion">Needs More Discussion</option>
                <option value="Canceled">Canceled</option>
              </select>
              <!-- Textarea for 'Notes' -->
              <textarea
                class="text-area-fields"
                v-else-if="header === 'Notes'"
                v-model="editedRow.data[headers.indexOf(header)]"
              ></textarea>
              <!-- Text input for other fields -->
              <input
                class="input-fields"
                v-else
                type="text"
                v-model="editedRow.data[headers.indexOf(header)]"
              />
            </div>
          </div>
          <!-- Buttons for saving or canceling changes -->
          <div class="modal-actions">
            <button class="ui-buttons" type="button" @click="saveChanges">Save</button>
            <button class="ui-buttons" type="button" @click="closeModal">Exit</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
// VT TODO: May want to use different unique ID scheme if database has ints for IDs, since this `uuid` library generates string IDs (e.g. '1b9d6bcd-bbfd-4b2d-9b5d-ab8dfbbd4bed')
import { v4 as uuidv4 } from 'uuid'; // Import UUID library for generating unique IDs

export default {
  data() {
    return {
      headers: [
        'Field Name',
        'Field Status',
        'Definition',
        'Definition Status',
        'Topic Name',
        'View Name',
        'Example',
        'Notes',
      ],
      rows: [
        {
          id: uuidv4(), // Unique ID for each row
          data: ['', '', '', '', '', '', '', ''], // Array holding row data
        },
      ],
      isEditing: false, // Tracks if the modal is open
      editedRow: null, // Holds the row currently being edited
      editingRowIndex: null, // Index of the row being edited
      leftHeaders: ['Field Name', 'Field Status', 'Topic Name', 'Definition'], // Headers for the left section of the modal
      rightHeaders: ['Example', 'Definition Status', 'View Name', 'Notes'], // Headers for the right section of the modal
    };
  },
  created() {
    // Automatically fetch data from the database after the `data()` is initialized
    // VT TODO: This function automatically runs after `data()` is initialized, so use this place to get data from database and add it to `this.rows` here
  },
  methods: {
    // Add a new row to the table
    addRow() {
      this.rows.unshift({
        id: uuidv4(), // Assign a new unique ID
        data: ['', '', '', '', '', '', '', ''], // Initialize with empty fields
      });
      // VT TODO: Add the new row to the database here
    },
    // Delete a row from the table
    deleteRow(index) {
      this.rows.splice(index, 1); // Remove row by index
      // VT TODO: Remove the corresponding row from the database using its unique ID here
    },
    // Open the modal and populate it with the selected row's data
    editRow(row, index) {
      this.editedRow = { ...row }; // Create a copy to avoid direct mutation
      this.editingRowIndex = index; // Store the index of the row being edited
      this.isEditing = true; // Open the modal
    },
    // Save changes made in the modal
    saveChanges() {
      if (this.editingRowIndex !== null) {
        // Update the specific row with the edited data while retaining the unique ID
        this.rows[this.editingRowIndex] = {
          ...this.editedRow,
          id: this.rows[this.editingRowIndex].id,
        };
      }
      // VT TODO: Update the database with the edited row's data here
      this.closeModal();
    },
    // Close the modal and reset editing state
    closeModal() {
      this.isEditing = false; // Close the modal
      this.editedRow = null; // Clear the edited row data
      this.editingRowIndex = null; // Reset the index
    },
  },
};
</script>

<style scoped>
#main-page {
  display: flex;
  flex-direction: column; /* Align content vertically */
  justify-content: center; /* Center content vertically */
  align-items: center; /* Center content horizontally */
  width: 90%; /* Take up most of the screen's width */
  max-width: 1200px; /* Optional: Set a max width for larger screens */
  margin: 0 auto; /* Center horizontally with margins */
  padding: 20px; /* Optional: Add padding inside the div */
  box-sizing: border-box; /* Include padding in width calculation */
}
.title-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%; /* Match the width of #main-page */
  margin-bottom: 20px;
}
#main-title {
  font-size: 50px;
  flex-grow: 1;
  text-align: center;
}
.ui-buttons {
  margin: 5px;
  padding: 5px 10px;
}
.main-table {
  width: 100%; /* Table stretches to fit the container */
  border-collapse: collapse;
}
.main-table-header, .main-table-data {
  padding: 8px;
  border: 1px solid #ddd;
  text-align: left;
}
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}
.modal-content {
  background: white;
  padding: 20px;
  border-radius: 5px;
  width: 600px;
  max-width: 90%;
  max-height: 80%; /* Set max height for the modal */
  overflow-y: auto; /* Make content scrollable if it overflows in y direction */
  overflow-x: auto; /* Make content scrollable if it overflows in y direction */
  display: flex;
  flex-direction: column;
}
#modal-title {
  margin-bottom: 15px;
}
.form-container {
  display: flex;
  justify-content: space-between;
  gap: 20px;
}
.form-column {
  flex: 1;
  display: flex;
  flex-direction: column;
}
.form-field {
  margin-bottom: 10px;
}
.text-area-fields, .input-fields {
  width: 100%;
  padding: 5px;
  margin-top: 5px;
  box-sizing: border-box;
}
/* Increase size of Definition and Notes textareas */
.text-area-fields {
  min-height: 120px; /* Larger height */
  min-width: 220px; /* Larger width */
}
.modal-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}
</style>
