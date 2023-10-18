<script>
    import { onMount, afterUpdate } from 'svelte';
    import { DateRangePicker } from '@syncfusion/ej2-calendars';
    import { dateStore } from './DateStore.js'; // Import the store
   
    let daterangepicker;
   
    let startDate = localStorage.getItem('startDate') || '';
    let endDate = localStorage.getItem('endDate') || '';
  
    onMount(() => {
      initializeDateRangePicker(startDate, endDate);
    });
  
    afterUpdate(() => {
      // After the component updates, check if the local storage values have changed
      const updatedStartDate = localStorage.getItem('startDate') || '';
      const updatedEndDate = localStorage.getItem('endDate') || '';
  
      if (startDate !== updatedStartDate || endDate !== updatedEndDate) {
        startDate = updatedStartDate;
        endDate = updatedEndDate;
      }
    });
  
    function initializeDateRangePicker(initialStartDate, initialEndDate) {
      daterangepicker = new DateRangePicker({
        placeholder: 'Select Range',
        start: 'Year', 
        depth: 'Year', 
        format: 'MMM yyyy',
        value: [
          new Date(initialStartDate || new Date()), // Initialize with either stored date or a default date
          new Date(initialEndDate || new Date())   // Initialize with either stored date or a default date
        ],
        change: () => {
          const selectedDates = daterangepicker.getSelectedRange();
          if (selectedDates && selectedDates.startDate && selectedDates.endDate) {
            const selectedStartDate = formatSelectedDate(selectedDates.startDate);
            const selectedEndDate = formatSelectedDate(selectedDates.endDate);
            localStorage.setItem('startDate', selectedStartDate);
            localStorage.setItem('endDate', selectedEndDate);
            dateStore.set({ startDate: selectedStartDate, endDate: selectedEndDate });
           
          }
        }
      });
      daterangepicker.appendTo('#element');
  ;
    }
  
    function formatSelectedDate(date) {
      const day = date.getDate();
      const month = date.getMonth() + 1;
      const year = date.getFullYear();
      return `${day.toString().padStart(2, '0')}/${month.toString().padStart(2, '0')}/${year}`;
    }
  
  
  </script>
  <div class="calendar-container">
    <div class="col-lg-12 control-section">
      <div id="wrapper">
        <input id="element" type="text" /><br/><br/>
      </div>
    </div>
  </div>
  <style>
    .calendar-container {
      position: absolute;
      top: 50px; /* Adjust the top position as needed */
      right: 15px; /* Adjust the left position as needed */
      width: 200px;
      height: 100px;
    }
  </style>