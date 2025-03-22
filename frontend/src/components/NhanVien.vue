<template>
  <div class="table-container">
    <div class="header">
      <h2 class="section-title">Quản lý Nhân Viên</h2>
      <button class="add-nhanvien-button" @click="openAddnhanvienForm">Thêm Nhân Viên</button>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Họ tên nhân viên</th>
          <th>Chức vụ</th>
          <th>Địa Chỉ</th>
          <th>Số Điện Thoại</th>
          <th>Quản lý</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="nhanvien in nhanviens" :key="nhanvien._id">
          <td>{{ nhanvien.HoTenNV }}</td>
          <td>{{ nhanvien.ChucVu }}</td>
          <td>{{ nhanvien.DiaChi }}</td>
          <td>{{ nhanvien.SoDienThoai }}</td>
          <td>
            <a href="#" @click.prevent="openEditForm(nhanvien)" class="edit-link">Edit</a>
            <a href="#" @click.prevent="deletenhanvienData(nhanvien._id)" class="delete-link">Delete</a>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Form chỉnh sửa nhân viên
   -->
    <div v-if="isEditModalOpen" class="edit-modal">
      <div class="modal-content">
        <h3>Chỉnh sửa thông tin nhân viên</h3>
        <form @submit.prevent="updatenhanvienData">
          <div class="form-container">
            <div class="form-column">
              <label>Họ tên nhân viên: <input v-model="editnhanvien.HoTenNV" type="text" required /></label>
              <label>Chức vụ: <input v-model="editnhanvien.ChucVu" type="text" required /></label>
            </div>
            <div class="form-column">
              <label>Địa Chỉ: <input v-model="editnhanvien.DiaChi" type="text" required /></label>
              <label>Số Điện Thoại: <input v-model="editnhanvien.SoDienThoai" type="text" required /></label>
            </div>
          </div>
          <div class="modal-actions">
            <button type="submit">Cập nhật</button>
            <button @click.prevent="closeEditForm">Hủy</button>
          </div>
        </form>
      </div>
    </div>

    <!-- Form thêm nhân viên-->
    <div v-if="isAddnhanvienModalOpen" class="add-nhanvien-modal">
      <div class="modal-content">
        <h3>Thêm Nhân Viên Mới</h3>
        <form @submit.prevent="addnhanvienData">
          <div class="form-container">
            <div class="form-column">
              <label>Họ tên nhân viên: <input v-model="newnhanvien.HoTenNV" type="text" required /></label>
              <label>Chức vụ: <input v-model="newnhanvien.ChucVu" type="text" required /></label>
            </div>
            <div class="form-column">
              <label>Địa Chỉ: <input v-model="newnhanvien.DiaChi" type="text" required /></label>
              <label>Số Điện Thoại: <input v-model="newnhanvien.SoDienThoai" type="text" required /></label>
            </div>
          </div>
          <label>Mật Khẩu: <input v-model="newnhanvien.Password" type="password" required /></label>
          <div class="modal-actions">
            <button type="submit">Thêm nhân viên</button>
            <button @click.prevent="closeAddnhanvienForm">Hủy</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
  
  <script setup>
  import { ref, onMounted } from "vue";
  import { nhanviens, fetchnhanviens, addnhanvien, updatenhanvien, deletenhanvien } from "../stores/nhanvien.js";
  
  const isEditModalOpen = ref(false);
  const editnhanvien = ref({});
  const isAddnhanvienModalOpen = ref(false);
  const newnhanvien = ref({
    HoTenNV: "",
    ChucVu: "",
    DiaChi: "",
    SoDienThoai: "",
    Password: ""
  });
  
  
  const openEditForm = (nhanvien) => {
    editnhanvien.value = { ...nhanvien}; // Clone data for editing
    isEditModalOpen.value = true;
  };
  
  const closeEditForm = () => {
    isEditModalOpen.value = false;
  };
  
  const updatenhanvienData = async () => {
    await updatenhanvien(editnhanvien.value);
    closeEditForm();
    fetchnhanviens();
  };
  
  const openAddnhanvienForm = () => {
    isAddnhanvienModalOpen.value = true;
  };
  
  const closeAddnhanvienForm = () => {
    isAddnhanvienModalOpen.value = false;
  };
  
  const addnhanvienData = async () => {
    await addnhanvien(newnhanvien.value);
    closeAddnhanvienForm();
    fetchnhanviens();
  };
  
  const deletenhanvienData = async (id) => {
    if (confirm("Bạn có chắc muốn xóa nhân viên này không?")) {
      await deletenhanvien(id);
      fetchnhanviens();
    }
  };
  
  onMounted(fetchnhanviens);
  </script>
  
  <style scoped>
  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .add-nhanvien-button {
    background-color: #4f46e5;
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 0.375rem;
    cursor: pointer;
  }

  .add-nhanvien-button:hover {
    background-color: #3730a3;
  }

  .add-nhanvien-modal,
  .edit-modal {
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

  .modal-content {
    background: white;
    padding: 2rem;
    border-radius: 0.5rem;
    width: 500px;
  }

  /* Chia form thành hai cột */
  .form-container {
    display: flex;
    justify-content: space-between;
    gap: 20px;
  }

  .form-column {
    width: 48%;
  }

  /* Định dạng input */
  .modal-content form label {
    display: block;
    margin-bottom: 10px;
  }

  .modal-content form input {
    width: 100%;
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 5px;
  }

  /* Định dạng phần mật khẩu */
  .modal-content form label:last-child {
    margin-top: 15px;
  }

  /* Căn chỉnh nút hành động */
  .modal-actions {
    display: flex;
    justify-content: center;
    gap: 10px;
    margin-top: 20px;
  }

  .modal-actions button {
    padding: 0.5rem 1rem;
    border-radius: 0.375rem;
    cursor: pointer;
    border: none;
  }

  .modal-actions button:nth-child(1) {
    background-color: #4f46e5;
    color: white;
  }

  .modal-actions button:nth-child(2) {
    background-color: #f44336;
    color: white;
  }
</style>