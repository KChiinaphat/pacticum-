<template>
  <div>
    <h2>ข้อมูลนิสิต</h2>

    <label for="studentId">เลือกนิสิต:</label>
    <select id="studentId" v-model="selectedStudentId" @change="fetchStudent">
      <option v-for="id in studentIds" :key="id" :value="id">นิสิต ID: {{ id }}</option>
    </select>
    <br><br>
    <img :src="`http://localhost:3000/${student.image}`" alt="Profile Image" style="max-width: 200px; max-height: 200px;">
    <p>ชื่อ: {{ student.name }}</p>
    <p>รหัสนิสิต: {{ student.studentId}}</p>
    <p>สาขา: {{ student.major }}</p>
    <p>ปีการศึกษา: {{ student.year }}</p>
    <p>อีเมล: {{ student.email }}</p>  
    <p>โรงเรียนเก่า: {{ student.school }}</p>
    <button @click="deleteStudent">ลบนิสิต</button>
  </div>
</template>

<script>
export default {
  data() {
    return { 
      student: {}, 
      studentIds: [], 
      selectedStudentId: null 
    };
  },
  mounted() {
    fetch('http://localhost:3000/students')
      .then(response => response.json())
      .then(data => {
        this.studentIds = data.map(student => student.id);
        if (this.studentIds.length > 0) {
          this.selectedStudentId = this.studentIds[0];
          this.fetchStudent();
        }
      })
      .catch(error => console.error("เกิดข้อผิดพลาด:", error));
  },
  methods: {
    fetchStudent() {
      fetch(`http://localhost:3000/students/${this.selectedStudentId}`)
        .then(response => response.json())
        .then(data => this.student = data)
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    },
    deleteStudent() {
      fetch(`http://localhost:3000/students/${this.student.id}`, {
        method: 'DELETE'
      })
      .then(() => {
        alert("ลบข้อมูลสำเร็จ!");
        this.student = {};
      })
      .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    }
  }
};
</script>
