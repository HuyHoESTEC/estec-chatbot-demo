<template>
    <div class="option-container">
        <p>ESTEC Chatbot</p>
        <div class="question-list">
            <div class="date-group" v-for="(group, index) in groupdedQuestions" :key="index">
                <div class="group-label">
                    {{ group.label }}
                </div>
                <ul>
                    <li class="question-item" v-for="(item, idx) in group.items" :key="idx" >
                        <span>{{ item.title }}</span>
                        <span v-if="item.unread" class="dot"></span>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'GenAiOption',
    data() {
        return {
            groupdedQuestions: [
                {
                    label: "Hôm nay",
                    items: [
                        { title: "Dữ liệu đầu lò ngày 10/03/2025 lúc 12:30:00" },
                        { title: "Dữ liệu đầu lò ngày 14/03/2025 lúc 12:30:00" },
                        { title: "Dữ liệu GA01, GA02, GA03 ngày 10/03/2025 lúc 12:30:00" },
                        { title: "Dữ liệu GA01, GA02, GA03 ngày 13/03/2025 lúc 12:30:00" }
                    ]
                },
                {
                    label: "7 ngày trước đó",
                    items: [
                        { title: "Dữ liệu GA01, GA02, GA03 ngày 11/03/2025 lúc 12:30:00" },
                        { title: "Dữ liệu đầu lò ngày xxx" },
                        { title: "Dữ liệu đầu lò ngày xxx" }
                    ]
                },
                {
                    label: "30 ngày trước đó",
                    items: [
                        { title: "Dữ liệu đầu lò ngày xxx" },
                        { title: "Dữ liệu đầu lò ngày xxx" },
                        { title: "Dữ liệu đầu lò ngày xxx" }
                    ]
                }
            ]
        }
    }
}
</script>

<style scoped>
.option-container {
    display: flex;
    flex-direction: column;
    width: 100%;
    height: 100vh;
    color: black;
    font-weight: bold;
    background: rgba(255, 255, 255, 0.2);
    backdrop-filter: blur(2px);
}

.question-list {
  width: 250px;
  color: #d0d3d4;
  font-family: Arial, sans-serif;
  padding: 10px;
}

.date-group {
  margin-bottom: 16px;
}

.group-label {
  font-size: 14px;
  font-weight: bold;
  margin-bottom: 8px;
  color: #424949;
}

.question-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 6px 8px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  color: #566573;
}

.question-item:hover {
  background: #fff;
}

.question-item span {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: block;
  max-width: 100%; /* Adjust width depending on layout */
}

.dot {
  width: 8px;
  height: 8px;
  background-color: #1976d2;
  border-radius: 50%;
}
</style>