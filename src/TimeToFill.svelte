<script>
    import { onMount, afterUpdate } from 'svelte';
     import { dateStore } from './DateStore.js'; // Import the store
 
     import { Chart, ColumnSeries, SplineSeries, Category, DateTime, Tooltip, Legend } from '@syncfusion/ej2-charts';
     import { Browser } from '@syncfusion/ej2-base';
     Chart.Inject(ColumnSeries, SplineSeries, Category, DateTime, Tooltip, Legend);

     export let appData;
     let chartData2 = [];
     let splineData = [];
 
     const currentDate = new Date();
     const currentYear = currentDate.getFullYear();
 
     let startDate = '';
     let endDate = '';
 
     // Calculate the start and end date objects
     const startDateObj = new Date(startDate);
     const endDateObj = new Date(endDate);
 
     // Calculate the range of months between the start and end date
     const startYear = startDateObj.getFullYear();
     const endYear = endDateObj.getFullYear();
     const startMonth = startDateObj.getMonth();
     const endMonth = endDateObj.getMonth();
 
     const monthNames = [];
 
     for (let year = startYear; year <= endYear; year++) {
         const start = (year === startYear) ? startMonth : 0;
         const end = (year === endYear) ? endMonth : 11;
 
         for (let month = start; month <= end; month++) {
             monthNames.push(`${year} ${new Date(year, month, 1).toLocaleString('default', { month: 'short' })}`);
         }
     }
 
 
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
            
            const response = await fetch(`${appData.service.endpoint}/dashboard/ats/data/placementmonthlymetrics?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`);
             if (response.ok) {
                 const jsonData = await response.json();
                 console.log(jsonData)
                 const dataMap = new Map(jsonData.map(item => [monthNames[item.month - 1], item]));
 
                 chartData2 = jsonData.map(item => ({
                 x: `${item.year} ${new Date(item.year, item.month - 1, 1).toLocaleString('default', { month: 'short' })}`,
                 y: item.placements
             }));
 
 
             splineData = jsonData.map(item => ({
                 x: `${item.year} ${new Date(item.year, item.month - 1, 1).toLocaleString('default', { month: 'short' })}`,
                 y: item.days
             }));
 
 
             
                 // Sort the data by date in ascending order
                 chartData2.sort((a, b) => new Date(a.x) - new Date(b.x));
                 splineData.sort((a, b) => new Date(a.x) - new Date(b.x));
 
                 updateChart();
             } else {
                 console.error('Failed to fetch data from the API');
             }
         } catch (error) {
             console.error('An error occurred:', error);
         }
     };
 
  
 
     function updateChart() {
         const chart = new Chart({
             primaryXAxis: {
                 valueType: 'Category',
                 majorGridLines: { width: 0 },
                 labelStyle: {
                     size: '15px',
                  fontWeight:"normal"
          
            }
         },
             primaryYAxis: {
                 edgeLabelPlacement: 'Shift',
                 majorTickLines: { width: 0 },
                 minorTickLines: { width: 0 },
                 lineStyle: { width: 0 },
                 title: "Placements",
                 titleStyle: {
                     fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                   fontWeight: '360',
                   color: "#767676;",
                   size: '18px',
                  
                  },
                 labelStyle: {
                     size: '15px',
                  fontWeight:"normal"
          
         
                 
               
             }
         },
        
             height: '350px',
             series: [
                 {
                     type: 'Column',
                     dataSource: chartData2,
                     xName: 'x',
                     yName: 'y',
                     fill: 'DodgerBlue',
                     name: 'Placements',
                     columnWidth: 0.5,
                 },
                 {
                     type: 'Spline',
                     dataSource: splineData,
                     xName: 'x',
                     yName: 'y',
                     yAxisName: 'splineAxis',
                     width: 2,
                     fill: 'blue',
                     marker: {
                         visible: true,
                         width: 10,
                         height: 10
                     },
                     name: 'Avg.Days to fill',
                   
                 },
             ],
             axes: [
                 {
                     name: 'splineAxis',
                     opposedPosition: true,
                     title: 'Total time to fill days',
                     titleStyle: {
                     fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                   fontWeight: '360',
                   color: "#767676;",
                   size: '18px',
                  
                  },
                     labelStyle: {
                     size: '15px',
                  fontWeight:"normal"
          
                     }
                   
                 },
             ],
            
             tooltip: {
                     enable: true,
                     shared: true,
                     format: ' ${series.name} :${point.y}',
                     fill: 'white', // Change the background color of the tooltip
                     textStyle: {
                         color: 'black', // Change the text (content) color of the tooltip
                         fontWeight:"bold",
                     
                     
                     },
                     border: {
                         width: 4, // Set the border width
                         color: 'whitesmoke' // Set the border color
                     }
                  },
             legendSettings: {
         visible: true,
         //Legend position as top
         position:'Bottom'
         }
         });
 
         chart.appendTo('#container2');
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
 
<div class="card card-fluid">
    <div class="card-header border-0">
        Time to Fill
    </div>
    <div class="card-body">
        <div id="container2"></div>
    </div>
</div>
  

<style>
       

 .card {
    position: relative;
    display: flex;
    flex-direction: column;
    min-width: 0;
 
    background-color: #fff;
    background-clip: border-box;
    border: 1px solid rgba(20,20,31,.12);
    border-radius: 0.25rem;
    margin: 40px;
    top:5px;
    

}
.card-body {
    flex: 1 1 auto;
    min-height: 1px;
    padding: 1rem;
}
.card-header {
  font-size: 16px; /* Adjust the font size as needed */
  font-weight: bold; /* Adjust the font weight as needed */
 

  padding: 15px; /* Adjust the padding as needed */
 
}

</style>
