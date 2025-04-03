<template>
  <div>
    
    <form @submit.prevent="handleUpdate" v-if="course">
      <input type="text" v-model="course.CourseId" placeholder="รหัสวิชา">
      <input type="text" v-model="course.name" placeholder="ชื่อวิชา">
      <input type="number" v-model="course.credit" placeholder="หน่วยกิต">
      <input type="text" v-model="course.grade" placeholder="เกรด">
      <button type="submit">บันทึก</button>
    </form>
    <div>
      <select v-model="selectedCourseId" @change="fetchCourse">
        <option disabled value="">เลือกรหัสวิชาและชื่อวิชา</option>
        <option v-for="course in courses" :key="course.CourseId" :value="course.CourseId">
          {{ course.CourseId }} - {{ course.name }}
        </option>
      </select>
      <button @click="handleDelete" :disabled="!selectedCourseName">ลบวิชา</button>
      <button @click="handleEdit" :disabled="!selectedCourseName">แก้ไข</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedCourseId: "",
      selectedCourseName: "",
      courseIds: [],
      courses: [],
      course: null
    };
  },
  created() {
    this.fetchCourseIds();
    this.fetchCourses();
  },
  methods: {
    fetchCourseIds() {
      fetch("http://localhost:3000/courses")
        .then(response => response.json())
        .then(data => {
          this.courseIds = data.map(course => course.CourseId);
        })
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    },
    fetchCourses() {
      fetch("http://localhost:3000/courses")
        .then(response => response.json())
        .then(data => {
          this.courses = data;
        })
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    },
    fetchCourse() {
      if (this.selectedCourseId) {
        fetch(`http://localhost:3000/courses/${this.selectedCourseId}`)
          .then(response => response.json())
          .then(data => {
            this.course = data;
            const selectedCourse = this.courses.find(course => course.CourseId === this.selectedCourseId);
            this.selectedCourseName = selectedCourse ? selectedCourse.name : ""; // Ensure selectedCourseName is updated
          })
          .catch(error => console.error("เกิดข้อผิดพลาด:", error));
      } else {
        this.selectedCourseName = ""; // Reset selectedCourseName if no course is selected
      }
    },
    handleUpdate() {
      fetch(`http://localhost:3000/courses/${this.course.CourseId}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(this.course)
      })
      .then(response => response.json())
      .then(() => alert("อัปเดตข้อมูลวิชาเรียบร้อย!"))
      .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    },
    handleDelete() {
      const courseToDelete = this.courses.find(course => course.CourseId === this.selectedCourseId);
      if (courseToDelete) {
        fetch(`http://localhost:3000/courses/${courseToDelete.CourseId}`, {
          method: 'DELETE'
        })
        .then(() => {
          alert("ลบวิชาเรียบร้อย!");
          this.fetchCourseIds();
          this.fetchCourses();
          this.selectedCourseId = "";
          this.selectedCourseName = "";
        })
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
      }
    },
    handleEdit() {
      const courseToEdit = this.courses.find(course => course.CourseId === this.selectedCourseId);
      if (courseToEdit) {
        this.fetchCourse();
      }
    }
  }
};
</script>
