<template>
    <div class="info-container">
        <p>ESTEC FACTORY - {{ formattedDateTime }}</p>
        <div class="active-sensor-list">
            <div class="sensor-group" v-for="(group, index) in activeSensorList" :key="index">
                <div class="group-label">
                    {{ group.label }}
                </div>
                <ul>
                    <li class="active-sensor-item" v-for="(item, idx) in group.items" :key="idx" >
                        <span>
                            <i class="sensor-icon active"></i>
                            {{ item.title }}
                        </span>
                    </li>
                </ul>
            </div>
        </div>
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
                        { title: "Giá trị NOx, Oxy GA02 lúc 00:00:00 ngày 12/05/2025 là bao nhiêu?" },
                        { title: "Dữ liệu cảm biến lúc 21:00:00 ngày 11/05/2025, cảm biến NOx và cảm biến Oxy GA03 có giá trị bao nhiêu?" },
                        { title: "Dữ liệu của cảm biến GA01, GA02 và GA03 tại thời điểm lúc 12:30:00, 13:30:00, 14:45:00, 15:55:00, 16:20:00 và 17:30:00 ngày 10/05/2025 là bao nhiêu, so sánh các giá trị với nhau và đánh giá dữ liệu" },
                        { title: "Cho tôi biết giá trị NOx, Oxy GA01 tại thời điểm 14:30 13/05/2025" },
                        { title: "Giá trị NOx, Oxy GA02 lúc 00:00:00 ngày 12/05/2025 là bao nhiêu?" },
                        { title: "Dữ liệu cảm biến lúc 21:00:00 ngày 11/05/2025, cảm biến NOx và cảm biến Oxy GA03 có giá trị bao nhiêu?" },
                        { title: "Dữ liệu cảm biến NOx và cảm biến Oxy GA03 của lò nung vào lúc 06:00:00 ngày 10/05/2025 là gì?" },
                        { title: "Dữ liệu cảm biến NOx và cảm biến Oxy GA03 lúc 10:00:00 ngày 09/05/2025 là bao nhiêu?" },
                        { title: "Dữ liệu cảm biến NOx và cảm biến Oxy GA03 tại thời điểm 18:45:00 ngày 08/05/2025 là bao nhiêu?" },
                        { title: "Dữ liệu cảm biến NOx và cảm biến Oxy GA03 tại thời điểm 14:00:00 ngày 07/05/2025 là bao nhiêu?" },
                    ]
                }
            ],
            activeSensorList: [
                {
                    label: "Danh sách cảm biến đang hoạt động",
                    items: [
                        { title: "Nhiệt độ vòi đốt lò" },
                        { title: "Dòng điện động cơ quay lò" },
                        { title: "Nhiệt độ đầu lò" },
                        { title: "Cảm biến GA02 đo nồng độ khí Oxi sau cyclon tầng 2 phía silo" },
                        { title: "Cảm biến GA01 đo nồng độ khí Oxi sau cyclon tầng 2 phía silo" },
                        { title: "Cảm biến GA03 đo nồng độ khí Oxi sau cyclon tầng 2 phía silo" },
                        { title: "Cảm biến GA01 đo nồng độ khí NOx ở đầu lò" },
                        { title: "Điểm thiết lập than PC" },
                        { title: "Tổng mức tiêu thụ đống" },
                        { title: "Nhiệt độ tháp dầu" },
                        { title: "Tốc độ lò SP" },
                        { title: "Nhiệt độ đầu của bộ thu hồi nhiệt" },
                        { title: "Than SP" },
                        { title: "Điểm thiết lập nhiên liệu thực tế" },
                        { title: "Tỷ lệ thay thế nhiệt bằng nhiên liệu thay thế" },
                        { title: "Lượng nhiên liệu tiêu thụ thực tế" },
                        { title: "Tốc độ quay lò thực tế" },
                        { title: "Nhiệt độ trung bình vùng cháy BZT" },
                        { title: "Tốc độ quạt thiết lập" },
                        { title: "Độ mở van điều khiển quạt" },
                        { title: "Nhiệt độ tầng cyclon C3" },
                        { title: "Nhiệt độ tầng cyclon C2" },
                        { title: "Lưu lượng than phun thực tế" },
                        { title: "Trọng lượng nguyên liệu vải cân được" },
                        { title: "Áp suất tại đầu lò (hood)" },
                        { title: "Quạt gió cấp 4 S1" },
                        { title: "Lưu lượng băng tải 02" },
                        { title: "Hàm lượng SO3 trong khí thải" },
                        { title: "Lưu lượng cấp liệu thực tế vùng SZ" },
                        { title: "Hàm lượng vôi tự do (CaO tự do) trong clinker" },
                        { title: "Áp suất thủy lực" },
                        { title: "Áp suất quạt gió thổi than 02" },
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
  max-height: 100vh; /* Hoặc một chiều cao cố định phù hợp với layout của bạn */
  overflow-y: auto; /* Cho phép hiển thị thanh cuộn khi nội dung vượt quá max-height */
}

.date-group {
  margin-bottom: 16px;
}

.date-group ul {
  list-style: none;
  padding: 0;
  margin: 0;
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

.suggest-question-list::-webkit-scrollbar {
  width: 8px; /* Độ rộng của thanh cuộn */
}

.suggest-question-list::-webkit-scrollbar-thumb {
  background-color: #ccc; /* Màu của "ngón tay" thanh cuộn */
  border-radius: 4px;
}

.suggest-question-list::-webkit-scrollbar-track {
  background-color: #f1f1f1; /* Màu của "đường ray" thanh cuộn */
}

/* Ẩn thanh cuộn khi không hover (tùy chọn, có thể không hoạt động trên mọi trình duyệt) */
.suggest-question-list {
  scrollbar-width: thin; /* Cho Firefox */
  scrollbar-color: #ccc #f1f1f1; /* Cho Firefox */
}

/* Ẩn thanh cuộn mặc định của WebKit (Chrome, Safari) khi không hover */
.suggest-question-list:hover::-webkit-scrollbar-thumb {
  opacity: 1; /* Hiển thị khi hover */
}

.suggest-question-list::-webkit-scrollbar-thumb {
  opacity: 0; /* Ẩn mặc định */
  transition: opacity 0.3s ease;
}


.active-sensor-list {
  width: 100%;
  color: #d0d3d4;
  font-family: Arial, sans-serif;
  padding: 10px;
  overflow-x: hidden;
  overflow-y: scroll;
  height: 40%;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  max-height: 100vh; /* Hoặc một chiều cao cố định phù hợp với layout của bạn */
  overflow-y: auto; /* Cho phép hiển thị thanh cuộn khi nội dung vượt quá max-height */
}

.sensor-group {
  margin-bottom: 16px;
}

.sensor-group ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.active-sensor-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 6px 2px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  color: #f4f6f7;
  text-align: left;
}

.active-sensor-item:hover {
  background: #f8f9f9;
  color: black;
}

.active-sensor-item span {
  white-space: inherit;
  overflow: hidden;
}

.active-sensor-list::-webkit-scrollbar {
  width: 8px; /* Độ rộng của thanh cuộn */
}

.active-sensor-list::-webkit-scrollbar-thumb {
  background-color: #ccc; /* Màu của "ngón tay" thanh cuộn */
  border-radius: 4px;
}

.active-sensor-list::-webkit-scrollbar-track {
  background-color: #f1f1f1; /* Màu của "đường ray" thanh cuộn */
}

/* Ẩn thanh cuộn khi không hover (tùy chọn, có thể không hoạt động trên mọi trình duyệt) */
.active-sensor-list {
  scrollbar-width: thin; /* Cho Firefox */
  scrollbar-color: #ccc #f1f1f1; /* Cho Firefox */
}

/* Ẩn thanh cuộn mặc định của WebKit (Chrome, Safari) khi không hover */
.active-sensor-list:hover::-webkit-scrollbar-thumb {
  opacity: 1; /* Hiển thị khi hover */
}

.active-sensor-list::-webkit-scrollbar-thumb {
  opacity: 0; /* Ẩn mặc định */
  transition: opacity 0.3s ease;
}

.sensor-icon {
  display: inline-block;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  margin-right: 8px;
}

.sensor-icon.active {
  background-color: #1e8449; /* Màu xanh lá cây cho trạng thái hoạt động */
}

@media (max-width: 767px) {
    .info-container > p {
        font-weight: bold;
        color: black;
    }

    .active-sensor-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 6px 2px;
    border-radius: 4px;
    cursor: pointer;
    font-size: 14px;
    color: #566573;
    text-align: left;
    }
}
</style>