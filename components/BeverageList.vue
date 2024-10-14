<template>
  <div>
    <h2>Select Your Beverages</h2>
    <div v-for="(beverage, index) in beverages" :key="index" class="beverage-item">
      <p>{{ beverage.name }} - R{{ beverage.price.toFixed(2) }}</p>
      <input
        type="number"
        v-model="quantities[index]"
        min="0"
        placeholder="Quantity"
      />
    </div>
    <div class="buttons">
        <button @click="addToTab">Add to Tab</button>
    <button @click="exportToCSV" v-if="order.length > 0">Export to CSV</button>
    <button @click="exportToPDF" v-if="order.length > 0">Export to PDF</button>
</div>

    <div v-if="order.length > 0" class="order-summary">
      <h3>Order Summary</h3>
      <ul>
        <li v-for="(item, index) in order" :key="index">
          {{ item.quantity }} x {{ item.name }} = R{{ item.total.toFixed(2) }}
        </li>
      </ul>
      <p>Total: R{{ totalAmount.toFixed(2) }}</p>
    </div>
  </div>
</template>

<script>
import Papa from 'papaparse';
import { jsPDF } from 'jspdf';

export default {
  data() {
    return {
      beverages: [
        { name: "Beer", price: 45.0 },
        { name: "Cider", price: 52.0 },
        { name: "Premix", price: 59.0 },
      ],
      quantities: [0, 0, 0], // Initialize quantities for each beverage
      order: [], // Initialize order array
    };
  },
  computed: {
    totalAmount() {
      return this.order.reduce((total, item) => total + item.total, 0);
    },
  },
  methods: {
    addToTab() {
      this.order = this.beverages.map((beverage, index) => ({
        name: beverage.name,
        quantity: this.quantities[index],
        price: beverage.price,
        total: this.quantities[index] * beverage.price,
      })).filter(item => item.quantity > 0);
    },
    exportToCSV() {
      const csvData = this.order.map(item => ({
        Beverage: item.name,
        Quantity: item.quantity,
        Price: item.price,
        Total: item.total,
      }));

      const csv = Papa.unparse(csvData);
      const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
      const url = URL.createObjectURL(blob);
      const link = document.createElement('a');
      link.setAttribute('href', url);
      link.setAttribute('download', 'order_summary.csv');
      link.style.visibility = 'hidden';
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    },
    exportToPDF() {
      const doc = new jsPDF();
      doc.text('Order Summary', 10, 10);
      
      this.order.forEach((item, index) => {
        const y = 20 + index * 10; // Position for each item
        doc.text(`${item.quantity} x ${item.name} = R${item.total.toFixed(2)}`, 10, y);
      });

      doc.text(`Total: R${this.totalAmount.toFixed(2)}`, 10, 20 + this.order.length * 10);
      doc.save('order_summary.pdf');
    },
  },
};
</script>

<style scoped>
.beverage-item {
  margin-bottom: 15px;
}

.order-summary {
  margin-top: 20px;
  border-top: 1px solid #ccc;
  padding-top: 10px;
}
</style>
