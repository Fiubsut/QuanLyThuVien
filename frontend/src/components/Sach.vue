<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="table-container">
    <div class="header">
      <h2 class="section-title">Quản lý sách</h2>
      <button class="add-book-button" @click="openAddBookForm">Thêm sách</button>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Tên sách</th>
          <th>Tác giả</th>
          <th>Giá sách</th>
          <th>Số quyển</th>
          <th>Quản lý</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="book in books" :key="book.id">
          <td>{{ book.TenSach }}</td>
          <td>{{ book.TacGia }}</td>
          <td>{{ book.DonGia }}</td>
          <td>{{ book.SoQuyen }}</td>
          <td>
            <a href="#" @click.prevent="openEditForm(book)" class="edit-link">Edit</a>
            <a href="#" class="delete-link" @click.prevent="deleteBookData(book)">Delete</a>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Form chỉnh sửa sách -->
    <div v-if="isEditModalOpen" class="edit-modal">
      <div class="modal-content">
        <h3>Chỉnh sửa thông tin sách</h3>
        <form @submit.prevent="updateBookData">
          <label>
            Tên sách:
            <input v-model="editBook.TenSach" type="text" required />
          </label>
          <label>
            Tác giả:
            <input v-model="editBook.TacGia" type="text" required />
          </label>
          <label>
            Giá sách:
            <input v-model="editBook.DonGia" type="number" required />
          </label>
          <label>
            Số quyển:
            <input v-model="editBook.SoQuyen" type="number" required />
          </label>
          <label>
            Năm xuất bản:
            <input v-model="editBook.NamXuatBan" type="number" required />
          </label>
          <label>
            Nhà xuất bản:
            <input v-model="editBook.TenNXB" type="text" required />
          </label>
          <div class="button-group">
            <button type="submit">Cập nhật</button>
            <button type="button" class="cancel-btn" @click.prevent="closeEditForm">Hủy</button>
          </div>
        </form>
      </div>
    </div>
    <div v-if="isAddBookModalOpen" class="add-book-modal">
      <div class="modal-content">
        <h3>Thêm sách mới</h3>
        <form @submit.prevent="addBook">
          <label>
            Tên sách:
            <input v-model="newBook.TenSach" type="text" required />
          </label>
          <label>
            Đơn giá:
            <input v-model="newBook.DonGia" type="number" required />
          </label>
          <label>
            Số quyển:
            <input v-model="newBook.SoQuyen" type="number" required />
          </label>
          <label>
            Tác giả:
            <input v-model="newBook.TacGia" type="text" required />
          </label>
          <label>
            Năm xuất bản:
            <input v-model="newBook.NamXuatBan" type="number" required />
          </label>
          <label>
            Nhà xuất bản:
            <input v-model="newBook.TenNXB" type="text" required />
          </label>
          <div class="button-group">
            <button type="submit" @click.prevent="addBookData">Thêm sách</button>
            <button type="button" class="cancel-btn" @click.prevent="closeAddBookForm">Hủy</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { books, fetchBooks, updateBook, addBook, deleteBook  } from "../stores/sach.js";
import { nxbs, fetchNxbs  } from "../stores/nhaxuatban.js";
import { ref, onMounted } from "vue";

const isEditModalOpen = ref(false);
const editBook = ref({});

const openEditForm = (book) => {
  editBook.value = { ...book }; // Sao chép thông tin sách vào biến tạm để chỉnh sửa
  isEditModalOpen.value = true;
  const nxb = nxbs.value.find((nxb) => nxb._id === book.MaNXB);
  if (nxb) {
    editBook.value.TenNXB = nxb.TenNXB;
  }
};

const closeEditForm = () => {
  isEditModalOpen.value = false;
};

// Hàm cập nhật sách
const updateBookData = async () => {
  await updateBook(editBook.value); // Gọi hàm cập nhật trong store
  closeEditForm(); // Đóng modal sau khi cập nhật
  await fetchBooks(); // Làm mới danh sách sách sau khi cập nhật
};

const isAddBookModalOpen = ref(false);
const newBook = ref({
  TenSach: "",
  TacGia: "",
  DonGia: 0,
  SoQuyen: 0,
  NamXuatBan: 0,
  TenNXB: "",
});

// Hàm mở modal thêm sách
const openAddBookForm = () => {
  isAddBookModalOpen.value = true;
};

// Hàm đóng modal thêm sách
const closeAddBookForm = () => {
  isAddBookModalOpen.value = false;
};

// Hàm thêm sách mới
const addBookData = async () => {
  await addBook(newBook.value); // Gọi hàm thêm sách trong store
  closeAddBookForm(); // Đóng modal sau khi thêm
  fetchBooks(); // Làm mới danh sách sách sau khi thêm
};

const deleteBookData = async (id) => {
  if (confirm("Bạn có chắc muốn xóa sách này không?")) {
    await deleteBook(id);
    await fetchBooks();
  }
};

// Đảm bảo fetchBooks được gọi khi component được mount
onMounted(() => {
  fetchBooks();
  fetchNxbs();
});
</script>

<style scoped>

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* Styles for the "Thêm sách" button */
.add-book-button {
  background-color: #4f46e5;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 0.375rem;
  cursor: pointer;
}
.add-book-button:hover {
  background-color: #3730a3;
}

/* Styles for the add book modal */
.add-book-modal {
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
/* Căn chỉnh lại modal */
.modal-content {
  background: white;
  padding: 2rem;
  border-radius: 10px;
  width: 450px;
  max-width: 90%;
  /* Đảm bảo modal không tràn màn hình */
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
  animation: fadeIn 0.3s ease-in-out;
}

.edit-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  /* Làm mờ nền */
  display: flex;
  justify-content: center;
  /* Căn giữa theo chiều ngang */
  align-items: center;
  /* Căn giữa theo chiều dọc */
  z-index: 1000;
  /* Hiển thị trên cùng */
}

/* Tiêu đề form */
.modal-content h3 {
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

.modal-content form input {
  width: calc(100% - 20px);
  /* Tránh tràn lề */
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.3s ease;
  display: block;
  margin-bottom: 15px;
}

.modal-content form input:focus {
  border-color: #4f46e5;
  outline: none;
  box-shadow: 0px 0px 5px rgba(79, 70, 229, 0.5);
}

/* Nhóm các nút lại với khoảng cách hợp lý */
.modal-content .button-group {
  display: flex;
  justify-content: space-between;
  /* Căn đều hai bên */
  gap: 15px;
  /* Tạo khoảng cách giữa các nút */
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

/* Nút "Cập nhật" hoặc "Thêm sách" */
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
}

.modal-content form button.cancel-btn:hover {
  background-color: #999;
}


.button-group {
  display: flex;
  justify-content: space-between;
  /* Căn đều hai bên */
  gap: 15px;
  /* Tạo khoảng cách giữa hai nút */
}

.button-group button {
  flex: 1;
  /* Đảm bảo các nút có cùng kích thước */
  padding: 12px;
  border-radius: 6px;
  font-size: 1rem;
  font-weight: bold;
  cursor: pointer;
}

/* Màu cho nút chính */
.button-group button[type="submit"] {
  background-color: #4f46e5;
  color: white;
}

.button-group button[type="submit"]:hover {
  background-color: #3730a3;
}

/* Nút "Hủy" */
.button-group .cancel-btn,
.modal-content form .cancel-btn {
  background-color: #e63946 !important;
  /* Đỏ */
  color: white !important;
  border: none;
  font-weight: bold;
  transition: background 0.3s ease-in-out;
  padding: 12px;
  border-radius: 6px;
  cursor: pointer;
}

/* Hover hiệu ứng */
.button-group .cancel-btn:hover,
.modal-content form .cancel-btn:hover {
  background-color: #b71c1c !important;
  /* Màu đỏ đậm hơn khi hover */
}
/* Hiệu ứng fadeIn */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }

  to {
    opacity: 1;
    transform: scale(1);
  }
}
</style>
