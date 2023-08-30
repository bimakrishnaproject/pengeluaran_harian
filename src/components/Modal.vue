<template>
  <div class="modal-overlay" v-if="show">
    <div class="modal-content">
      <h2 class="none">Tambah Entri</h2>
      <form @submit.prevent="addItem">
        <p class="none top" for="itemName">Nama</p>
        <input
          v-model="newItem.nama"
          id="itemName"
          type="text"
          required
          class="input"
        />

        <p class="none top" for="itemPrice">Harga</p>
        <input
          v-model="newItem.pengeluaraan"
          id="itemPrice"
          type="number"
          required
          class="input"
          @input="removeLeadingZero"
        />
        <div class="error">
          {{ newItem?.errorMessage }}
        </div>

        <div class="modal-buttons">
          <button
            class="button margin width grey"
            type="button"
            @click="cancel"
          >
            Batal
          </button>
          <button class="button width" type="submit">Kirim</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: Boolean,
  },
  data() {
    return {
      newItem: {
        nama: "",
        pengeluaraan: 0,
        errorMessage: "",
      },
    };
  },
  methods: {
    addItem() {
      if (this.newItem.pengeluaraan < 1) {
        this.newItem = { errorMessage: "Tidak boleh dibawah 1 rupiah" };
        return;
      }
      this.$emit("add-item", this.newItem);
      this.newItem = { nama: "", pengeluaraan: 0 };
    },
    cancel() {
      this.$emit("cancel");
      this.newItem = { nama: "", pengeluaraan: 0 };
    },
    removeLeadingZero() {
      if (
        this.newItem.pengeluaraan.length > 1 &&
        this.newItem.pengeluaraan[0] === "0"
      ) {
        this.newItem.pengeluaraan = this.newItem.pengeluaraan.slice(1);
      }
    },
  },
};
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  width: 40vh;
}

.modal-buttons {
  display: flex;
  justify-content: flex-end;
  margin-top: 10px;
}

.input {
  width: 37vh;
  border-radius: 6px;
  border: 0.5px rgb(182, 182, 182) solid;
  padding: 10px;
  margin-top: 5px;
}

.top {
  margin-top: 15px;
}

.margin {
  margin-right: 5px;
}

.width {
  width: 80px;
}

.grey {
  background-color: gray;
}

.error {
  color: brown;
  margin-top: 5px;
}
</style>
