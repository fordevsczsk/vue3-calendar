<script setup>
    import { ref, computed } from 'vue';
    import { onClickOutside } from '@vueuse/core';
    
    import CalendarIcon from '@/components/Icons/CalendarIcon.vue';
    import CalendarDropdown from '@/components/CalendarDropdown.vue';

    const isDropdownActive = ref(false);

    const toggleDropdown = () => {
        isDropdownActive.value = !isDropdownActive.value;
    };

    const selectedDay = ref();
    const formattedSelectedDay = computed(() => {
        if (selectedDay.value) {
            const day = selectedDay.value.getDate();
            const month = selectedDay.value.getMonth() + 1;
            const year = selectedDay.value.getFullYear();

            return `${day}. ${month}. ${year}`;
        } else {
            return 'Vyberte datum';
        }
    });

    const dateSelect = (day) => {
        selectedDay.value = day;
        toggleDropdown();
    };

    const calendarWrapper = ref();

    onClickOutside(calendarWrapper, () => {
        isDropdownActive.value = false;
    });
</script>

<template>
    <div class="input-calendar">
        <label class="input-calendar__label">
            Datum:
        </label>
        <div class="input-calendar__calendar" ref="calendarWrapper">
            <button class="input-calendar__button" :class="{ 'is-selected': selectedDay }" @click.prevent="toggleDropdown">
                <span class="input-calendar__value">
                    {{ formattedSelectedDay }}
                </span>
                <CalendarIcon class="input-calendar__icon"/>
            </button>
            <div class="input-calendar__calendar-wrapper" :class="{ 'is-active': isDropdownActive }">
                <CalendarDropdown @dateSelect="dateSelect"/>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
    @use '@/assets/sass/components/calendar/input';
</style>