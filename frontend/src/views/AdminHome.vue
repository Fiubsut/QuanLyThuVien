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
      <div class="dashboard">
        <h1 class="section-title">Thống Kê</h1>
        <div class="stats-grid">
          <div v-for="stat in stats" :key="stat.name" class="stat-box">
            <dt class="stat-name">{{ stat.name }}</dt>
            <dd class="stat-value">{{ stat.value }}</dd>
          </div>
        </div>
      </div>

      <!-- Hiển thị nội dung theo chiều dọc -->
      <div class="content-sections">
        <section class="content-box">
          <h2 class="content-title">Quản lý Sách</h2>
          <Sach />
        </section>

        <section class="content-box">
          <h2 class="content-title">Quản lý Độc giả</h2>
          <Docgia />
        </section>

        <section class="content-box">
          <h2 class="content-title">Theo dõi Mượn Sách</h2>
          <Theodoimuonsach />
        </section>

        <section class="content-box">
          <h2 class="content-title">Quản lý Nhà Xuất Bản</h2>
          <NhaXuatBan />
        </section>

        <section class="content-box">
          <h2 class="content-title">Quản lý Nhân Viên</h2>
          <NhanVien />
        </section>
      </div>
    </main>
  </div>
</template>

<script setup>
import Sach from "../components/Sach.vue";
import Docgia from "../components/DocGia.vue"; // Corrected the file name capitalization
import Theodoimuonsach from "../components/TheoDoiMuonSach.vue"; // Corrected the file name capitalization
import NhaXuatBan from "../components/NhaXuatBan.vue"; // Corrected the file name capitalization
import NhanVien from "../components/NhanVien.vue"; // Corrected the file name capitalization
import { ref, onMounted } from "vue";
import { useAuthStore } from "../stores/auth";
import { useRouter } from "vue-router";
import axios from 'axios';

// Initializing the stores and router
const authStore = useAuthStore();
const router = useRouter();

// Dropdown visibility state
const showDropdown = ref(false);


const goToAdminInfo = () => {
    router.push("/info-admin"); // Đảm bảo route có tên là 'InfoAdmin'
}

// Function to toggle the dropdown menu
const toggleDropdown = () => {
  showDropdown.value = !showDropdown.value;
};

// Function to handle logout and redirect
const logout = () => {
  authStore.logout();
  router.push("/");
};

const stats = ref([
  { name: "Số lượng sách", value: 0 },
  { name: "Số lượng quyển", value: 0},
  { name: "Sách đang mượn", value: 0 },
  { name: "Số lượng người dùng", value: 0 },
]);

const fetchStats = async () => {
  try {
    const response = await axios.get("http://localhost:5000/api/stats");

    // Log response to check if data is correctly received
    console.log("API Response:", response.data);

    // Update stats value
    stats.value[0].value = response.data.totalBooks;
    stats.value[1].value = response.data.totalCopies;
    stats.value[2].value = response.data.booksOnLoan;
    stats.value[3].value = response.data.activeUsers;

    // Log stats to check if it has been updated
    console.log("Updated stats:", stats.value);

  } catch (error) {
    console.error("Error fetching stats:", error);
  }
};
onMounted(fetchStats);

</script>


<style>
.container {
  background-color: #f7fafc;
  min-height: 100vh;
  padding-bottom: 2rem;
}

/* Navbar giữ nguyên */
.navbar {
  background: #1e1e2f !important;
  color: white !important;
  padding: 1rem;
  width: 100%;
  display: flex;
  justify-content: center;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
}
main {
  margin-top: 50px; /* Điều chỉnh khoảng cách theo chiều cao của navbar */
  padding: 1rem;
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
  max-width: 1024px;
  margin: 2rem auto;
  padding: 1rem;
}

/* Tiêu đề */
.section-title {
  font-size: 2rem;
  padding: 15px 30px;
  font-weight: bold;
  text-align: center;
  color: black;
  margin-bottom: 1.5rem;
  background-color: white;
  border-radius: 10px;
}

/* Bố cục dọc */
.content-sections {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

/* Hộp nội dung */
.content-box {
  background-color: white;
  padding: 1.5rem;
  border-radius: 10px;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
}

/* Tiêu đề của mỗi phần */
.content-title {
  font-size: 1.5rem;
  font-weight: bold;
  color: #1f2937;
  margin-bottom: 1rem;
}

/* Thống kê */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
  margin-bottom: 2rem;
}

.stat-box {
  background-color: white;
  padding: 1.5rem;
  border-radius: 0.5rem;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.stat-name {
  font-size: 0.875rem;
  color: #6b7280;
}

.stat-value {
  font-size: 1.875rem;
  font-weight: bold;
  color: #1f2937;
}

/* User info dropdown */
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
  border-radius: 6px;
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
</style>
