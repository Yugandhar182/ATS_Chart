<script>
  import { onMount, afterUpdate } from 'svelte';
  import { dateStore } from './DateStore.js'; // Import the store
  import { Chart, BarSeries, Category, DataLabel } from '@syncfusion/ej2-charts';
  Chart.Inject(BarSeries, Category, DataLabel);



  export let  appData;
  let chartData = [];
  let chart = null; // Initialize chart with null
  

  let startDate = '';
  let endDate = '';

  // Function to check if local storage has date information
  function getDatesFromLocalStorage() {
    const storedStartDate = localStorage.getItem('startDate');
    const storedEndDate = localStorage.getItem('endDate');

    if (storedStartDate && storedEndDate) {
      startDate = storedStartDate;
      endDate = storedEndDate;
    }
  }

  // Subscribe to the dateStore
  dateStore.subscribe((value) => {
    startDate = value.startDate;
    endDate = value.endDate;
    fetchData(startDate, endDate); // Fetch data whenever the date changes
  });


  async function fetchData(startDate, endDate) {
    try {
    
      const response = await fetch(`${appData.service.endpoint}/dashboard/ats/data/rejectreasons?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`);

      if (response.ok) {
        const jsonData = await response.json();
        console.log("Data fetched:", jsonData);
        chartData = jsonData.slice(0, 6).map((item, index) => ({
          x: item.reason || 'Unknown',
          y: item.count,
        
        }));
        chartData.sort((a, b) => a.y - b.y);
        updateChart();
      } else {
        const errorText = await response.text();
        console.error("Failed to fetch data. Error:", errorText);
      }
    } catch (error) {
      console.error("Error:", error);
    }
  }

 
  function updateChart() {
    const data = chartData.map(item => ({ ...item }));
    if (chart) {
      chart.destroy();
    }
    chart = new Chart({
      primaryXAxis: {
        valueType: 'Category',
        majorGridLines: { width: 0 },
        labelStyle: {
          size: '1rem',
          fontWeight: "510",
          fontFamily: 'sans-serif',
        },
      },
      primaryYAxis: {
        majorTickLines: { width: 0 },
        minorTickLines: { width: 0 },
        lineStyle: { width: 0 },
        interval: 5,
        labelStyle: {
          size: '1rem',
          fontWeight: "400",
          fontFamily: 'sans-serif'
        },
      },
      width: '1350px',
      height: "390px",
      series: [
        {
          type: 'Bar',
          dataSource: chartData,
          xName: 'x',
          width: 2,
          yName: 'y',
          columnSpacing: 0.1,
          animation: {
            enable: true,
            duration: 1000,
            delay: 200
          },
          cornerRadius: {
            topRight: 5,
            bottomRight: 5
          },
          marker: {
            dataLabel: {
              visible: true,
              position: "Bottom",
              font: {
                fontWeight: 'bold',
                color: 'white',
                size: '17px'
              }
            }
          },
        },
      ],
    });

    chart.appendTo('#container');
  }

  onMount(() => {
    getDatesFromLocalStorage(); // Try to get dates from local storage
    fetchData(startDate, endDate); // Fetch data on component mount
  });

  afterUpdate(() => {
    fetchData(startDate, endDate); // Fetch data after updates
    updateChart(); // Update the chart immediately

    // Store the dates in local storage for future use
    localStorage.setItem('startDate', startDate);
    localStorage.setItem('endDate', endDate);
  });
</script>
<div class="container1">
  <h1 class="h1">Reject Reasons</h1>
<div id='container'></div>
</div>
<style>
  .container1 {
    background-color: white;
    width: 1400px;
    height: 450px;
    margin-left:40px;
    margin-top:120px;
       
  }
  #container {
                margin-top: 40px; /* Add top margin */
                margin-bottom: 20px; /* Add bottom margin */
            }
  .h1{
    font-size: 17px;
    font-weight: bold;
    margin-left: 10px;
   margin-top: -10px;
 }
</style>
