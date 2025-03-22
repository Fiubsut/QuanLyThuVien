<template>
  <div class="table-container">
    <div class="header">
      <h2 class="section-title">Quản lý mượn sách</h2>
      <button class="add-request-button" @click="openAddRequestForm">Thêm đơn mượn sách</button>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Tên sách</th>
          <th>Số điện thoại độc giả</th>
          <th>Ngày mượn</th>
          <th>Ngày trả</th>
          <th>Trạng thái</th>
          <th>Quản lý</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="record in records" :key="record._id">
          <td>{{ record.MaSach.TenSach }}</td>
          <td>{{ record.MaDocGia.SoDienThoai }}</td>
          <td>{{ formatDate(record.NgayMuon) }}</td>
          <td>{{ formatDate(record.NgayTra) }}</td>
          <td>
            <span :class="{
                'status-approved': record.TrangThai === 'Đã trả',
                'status-pending': record.TrangThai === 'Đang mượn',
                'status-waiting': record.TrangThai === 'Chờ duyệt'
              }">
              {{ record.TrangThai }}
            </span>
          </td>
          <td>
            <a href="#" @click.prevent="showDetail(record)" class="detail-link">Chi tiết</a>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Modal chi tiết đơn mượn sách (có thể chỉnh sửa) -->
    <div v-if="isDetailModalOpen" class="detail-modal">
      <div class="modal-content">
        <h3>Chi tiết đơn mượn sách</h3>
        <form @submit.prevent="updateRequestData">
          <div class="form-container">
            <!-- Cột bên trái -->
            <div class="form-column">
              <label>
                Mã Sách:
                <input type="text" v-model="detailRecord.MaSach._id" />
              </label>
              <label>
                Tên Sách:
                <input type="text" v-model="detailRecord.MaSach.TenSach" />
              </label>
              <label>
                Mã Độc Giả:
                <input type="text" v-model="detailRecord.MaDocGia._id" />
              </label>
              <label>
                Tên Độc Giả:
                <input type="text" v-model="detailRecord.MaDocGia.Ten" />
              </label>
            </div>

            <!-- Cột bên phải -->
            <div class="form-column">
              <label>
                Số Điện Thoại Độc Giả:
                <input type="text" v-model="detailRecord.MaDocGia.SoDienThoai" />
              </label>
              <label>
                Ngày Mượn:
                <input type="date" :value="formatDate(detailRecord.NgayMuon)" required />
              </label>
              <label>
                Ngày Trả:
                <input type="date" :value="formatDate(detailRecord.NgayTra)" required />
              </label>
              <label>
                Trạng Thái:
                <input type="text" v-model="detailRecord.TrangThai" disabled />
              </label>
            </div>
          </div>

          <!-- Khu vực nút hành động -->
          <div class="modal-actions">
            <button @click="approveRequestdata(detailRecord)">Duyệt</button>
            <button @click="markReturneddata(detailRecord)">Trả</button>
            <button @click="deleteRecorddata(detailRecord._id)">Xóa</button>
            <button @click="closeDetailModal">Đóng</button>
          </div>
        </form>
      </div>
    </div>


    <!-- Form thêm đơn mượn sách mới -->
    <div v-if="isAddRequestModalOpen" class="add-request-modal">
      <div class="modal-content">
        <h3>Thêm đơn mượn sách mới</h3>
        <form @submit.prevent="addRequestData">
          <label>
            Tên Sách:
            <input type="text" v-model="newRequest.TenSach" />
          </label>
          <label>
            Tên Độc Giả:
            <input type="text" v-model="newRequest.TenDocGia" />
          </label>
          <label>
            Ngày Mượn:
            <input v-model="newRequest.NgayMuon" type="date" required />
          </label>
          <label>
            Ngày Trả:
            <input v-model="newRequest.NgayTra" type="date" required />
          </label>
          <button type="submit">Thêm đơn mượn</button>
          <button @click.prevent="closeAddRequestForm" class="cancel-btn">Hủy</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { fetchRecords, approveRequest, markReturned, deleteRecord, updateRequest, addRequest } from "../stores/theodoimuonsach";

const records = ref([]);

// State cho modal chi tiết
const isDetailModalOpen = ref(false);
const detailRecord = ref({});

// Mở modal và hiển thị thông tin chi tiết
const showDetail = (record) => {
  detailRecord.value = JSON.parse(JSON.stringify(record)); // Tạo bản sao
  isDetailModalOpen.value = true;
};

// Đóng modal chi tiết
const closeDetailModal = () => {
  isDetailModalOpen.value = false;
};

// Phê duyệt yêu cầu mượn sách
const approveRequestdata = async (record) => {
  await approveRequest(record._id);
  await loadRecords(); // Làm mới danh sách sau khi duyệt
  closeDetailModal();
};

// Đánh dấu sách đã trả
const markReturneddata = async (record) => {
  await markReturned(record._id);
  await loadRecords(); // Làm mới danh sách sau khi trả sách
  closeDetailModal();
};

// Xóa đơn mượn sách
const deleteRecorddata = async (id) => {
  if (confirm("Bạn có chắc chắn muốn xóa bản ghi này không?")) {
    await deleteRecord(id);
    await loadRecords(); // Làm mới danh sách sau khi xóa
    closeDetailModal();
  }
};

// Modal state for adding new request
const isAddRequestModalOpen = ref(false);
const newRequest = ref({
  TenSach: "",
  TenDocGia: "",
  NgayMuon: "",
  NgayTra: "",
});

// Mở form thêm đơn mượn
const openAddRequestForm = () => {
  isAddRequestModalOpen.value = true;
};

// Đóng form thêm đơn mượn
const closeAddRequestForm = () => {
  isAddRequestModalOpen.value = false;
  newRequest.value = {
    TenSach: "",
    TenDocGia: "",
    NgayMuon: "",
    NgayTra: "",
  };
};

// Thêm đơn mượn sách mới
const addRequestData = async () => {
  await addRequest(newRequest.value);
  closeAddRequestForm();
  loadRecords();
};

// Lấy danh sách đơn mượn, sách, và độc giả
const loadRecords = async () => {
  records.value = await fetchRecords();
};

// Định dạng ngày
const formatDate = (date) => {
  return date ? new Date(date).toISOString().split("T")[0] : "";
};

// Cập nhật thông tin đơn mượn sách
const updateRequestData = async () => {
  const requestData = {
        MaSach: detailRecord.value.MaSach,
        MaDocGia: detailRecord.value.MaDocGia,
        NgayMuon: detailRecord.value.NgayMuon,
        NgayTra: detailRecord.value.NgayTra,
        TrangThai: detailRecord.value.TrangThai,
    };

    try {
        await updateRequest(detailRecord.value._id, requestData); // Giả sử updateRequest chấp nhận ID và dữ liệu mới
        await loadRecords(); // Làm mới danh sách sau khi cập nhật
        closeDetailModal();
    } catch (error) {
        console.error("Error updating request:", error);
    }
};

onMounted(() => {
  loadRecords();
});
</script>

<style scoped>
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
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
  width: 600px;
  /* Tăng chiều rộng */
}

/* Căn chỉnh các cột trong form */
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

/* Căn chỉnh các nút */
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
  background-color: #4caf50;
  color: white;
}

.modal-actions button:nth-child(2) {
  background-color: #2196f3;
  color: white;
}

.modal-actions button:nth-child(3) {
  background-color: #f44336;
  color: white;
}

.modal-actions button:nth-child(4) {
  background-color: #9e9e9e;
  color: white;
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

/* Định dạng trạng thái */
.status-approved,
.status-pending,
.status-waiting {
  display: inline-block;
  padding: 5px 10px;
  border-radius: 20px;
  /* Bo tròn khung */
  font-weight: bold;
  text-align: center;
  min-width: 80px;
}

/* Màu cho từng trạng thái */
.status-approved {
  background-color: #4caf50;
  /* Xanh lá */
  color: white;
}

.status-pending {
  background-color: #ff9800;
  /* Cam */
  color: white;
}

.status-waiting {
  background-color: #f44336;
  /* Đỏ */
  color: white;
}
</style>
