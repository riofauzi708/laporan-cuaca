<template>
    <div class="weather">
        <h1>Weather App</h1>
        <input v-model="city" placeholder="Enter city" @keyup.enter="getWeather" />
        <button @click="getWeather">Get Weather</button>
        <div v-if="loading" class="loading">Loading...</div>
        <div v-if="error" class="error">{{ error }}</div>
        <div v-if="weather" class="weather-info">
            <h2>{{ weather.name }}</h2>
            <p>{{ weather.weather[0].description }}</p>
            <p>Temperature: {{ formattedTemperature }}</p>
            <img :src="`http://openweathermap.org/img/wn/${weather.weather[0].icon}.png`" alt="Weather Icon" />
        </div>
        <div v-if="forecast.length" class="forecast">
            <h3>5-Day Forecast</h3>
            <div v-for="day in forecast" :key="day.date" class="forecast-day">
                <p>{{ day.date }}</p>
                <p>{{ day.temp }}°{{ tempUnit }}</p>
                <img :src="`http://openweathermap.org/img/wn/${day.icon}.png`" alt="Weather Icon" />
            </div>
        </div>
        <div class="unit-toggle">
            <span @click="toggleUnit" class="unit-link">Switch to {{ targetUnit }}</span>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';
import axios from 'axios';

export default defineComponent({
    setup() {
        const city = ref('');
        const weather = ref<any>(null);
        const loading = ref(false);
        const error = ref<string | null>(null);
        const forecast = ref<Array<any>>([]);
        const tempUnit = ref('C');
        const targetUnit = computed(() => tempUnit.value === 'C' ? 'Fahrenheit' : 'Celsius');

        const getWeather = async () => {
            const apiKey = '40c1b88a1e0fd43822d06e936d2b8ece';
            const units = tempUnit.value === 'F' ? 'imperial' : 'metric';
            const weatherUrl = `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=${units}&appid=${apiKey}`;
            const forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city.value}&units=${units}&appid=${apiKey}`;

            loading.value = true;
            error.value = null;
            weather.value = null;
            forecast.value = [];

            try {
                const weatherResponse = await axios.get(weatherUrl);
                weather.value = weatherResponse.data;

                const forecastResponse = await axios.get(forecastUrl);
                const dailyData = forecastResponse.data.list.filter((reading: any) => reading.dt_txt.includes("18:00:00"));

                forecast.value = dailyData.map((reading: any) => ({
                    date: new Date(reading.dt_txt).toLocaleDateString(),
                    temp: reading.main.temp,
                    icon: reading.weather[0].icon,
                }));
            } catch (err) {
                error.value = 'City not found or error fetching data';
            } finally {
                loading.value = false;
            }
        };

        const formattedTemperature = computed(() => {
            if (!weather.value) return '';
            const temp = weather.value.main.temp;
            return `${temp}°${tempUnit.value}`;
        });

        const toggleUnit = () => {
            tempUnit.value = tempUnit.value === 'C' ? 'F' : 'C';
            getWeather(); // Panggil getWeather lagi untuk mengambil data dengan unit yang baru diubah
        };

        return {
            city,
            weather,
            loading,
            error,
            forecast,
            tempUnit,
            formattedTemperature,
            toggleUnit,
            targetUnit,
            getWeather,
        };
    },
});
</script>

<style scoped>
.weather {
    text-align: center;
    margin-top: 50px;
}

input {
    padding: 10px;
    margin: 10px;
    border-radius: 5px;
    border: 1px solid #ccc;
}

button {
    padding: 10px 20px;
    margin: 10px;
    border: none;
    border-radius: 5px;
    background-color: #007BFF;
    color: white;
    cursor: pointer;
}

button:hover {
    background-color: #0056b3;
}

.loading {
    margin: 20px;
    color: #007BFF;
}

.error {
    margin: 20px;
    color: red;
}

.weather-info {
    margin-top: 20px;
    border-top: 1px solid #ccc;
    padding-top: 20px;
}

.forecast {
    margin-top: 20px;
}

.forecast-day {
    display: inline-block;
    margin: 10px;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
}

.unit-toggle {
    margin-top: 20px;
}

.unit-link {
    color: #007BFF;
    cursor: pointer;
}

.unit-link:hover {
    text-decoration: underline;
}
</style>