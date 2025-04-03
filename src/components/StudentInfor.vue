<template>
  <div class="student-info-container">
    <h2 class="section-title">ข้อมูลนิสิต</h2>
    
    <div class="student-selector">
      <label for="studentId">เลือกนิสิต:</label>
      <select 
        id="studentId" 
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

    <div v-if="selectedStudentId && student.id" class="student-card">
      <div class="student-profile">
        <div class="student-image-container">
          <img 
            :src="student.image ? `http://localhost:3000/${student.image}` : '/placeholder-profile.png'" 
            alt="รูปโปรไฟล์นิสิต" 
            class="student-image"
          >
        </div>
        
        <div class="student-id-badge">
          {{ student.studentId || 'ไม่ระบุรหัสนิสิต' }}
        </div>
      </div>

      <div class="student-details">
        <div class="detail-row">
          <span class="detail-label">ชื่อ-นามสกุล:</span>
          <span class="detail-value">{{ student.name || 'ไม่ระบุ' }}</span>
        </div>
        
        <div class="detail-row">
          <span class="detail-label">สาขา:</span>
          <span class="detail-value">{{ student.major || 'ไม่ระบุ' }}</span>
        </div>
        
        <div class="detail-row">
          <span class="detail-label">ปีการศึกษา:</span>
          <span class="detail-value">{{ student.year || 'ไม่ระบุ' }}</span>
        </div>
        
        <div class="detail-row">
          <span class="detail-label">อีเมล:</span>
          <span class="detail-value">
            <a v-if="student.email" :href="`mailto:${student.email}`" class="email-link">{{ student.email }}</a>
            <span v-else>ไม่ระบุ</span>
          </span>
        </div>
        
        <div class="detail-row">
          <span class="detail-label">โรงเรียนเก่า:</span>
          <span class="detail-value">{{ student.school || 'ไม่ระบุ' }}</span>
        </div>
      </div>

      <div class="student-actions">
        <button @click="deleteStudentConfirm" class="btn-delete">
          ลบข้อมูลนิสิต
        </button>
      </div>
    </div>

    <!-- Modal Confirmation Dialog -->
    <div v-if="showDeleteConfirm" class="modal-overlay">
      <div class="modal-content">
        <h3 class="modal-title">ยืนยันการลบข้อมูล</h3>
        <p>คุณต้องการลบข้อมูลนิสิต {{ student.name }} ใช่หรือไม่?</p>
        <div class="modal-actions">
          <button @click="deleteStudent" class="btn-confirm">ยืนยัน</button>
          <button @click="showDeleteConfirm = false" class="btn-cancel">ยกเลิก</button>
        </div>
      </div>
    </div>
    <div v-if="courses.length" class="courses-section">
        <h3 class="sub-section-title">รายวิชาที่ลงทะเบียน</h3>
        
        <div class="courses-list">
          <div v-for="course in courses" :key="course.CourseId" class="course-card">
            <div class="course-header">
              <span class="course-id">{{ course.CourseId }}</span>
              <span class="course-grade">เกรด: {{ course.grade || 'ไม่มีเกรด' }}</span>
            </div>
            <div class="course-name">{{ course.name }}</div>
            <div class="course-credit">{{ course.credit }} หน่วยกิต</div>
          </div>
        </div>
      </div>
  </div>
  
</template>

<script>
export default {
  data() {
    return { 
      student: {}, 
      studentIds: [], 
      selectedStudentId: null,
      students: [],
      courses: [],
      showDeleteConfirm: false
    };
  },
  mounted() {
    this.fetchStudents();
    this.fetchCourses();
  },
  methods: {
    fetchCourses() {
      fetch("http://localhost:3000/courses")
        .then(response => response.json())
        .then(data => {
          this.courses = data;
        })
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    },
    fetchStudents() {
      fetch('http://localhost:3000/students')
        .then(response => response.json())
        .then(data => {
          this.students = data;
          this.studentIds = data.map(student => student.id);
          if (this.studentIds.length > 0 && !this.selectedStudentId) {
            this.selectedStudentId = this.studentIds[0];
            this.fetchStudent();
          }
        })
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    },
    fetchStudent() {
      if (!this.selectedStudentId) {
        this.student = {};
        return;
      }
      
      fetch(`http://localhost:3000/students/${this.selectedStudentId}`)
        .then(response => response.json())
        .then(data => this.student = data)
        .catch(error => {
          console.error("เกิดข้อผิดพลาด:", error);
          this.student = {};
        });
    },
    getStudentNameById(id) {
      const student = this.students.find(s => s.id === id);
      return student ? student.name : null;
    },
    deleteStudentConfirm() {
      this.showDeleteConfirm = true;
    },
    deleteStudent() {
  fetch(`http://localhost:3000/students/${this.student.id}`, {
    method: 'DELETE'
  })
  .then(() => {
    this.showDeleteConfirm = false;
    this.student = {};
    this.selectedStudentId = null;
    this.fetchStudents();
    
    alert('ลบข้อมูลนิสิตเรียบร้อยแล้ว');  // ใช้ alert แทนการใช้ this.$notify
  })
  .catch(error => {
    console.error("เกิดข้อผิดพลาด:", error);
    alert('ไม่สามารถลบข้อมูลได้ กรุณาลองใหม่อีกครั้ง');  // ใช้ alert แทนการใช้ this.$notify
  });
}

  }
};
</script>

<style scoped>
.student-info-container {
  max-width: 800px;
  margin: 0 auto;
  position: relative;
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

.student-card {
  background-color: white;
  border-radius: var(--border-radius);
  box-shadow: var(--shadow);
  overflow: hidden;
  margin-bottom: 20px;
  position: relative;
}

.student-profile {
  background: linear-gradient(135deg, var(--primary-light), var(--primary-dark));
  padding: 30px;
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}

.student-image-container {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background-color: white;
  padding: 3px;
  overflow: hidden;
  margin-bottom: 10px;
}

.student-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 50%;
}

.student-id-badge {
  background-color: rgba(255, 255, 255, 0.9);
  color: var(--primary-dark);
  padding: 5px 15px;
  border-radius: 20px;
  font-weight: 600;
  font-size: 0.9rem;
}

.student-details {
  padding: 25px;
}

.detail-row {
  display: flex;
  margin-bottom: 15px;
  border-bottom: 1px solid #f0f0f0;
  padding-bottom: 10px;
}

.detail-row:last-child {
  margin-bottom: 0;
  border-bottom: none;
}

.detail-label {
  flex: 0 0 120px;
  font-weight: 500;
  color: var(--text-light);
}

.detail-value {
  flex: 1;
  color: var(--text-color);
}

.email-link {
  color: var(--primary-color);
  text-decoration: none;
}

.email-link:hover {
  text-decoration: underline;
}

.student-actions {
  padding: 0 25px 25px;
  display: flex;
  justify-content: flex-end;
}

.btn-delete {
  background-color: var(--accent-color);
}

.btn-delete:hover {
  background-color: #e45a5a;
}

/* Modal styling */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background-color: white;
  border-radius: var(--border-radius);
  padding: 25px;
  max-width: 400px;
  width: 100%;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.modal-title {
  color: var(--text-color);
  margin-bottom: 15px;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  margin-top: 25px;
  gap: 10px;
}

.btn-confirm {
  background-color: var(--accent-color);
}

.btn-cancel {
  background-color: #6c757d;
}

@media (max-width: 768px) {
  .student-selector {
    flex-direction: column;
    align-items: flex-start;
    gap: 5px;
  }
  
  .detail-row {
    flex-direction: column;
  }
  
  .detail-label {
    margin-bottom: 5px;
  }
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
</style>