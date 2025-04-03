<template>
  <div class="course-form-container">
    <h2 class="form-title">เพิ่มรายวิชาใหม่</h2>
    <form @submit.prevent="handleSubmit" class="course-form">
      <div class="form-group">
        <label for="Id">รหัสวิชา</label>
        <input 
          type="text" 
          id="courseId" 
          v-model="newCourse.id" 
          required
          class="form-input"
        >
      </div>
      
      <div class="form-group">
        <label for="courseName">ชื่อวิชา</label>
        <input 
          type="text" 
          id="courseName" 
          v-model="newCourse.name" 
          required
          class="form-input"
        >
      </div>
      
      <div class="form-group">
        <label for="courseCredit">หน่วยกิต</label>
        <input 
          type="number" 
          id="courseCredit" 
          v-model="newCourse.credit" 
          required
          min="1"
          max="12"
          class="form-input"
        >
      </div>
      
      <div class="form-group">
        <label for="courseGrade">เกรด</label>
        <select 
          id="courseGrade" 
          v-model="newCourse.grade" 
          required
          class="form-input"
        >
          <option value="" disabled selected>เลือกเกรด</option>
          <option value="A">A</option>
          <option value="B+">B+</option>
          <option value="B">B</option>
          <option value="C+">C+</option>
          <option value="C">C</option>
          <option value="D+">D+</option>
          <option value="D">D</option>
          <option value="F">F</option>
          <option value="W">W</option>
        </select>
      </div>
      
      <button type="submit" class="submit-button">
        <i class="fas fa-plus-circle"></i> เพิ่มวิชา
      </button>
    </form>
    
    <div v-if="showSuccessMessage" class="success-message">
      <i class="fas fa-check-circle"></i> เพิ่มวิชาสำเร็จ!
    </div>
    
    <div v-if="showErrorMessage" class="error-message">
      <i class="fas fa-exclamation-circle"></i> {{ errorMessage }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newCourse: { 
        id: '',  // เปลี่ยนจาก 'Id' เป็น 'id'
        name: '', 
        credit: '', 
        grade: '' 
      },
      showSuccessMessage: false,
      showErrorMessage: false,
      errorMessage: ''
    };
  },
  methods: {
    handleSubmit() {
      this.showSuccessMessage = false;
      this.showErrorMessage = false;
      
      // Validate form
      if (!this.newCourse.id || !this.newCourse.name || !this.newCourse.credit || !this.newCourse.grade) {
        this.showErrorMessage = true;
        this.errorMessage = 'กรุณากรอกข้อมูลให้ครบทุกช่อง';
        return;
      }
      
      fetch('http://localhost:3000/courses', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(this.newCourse)
      })
      .then(response => {
        if (!response.ok) {
          throw new Error('เกิดข้อผิดพลาดในการเพิ่มวิชา');
        }
        return response.json();
      })
      .then(() => {
        this.showSuccessMessage = true;
        // Reset form
        this.newCourse = { id: '', name: '', credit: '', grade: '' };
        
        // Auto-hide success message after 3 seconds
        setTimeout(() => {
          this.showSuccessMessage = false;
        }, 3000);
      })
      .catch(error => {
        this.showErrorMessage = true;
        this.errorMessage = error.message || 'เกิดข้อผิดพลาดในการเพิ่มวิชา';
        console.error("เกิดข้อผิดพลาด:", error);
      });
    }
  }
};
</script>


<style scoped>
.course-form-container {
  max-width: 500px;
  margin: 0 auto; 
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.form-title {
  text-align: center;
  color: #3f51b5;
  margin-bottom: 20px;
  font-size: 1.5rem;
}

.course-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

label {
  margin-bottom: 5px;
  font-weight: 500;
  color: #555;
}

.form-input {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
  transition: border-color 0.3s;
}

.form-input:focus {
  border-color: #3f51b5;
  outline: none;
  box-shadow: 0 0 0 2px rgba(63, 81, 181, 0.2);
}

.submit-button {
  background-color: #3f51b5;
  color: white;
  border: none;
  padding: 12px;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s;
  margin-top: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 8px;
}

.submit-button:hover {
  background-color: #303f9f;
}

.success-message {
  margin-top: 15px;
  padding: 10px;
  background-color: #e8f5e9;
  color: #2e7d32;
  border-radius: 4px;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.error-message {
  margin-top: 15px;
  padding: 10px;
  background-color: #ffebee;
  color: #c62828;
  border-radius: 4px;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}
</style>