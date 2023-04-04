<script setup>
    import { ref, computed } from 'vue';

    import ArrowIcon from '@/components/Icons/ArrowIcon.vue';
    
    const emits = defineEmits('dateSelect');
    
    const WEEKDAYS = ['Po', 'Út', 'St', 'Čt', 'Pá', 'So', 'Ne'];
    const MONTHS = ['Leden', 'Únor', 'Březen', 'Duben', 'Květen', 'Červen', 'Červenec', 'Srpen', 'Září', 'Říjen', 'Listopad', 'Prosinec'];

    const selectedMonth = ref(new Date().getMonth());
    const selectedYear = ref(new Date().getFullYear());
    const selectedDay = ref();

    const selectedMonthName = computed(() => {
        return MONTHS[selectedMonth.value];
    });

    const getDaysInMonth = (month, year) => {
        let date = new Date(year, month, 1);
        const days = [];

        while (date.getMonth() === month) {
            days.push(new Date(date));
            date.setDate(date.getDate() + 1);
        };

        return days;
    };

    const getCalendarPlaceholders = (month, year) => {
        const date = new Date(year, month, 1);
        const firstDay = date.getDay();

        return firstDay === 0 ? 6 : firstDay - 1;
    }

    const daysInSelectedMonth = computed(() => {
        return {
            placeholders: getCalendarPlaceholders(selectedMonth.value, selectedYear.value),
            days: getDaysInMonth(selectedMonth.value, selectedYear.value),
        }
    });

    const isToday = (day) => {
        const today = new Date();

        if (selectedMonth.value !==  new Date().getMonth() || selectedYear.value !== new Date().getFullYear()) {
            return false;
        }

        return today.getDate() === day.getDate();
    };

    const isSelected = (day) => {
        if (!selectedDay.value || !selectedMonth || !selectedYear) return false;

        if (day.getDate() === selectedDay.value.getDate() && selectedDay.value.getMonth() === selectedMonth.value && selectedDay.value.getFullYear() === selectedYear.value) {
            return true;
        } else {
            return false;
        }
    }

    const toggleRange = (direction, range) => {
        if (direction === 'increase') {
            if (range === 'year') {
                selectedYear.value++;
            } else {
                if (selectedMonth.value === 11) {
                    selectedMonth.value = 0;
                    selectedYear.value++;
                } else {
                    selectedMonth.value++;
                }
            }
        } else if (direction === 'decrease') {
            if (range === 'year') {
                selectedYear.value--;
            } else {
                if (selectedMonth.value === 0) {
                    selectedMonth.value = 11;
                    selectedYear.value--;
                } else {
                    selectedMonth.value--;
                }
            }
        } else {
            selectedMonth.value = new Date().getMonth();
            selectedYear.value = new Date().getFullYear();
        }
    };

    const dayClickHandler = (day) => {
        selectedDay.value = day;
        emits('dateSelect', day);
    };
</script>

<template>
    <div class="calendar-dropdown">
        <div class="calendar-dropdown__header">
            <div class="calendar-dropdown__prev">
                <button class="calendar-dropdown__step-icon-button" @click="toggleRange('decrease', 'month')">
                    <ArrowIcon class="calendar-dropdown__step-icon calendar-dropdown__step-icon--prev"/>
                </button>
            </div>
            <div class="calendar-dropdown__selected-range">
                <span class="calendar-dropdown__selected-month">{{ selectedMonthName }}</span>
                <span class="calendar-dropdown__selected-year">{{ selectedYear }}</span>
            </div>
            <div class="calendar-dropdown__next">
                <button class="calendar-dropdown__step-icon-button" @click="toggleRange('increase', 'month')">
                    <ArrowIcon class="calendar-dropdown__step-icon calendar-dropdown__step-icon--next"/>
                </button>
            </div>
        </div>
        <div class="calendar-dropdown__calendar">
            <ol class="calendar-dropdown__weekdays">
                <li class="calendar-dropdown__weekday" v-for="weekday in WEEKDAYS" :key="weekday">
                    {{ weekday }}
                </li>
            </ol>
            <ol class="calendar-dropdown__days">
                <li class="calendar-dropdown__day is-placeholder" v-for="placeholder in daysInSelectedMonth.placeholders" :key="placeholder"></li>
                <li class="calendar-dropdown__day" :class="{ 'is-today': isToday(day), 'is-selected': isSelected(day) }" v-for="(day, index) in daysInSelectedMonth.days" :key="index" @click="dayClickHandler(day)">{{ day.getDate() }}</li>
            </ol>
        </div>
    </div>
</template>

<style lang="scss" scoped>
    @use '@/assets/sass/components/calendar/dropdown';
</style>