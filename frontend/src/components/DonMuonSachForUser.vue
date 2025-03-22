<template>
  <div class="table-container">
    <div class="header">
      <h2 class="section-title">Đơn mượn sách</h2>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Tên sách</th>
          <th>Số điện thoại độc giả</th>
          <th>Ngày mượn</th>
          <th>Ngày trả</th>
          <th>Trạng thái</th>
          <th>Thông tin</th>
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
                'status-waiting': record.TrangThai === 'Chờ duyệt',
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
    <div v-if="isDetailModalOpen" class="detail-modal">
      <div class="modal-content">
        <h3 class="modal-title">Chi tiết đơn mượn sách</h3>
        <div class="modal-body">
          <div class="info-grid">
            <div class="info-item">
              <label>Mã Sách:</label>
              <span>{{ detailRecord.MaSach._id }}</span>
            </div>
            <div class="info-item">
              <label>Tên Sách:</label>
              <span>{{ detailRecord.MaSach.TenSach }}</span>
            </div>
            <div class="info-item">
              <label>Mã Độc Giả:</label>
              <span>{{ detailRecord.MaDocGia._id }}</span>
            </div>
            <div class="info-item">
              <label>Tên Độc Giả:</label>
              <span>{{ detailRecord.MaDocGia.Ten }}</span>
            </div>
            <div class="info-item">
              <label>Số Điện Thoại:</label>
              <span>{{ detailRecord.MaDocGia.SoDienThoai }}</span>
            </div>
            <div class="info-item">
              <label>Ngày Mượn:</label>
              <span>{{ formatDate(detailRecord.NgayMuon) }}</span>
            </div>
            <div class="info-item">
              <label>Ngày Trả:</label>
              <span>{{ formatDate(detailRecord.NgayTra) }}</span>
            </div>
            <div class="info-item">
              <label>Trạng Thái:</label>
              <span class="status-badge" :class="{
                'status-approved': detailRecord.TrangThai === 'Đã trả',
                'status-pending': detailRecord.TrangThai === 'Đang mượn',
                'status-waiting': detailRecord.TrangThai === 'Chờ duyệt',
              }">{{ detailRecord.TrangThai }}</span>
            </div>
          </div>
        </div>
        <div class="modal-actions">
          <button class="close-btn" @click="closeDetailModal">Đóng</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { records,
  fetchUserRecords,
} from "../stores/donmuonsach";

// State cho modal chi tiết
const isDetailModalOpen = ref(false);
const detailRecord = ref({});

// Mở modal và hiển thị thông tin chi tiết
const showDetail = (record) => {
  detailRecord.value = record;
  isDetailModalOpen.value = true;
};

// Đóng modal chi tiết
const closeDetailModal = () => {
  isDetailModalOpen.value = false;
};

const loadUserRecords = async () => {
  records.value = await fetchUserRecords();
};


// Định dạng ngày
const formatDate = (date) => {
  return date ? new Date(date).toISOString().split("T")[0] : "";
};

onMounted(() => {
  loadUserRecords();
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
  padding: 1.5rem;
  border-radius: 12px;
  width: 450px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  text-align: center;
}

.modal-title {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 1rem;
}

.modal-body {
  padding: 10px;
}

.info-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  text-align: left;
}

.info-item {
  background: #f8f9fa;
  padding: 10px;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
}

.info-item label {
  font-weight: bold;
  font-size: 14px;
  color: #555;
}

.info-item span {
  font-size: 15px;
  color: #333;
}

.status-badge {
  padding: 5px 10px;
  border-radius: 5px;
  font-weight: bold;
}

.status-approved {
  background-color: #e3342f;
  color: black;
}

.status-pending {
  background-color: #4caf50 !important;
  color: white !important;
}

.status-waiting {
  background-color: #fef3c7;
  color: #060200;
}

.modal-actions {
  margin-top: 1rem;
  display: flex;
  justify-content: center;
}

.close-btn {
  background: #e3342f;
  color: white;
  padding: 8px 16px;
  border-radius: 6px;
  border: none;
  cursor: pointer;
  transition: background 0.3s;
}

.close-btn:hover {
  background: #cc1f1a;
}

.table-container {
  margin-top: 40px;
  /* Đẩy xuống dưới */
  padding-bottom: 20px;
  /* Tạo khoảng trống phía dưới */
}
</style>
