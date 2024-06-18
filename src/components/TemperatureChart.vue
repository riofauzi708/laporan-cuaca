<template>
    <div>
        <line-chart :chart-data="chartData" :options="options"></line-chart>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { Line } from 'vue-chartjs';
import { Chart as ChartJS, Title, Tooltip, Legend, LineElement, PointElement, LinearScale, CategoryScale } from 'chart.js';

ChartJS.register(Title, Tooltip, Legend, LineElement, PointElement, LinearScale, CategoryScale);

export default defineComponent({
    components: {
        LineChart: Line,
    },
    props: {
        data: {
            type: Array,
            required: true
        },
        labels: {
            type: Array,
            required: true
        }
    },
    setup(props) {
        const chartData = ref({
            labels: props.labels,
            datasets: [
                {
                    label: 'Temperature',
                    backgroundColor: '#f87979',
                    data: props.data,
                },
            ],
        });

        const options = ref({
            responsive: true,
            maintainAspectRatio: false,
        });

        return { chartData, options };
    }
});
</script>

<style scoped></style>