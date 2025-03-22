<template>
  <div class="container">
    <!-- Navigation -->
    <nav class="navbar">
      <div class="nav-content">
        <div class="nav-left">
          <span class="nav-title">Library</span>
        </div>
        <div class="nav-links">
          <RouterLink to="/user-home" class="nav-link">Trang chủ</RouterLink>
          <RouterLink to="/don-muon-sach" class="nav-link">Đơn mượn sách</RouterLink>
          <a class="user-info" @click="toggleDropdown">
            Xin chào, {{ authStore.user?.Ten }}
            <div v-if="showDropdown" class="dropdown">
              <button @click.prevent="goToUserInfo">Thông tin tài khoản</button>
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
        <form @submit.prevent="handleSave">
          <div class="info-grid">
            <div v-for="(value, key) in editForm" :key="key" class="info-item">
              <label :for="key">{{ labels[key] }}:</label>
              <input :id="key" v-model="editForm[key]" :disabled="!isEditing"
                :type="key === 'Password' ? 'password' : 'text'" />
            </div>
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
import { updateDocGia } from "@/stores/docgia";

const authStore = useAuthStore();
const router = useRouter();
const showDropdown = ref(false);
const isEditing = ref(false);

const labels = {
  HoLot: "Họ lót",
  Ten: "Tên",
  NgaySinh: "Ngày sinh",
  Phai: "Phái",
  DiaChi: "Địa chỉ",
  SoDienThoai: "Số Điện Thoại",
  Password: "Đổi mật khẩu",
};

const editForm = ref({
  HoLot: authStore.user?.HoLot || "",
  Ten: authStore.user?.Ten || "",
  NgaySinh: authStore.user?.NgaySinh || "",
  Phai: authStore.user?.Phai || "",
  DiaChi: authStore.user?.DiaChi || "",
  SoDienThoai: authStore.user?.SoDienThoai || "",
  Password: "",
});

const enableEditing = () => (isEditing.value = true);
const cancelEditing = () => {
  isEditing.value = false;
  editForm.value = {
    HoLot: authStore.user?.HoLot || "",
    Ten: authStore.user?.Ten || "",
    NgaySinh: authStore.user?.NgaySinh || "",
    Phai: authStore.user?.Phai || "",
    DiaChi: authStore.user?.DiaChi || "",
    SoDienThoai: authStore.user?.SoDienThoai || "",
    Password: "", // Reset password field
  };
};

const handleSave = async () => {
  try {
    await updateDocGia(authStore.user, editForm.value);
    alert("Cập nhật thành công!");
    authStore.user = { ...authStore.user, ...editForm.value };
    isEditing.value = false;
  // eslint-disable-next-line no-unused-vars
  } catch (error) {
    alert("Lỗi khi cập nhật.");
  }
};

const toggleDropdown = () => (showDropdown.value = !showDropdown.value);
const logout = () => {
  authStore.logout();
  router.push("/");
};
</script>

<style scoped>
.container {
  background: linear-gradient(to right, #667eea, #764ba2);
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 2rem;
}

.navbar {
  background: #1e1e2f;
  color: white;
  padding: 1rem;
  width: 100%;
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

.user-info {
  position: relative;
  cursor: pointer;
}

.dropdown {
  position: absolute;
  right: 0;
  top: 30px;
  background: white;
  color: black;
  padding: 10px;
  border-radius: 6px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.main-content {
  width: 50%;
  background: white;
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
}

.account-card {
  width: 100%;
}

.section-title {
  font-size: 1.8rem;
  font-weight: bold;
  text-align: center;
  margin-bottom: 1.5rem;
}

.info-grid {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.info-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #f3f4f6;
  padding: 0.5rem;
  border-radius: 6px;
}

.info-item label {
  font-weight: bold;
  flex: 1;
}

.info-item input {
  flex: 2;
  padding: 0.5rem;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
}

.action-buttons {
  display: flex;
  justify-content: center;
  gap: 1rem;
  margin-top: 1rem;
}

button {
  background: #4f46e5;
  color: white;
  padding: 0.7rem 1.5rem;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: 0.3s;
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
