<template>
  <div class="edit-student-container">
    <h2 class="section-title">แก้ไขข้อมูลนิสิต</h2>
    
    <div class="student-selector">
      <label for="student-select">เลือกนิสิต:</label>
      <select 
        id="student-select" 
        v-model="selectedStudentId" 
        @change="fetchStudent"
        :disabled="studentIds.length === 0"
      >
        <option disabled value="">-- เลือกนิสิต --</option>
        <option v-for="id in studentIds" :key="id" :value="id">
          {{ getStudentNameById(id) || `นิสิต ID: ${id}` }}
        </option>
      </select>
    </div>

    <div v-if="!selectedStudentId && studentIds.length > 0" class="select-prompt">
      กรุณาเลือกนิสิตจากรายการด้านบน
    </div>

    <div v-if="studentIds.length === 0" class="no-data-message">
      ไม่พบข้อมูลนิสิต กรุณาเพิ่มนิสิตใหม่
    </div>

    <div v-if="selectedStudentId && student" class="edit-form-container">
      <form @submit.prevent="handleUpdate" class="student-form">
        <div class="form-header">
          <div class="student-image-container">
            <img 
              :src="student.image ? `http://localhost:3000/${student.image}` : '/placeholder-profile.png'" 
              alt="รูปโปรไฟล์นิสิต" 
              class="student-image"
            >
            <label for="image-upload" class="image-upload-label">
              <span class="upload-icon">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16">
                  <path d="M12.854.146a.5.5 0 0 0-.707 0L10.5 1.793 14.207 5.5l1.647-1.646a.5.5 0 0 0 0-.708l-3-3zm.646 6.061L9.793 2.5 3.293 9H3.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.207l6.5-6.5zm-7.468 7.468A.5.5 0 0 1 6 13.5V13h-.5a.5.5 0 0 1-.5-.5V12h-.5a.5.5 0 0 1-.5-.5V11h-.5a.5.5 0 0 1-.5-.5V10h-.5a.499.499 0 0 1-.175-.032l-.179.178a.5.5 0 0 0-.11.168l-2 5a.5.5 0 0 0 .65.65l5-2a.5.5 0 0 0 .168-.11l.178-.178z"/>
                </svg>
              </span>
            </label>
            <input type="file" id="image-upload" class="file-input" @change="onFileChange">
          </div>
        </div>

        <div class="form-grid">
          <div class="form-group">
            <label for="studentId">รหัสนิสิต</label>
            <input 
              type="text" 
              id="studentId" 
              v-model="student.studentId" 
              required
            >
          </div>

          <div class="form-group">
            <label for="name">ชื่อ-นามสกุล</label>
            <input 
              type="text" 
              id="name" 
              v-model="student.name" 
              required
            >
          </div>

          <div class="form-group">
            <label for="major">สาขา</label>
            <input 
              type="text" 
              id="major" 
              v-model="student.major" 
              required
            >
          </div>

          <div class="form-group">
            <label for="year">ปีการศึกษา</label>
            <input 
              type="text" 
              id="year" 
              v-model="student.year" 
              required
            >
          </div>

          <div class="form-group">
            <label for="email">อีเมล</label>
            <input 
              type="email" 
              id="email" 
              v-model="student.email" 
              required
            >
          </div>

          <div class="form-group">
            <label for="school">โรงเรียนเก่า</label>
            <input 
              type="text" 
              id="school" 
              v-model="student.school"
            >
          </div>
        </div>

        <div class="form-actions">
          <button type="submit" class="btn-save">บันทึกข้อมูล</button>
          <button type="button" class="btn-cancel" @click="resetForm">ยกเลิก</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedStudentId: "",
      studentIds: [],
      students: [],
      student: null,
      imageFile: null,
    };
  },
  created() {
    this.fetchStudents();

  },
  methods: {
    fetchStudents() {
      fetch("http://localhost:3000/students")
        .then(response => response.json())
        .then(data => {
          this.students = data;
          this.studentIds = data.map(student => student.id);
        })
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    },
    getStudentNameById(id) {
      const student = this.students.find(s => s.id === id);
      return student ? student.name : null;
    },
    fetchStudent() {
      if (this.selectedStudentId) {
        fetch(`http://localhost:3000/students/${this.selectedStudentId}`)
          .then(response => response.json())
          .then(data => {
            this.student = data;
          })
          .catch(error => console.error("เกิดข้อผิดพลาด:", error));
      }
    },
    onFileChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.imageFile = file;
        // In a real application, you would upload the file to a server
        // For now, we'll just update the local state for preview
        this.student.newImage = URL.createObjectURL(file);
      }
    },
    handleUpdate() {
  fetch(`http://localhost:3000/students/${this.student.id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(this.student)
  })
  .then(response => response.json())
  .then(() => {
    // แสดงการแจ้งเตือนสำเร็จ
    alert('อัปเดตข้อมูลเรียบร้อยแล้ว');
  })
  .catch(error => {
    console.error("เกิดข้อผิดพลาด:", error);
    // แสดงการแจ้งเตือนข้อผิดพลาด
    alert('ไม่สามารถอัปเดตข้อมูลได้ กรุณาลองใหม่อีกครั้ง');
  });
},

    resetForm() {
      this.fetchStudent(); // Reload the original data
      if (this.student && this.student.newImage) {
        URL.revokeObjectURL(this.student.newImage);
        delete this.student.newImage;
      }
      this.imageFile = null;
    },
  }
};
</script>

<style scoped>
.edit-student-container {
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

.student-selector {
  margin-bottom: 25px;
  display: flex;
  align-items: center;
  gap: 15px;
}

.student-selector label {
  font-weight: 500;
  min-width: 100px;
}

.student-selector select {
  flex: 1;
}

.select-prompt, .no-data-message {
  background-color: #f8f9fa;
  border: 1px dashed #dee2e6;
  border-radius: var(--border-radius);
  padding: 30px;
  text-align: center;
  color: #6c757d;
  margin: 20px 0;
}

.edit-form-container {
  background-color: white;
  border-radius: var(--border-radius);
  box-shadow: var(--shadow);
  padding: 25px;
  margin-bottom: 30px;
}

.form-header {
  display: flex;
  justify-content: center;
  margin-bottom: 25px;
}

.student-image-container {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background-color: #f8f9fa;
  overflow: hidden;
  position: relative;
}

.student-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.image-upload-label {
  position: absolute;
  bottom: 0;
  right: 0;
  background-color: var(--primary-color);
  color: white;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.upload-icon {
  display: flex;
  align-items: center;
  justify-content: center;
}

.file-input {
  display: none;
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
  margin-bottom: 25px;
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
  justify-content: flex-end;
}

.btn-save {
  background-color: var(--primary-color);
}

.btn-cancel {
  background-color: #6c757d;
}

.courses-section {
  margin-top: 30px;
  border-top: 1px solid var(--border-color);
  padding-top: 20px;
}

.sub-section-title {
  color: var(--primary-color);
  margin-bottom: 15px;
  font-size: 1.2rem;
}

.courses-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 15px;
}

.course-card {
  background-color: #f8f9fa;
  border-radius: var(--border-radius);
  padding: 15px;
  border-left: 4px solid var(--primary-color);
}

.course-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.course-id {
  font-weight: 600;
  color: var(--primary-dark);
  font-size: 0.9rem;
}

.course-grade {
  background-color: var(--primary-light);
  color: white;
  font-size: 0.8rem;
  padding: 2px 8px;
  border-radius: 10px;
}

.course-name {
  font-weight: 500;
  margin-bottom: 5px;
}

.course-credit {
  font-size: 0.9rem;
  color: var(--text-light);
}

@media (max-width: 768px) {
  .student-selector {
    flex-direction: column;
    align-items: flex-start;
    gap: 5px;
  }
  
  .form-grid {
    grid-template-columns: 1fr;
  }
  
  .courses-list {
    grid-template-columns: 1fr;
  }
}
</style>