<template>
    <form @submit.prevent="handleSubmit">
      <input type="file" @change="onFileChange" accept="images/*">
      <img v-if="newStudent.image" :src="newStudent.image" alt="Profile Image" style="max-width: 200px; max-height: 200px;">
      <input type="text" v-model="newStudent.name" placeholder="ชื่อ">
      <input type="text" v-model="newStudent.studenId" placeholder="รหัสนิสิต">
      <input type="text" v-model="newStudent.major" placeholder="สาขา">
      <input type="text" v-model="newStudent.year" placeholder="ปีการศึกษา">
      <input type="text" v-model="newStudent.email" placeholder="อีเมล">
      <input type="text" v-model="newStudent.school" placeholder="โรงเรียนเก่า">
      
      <button type="submit">เพิ่มนิสิต</button>
    </form>
  </template>
  
  <script>
  export default {
    data() {
      return {
        newStudent: { name: '', studentId: '', major: '', year: '', email: '', school : '', image: '' },
        imageFile: null
      };
    },
    methods: {
      handleSubmit() {
        fetch('http://localhost:3000/students', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(this.newStudent)
        })
        .then(response => response.json())
        .then(() => alert("เพิ่มนิสิตสำเร็จ!"))
        .catch(error => console.error("เกิดข้อผิดพลาด:", error));
      }
    }
  };
  </script>
  