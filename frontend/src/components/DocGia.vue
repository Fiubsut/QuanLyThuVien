<template>
  <div class="table-container">
    <div class="header">
      <h2 class="section-title">Quản lý Độc Giả</h2>
      <button class="add-docgia-button" @click="openAddDocGiaForm">Thêm Độc Giả</button>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Họ Lót</th>
          <th>Tên</th>
          <th>Ngày Sinh</th>
          <th>Phái</th>
          <th>Địa Chỉ</th>
          <th>Số Điện Thoại</th>
          <th>Quản lý</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="docgia in docgias" :key="docgia._id">
          <td>{{ docgia.HoLot }}</td>
          <td>{{ docgia.Ten }}</td>
          <td>{{ new Date(docgia.NgaySinh).toLocaleDateString() }}</td>
          <td>{{ docgia.Phai }}</td>
          <td>{{ docgia.DiaChi }}</td>
          <td>{{ docgia.SoDienThoai }}</td>
          <td>
            <a href="#" @click.prevent="openEditForm(docgia)" class="edit-link">Edit</a>
            <a href="#" @click.prevent="deleteDocGiaData(docgia._id)" class="delete-link">Delete</a>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Form thêm độc giả -->
    <div v-if="isAddDocGiaModalOpen" class="modal-overlay">
      <div class="modal-content">
        <h2 class="modal-title">Thêm Độc Giả Mới</h2>
        <form @submit.prevent="addDocGiaData" class="form-container">
          <div class="form-row">
            <div class="form-group">
              <label>Họ Lót:</label>
              <input v-model="newDocGia.HoLot" type="text" required />
            </div>
            <div class="form-group">
              <label>Tên:</label>
              <input v-model="newDocGia.Ten" type="text" required />
            </div>
          </div>

          <div class="form-row">
            <div class="form-group">
              <label>Phái:</label>
              <select v-model="newDocGia.Phai" required>
                <option value="Nam">Nam</option>
                <option value="Nữ">Nữ</option>
              </select>
            </div>
            <div class="form-group">
              <label>Ngày Sinh:</label>
              <input v-model="newDocGia.NgaySinh" type="date" required />
            </div>
          </div>

          <div class="form-row">
            <div class="form-group">
              <label>Địa Chỉ:</label>
              <input v-model="newDocGia.DiaChi" type="text" required />
            </div>
            <div class="form-group">
              <label>Số Điện Thoại:</label>
              <input v-model="newDocGia.SoDienThoai" type="text" required />
            </div>
          </div>

          <div class="form-group full-width">
            <label>Mật Khẩu:</label>
            <input v-model="newDocGia.Password" type="password" required />
          </div>

          <div class="button-group">
            <button type="submit" class="primary-btn">Thêm Độc Giả</button>
            <button class="secondary-btn" @click.prevent="closeAddDocGiaForm">Hủy</button>
          </div>
        </form>
      </div>
    </div>

    <!-- Form chỉnh sửa độc giả -->
    <div v-if="isEditModalOpen" class="modal-overlay">
      <div class="modal-content">
        <h2 class="modal-title">Chỉnh sửa thông tin Độc Giả</h2>
        <form @submit.prevent="updateDocGiaData" class="form-container">
          <div class="form-row">
            <div class="form-group">
              <label>Họ Lót:</label>
              <input v-model="editDocGia.HoLot" type="text" required />
            </div>
            <div class="form-group">
              <label>Tên:</label>
              <input v-model="editDocGia.Ten" type="text" required />
            </div>
          </div>

          <div class="form-row">
            <div class="form-group">
              <label>Ngày Sinh:</label>
              <input v-model="editDocGia.NgaySinh" type="date" required />
            </div>
            <div class="form-group">
              <label>Phái:</label>
              <select v-model="editDocGia.Phai" required>
                <option value="Nam">Nam</option>
                <option value="Nữ">Nữ</option>
              </select>
            </div>
          </div>

          <div class="form-row">
            <div class="form-group">
              <label>Địa Chỉ:</label>
              <input v-model="editDocGia.DiaChi" type="text" required />
            </div>
            <div class="form-group">
              <label>Số Điện Thoại:</label>
              <input v-model="editDocGia.SoDienThoai" type="text" required />
            </div>
          </div>

          <div class="button-group">
            <button type="submit" class="primary-btn">Cập nhật</button>
            <button class="secondary-btn" @click.prevent="closeEditForm">Hủy</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { docgias, fetchDocGias, addDocGia, updateDocGia, deleteDocGia } from "../stores/docgia.js";

const isEditModalOpen = ref(false);
const editDocGia = ref({});
const isAddDocGiaModalOpen = ref(false);
const newDocGia = ref({
  HoLot: "",
  Ten: "",
  NgaySinh: "",
  Phai: "",
  DiaChi: "",
  SoDienThoai: "",
  Password: ""
});

const formatDate = (date) => {
  return date ? new Date(date).toISOString().split("T")[0] : "";
};

const openEditForm = (docgia) => {
  editDocGia.value = { ...docgia,
    NgaySinh: formatDate(docgia.NgaySinh),
   }; // Clone data for editing
  isEditModalOpen.value = true;
};

const closeEditForm = () => {
  isEditModalOpen.value = false;
};

const updateDocGiaData = async () => {
  await updateDocGia(editDocGia.value);
  closeEditForm();
  fetchDocGias();
};

const openAddDocGiaForm = () => {
  isAddDocGiaModalOpen.value = true;
};

const closeAddDocGiaForm = () => {
  isAddDocGiaModalOpen.value = false;
};

const addDocGiaData = async () => {
  await addDocGia(newDocGia.value);
  closeAddDocGiaForm();
  fetchDocGias();
};

const deleteDocGiaData = async (id) => {
  if (confirm("Bạn có chắc muốn xóa độc giả này không?")) {
    await deleteDocGia(id);
    fetchDocGias();
  }
};

onMounted(fetchDocGias);
</script>

<style scoped>
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* Nút "Thêm Độc Giả" */
.add-docgia-button {
  background-color: #4f46e5;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 0.375rem;
  cursor: pointer;
  transition: background 0.3s ease-in-out;
}

.add-docgia-button:hover {
  background-color: #3730a3;
}

/* Bố cục hai cột */
.form-row {
  display: flex;
  justify-content: space-between;
  gap: 15px;
}

.form-group {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.full-width {
  width: 100%;
}

/* Căn chỉnh các input */
.form-group input,
.form-group select {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 1rem;
}

/* Nút hành động */
.button-group {
  display: flex;
  justify-content: space-between;
  gap: 10px;
  margin-top: 15px;
}

.primary-btn {
  background-color: #4f46e5;
  color: white;
  padding: 12px;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  cursor: pointer;
  flex: 1;
}

.primary-btn:hover {
  background-color: #3730a3;
}

.secondary-btn {
  background-color: #f44336;
  color: white;
  padding: 12px;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  cursor: pointer;
  flex: 1;
}

.secondary-btn:hover {
  background-color: #999;
}

/* Lớp nền mờ cho modal */
.edit-modal,
.add-book-modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

/* Căn chỉnh lại modal */
.modal-content {
  background: white;
  padding: 1.5rem;
  border-radius: 10px;
  width: 350px; /* Giảm kích thước chiều rộng */
  max-width: 90%;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
  animation: fadeIn 0.3s ease-in-out;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%); /* Đưa form vào giữa màn hình */
}

/* Tiêu đề form */
.modal-title {
  font-size: 1.6rem;
  font-weight: bold;
  margin-bottom: 1.5rem;
  text-align: center;
  color: #333;
}

/* Nhãn và ô nhập */
.modal-content form label {
  display: block;
  font-weight: 600;
  margin-bottom: 8px;
  color: #444;
}

.modal-content form input,
.modal-content form select {
  width: calc(100% - 20px);
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.3s ease;
  display: block;
  margin-bottom: 15px;
}

.modal-content form input:focus,
.modal-content form select:focus {
  border-color: #4f46e5;
  outline: none;
  box-shadow: 0px 0px 5px rgba(79, 70, 229, 0.5);
}

/* Nhóm nút */
.modal-content .button-group {
  display: flex;
  justify-content: space-between;
  gap: 10px;
  margin-top: 15px;
}

/* Nút hành động */
.modal-content form button {
  flex: 1;
  padding: 12px;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  cursor: pointer;
  transition: background 0.3s ease;
  font-weight: bold;
}

/* Nút "Thêm Độc Giả" hoặc "Cập Nhật" */
.modal-content form button[type="submit"] {
  background-color: #4f46e5;
  color: white;
}

.modal-content form button[type="submit"]:hover {
  background-color: #3730a3;
}

/* Nút "Hủy" */
.modal-content form button.cancel-btn {
  background-color: #ccc;
  color: black;
}

.modal-content form button.cancel-btn:hover {
  background-color: #999;
}

</style>

