<template>
  <div>
    <h1>ระบบจัดการนิสิต</h1>
    <AddStudentForm />
    <StudentInfo />
    <EditStudentForm/>
    <AddCourse/>
    <EditCourse/>
    
  </div>
</template>

<script>
import StudentInfo from "./components/StudentInfor.vue";
import AddStudentForm from "./components/AddStudentForm.vue";
import EditStudentForm from "./components/EditStudentForm.vue";
import AddCourse from "./components/AddCourseForm.vue";
import EditCourse from "./components/EditCourseForm.vue";

export default {
  components: {
    StudentInfo,
    AddStudentForm,
    EditStudentForm,
    AddCourse,
    EditCourse
  }
};
</script>
