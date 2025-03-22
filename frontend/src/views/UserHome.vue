<template>
  <div class="container">
    <!-- Navigation -->
    <nav class="navbar">
      <div class="nav-content">
        <div class="nav-left">
          <span class="nav-title"><i class="fas fa-book"></i>Library Vietnam</span>
        </div>
        <div class="nav-links">
          <input v-model="searchQuery" @input="searchBooks" type="text" class="search-bar"
            placeholder="Tìm kiếm sách..." />
          <RouterLink to="/user-home" class="nav-link">
            <i class="fas fa-home"></i> Trang chủ
          </RouterLink>
          <RouterLink to="/don-muon-sach" class="nav-link">
            <i class="fas fa-receipt"></i> Đơn mượn sách
          </RouterLink>
          <a class="user-info" @click="toggleDropdown">
            <i class="fas fa-user"></i> Xin chào, {{ authStore.user?.Ten }}
            <div v-if="showDropdown" class="dropdown">
              <button @click.prevent="goToUserInfo">
                <i class="fas fa-id-card"></i> Thông tin tài khoản
              </button>
              <button @click="logout">
                <i class="fas fa-sign-out-alt"></i> Đăng xuất
              </button>
            </div>
          </a>
        </div>
      </div>
    </nav>

    <!-- Main Content -->
    <main class="main-content">
      <div class="dashboard">
        <!-- Hiển thị kết quả tìm kiếm -->
        <div v-if="isSearching">
          <h2 class="search-title"><i class="fas fa-search"></i> Kết quả tìm kiếm</h2>
          <div class="search-results-container">
            <table v-if="searchResults.length > 0" class="table">
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
                <tr v-for="book in searchResults" :key="book._id">
                  <td>{{ book.TenSach }}</td>
                  <td>{{ book.TacGia }}</td>
                  <td>{{ book.DonGia }}</td>
                  <td>{{ book.SoQuyen }}</td>
                  <td>
                    <a href="#" @click.prevent="openAddRequestForm(book)" class="edit-link">
                      <i class="fas fa-plus-circle"></i> Đăng ký
                    </a>
                  </td>
                </tr>
              </tbody>
            </table>
            <p v-else class="no-results">Không tìm thấy kết quả phù hợp.</p>
          </div>
        </div>

        <!-- Hiển thị thành phần SachForUser nếu không tìm kiếm -->
        <SachForUser v-else />
      </div>
    </main>

    <!-- Modal đăng ký mượn sách -->
    <div v-if="isAddRequestModalOpen" class="add-request-modal">
      <div class="modal-content">
        <h3><i class="fas fa-book-reader"></i> Thêm đơn mượn sách</h3>
        <form @submit.prevent="addRequestData">
          <label>
            <i class="fas fa-book"></i> Tên Sách:
            <input type="text" v-model="newRequest.TenSach" disabled />
          </label>
          <label>
            <i class="fas fa-user"></i> Tên Độc Giả:
            <input type="text" v-model="newRequest.TenDocGia" disabled />
          </label>
          <label>
            <i class="fas fa-calendar-day"></i> Ngày Mượn:
            <input v-model="newRequest.NgayMuon" type="date" required />
          </label>
          <label>
            <i class="fas fa-calendar-check"></i> Ngày Trả:
            <input v-model="newRequest.NgayTra" type="date" required />
          </label>
          <button type="submit" class="confirm-button">
            <i class="fas fa-check-circle"></i> Thêm đơn mượn
          </button>
          <button @click.prevent="closeAddRequestForm" class="cancel-button">
            <i class="fas fa-times-circle"></i> Hủy
          </button>
        </form>
      </div>
    </div>
  </div>
</template>


<script setup>
import SachForUser from "../components/SachForUser.vue";
import { ref } from "vue";
import { useAuthStore } from "../stores/auth";
import { useRouter } from "vue-router";
import { searchBooksByName, searchBooksByAuthor } from "../stores/sach";
import { addRequest } from "../stores/theodoimuonsach";

const authStore = useAuthStore();
const router = useRouter();
const showDropdown = ref(false);
const searchQuery = ref(""); // Biến lưu trữ từ khóa tìm kiếm
const searchResults = ref([]); // Lưu kết quả tìm kiếm

const isSearching = ref(false); // Trạng thái để kiểm tra người dùng có đang tìm kiếm không

// Tìm kiếm sách
const searchBooks = async () => {
  isSearching.value = true; // Đánh dấu trạng thái là đang tìm kiếm
  if (!searchQuery.value.trim()) {
    searchResults.value = [];
    isSearching.value = false; // Nếu không nhập gì, xóa kết quả tìm kiếm
    return;
  }

  // Thử tìm kiếm theo tên trước
  const resultsByName = await searchBooksByName(searchQuery.value);

  // Nếu không tìm thấy theo tên, tìm kiếm theo tác giả
  if (resultsByName.length === 0) {
    const resultsByAuthor = await searchBooksByAuthor(searchQuery.value);
    searchResults.value = resultsByAuthor;
  } else {
    searchResults.value = resultsByName;
  }
};

// Điều hướng đến thông tin tài khoản
const goToUserInfo = () => {
  router.push("/info-user");
};

// Toggle menu
const toggleDropdown = () => {
  showDropdown.value = !showDropdown.value;
};

// Logout
const logout = () => {
  authStore.logout();
  router.push("/");
};
const isAddRequestModalOpen = ref(false);
  const newRequest = ref({
    TenSach: "",
    TenDocGia: authStore.user?.Ten, // Tên độc giả lấy từ thông tin đăng nhập
    NgayMuon: "",
    NgayTra: "",
  });
  
  // Mở form đăng ký mượn sách và điền tự động tên sách
  const openAddRequestForm = (book) => {
    newRequest.value.TenSach = book.TenSach; // Điền tên sách từ book vào form
    newRequest.value.TenDocGia = authStore.user?.Ten || ""; // Lấy tên người dùng từ auth store
    isAddRequestModalOpen.value = true;
  };
  
  // Đóng form thêm đơn mượn
  const closeAddRequestForm = () => {
    isAddRequestModalOpen.value = false;
    newRequest.value = {
      TenSach: "",
      TenDocGia: authStore.user?.Ten || "", // Reset lại tên độc giả
      NgayMuon: "",
      NgayTra: "",
    };
  };
  
  // Thêm đơn mượn sách mới
  const addRequestData = async () => {
    await addRequest(newRequest.value);
    closeAddRequestForm();
  };
</script>

<style>
.search-bar {
  padding: 0.5rem;
  margin-left: 1rem;
  border: 1px solid #ccc;
  border-radius: 0.375rem;
  font-size: 0.875rem;
  width: 200px;
}

.search-bar:focus {
  outline: none;
  border-color: #4f46e5;
  box-shadow: 0 0 0 2px rgba(79, 70, 229, 0.4);
}

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
  max-width: 1024px;
  margin: 2rem auto;
  padding: 1rem;
}
.section-title {
  font-size: 2rem;
  font-weight: bold;
  margin-bottom: 1rem;
}
.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
}
.stat-box {
  background-color: white;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
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
.section {
  margin-top: 2rem;
}
/* Thêm nền trắng cho phần kết quả tìm kiếm */
.table-container {
  background-color: white;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
}

/* Đảm bảo bảng kết quả tìm kiếm có nền trắng */
.table {
  background-color: white;
  color: black;
}

/* Chỉnh màu chữ để dễ đọc */
.table th,
.table td {
  color: #1e1e2f;
}

/* Thêm đường viền để dễ nhìn */
.table th {
  background-color: #f3f4f6;
  border-bottom: 2px solid #ddd;
}

.table tbody tr:nth-child(even) {
  background-color: #f9fafb;
}

.table tbody tr:hover {
  background-color: #e2e8f0;
}

/* Chỉnh màu chữ khi không tìm thấy kết quả */
.no-results {
  color: white;
  background-color: rgba(0, 0, 0, 0.4);
  padding: 10px;
  border-radius: 5px;
  text-align: center;
}
.edit-link {
  color: #4f46e5;
  margin-right: 0.5rem;
}
.edit-link:hover {
  color: #3730a3;
}
.delete-link {
  color: #e11d48;
}
.delete-link:hover {
  color: #9f1239;
}
.status-returned {
  background-color: #d1fae5;
  color: #065f46;
  padding: 0.25rem 0.5rem;
  border-radius: 0.375rem;
  font-size: 0.75rem;
}
.status-pending {
  background-color: #fef3c7;
  color: #92400e;
  padding: 0.25rem 0.5rem;
  border-radius: 0.375rem;
  font-size: 0.75rem;
}
.manage-link {
  color: #4f46e5;
}
.manage-link:hover {
  color: #3730a3;
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

.dropdown p {
  margin: 0;
  padding: 10px;
  border-bottom: 1px solid #ddd;
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
.add-request-button {
    background-color: #4f46e5;
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 0.375rem;
    cursor: pointer;
  }
  .add-request-button:hover {
    background-color: #3730a3;
  }
  .add-request-modal,
  .detail-modal {
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
    width: 400px;
  }
  .modal-content h3 {
    margin-top: 0;
  }
  .modal-content form label {
    display: block;
    margin: 10px 0;
  }
  
  .modal-actions button {
    margin-right: 0.5rem;
    padding: 0.5rem 1rem;
    border-radius: 0.375rem;
    cursor: pointer;
  }

  .search-title {
    color: black;
    font-size: 1.5rem;
    font-weight: bold;
    text-align: center;
    margin-bottom: 15px;
    background: white;
    padding: 10px;
    border-radius: 8px;
  }
</style>
