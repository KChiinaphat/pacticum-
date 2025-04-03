<template>
  <div class="form-container">
    <h2 class="section-title">เพิ่มข้อมูลนิสิต</h2>
    
    <form @submit.prevent="handleSubmit" class="student-form">
      <div class="image-upload-container">
        <div class="preview-container" @click="triggerFileInput">
          <img v-if="newStudent.image" :src="newStudent.image" alt="Profile Image" class="profile-preview">
          <div v-else class="upload-placeholder">
            <span class="upload-icon">+</span>
            <span>อัพโหลดรูป</span>
          </div>
        </div>
        <input 
          type="file" 
          ref="fileInput"
          @change="onFileChange" 
          accept="image/*" 
          class="file-input"
        >
      </div>

      <div class="form-grid">
        <div class="form-group">
          <label for="studentId">รหัสนิสิต</label>
          <input 
            type="text" 
            id="studentId" 
            v-model="newStudent.studentId" 
            placeholder="กรอกรหัสนิสิต"
            required
          >
        </div>

        <div class="form-group">
          <label for="name">ชื่อ-นามสกุล</label>
          <input 
            type="text" 
            id="name" 
            v-model="newStudent.name" 
            placeholder="กรอกชื่อ-นามสกุล"
            required
          >
        </div>

        <div class="form-group">
          <label for="major">สาขา</label>
          <input 
            type="text" 
            id="major" 
            v-model="newStudent.major" 
            placeholder="กรอกสาขา"
            required
          >
        </div>

        <div class="form-group">
          <label for="year">ปีการศึกษา</label>
          <input 
            type="text" 
            id="year" 
            v-model="newStudent.year" 
            placeholder="กรอกปีการศึกษา"
            required
          >
        </div>

        <div class="form-group">
          <label for="email">อีเมล</label>
          <input 
            type="email" 
            id="email" 
            v-model="newStudent.email" 
            placeholder="example@email.com"
            required
          >
        </div>

        <div class="form-group">
          <label for="school">โรงเรียนเก่า</label>
          <input 
            type="text" 
            id="school" 
            v-model="newStudent.school" 
            placeholder="กรอกชื่อโรงเรียนเก่า"
          >
        </div>
      </div>

      <div class="form-actions">
        <button type="submit" class="btn-submit">เพิ่มนิสิต</button>
        <button type="button" class="btn-reset" @click="resetForm">ล้างข้อมูล</button>
      </div>
    </form>
  </div>
</template>
  
<script>
export default {
  data() {
    return {
      newStudent: { 
        name: '', 
        studentId: '', 
        major: '', 
        year: '', 
        email: '', 
        school: '', 
        image: '' 
      },
      imageFile: null
    };
  },
  methods: {
    onFileChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.imageFile = file;
        const reader = new FileReader();
        reader.onload = e => {
          this.newStudent.image = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    },
    handleSubmit() {
  fetch('http://localhost:3000/students', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(this.newStudent)
  })
  .then(response => response.json())
  .then(() => {
    this.resetForm();
    // แสดงการแจ้งเตือนสำเร็จด้วย alert
    alert('เพิ่มนิสิตสำเร็จ!');
  })
  .catch(error => {
    console.error("เกิดข้อผิดพลาด:", error);
    // แสดงการแจ้งเตือนข้อผิดพลาดด้วย alert
    alert('ไม่สามารถเพิ่มนิสิตได้ กรุณาลองใหม่อีกครั้ง');
  });
},

    resetForm() {
      this.newStudent = { 
        name: '', 
        studentId: '', 
        major: '', 
        year: '',
        email: '', 
        school: '', 
        image: '' 
      };
      this.imageFile = null;
      if (this.$refs.fileInput) {
        this.$refs.fileInput.value = '';
      }
    },
    triggerFileInput() {
      this.$refs.fileInput.click();
    }
  }
};
</script>

<style scoped>
.form-container {
  max-width: 800px;
  margin: 0 auto;
}

.section-title {
  color: var(--primary-color);
  margin-bottom: 20px;
  font-size: 1.5rem;
  border-bottom: 2px solid var(--primary-light);
  padding-bottom: 10px;
}

.student-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.image-upload-container {
  margin-bottom: 20px;
  display: flex;
  justify-content: center;
}

.preview-container {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  overflow: hidden;
  border: 2px dashed var(--border-color);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s;
}

.preview-container:hover {
  border-color: var(--primary-color);
}

.profile-preview {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.upload-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: var(--text-light);
}

.upload-icon {
  font-size: 2rem;
  margin-bottom: 5px;
}

.file-input {
  display: none;
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

label {
  margin-bottom: 5px;
  font-weight: 500;
  color: var(--text-color);
}

.form-actions {
  display: flex;
  gap: 10px;
  margin-top: 20px;
}

.btn-submit {
  flex: 1;
  background-color: var(--primary-color);
}

.btn-reset {
  background-color: #6c757d;
}

@media (max-width: 768px) {
  .form-grid {
    grid-template-columns: 1fr;
  }
}
</style>