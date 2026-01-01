<template>
  <section class="app">
    <h1>Expense Calculator</h1>

    <!-- form -->
    <section class="form">
      <section class="input-section">
        <label for="item"> Enter your item name </label>
        <input v-model="itemName" id="item" />
      </section>

      <section class="input-section">
        <label for="amount"> Enter the item amount</label>
        <input v-model="itemAmount" id="amount" />
      </section>

      <span :class="{ hidden: !error }">{{ error ?? "" }}</span>

      <button @click="addItem">Add Expense</button>
    </section>

    <!-- expense data -->
    <table :class="{ hidden: items.length <= 0 }">
      <thead>
        <tr>
          <th>S.N.</th>
          <th>Name</th>
          <th>Amount</th>
          <th></th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="item in items" :key="item.id">
          <td>{{ items.indexOf(item) + 1 }}</td>
          <td>{{ item.name }}</td>
          <td>{{ item.amount }}</td>
          <td>
            <button @click="deleteItem(item.id.toString())">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <p :class="{ hidden: items.length > 0 }">Empty Item</p>

    <!-- total expense -->
    <section class="total-expense">
      Total <span>Rs. {{ totalValue }}</span>
    </section>
  </section>
</template>

<style scoped>
.app {
  min-height: 100vh;
  width: 100vw;
  padding: 4vh 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8vh;
}

.form {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2vh;
}

.input-section {
  width: 25vw;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.input-section input {
  padding: 1vh 0;
  border: none;
  border-radius: 2px;
  text-indent: 0.5rem;
}

.form span {
  color: #e22222;
  font-weight: 500;
}

table {
  border-collapse: collapse;
  width: 50vw;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);

  tbody {
    display: block;
    max-height: 30vh; /* Adjust height as needed */
    overflow-y: auto;
  }

  tr {
    display: table;
    table-layout: fixed;
    width: 100%;
    text-align: center;
  }

  th {
    background-color: #333;
    color: white;
    padding: 1rem;
    font-weight: 600;
    width: 100%;
  }

  td {
    padding: 0.8rem 1rem;
    border-bottom: 1px solid #ddd;
  }

  tr:hover {
    background-color: #b9b9b950;
  }

  button {
    padding: 0.5rem 1rem;
    background-color: #e22222;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
  }

  button:hover {
    background-color: #c41e1e;
  }
}

.total-expense {
  width: 50vw;
  background: #333;
  padding: 1vh 1vw;
  border-radius: 2px;
  font-size: 1.1rem;
  font-family: monospace;
  display: flex;
  align-items: center;
  justify-content: space-between;
  text-transform: uppercase;

  span {
    color: yellow;
    font-size: 1.6rem;
    text-transform: capitalize;
  }
}

.hidden {
  display: none;
}

@media (max-width: 768px) {
  .app {
    padding: 2vh 0;
    gap: 4vh;
  }

  .input-section {
    width: 80vw;
    flex-direction: column;
    gap: 0.5vh;
  }

  table {
    width: 90vw;
  }

  .total-expense {
    width: 90vw;
    font-size: 1rem;

    span {
      font-size: 1.3rem;
    }
  }
}

@media (max-width: 480px) {
  h1 {
    font-size: 1.5rem;
  }

  .input-section {
    width: 90vw;
  }

  table {
    width: 95vw;
    font-size: 0.85rem;
  }

  table th {
    padding: 0.5rem;
  }

  table td {
    padding: 0.5rem;
  }

  .total-expense {
    width: 95vw;
    flex-direction: column;
    gap: 0.5vh;
    text-align: center;
  }
}
</style>

<script setup lang="ts">
import { onMounted, ref, type Ref } from "vue";

interface Item {
  id: number;
  name: string;
  amount: number;
}

const itemName: Ref<string> = ref("");
const itemAmount: Ref<string> = ref("");

const totalValue: Ref<number> = ref(0);

const error: Ref<string> = ref("");

const items = ref<Item[]>([]);

const loadItemsFromLocalStorage = () => {
  const storedItems = localStorage.getItem("items");
  if (storedItems) {
    items.value = JSON.parse(storedItems);
    calculateTotal();
  }
};

const saveItemsToLocalStorage = () => {
  localStorage.setItem("items", JSON.stringify(items.value));
};

const addItem = () => {
  error.value = "";

  if (!itemName.value.trim() || !itemAmount.value.trim()) {
    error.value = "Item name cannot be empty";
    return;
  }

  const amount = Number(itemAmount.value);

  if (isNaN(amount)) {
    error.value = "Amount must be a valid number";
    return;
  }

  items.value.push({
    id: Date.now(),
    name: itemName.value,
    amount,
  });

  calculateTotal();
  reverseList();
  saveItemsToLocalStorage();

  itemName.value = "";
  itemAmount.value = "";
};

const calculateTotal = () => {
  const total = items.value
    .map((item) => Number(item.amount))
    .reduce((initial, next) => initial + next, 0);

  totalValue.value = total;
};

const deleteItem = (id: string) => {
  items.value = items.value.filter((item) => item.id.toString() !== id);
  calculateTotal();
  saveItemsToLocalStorage();
};

const reverseList = () => {
  items.value.sort((a, b) => b.id - a.id);
};

onMounted(loadItemsFromLocalStorage);
</script>
