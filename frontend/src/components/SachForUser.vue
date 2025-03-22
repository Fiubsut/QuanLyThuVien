<template>
  <div class="table-container">
    <div class="header">
      <h2 class="section-title">Danh Mục Sách</h2>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Tên sách</th>
          <th>Tác giả</th>
          <th>Giá sách</th>
          <th>Số quyển</th>
          <th>Mượn sách</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="book in books" :key="book.id">
          <td>{{ book.TenSach }}</td>
          <td>{{ book.TacGia }}</td>
          <td>{{ book.DonGia }}</td>
          <td>{{ book.SoQuyen }}</td>
          <td>
            <a href="#" @click.prevent="openAddRequestForm(book)" class="edit-link">Đăng ký</a>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Form đăng ký mượn sách -->
    <div v-if="isAddRequestModalOpen" class="add-request-modal">
      <div class="modal-content">
        <h2 class="modal-title">Thêm đơn mượn sách mới</h2>
        <form @submit.prevent="addRequestData">
          <div class="form-group">
            <label for="tenSach">Tên Sách:</label>
            <input type="text" id="tenSach" v-model="newRequest.TenSach" disabled />
          </div>
          <div class="form-group">
            <label for="tenDocGia">Tên Độc Giả:</label>
            <input type="text" id="tenDocGia" v-model="newRequest.TenDocGia" disabled />
          </div>
          <div class="form-group">
            <label for="ngayMuon">Ngày Mượn:</label>
            <input type="date" id="ngayMuon" v-model="newRequest.NgayMuon" required />
          </div>
          <div class="form-group">
            <label for="ngayTra">Ngày Trả:</label>
            <input type="date" id="ngayTra" v-model="newRequest.NgayTra" required />
          </div>
          <div class="modal-actions">
            <button type="submit" class="submit-btn">Thêm đơn mượn</button>
            <button type="button" class="cancel-btn" @click="closeAddRequestForm">Hủy</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { books, fetchBooks } from "../stores/sach.js";
import { ref, onMounted } from "vue";
import { addRequest } from "../stores/theodoimuonsach";
import { useAuthStore } from "../stores/auth";

const authStore = useAuthStore();
const isAddRequestModalOpen = ref(false);
const newRequest = ref({
  TenSach: "",
  TenDocGia: authStore.user?.Ten || "",
  NgayMuon: "",
  NgayTra: "",
});

const openAddRequestForm = (book) => {
  newRequest.value.TenSach = book.TenSach;
  newRequest.value.TenDocGia = authStore.user?.Ten || "";
  isAddRequestModalOpen.value = true;
};

const closeAddRequestForm = () => {
  isAddRequestModalOpen.value = false;
  newRequest.value = {
    TenSach: "",
    TenDocGia: authStore.user?.Ten || "",
    NgayMuon: "",
    NgayTra: "",
  };
};

const addRequestData = async () => {
  await addRequest(newRequest.value);
  closeAddRequestForm();
};

onMounted(() => {
  fetchBooks();
});
</script>

<style scoped>
/* Modal background */
.add-request-modal {
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

/* Modal content */
.modal-content {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  width: 500px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

/* Tiêu đề */
.modal-title {
  font-size: 22px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 20px;
}

/* Form group */
.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
}

.form-group input {
  width: 100%;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

/* Nút hành động */
.modal-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.submit-btn {
  background-color: #4f46e5;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
}

.cancel-btn {
  background-color: #e53e3e;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
}

.submit-btn:hover {
  background-color: #3730a3;
}

.cancel-btn:hover {
  background-color: #c53030;
}
</style>
