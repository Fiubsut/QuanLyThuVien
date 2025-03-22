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
    <main class="main-content">
      <div class="account-card">
        <h1 class="section-title">Thông Tin Tài Khoản</h1>
        <form @submit.prevent="handleSave" class="form-container">
          <div class="info-item" v-for="(value, key) in editForm" :key="key">
            <label :for="key">{{ labelMapping[key] }}:</label>
            <input :id="key" v-model="editForm[key]" :type="key === 'Password' ? 'password' : 'text'"
              :disabled="!isEditing" class="input-field" />
          </div>
          <div class="action-buttons">
            <button v-if="!isEditing" @click.prevent="enableEditing" class="edit-btn">Chỉnh sửa</button>
            <button v-else type="submit" class="save-btn">Lưu</button>
            <button v-if="isEditing" @click.prevent="cancelEditing" class="cancel-btn">Hủy</button>
          </div>
        </form>
      </div>
    </main>
  </div>
</template>


<script setup>
import { ref } from "vue";
import { useAuthStore } from "../stores/auth";
import { useRouter } from "vue-router";
import { updatenhanvien } from "@/stores/nhanvien";

// Initializing the stores and router
const authStore = useAuthStore();
const router = useRouter();

// Dropdown visibility state
const showDropdown = ref(false);

// Trạng thái chỉnh sửa
const isEditing = ref(false);

// Map labels cho input fields
const labelMapping = {
  HoTenNV: "Họ tên Nhân viên",
  ChucVu: "Chức vụ",
  DiaChi: "Địa chỉ",
  SoDienThoai: "Số Điện Thoại",
  Password: "Đổi mật khẩu"
};

// Form chỉnh sửa (bắt đầu với dữ liệu của người dùng)
const editForm = ref({
  HoTenNV: authStore.user?.HoTenNV || "",
  ChucVu: authStore.user?.ChucVu || "",
  DiaChi: authStore.user?.DiaChi || "",
  SoDienThoai: authStore.user?.SoDienThoai || "",
  Password: "",
});

// Bật chế độ chỉnh sửa
const enableEditing = () => {
  isEditing.value = true;
};

// Hủy chỉnh sửa
const cancelEditing = () => {
  isEditing.value = false;
  editForm.value = {
    HoTenNV: authStore.user?.HoTenNV || "",
    ChucVu: authStore.user?.ChucVu || "",
    DiaChi: authStore.user?.DiaChi || "",
    SoDienThoai: authStore.user?.SoDienThoai || "",
    Password: "",
  };
};

// Lưu thông tin
const handleSave = async () => {
  try {
    await updatenhanvien(authStore.user, editForm.value);
    alert("Cập nhật thông tin thành công!");
    authStore.user = { ...authStore.user, ...editForm.value }; // Cập nhật lại thông tin người dùng trong store
    isEditing.value = false;
  } catch (error) {
    console.error("Lỗi khi cập nhật thông tin:", error);
    alert("Có lỗi xảy ra khi cập nhật thông tin.");
  }
};

// Function to toggle the dropdown menu
const toggleDropdown = () => {
  showDropdown.value = !showDropdown.value;
};

// Function to handle logout and redirect
const logout = () => {
  authStore.logout();
  router.push("/");
};
</script>

<style scoped>
.container {
  background-color: #f7fafc;
  min-height: 100vh;
}

/* Navbar styling */
.navbar {
  background: #1e1e2f;
  color: white;
  padding: 1rem;
  display: flex;
  justify-content: center;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
}

.nav-content {
  display: flex;
  justify-content: space-between;
  width: 80%;
}

.nav-links a {
  color: white;
  margin-left: 1rem;
  text-decoration: none;
  transition: 0.3s;
}

.nav-links a:hover {
  color: #ffd700;
}

/* Main content */
.main-content {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 80vh;
}

/* Account card styling */
.account-card {
  background-color: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 400px;
}

/* Form styles */
.form-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.info-item {
  display: flex;
  flex-direction: column;
  gap: 0.3rem;
}

.input-field {
  padding: 0.6rem;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 1rem;
  width: 100%;
}

/* Action buttons */
.action-buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 1rem;
}

button {
  flex: 1;
  padding: 0.6rem;
  border-radius: 6px;
  border: none;
  font-size: 1rem;
  cursor: pointer;
  margin: 0 5px;
}

.edit-btn {
  background-color: #4f46e5;
  color: white;
}

.save-btn {
  background-color: #28a745;
  color: white;
}

.cancel-btn {
  background-color: #dc3545;
  color: white;
}

button:hover {
  opacity: 0.9;
}
</style>
