<template>
  <div>
    <div class="container">
      <h1 class="none">Diari Jajan {{ currentMonthAndYear }}</h1>
      <h3 class="none">
        Pengeluaran bulan ini {{ formatMoney(totalPengeluaranKeseluruhan) }}
      </h3>
      <button class="button" @click="openModal">TAMBAH ITEM</button>

      <div class="center">
        <div v-for="(items, tanggal) in sortedBoxes" :key="tanggal">
          <div class="boxWrap">
            <div>
              <h3 class="title">{{ formatDate(tanggal) }}</h3>
              <Box v-for="item in items.detail" :key="item.jam" :box="item" />
            </div>
            <div class="total-pengeluaran">
              Total Pengeluaran:
              {{ formatMoney(totalPengeluaranPerTanggal[tanggal]) }}
            </div>
          </div>
        </div>
      </div>
    </div>
    <modal :show="isModalVisible" @add-item="addItem" @cancel="closeModal" />
  </div>
</template>

<script>
import Box from "./components/Box.vue";
import jsonData from "./json/json-test-fe.json"; // Sesuaikan path dengan lokasi file JSON Anda
import Modal from "./components/Modal.vue";

export default {
  name: "App",
  components: {
    Box,
    Modal,
  },
  data() {
    return {
      boxes: {},
      totalPengeluaranPerTanggal: {},
      isModalVisible: false,
    };
  },
  computed: {
    sortedBoxes() {
      return Object.fromEntries(
        Object.entries(this.boxes).sort((a, b) => {
          const dateA = new Date(a[0]);
          const dateB = new Date(b[0]);
          return dateB - dateA;
        })
      );
    },
    totalPengeluaranKeseluruhan() {
      return Object.values(this.totalPengeluaranPerTanggal).reduce(
        (total, pengeluaran) => total + pengeluaran,
        0
      );
    },
    currentMonthAndYear() {
      return new Date().toLocaleDateString("id-ID", {
        month: "long",
        year: "numeric",
      });
    },
  },
  created() {
    this.getData();
  },
  methods: {
    openModal() {
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
    },
    addItem(newItem) {
      const now = new Date();
      const hours = now.getHours().toString().padStart(2, "0");
      const minutes = now.getMinutes().toString().padStart(2, "0");
      const currentTime = `${hours}:${minutes}`;

      const existingData =
        JSON.parse(localStorage.getItem("pengeluaranData")) || [];
      existingData.push({
        ...newItem,
        tanggal: new Date().toLocaleDateString(),
        jam: currentTime,
        pengeluaraan: Number(newItem?.pengeluaraan),
      });
      this.boxes = this.groupDataByDate(existingData);
      this.isModalVisible = false;
      localStorage.setItem("pengeluaranData", JSON.stringify(existingData));
    },
    getData() {
      const storedData = localStorage.getItem("pengeluaranData");
      if (storedData) {
        this.boxes = this.groupDataByDate(JSON.parse(storedData));
      } else {
        this.boxes = this.groupDataByDate(jsonData.detail);
        localStorage.setItem(
          "pengeluaranData",
          JSON.stringify(jsonData.detail)
        );
      }
    },
    groupDataByDate(data) {
      const groupedData = {};
      data.forEach((item) => {
        const date = item.tanggal;
        if (!groupedData[date]) {
          groupedData[date] = {
            totalBulan: 0,
            detail: [],
          };
        }
        groupedData[date].totalBulan += item.pengeluaraan;
        groupedData[date].detail.push(item);
      });

      this.totalPengeluaranPerTanggal = Object.fromEntries(
        Object.entries(groupedData).map(([date, { totalBulan }]) => [
          date,
          totalBulan,
        ])
      );
      return groupedData;
    },
    formatMoney(value) {
      return `Rp ${new Intl.NumberFormat("id-ID").format(value)}`;
    },
    formatDate(dateString) {
      const options = { day: "numeric", month: "long" };
      const date = new Date(dateString);
      return date.toLocaleDateString("id-ID", options);
    },
  },
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.center {
  margin-top: 20px;
  display: flex;
  flex-direction: row;
  align-items: flex-start;
  gap: 15px;
  flex-wrap: wrap;
  margin-left: calc((100% - 300px * 3) / 6);
  margin-right: calc((100% - 300px * 3) / 6);
}
.boxWrap {
  height: 50vh;
  overflow-y: auto;
  width: 300px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
  border-radius: 12px;
  display: flex;
  flex-direction: column;
}
.title {
  padding: 10px;
  border-bottom: 1px solid #ccc;
}
.total-pengeluaran {
  margin-top: auto;
  padding: 10px;
  border-top: 1px solid #ccc;
}
.none {
  margin: 0;
  padding: 0;
}
.button {
  background-color: rgb(31, 31, 160);
  padding: 10px;
  border: none;
  border-radius: 2px;
  color: white;
  margin-top: 20px;
}
</style>
