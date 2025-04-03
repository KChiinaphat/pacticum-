<template>
  <form @submit.prevent="handleSubmit">
    <input type="text" v-model="newCourse.CourseId" placeholder="รหัสวิชา">
    <input type="text" v-model="newCourse.name" placeholder="ชื่อวิชา">
    <input type="number" v-model="newCourse.credit" placeholder="หน่วยกิต">
    <input type="text" v-model="newCourse.grade" placeholder="เกรด">
    <button type="submit">เพิ่มวิชา</button>
  </form>
</template>

<script>
export default {
  data() {
    return {
      newCourse: { CourseId: '', name: '', credit: '', grade: '' }
    };
  },
  methods: {
    handleSubmit() {
      fetch('http://localhost:3000/courses', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(this.newCourse)
      })
      .then(response => response.json())
      .then(() => alert("เพิ่มวิชาสำเร็จ!"))
      .catch(error => console.error("เกิดข้อผิดพลาด:", error));
    }
  }
};
</script>
