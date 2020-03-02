<template>
    <div class="navbar">
        <ReuploadDataButton>Upload New Data</ReuploadDataButton>
        <ViewUploadedDataButton>View Uploaded Data</ViewUploadedDataButton>

        <DropDown :emitEventName="yearSelectedEmitted" :options="years" :selectedOption="selectedYear"></DropDown>
        <DropDown :emitEventName="monthSelectedEmitted" :options="months" :selectedOption="selectedMonth"></DropDown>
    </div>
</template>

<script>
    import Button from '../../components/ui/Button.vue'
    import ViewUploadedDataButton from '../../components/ui/ViewUploadedDataButton.vue'
    import ReuploadDataButton from '../../components/ui/ReuploadDataButton.vue'
    import DropDown from '../../components/inputs/DropDown.vue'

    export default {
        name: 'NavBar',
        components: {
            'V-Button': Button,
            ViewUploadedDataButton,
            ReuploadDataButton,
            DropDown
        },
        props: {
            years: Array
        },
        data: function () {
            return {
                selectedYear: { id: null, text: 'All Years' },
                months: [
                    { id: null, text: 'All Months' },
                    { id: 0, text: 'Jan' },
                    { id: 1, text: 'Feb' },
                    { id: 2, text: 'Mar' },
                    { id: 3, text: 'Apr' },
                    { id: 4, text: 'May' },
                    { id: 5, text: 'Jun' },
                    { id: 6, text: 'Jul' },
                    { id: 7, text: 'Aug' },
                    { id: 8, text: 'Sep' },
                    { id: 9, text: 'Oct' },
                    { id: 10, text: 'Nov' },
                    { id: 11, text: 'Dec' }
                ],
                selectedMonth: { id: null, text: 'All Months' },

                yearSelectedEmitted: 'year-selected-changed',
                monthSelectedEmitted: 'month-selected-changed'
            }
        }
    }</script>


<style scoped>
    nav {
        width: 100%;
        height: 40px;
        text-align: left;
        position: relative;
    }
</style>
