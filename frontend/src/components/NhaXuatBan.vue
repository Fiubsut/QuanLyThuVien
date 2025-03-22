<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="table-container">
    <div class="header">
      <h2 class="section-title">Quản lý Nhà Xuất Bản</h2>
      <button class="add-nxb-button" @click="openAddNxbForm">
        Thêm Nhà Xuất Bản
      </button>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Mã Nhà Xuất Bản</th>
          <th>Tên Nhà Xuất Bản</th>
          <th>Địa Chỉ</th>
          <th>Quản lý</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="nxb in nxbs" :key="nxb.id">
          <td>{{ nxb._id }}</td>
          <td>{{ nxb.TenNXB }}</td>
          <td>{{ nxb.DiaChi }}</td>
          <td>
            <a href="#" @click.prevent="openEditForm(nxb)" class="edit-link">Edit</a>
            <a href="#" class="delete-link" @click.prevent="deleteNxbData(nxb)">Delete</a>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Form chỉnh sửa nhà xuất bản -->
    <div v-if="isEditModalOpen" class="edit-modal">
      <div class="modal-content">
        <h3>Chỉnh sửa thông tin Nhà Xuất Bản</h3>
        <form @submit.prevent="updateNxbData">
          <label>
            Tên Nhà Xuất Bản:
            <input v-model="editNxb.TenNXB" type="text" required />
          </label>
          <label>
            Địa Chỉ:
            <input v-model="editNxb.DiaChi" type="text" />
          </label>
          <button type="submit">Cập nhật</button>
          <button class="cancel-btn"  @click.prevent="closeEditForm">Hủy</button>
        </form>
      </div>
    </div>

    <!-- Form thêm nhà xuất bản mới -->
    <div v-if="isAddNxbModalOpen" class="add-nxb-modal">
      <div class="modal-content">
        <h3>Thêm Nhà Xuất Bản Mới</h3>
        <form @submit.prevent="addNxbData">
          <label for="TenNXB">Tên Nhà Xuất Bản:</label>
          <input v-model="newNxb.TenNXB" id="TenNXB" type="text" required />

          <label for="DiaChi">Địa Chỉ:</label>
          <input v-model="newNxb.DiaChi" id="DiaChi" type="text" />

          <button type="submit">Thêm Nhà Xuất Bản</button>
          <button type="button" class="cancel-btn" @click.prevent="closeAddNxbForm">Hủy</button>
        </form>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { nxbs, fetchNxbs, updateNxb, addNxb, deleteNxb  } from "../stores/nhaxuatban.js";

const isEditModalOpen = ref(false);
const editNxb = ref({});

const openEditForm = (nxb) => {
  editNxb.value = { ...nxb }; // Sao chép thông tin nhà xuất bản vào biến tạm để chỉnh sửa
  isEditModalOpen.value = true;
};

const closeEditForm = () => {
  isEditModalOpen.value = false;
};

// Hàm cập nhật nhà xuất bản
const updateNxbData = async () => {
  await updateNxb(editNxb.value); // Gọi hàm cập nhật trong store
  closeEditForm(); // Đóng modal sau khi cập nhật
  await fetchNxbs(); // Làm mới danh sách nhà xuất bản sau khi cập nhật
};

const deleteNxbData = async (id) => {
  if (confirm("Bạn có chắc muốn xóa nhà xuất bản này không?")) {
    await deleteNxb(id);
    await fetchNxbs();
  }
};

const isAddNxbModalOpen = ref(false);
const newNxb = ref({
  TenNXB: "",
  DiaChi: "",
});

// Hàm mở modal thêm nhà xuất bản
const openAddNxbForm = () => {
  isAddNxbModalOpen.value = true;
};

// Hàm đóng modal thêm nhà xuất bản
const closeAddNxbForm = () => {
  isAddNxbModalOpen.value = false;
};

// Hàm thêm nhà xuất bản mới
const addNxbData = async () => {
  await addNxb(newNxb.value); // Gọi hàm thêm nhà xuất bản trong store
  closeAddNxbForm(); // Đóng modal sau khi thêm
  fetchNxbs(); // Làm mới danh sách nhà xuất bản sau khi thêm
};

// Đảm bảo fetchNxbs được gọi khi component được mount
onMounted(() => {
  fetchNxbs();
});
</script>

<style scoped>
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.add-nxb-button {
  background-color: #4f46e5;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 0.375rem;
  cursor: pointer;
}
.add-nxb-button:hover {
  background-color: #3730a3;
}

.add-nxb-modal,
.edit-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  /* Nền mờ tối */
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  padding: 2rem;
  border-radius: 10px;
  width: 450px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
  text-align: center;
}

.modal-content h3 {
  margin-bottom: 1rem;
  font-size: 1.5rem;
  font-weight: bold;
}

.modal-content form label {
  display: block;
  text-align: left;
  margin-bottom: 10px;
  font-weight: 500;
}

.modal-content form input {
  width: 100%;
  padding: 8px;
  border-radius: 5px;
  border: 1px solid #ccc;
  margin-bottom: 15px;
}

.modal-content form button {
  width: 45%;
  padding: 10px;
  border: none;
  border-radius: 5px;
  font-size: 1rem;
  cursor: pointer;
}

.modal-content form button[type="submit"] {
  background-color: #4f46e5;
  color: white;
  margin-right: 10px;
}

.modal-content form button[type="submit"]:hover {
  background-color: #3730a3;
}

.modal-content form button[type="button"] {
  background-color: #e5e5e5;
}

.modal-content form button[type="button"]:hover {
  background-color: #d4d4d4;
}

/* Nút "Hủy" (dùng trong form thêm đơn mượn) */
.modal-content form button.cancel-btn {
  background-color: #e63946 !important;
  /* Màu đỏ */
  color: white !important;
  /* Chữ trắng */
  border: none;
  font-weight: bold;
  transition: background 0.3s ease-in-out;
  padding: 10px;
  border-radius: 5px;
  cursor: pointer;
}

/* Hiệu ứng hover */
.modal-content form button.cancel-btn:hover {
  background-color: #b71c1c !important;
  /* Màu đỏ đậm hơn khi hover */
}
</style>
