<template>
    <div class="info-container">
        <p>ESTEC FACTORY - {{ formattedDateTime }}</p>
        <div class="suggest-question-list">
            <div class="date-group" v-for="(group, index) in groupdedQuestions" :key="index">
                <div class="group-label">
                    {{ group.label }}
                </div>
                <ul>
                    <li class="suggest-question-item" v-for="(item, idx) in group.items" :key="idx" >
                        <span>{{ item.title }}</span>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
import { onMounted, onUnmounted, ref } from 'vue';

export default {
    name: 'UserInformation',
    data() {
        return {
            groupdedQuestions: [
                {
                    label: "Gợi ý câu hỏi",
                    items: [
                        { title: "Dữ liệu đầu lò ngày 10/03/2025 lúc 12:30:00" },
                        { title: "Dữ liệu GA01 ngày 14/03/2025 lúc 12:30:00" },
                        { title: "Dữ liệu GA01, GA02 ngày 14/03/2025 lúc 12:30:00" },
                        { title: "Dữ liệu GA01, GA02, GA03 ngày 14/03/2025 lúc 12:30:00" },
                        { title: "Dữ liệu GA01, GA02, GA03 ngày 13/03/2025 lúc 09:15:00, 10:30:00, 04:40:00" },
                        { title: "Dữ liệu GA01, GA02 ngày 13/03/2025 lúc 09:15:00, 10:30:00, 04:40:00" },
                        { title: "Dữ liệu GA01 ngày 13/03/2025 lúc 09:15:00, 10:30:00, 04:40:00" },
                        { title: "Dữ liệu đầu lò ngày 13/03/2025 lúc 09:15:00, 10:30:00, 04:40:00" },
                        { title: "Dữ liệu GA01, GA02, GA03 ngày hôm qua lúc 09:15:00, 10:30:00, 04:40:00" },
                        { title: "Dữ liệu GA01, GA02, GA03 ngày hôm nay lúc 09:15:00, 10:30:00, 04:40:00" },
                    ]
                }
            ]
        }
    },
    setup() {
        const formattedDateTime = ref('');
        let intervalId = null;

        const updateDateTime = () => {
            const now = new Date();
            const options = {
                year: 'numeric',
                month: 'numeric',
                day: 'numeric',
                hour: 'numeric',
                minute: 'numeric',
                second: 'numeric',
            };
            formattedDateTime.value = new Intl.DateTimeFormat(undefined, options).format(now);
        };

        onMounted(() => {
            updateDateTime();
            intervalId = setInterval(updateDateTime, 1000);
        });

        onUnmounted(() => {
            clearInterval(intervalId);
        });

        return {
            formattedDateTime
        }
    }
}
</script>

<style>
.info-container {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    width: 100%;
    height: 100%;
    color: black;
}

.info-container > p {
    font-weight: bold;
    font-size: 20px;
    color: white;
}

.suggest-question-list {
  width: 100%;
  color: #d0d3d4;
  font-family: Arial, sans-serif;
  padding: 10px;
  overflow-x: hidden;
  overflow-y: scroll;
  height: 40%;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(2px);
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

.suggest-question-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 6px 8px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  color: #566573;
  text-align: left;
}

.suggest-question-item:hover {
  background: #f8f9f9;
}

.suggest-question-item span {
  white-space: inherit;
  overflow: hidden;
}
</style>