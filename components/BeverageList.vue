<template>
  <div class="beverage-list">
    <h2>Select Your Beverages</h2>

    <!-- Beverage List with quantity selection -->
    <div v-for="(price, beverage) in prices" :key="beverage" class="beers">
      <label :for="beverage">{{ capitalize(beverage) }} (R{{ price }}):</label>
      <input type="number" v-model="selectedBeverages[beverage]" min="0" placeholder="0" />
    </div>

    <!-- Input for splitting the bill -->
    <div class="split-bill">
      <label for="split">Number of people splitting the bill:</label>
      <input type="number" v-model="splitCount" min="1" placeholder="Enter number of people" />
    </div>

    <!-- Button to calculate the bill -->
    <button @click="calculateBill" class="btn-calculate">Calculate Bill</button>

    <!-- Display total bill and per-person amount -->
    <div v-if="totalBill > 0" class="bill-details">
      <p>Total Bill: R{{ totalBill }}</p>
      <p v-if="splitCount > 1">Each Person Pays: R{{ perPersonAmount }}</p>
    </div>

    <!-- Buttons for Exporting as CSV and PDF -->
    <div v-if="totalBill > 0" class="export-buttons">
      <button @click="exportToCSV" class="btn-export">Export as CSV</button>
      <button @click="exportToPDF" class="btn-export">Export as PDF</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { jsPDF } from 'jspdf';

// Beverage prices
const prices = {
  beer: 45.00,
  cider: 52.00,
  premix: 59.00
};

// Reactive state for split count, selected beverages, and total bill
const splitCount = ref(1); // Default to 1 person (no split)
const totalBill = ref(0);
const perPersonAmount = ref(0);
const selectedBeverages = ref({
  beer: 0,
  cider: 0,
  premix: 0
});

// Capitalize beverage names for display
const capitalize = (word) => word.charAt(0).toUpperCase() + word.slice(1);

// Function to calculate the total bill and amount per person
const calculateBill = () => {
  totalBill.value = (selectedBeverages.value.beer * prices.beer) +
                    (selectedBeverages.value.cider * prices.cider) +
                    (selectedBeverages.value.premix * prices.premix);

  // Calculate the per person amount if splitting
  if (splitCount.value > 1) {
    perPersonAmount.value = (totalBill.value / splitCount.value).toFixed(2);
  } else {
    perPersonAmount.value = totalBill.value.toFixed(2);
  }
};

// Function to export the data to CSV
const exportToCSV = () => {
  const rows = [
    ['Beverage', 'Quantity', 'Price'],
    ['Beer', selectedBeverages.value.beer, prices.beer],
    ['Cider', selectedBeverages.value.cider, prices.cider],
    ['Premix', selectedBeverages.value.premix, prices.premix],
    ['Total', '', totalBill.value],
    ['Split Between', splitCount.value, perPersonAmount.value]
  ];

  let csvContent = "data:text/csv;charset=utf-8," 
    + rows.map(e => e.join(",")).join("\n");

  const encodedUri = encodeURI(csvContent);
  const link = document.createElement("a");
  link.setAttribute("href", encodedUri);
  link.setAttribute("download", "tab.csv");
  document.body.appendChild(link); // Required for Firefox

  link.click();
};

// Function to export the data to PDF using jsPDF
const exportToPDF = () => {
  const doc = new jsPDF();
  
  doc.text("Open Bar Tab Receipt", 10, 10);
  doc.text(`Beer: ${selectedBeverages.value.beer} x R${prices.beer}`, 10, 20);
  doc.text(`Cider: ${selectedBeverages.value.cider} x R${prices.cider}`, 10, 30);
  doc.text(`Premix: ${selectedBeverages.value.premix} x R${prices.premix}`, 10, 40);
  doc.text(`Total Bill: R${totalBill.value}`, 10, 50);
  
  if (splitCount.value > 1) {
    doc.text(`Split Between: ${splitCount.value} people`, 10, 60);
    doc.text(`Each Person Pays: R${perPersonAmount.value}`, 10, 70);
  }
  
  doc.save('tab.pdf');
};
</script>