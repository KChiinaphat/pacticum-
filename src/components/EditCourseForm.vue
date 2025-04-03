<template>
    <div class="container">
      <div class="course-selector">
        <select v-model="selectedCourseId" @change="fetchCourse">
          <option disabled value="">เลือกรหัสวิชาและชื่อวิชา</option>
          <option v-for="course in courses" :key="course.CourseId" :value="course.CourseId">
            {{ course.CourseId }} - {{ course.name }}
          </option>
        </select>
        <div class="button-group">
          <button @click="handleEdit" :disabled="!selectedCourseId" class="edit-btn">แก้ไข</button>
          <button @click="handleDelete" :disabled="!selectedCourseId" class="delete-btn">ลบวิชา</button>
        </div>
      </div>
  
      <form @submit.prevent="handleUpdate" v-if="course" class="edit-form">
        <div class="form-group">
          <label>รหัสวิชา</label>
          <input type="text" v-model="course.CourseId" placeholder="รหัสวิชา">
        </div>
        <div class="form-group">
          <label>ชื่อวิชา</label>
          <input type="text" v-model="course.name" placeholder="ชื่อวิชา">
        </div>
        <div class="form-group">
          <label>หน่วยกิต</label>
          <input type="number" v-model="course.credit" placeholder="หน่วยกิต">
        </div>
        <div class="form-group">
          <label>เกรด</label>
          <input type="text" v-model="course.grade" placeholder="เกรด">
        </div>
        <button type="submit" class="save-btn">บันทึก</button>
      </form>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        selectedCourseId: "",
        courses: [],
        course: null
      };
    },
    created() {
      this.fetchCourses();
    },
    methods: {

      fetchCourses() {
        fetch("http://localhost:3000/courses")
          .then(response => response.json())
          .then(data => {
            console.log("Fetched courses:", data);
            this.courses = data;
          })
          .catch(error => console.error("เกิดข้อผิดพลาด:", error));
      },
  
      
      fetchCourse() {
  if (this.selectedCourseId) {
    // ใช้ `id` ในการค้นหา
    fetch(`http://localhost:3000/courses?id=${this.selectedCourseId}`)
      .then(response => response.json())
      .then(data => {
        if (data.length === 0) {
          throw new Error(`ไม่พบวิชา รหัส: ${this.selectedCourseId}`);
        }
        console.log("Fetched course:", data[0]);
        this.course = { ...data[0] }; // กำหนดข้อมูลวิชาที่จะทำการแก้ไข
      })
      .catch(error => {
        console.error("เกิดข้อผิดพลาด:", error);
        alert(error.message); // แจ้งเตือนข้อผิดพลาด
        this.course = null; // ล้างข้อมูลวิชาเมื่อเกิดข้อผิดพลาด
      });
  } else {
    this.course = null; // ล้างข้อมูลเมื่อไม่มีการเลือกวิชา
  }
},



      handleUpdate() {
  if (this.course) {
    fetch(`http://localhost:3000/courses/${this.course.id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(this.course)
    })
    .then(response => {
      if (!response.ok) {
        throw new Error("ไม่สามารถอัปเดตข้อมูลได้");
      }
      return response.json();
    })
    .then(() => {
      alert("อัปเดตข้อมูลวิชาเรียบร้อย!");
      this.fetchCourses(); // รีเฟรชรายการวิชา
      this.selectedCourseId = ""; // รีเซ็ตตัวเลือก
      this.course = null; // เคลียร์ข้อมูลวิชาที่เลือก
    })
    .catch(error => console.error("เกิดข้อผิดพลาด:", error));
  }
},
handleDelete() {
  if (confirm(`ต้องการลบวิชา ${this.selectedCourseId} ใช่หรือไม่?`)) {
    fetch(`http://localhost:3000/courses/${this.selectedCourseId}`, {
      method: 'DELETE'
    })
    .then(response => {
      if (!response.ok) {
        throw new Error("ไม่สามารถลบข้อมูลได้");
      }
      return response.json();
    })
    .then(() => {
      alert("ลบวิชาเรียบร้อย!");
      this.fetchCourses(); // รีเฟรชรายการวิชา
      this.selectedCourseId = ""; // รีเซ็ตตัวเลือก
      this.course = null; // เคลียร์ข้อมูลวิชาที่เลือก
    })
    .catch(error => console.error("เกิดข้อผิดพลาด:", error));
  }
},

  
      handleEdit() {
  if (this.selectedCourseId) {
    this.fetchCourse();
  } else {
    alert("กรุณาเลือกวิชาก่อนทำการแก้ไข");
  }
}
    }
  };
  </script>
  

  
  <style scoped>
  .container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    font-family: 'Prompt', sans-serif;
  }
  
  .course-selector {
    margin-bottom: 20px;
    display: flex;
    flex-direction: column;
    gap: 10px;
  }
  
  select {
    padding: 10px;
    border-radius: 4px;
    border: 1px solid #ddd;
    font-size: 16px;
    width: 100%;
  }
  
  .button-group {
    display: flex;
    gap: 10px;
  }
  
  button {
    padding: 10px 15px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 16px;
    transition: background-color 0.3s;
  }
  
  button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
  
  .edit-btn {
    background-color: #4CAF50;
    color: white;
  }
  
  .delete-btn {
    background-color: #f44336;
    color: white;
  }
  
  .save-btn {
    background-color: #2196F3;
    color: white;
    width: 100%;
    margin-top: 10px;
  }
  
  .edit-form {
    background-color: #f9f9f9;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  }
  
  .form-group {
    margin-bottom: 15px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  
  input {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
    box-sizing: border-box;
  }
  
  @media (min-width: 768px) {
    .course-selector {
      flex-direction: row;
      align-items: center;
    }
    
    select {
      flex: 1;
    }
  }
  </style>