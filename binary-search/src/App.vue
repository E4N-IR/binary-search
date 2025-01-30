<template>
  <v-container>
    <v-row>
      <v-col>
        <!-- Input field to enter a number and add it to the list -->
        <v-text-field
          v-model="newUser"
          label="Enter a number"
          @keyup.enter="addUser"
        ></v-text-field>
        <v-btn @click="addUser">Add</v-btn>
      </v-col>
    </v-row>
    <v-row>
      <!-- Display the list of numbers with the current middle element highlighted -->
      <v-col
        v-for="(item, index) in list"
        :key="index"
        :class="{ highlight: index === middle }"
      >
        {{ item }}
      </v-col>
    </v-row>
    <!-- Input field to enter the target number to search for -->
    <v-text-field
      v-model="target"
      label="Enter number to search"
    ></v-text-field>
    <!-- Buttons to start the search and sort the list -->
    <v-btn @click="startSearch">Start Search</v-btn>
    <v-btn @click="sortList">Sort List</v-btn>
    <!-- Display the search result -->
    <v-alert v-if="searchResult !== null" type="info">
      {{ searchResult }}
    </v-alert>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      list: [], // List of numbers
      newUser: "", // New number to be added to the list
      target: null, // Target number to search for
      low: 0, // Lower bound for binary search
      high: null, // Upper bound for binary search
      middle: null, // Current middle index
      searchResult: null, // Search result message
    };
  },
  methods: {
    // Add a new number to the list
    addUser() {
      if (this.newUser !== "") {
        this.list.push(Number(this.newUser));
        this.newUser = "";
      }
    },
    // Start the binary search
    startSearch() {
      this.low = 0;
      this.high = this.list.length - 1;
      this.searchResult = null;
      this.binarySearch();
    },
    // Perform the binary search
    async binarySearch() {
      let count = 0;
      while (this.low <= this.high) {
        this.middle = Math.floor((this.low + this.high) / 2);
        await this.highlightMiddle();
        if (this.list[this.middle] === Number(this.target)) {
          count++;
          this.searchResult = `Item found at index ${this.middle}. Found ${count} time(s).`;
          // Check for duplicates on both sides
          let left = this.middle - 1;
          while (left >= this.low && this.list[left] === Number(this.target)) {
            count++;
            left--;
          }
          let right = this.middle + 1;
          while (
            right <= this.high &&
            this.list[right] === Number(this.target)
          ) {
            count++;
            right++;
          }
          this.searchResult = `Item found ${count} time(s).`;
          return;
        } else if (this.list[this.middle] < Number(this.target)) {
          this.low = this.middle + 1;
        } else {
          this.high = this.middle - 1;
        }
      }
      this.searchResult = `Item not found. Found ${count} time(s).`;
    },
    // Highlight the current middle element
    highlightMiddle() {
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve();
        }, 1000); // Adjust the delay as needed
      });
    },
    // Sort the list in ascending order
    sortList() {
      this.list.sort((a, b) => a - b);
    },
  },
};
</script>

<style>
/* Highlight the current middle element */
.highlight {
  background-color: yellow;
}
</style>
