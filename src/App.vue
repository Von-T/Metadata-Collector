<template>
  <div>
    <div id="main-page">
      <div class="title-container">
        <button @click="addRow">Add Use Case</button>
        <h1>Fields to Discuss</h1>
      </div>
      <table>
        <thead>
          <tr>
            <th v-for="(header, index) in headers" :key="index">{{ header }}</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in rows" :key="row.id">
            <!-- Display each field -->
            <td v-for="(cell, cellIndex) in row.data" :key="cellIndex">
              <!-- Render dropdown for 'Field Status' and 'Definition Status' -->
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
              <!-- Render text for other fields -->
              <template v-else>
                <span>{{ cell.length > 10 ? cell.slice(0, 10) + '...' : cell }}</span> <!-- Limit the amount of text that can display in each cell in main page -->
              </template>
            </td>
            <td>
              <button @click="editRow(row, rowIndex)">Edit</button>
              <button @click="deleteRow(rowIndex)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Pop-up Modal -->
    <div v-if="isEditing" class="modal">
      <div class="modal-content">
        <h3>Edit Use Case</h3>
        <form class="form-container">
          <div class="form-column">
            <div v-for="(header, index) in leftHeaders" :key="index" class="form-field">
              <label>{{ header }}</label>
              <select
                v-if="header === 'Field Status'"
                v-model="editedRow.data[headers.indexOf(header)]"
              >
                <option value="Approved">Approved</option>
                <option value="Needs More Discussion">Needs More Discussion</option>
                <option value="Canceled">Canceled</option>
              </select>
              <textarea
                v-else-if="header === 'Definition'"
                v-model="editedRow.data[headers.indexOf(header)]"
              ></textarea>
              <input v-else type="text" v-model="editedRow.data[headers.indexOf(header)]" />
            </div>
          </div>
          <div class="form-column">
            <div v-for="(header, index) in rightHeaders" :key="index" class="form-field">
              <label>{{ header }}</label>
              <select
                v-if="header === 'Definition Status'"
                v-model="editedRow.data[headers.indexOf(header)]"
              >
                <option value="Approved">Approved</option>
                <option value="Needs More Discussion">Needs More Discussion</option>
                <option value="Canceled">Canceled</option>
              </select>
              <textarea
                v-else-if="header === 'Notes'"
                v-model="editedRow.data[headers.indexOf(header)]"
              ></textarea>
              <input v-else type="text" v-model="editedRow.data[headers.indexOf(header)]" />
            </div>
          </div>
          <div class="modal-actions">
            <button type="button" @click="saveChanges">Save</button>
            <button type="button" @click="closeModal">Exit</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import { v4 as uuidv4 } from 'uuid' // UUID library for unique IDs

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
          id: uuidv4(), // Unique ID
          data: ['', '', '', '', '', '', '', ''], // Row data
        },
      ],
      isEditing: false,
      editedRow: null,
      editingRowIndex: null,
      leftHeaders: ['Field Name', 'Field Status', 'Topic Name', 'Definition'],
      rightHeaders: ['Example', 'Definition Status', 'View Name', 'Notes'],
    }
  },
  methods: {
    addRow() {
      this.rows.unshift({
        id: uuidv4(), // Generate a new unique ID
        data: ['', '', '', '', '', '', '', ''], // Empty row data
      })
      // TODO: Add the new row to the database (note every row should have a unique ID)
    },
    deleteRow(index) {
      // TODO: Remove the corresponding row from the database using its ID (note every row should have a unique ID)
      this.rows.splice(index, 1)
    },
    editRow(row, index) {
      this.editedRow = { ...row } // Copy the row data
      this.editingRowIndex = index
      this.isEditing = true // Show the modal
    },
    saveChanges() {
      if (this.editingRowIndex !== null) {
        // Update the row data while preserving the ID
        this.rows[this.editingRowIndex] = {
          ...this.editedRow,
          id: this.rows[this.editingRowIndex].id,
        }
      }
      // TODO: Update the database with the changes using the row's ID (note every row should have a unique ID)
      console.log(this.rows[this.editingRowIndex])
      this.closeModal()
    },
    closeModal() {
      this.isEditing = false
      this.editedRow = null
      this.editingRowIndex = null
    },
  },
}
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
/* Layout styles */
.title-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%; /* Match the width of #main-page */
  margin-bottom: 20px;
}
h1 {
  font-size: 50px;
  flex-grow: 1;
  text-align: center;
}
button {
  margin: 5px;
  padding: 5px 10px;
}
table {
  width: 100%; /* Table stretches to fit the container */
  border-collapse: collapse;
}
th,
td {
  padding: 8px;
  border: 1px solid #ddd;
  text-align: left;
}
/* Modal styles */
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
  overflow-y: auto; /* Make content scrollable if it overflows */
  display: flex;
  flex-direction: column;
}
h3 {
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
textarea,
input {
  width: 100%;
  padding: 5px;
  margin-top: 5px;
  box-sizing: border-box;
}

/* Increase size of Definition and Notes textareas */
textarea {
  min-height: 120px; /* Larger height */
  min-width: 220px; /* Larger width */
}

.modal-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}
</style>
