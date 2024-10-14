<template>
  <div>
    <BeverageList @submitOrder="addToTab" />
    <input v-model="splitBy" type="number" placeholder="Split by (Optional)" />
    <button @click="calculateTotal">Calculate Total</button>
    <p>Total: R{{ total }}</p>
    <p v-if="splitBy > 1">Per Person: R{{ totalPerPerson }}</p>
    <button @click="exportAsCSV">Export as CSV</button>
    <button @click="exportAsPDF">Export as PDF</button>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { jsPDF } from 'jspdf';
import { BeverageList } from './BeverageList.vue';
import Papa from 'papaparse';

const openTab = ref([]);
const total = ref(0);
const splitBy = ref(1);
const totalPerPerson = computed(() => (total.value / splitBy.value).toFixed(2));

function addToTab(order) {
  // Logic to add the order to the open tab.
}

function calculateTotal() {
  // Calculate the total bill.
}

function exportAsCSV() {
  const csvData = Papa.unparse(openTab.value);
  const blob = new Blob([csvData], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const link = document.createElement('a');
  link.href = url;
  link.setAttribute('download', 'tab.csv');
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
}

function exportAsPDF() {
  const doc = new jsPDF();
  doc.text('Receipt', 10, 10);
  // Add content to the PDF
  doc.save('receipt.pdf');
}
</script>
