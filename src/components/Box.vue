<template>
  <div class="box row spaceBetween">
    <div class="row">
      <div class="tgl">{{ box.jam }}</div>
      <div class="name">{{ box.nama }}</div>
    </div>
    <div class="tgl">{{ formatMoney(box.pengeluaraan) }}</div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    box: Object,
    data: Array,
  },
  mounted() {
    this.$emit("total-bulan", this.totalBulan, this.box.tanggal.substr(3, 7));
  },
  computed: {
    totalBulan() {
      return this.box.totalBulan || 0;
    },
  },
  methods: {
    formatMoney(value) {
      return `Rp ${new Intl.NumberFormat("id-ID").format(value)}`;
    },
  },
};
</script>

<style scoped>
.box {
  border-bottom: 1px solid #ccc;
  padding: 10px;
  margin: 10px;
}
.name {
  margin-left: 5px;
}
.row {
  display: flex;
  flex-direction: row;
}
.spaceBetween {
  justify-content: space-between;
}
.total {
  position: absolute;
  bottom: 5px;
}
</style>
