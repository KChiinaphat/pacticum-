<template>
  <div>
    <select v-model="selectedStudentId" @change="fetchStudent">
      <option disabled value="">เลือกไอดีของนักเรียน</option>
      <option v-for="id in studentIds" :key="id" :value="id">{{ id }}</option>
    </select>
    <form @submit.prevent="handleUpdate" v-if="student">
      <img :src="`http://localhost:3000/${student.image}`" alt="Profile Image" style="max-width: 200px; max-height: 200px;">
      <input type="text" v-model="student.studentId">
      <input type="text" v-model="student.name">
      <input type="text" v-model="student.major">
      <input type="text" v-model="student.year">
      <input type="text" v-model="student.email">
      <button type="submit">บันทึก</button>
    </form>

    <div v-if="courses.length">
      <h3>Courses</h3>
      <ul>
        <li v-for="course in courses" :key="course.CourseId">
          <strong>Course ID:</strong> {{ course.CourseId }}<br>
          <strong>Name:</strong> {{ course.name }}<br>
          <strong>Credit:</strong> {{ course.creadit }}<br>
          <strong>Grade:</strong> {{ course.grade }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedStudentId: "",
      studentIds: [],
      student: null,
      courses: [] // Add a new data property for courses
    };
  },
  created() {
    this.fetchStudentIds();
    this.fetchCourses(); // Fetch courses when the component is created
  },
  methods: {
    fetchStudentIds() {
      fetch("http://localhost:3000/students")
        .then(response => response.json())
        .then(data => {
          this.studentIds = data.map(student => student.id);
        })
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
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
    handleUpdate() {
      fetch(`http://localhost:3000/students/${this.student.id}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(this.student)
      })
      .then(response => response.json())
      .then(() => alert("อัปเดตข้อมูลเรียบร้อย!"))
      .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    },
    fetchCourses() {
      fetch("http://localhost:3000/courses")
        .then(response => response.json())
        .then(data => {
          this.courses = data;
        })
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    }
  }
};
</script>
