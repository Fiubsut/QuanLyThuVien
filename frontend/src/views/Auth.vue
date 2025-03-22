<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="auth-container">
    <div class="left-panel">
      <h1>Welcome to Library Vietnam</h1>
      <p>Hệ thống quản lý thư viện thông minh giúp bạn dễ dàng tra cứu và quản lý sách.</p>
    </div>
    <div class="right-panel">
      <div class="auth-form">
        <h2>{{ isLogin ? "Đăng Nhập" : "Đăng Ký" }}</h2>
        <form @submit.prevent="handleSubmit">
          <div v-if="!isLogin">
            <input v-model="registerForm.HoLot" placeholder="Họ Lót" required />
            <input v-model="registerForm.Ten" placeholder="Tên" required />
            <input v-model="registerForm.NgaySinh" type="date" required />
            <input v-model="registerForm.Phai" placeholder="Phái (Nam/Nữ)" required />
            <input v-model="registerForm.DiaChi" placeholder="Địa chỉ" required />
          </div>
          <input v-model="loginForm.SoDienThoai" placeholder="Số điện thoại" required />
          <input v-model="loginForm.Password" type="password" placeholder="Mật khẩu" required />
          <button type="submit">{{ isLogin ? "Đăng Nhập" : "Đăng Ký" }}</button>
        </form>
        <button @click="toggleAuthMode" class="switch-mode">
          {{ isLogin ? "Chưa có tài khoản? Đăng ký ngay" : "Đã có tài khoản? Đăng nhập" }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { useAuthStore } from "../stores/auth";
import { useRouter } from "vue-router";
import { ref, reactive } from "vue";

export default {
  setup() {
    const authStore = useAuthStore();
    const router = useRouter();
    const isLogin = ref(true);
    const loginForm = reactive({ SoDienThoai: "", Password: "" });
    const registerForm = reactive({
      HoLot: "",
      Ten: "",
      NgaySinh: "",
      Phai: "",
      DiaChi: "",
      SoDienThoai: "",
      Password: "",
    });

    const toggleAuthMode = () => { isLogin.value = !isLogin.value; };
    const handleSubmit = async () => {
      try {
        if (isLogin.value) {
          await authStore.login(loginForm);
        } else {
          await authStore.register(registerForm);
        }
        router.push(authStore.role === "admin" ? { name: "AdminHome" } : { name: "UserHome" });
      } catch (error) {
        console.error("Lỗi xác thực:", error);
      }
    };
    return { isLogin, loginForm, registerForm, toggleAuthMode, handleSubmit };
  },
};
</script>

<style scoped>
.auth-container {
  display: flex;
  height: 100vh;
  background: linear-gradient(135deg, #6e8efb, #a777e3);
}

.left-panel {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: white;
  padding: 40px;
  text-align: center;
}

.right-panel {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  background: white;
  border-radius: 20px 0 0 20px;
  box-shadow: -5px 0 10px rgba(0, 0, 0, 0.1);
}

.auth-form {
  width: 350px;
  padding: 20px;
  text-align: center;
}

input {
  width: 100%;
  padding: 12px;
  margin-bottom: 10px;
  border-radius: 8px;
  border: 1px solid #ccc;
}

button {
  width: 100%;
  padding: 12px;
  border-radius: 8px;
  border: none;
  background: #6e8efb;
  color: white;
  cursor: pointer;
}

.switch-mode {
  background: none;
  border: none;
  color: #6e8efb;
  cursor: pointer;
  margin-top: 10px;
}
</style>

