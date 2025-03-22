<template>
  <div class="container">
    <!-- Navigation -->
    <nav class="navbar">
      <div class="nav-content">
        <div class="nav-left">
          <span class="nav-title">Library Admin</span>
        </div>
        <div class="nav-links">
          <RouterLink to="/admin-home" class="nav-link">Trang chủ</RouterLink>
          <RouterLink to="/quan-ly-sach" class="nav-link">Quản lý Sách</RouterLink>
          <RouterLink to="/quan-ly-doc-gia" class="nav-link">Quản lý Độc giả</RouterLink>
          <RouterLink to="/quan-ly-muon-sach" class="nav-link">Quản lý Mượn sách</RouterLink>
          <RouterLink to="/quan-ly-nxb" class="nav-link">Quản lý NXB</RouterLink>
          <RouterLink to="/quan-ly-nhan-vien" class="nav-link">Quản lý Nhân viên</RouterLink>

          <a class="user-info" @click="toggleDropdown">
            Xin chào, {{ authStore.user?.HoTenNV }}
            <div v-if="showDropdown" class="dropdown">
              <button @click.prevent="goToAdminInfo">Thông tin tài khoản</button>
              <button @click="logout">Đăng xuất</button>
            </div>
          </a>
        </div>
      </div>
    </nav>

    <!-- Main Content -->
    <main>
      <DocGia />
      <!-- Modal -->
      <div v-if="showModal" class="modal-overlay">
        <div class="modal">
          <h2>Thêm Độc Giả</h2>
          <form @submit.prevent="submitForm">
            <label for="name">Tên độc giả:</label>
            <input id="name" v-model="docGia.ten" type="text" required />

            <label for="email">Email:</label>
            <input id="email" v-model="docGia.email" type="email" required />

            <div class="modal-buttons">
              <button type="submit" class="save-btn">Lưu</button>
              <button type="button" class="cancel-btn" @click="showModal = false">Hủy</button>
            </div>
          </form>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import DocGia from "../components/DocGia.vue";
import { ref } from "vue";
import { useAuthStore } from "../stores/auth";
import { useRouter } from "vue-router";

// Trạng thái modal
const showModal = ref(false);

// Dữ liệu độc giả
const docGia = ref({
  ten: "",
  email: "",
});

// Function xử lý lưu dữ liệu
const submitForm = () => {
  console.log("Thông tin độc giả:", docGia.value);
  showModal.value = false; // Đóng modal sau khi submit
};

// Auth và Router
const authStore = useAuthStore();
const router = useRouter();
const showDropdown = ref(false);

const goToAdminInfo = () => {
  router.push("/info-admin");
};

const toggleDropdown = () => {
  showDropdown.value = !showDropdown.value;
};

const logout = () => {
  authStore.logout();
  router.push("/");
};
</script>

<style>
/* Giữ nguyên CSS cũ */
.container {
  background-color: #f7fafc;
  min-height: 100vh;
}

.navbar {
  background-color: #000000;
  color: white;
  padding: 1rem;
}

.nav-content {
  display: flex;
  justify-content: space-between;
  max-width: 1024px;
  margin: 0 auto;
}

.nav-title {
  font-size: 1.5rem;
  font-weight: bold;
}

.nav-links a {
  color: white;
  margin-left: 1rem;
  text-decoration: none;
  font-size: 0.875rem;
}

.nav-links a:hover {
  background-color: #3869ca;
  padding: 0.5rem;
  border-radius: 0.375rem;
}

.user-info {
  position: relative;
  cursor: pointer;
}

.dropdown {
  position: absolute;
  right: 0;
  top: 30px;
  background-color: white;
  color: black;
  border: 1px solid #ddd;
  border-radius: 4px;
  width: 150px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  text-align: center;
  padding: 10px 0;
}

.dropdown button {
  width: 100%;
  background-color: transparent;
  color: #333;
  padding: 10px;
  cursor: pointer;
  border: none;
}

.dropdown button:hover {
  background-color: #f0f0f0;
}

/* CSS mới cho modal */
.open-modal-btn {
  background: #4caf50;
  color: white;
  padding: 10px 15px;
  border: none;
  cursor: pointer;
  border-radius: 5px;
  margin: 20px;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background: white;
  padding: 20px;
  border-radius: 10px;
  width: 400px;
  text-align: center;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
}

.modal-buttons {
  margin-top: 15px;
  display: flex;
  justify-content: space-around;
}

.save-btn {
  background: #2196f3;
  color: white;
  padding: 8px 15px;
  border: none;
  cursor: pointer;
  border-radius: 5px;
}

.cancel-btn {
  background: #f44336;
  color: white;
  padding: 8px 15px;
  border: none;
  cursor: pointer;
  border-radius: 5px;
}
</style>
