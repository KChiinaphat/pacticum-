<template>
  <div class="app-container">
    <header class="app-header">
      <h1 class="app-title">ระบบจัดการนิสิต</h1>
    </header>

    <div class="tabs-container">
      <div class="tabs">
        <button 
          v-for="(tab, index) in tabs" 
          :key="index" 
          :class="['tab-button', { active: activeTab === tab.id }]"
          @click="activeTab = tab.id"
        >
          {{ tab.label }}
        </button>
      </div>

      <div class="tab-content">
        <div v-if="activeTab === 'studentInfo'" class="content-section">
          <StudentInfo />
        </div>
        <div v-if="activeTab === 'addStudent'" class="content-section">
          <AddStudentForm />
        </div>
        <div v-if="activeTab === 'editStudent'" class="content-section">
          <EditStudentForm />
        </div>
        <div v-if="activeTab === 'addCourse'" class="content-section">
          <AddCourse />
        </div>
        <div v-if="activeTab === 'editCourse'" class="content-section">
          <EditCourse />
        </div>
      </div>
    </div>
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
  },
  data() {
    return {
      activeTab: 'studentInfo',
      tabs: [
        { id: 'studentInfo', label: 'ข้อมูลนิสิต' },
        { id: 'addStudent', label: 'เพิ่มนิสิต' },
        { id: 'editStudent', label: 'แก้ไขนิสิต' },
        { id: 'addCourse', label: 'เพิ่มวิชา' },
        { id: 'editCourse', label: 'จัดการวิชา' }
      ]
    };
  }
};
</script>

<style>
:root {
  --primary-color: #4a6fa5;
  --primary-light: #6989bd;
  --primary-dark: #344e78;
  --accent-color: #ff6b6b;
  --text-color: #333;
  --text-light: #666;
  --bg-color: #f8f9fa;
  --card-bg: white;
  --border-color: #dee2e6;
  --shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  --border-radius: 6px;
  --font-family: 'Kanit', 'Sarabun', sans-serif;
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: var(--font-family);
  background-color: var(--bg-color);
  color: var(--text-color);
  line-height: 1.6;
}

.app-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.app-header {
  margin-bottom: 30px;
  text-align: center;
  padding: 20px 0;
  border-bottom: 1px solid var(--border-color);
}

.app-title {
  color: var(--primary-color);
  font-size: 2.2rem;
  font-weight: 600;
}

.tabs-container {
  background-color: var(--card-bg);
  border-radius: var(--border-radius);
  box-shadow: var(--shadow);
  overflow: hidden;
}

.tabs {
  display: flex;
  background-color: var(--primary-light);
  overflow-x: auto;
}

.tab-button {
  background-color: transparent;
  border: none;
  color: rgba(255, 255, 255, 0.8);
  padding: 12px 18px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s;
  white-space: nowrap;
  font-weight: 500;
}

.tab-button:hover {
  background-color: rgba(255, 255, 255, 0.1);
}

.tab-button.active {
  background-color: var(--primary-dark);
  color: white;
}

.tab-content {
  padding: 25px;
}

.content-section {
  animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Form styling (shared across components) */
input, select, button {
  font-family: var(--font-family);
}

input[type="text"],
input[type="number"],
input[type="email"],
input[type="file"],
select {
  width: 100%;
  padding: 10px 12px;
  margin-bottom: 15px;
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius);
  font-size: 1rem;
}

input[type="text"]:focus,
input[type="number"]:focus,
input[type="email"]:focus,
select:focus {
  outline: none;
  border-color: var(--primary-color);
  box-shadow: 0 0 0 2px rgba(74, 111, 165, 0.2);
}

button {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: var(--border-radius);
  padding: 10px 16px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

button:hover {
  background-color: var(--primary-dark);
}

button.danger {
  background-color: var(--accent-color);
}

button.danger:hover {
  background-color: #e45a5a;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .tabs {
    flex-direction: column;
  }
  
  .tab-content {
    padding: 15px;
  }
}
</style>