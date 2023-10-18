<script>
  import { Card } from 'flowbite-svelte';
  import { onMount,afterUpdate } from 'svelte';
  import { dateStore } from './DateStore.js'; // Import the store


  export let  appData;
   let metrics = null; 
   let startDate = "";
   let endDate = "";

   function getDatesFromLocalStorage() {
  const storedStartDate = localStorage.getItem('startDate');
  const storedEndDate = localStorage.getItem('endDate');

  if (storedStartDate && storedEndDate) {
    startDate = storedStartDate;
    endDate = storedEndDate;
    fetchData(startDate, endDate); // Fetch data when dates are loaded from local storage
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
       
        const response = await fetch(`${appData.service.endpoint}/dashboard/ats/metrics?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`);
  
        if (response.ok) {
          metrics = await response.json();
      } else {
          const errorText = await response.text();
          console.error("Failed to fetch data. Error:", errorText);
        }
      } catch (error) {
        console.error("Error:", error);
      }
    }
    onMount(() => {
      getDatesFromLocalStorage(); // Try to get dates from local storage
      fetchData(startDate, endDate); // Fetch data on component mount
    });
  
    afterUpdate(() => {
    
      localStorage.setItem('startDate', startDate);
      localStorage.setItem('endDate', endDate);
    });
 </script>
 
 <div class="container">
  {#if metrics !== null}
  <div class="card1">
    <span class="hover-text">Total Jobs</span>
    <Card>
        <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalJobs}</h5>
        <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Jobs</p>
    </Card>
</div>
  
   <div class="card2">
    <span class="hover-text2">Total Placements</span>
     <Card  >
     <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalPlacements}</h5>
    <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Placements</p>
   </Card>
 </div>
   <div class="card3">
    <span class="hover-text3">Total Fee/Commission</span>
   <Card style="background-color:  rgb(84, 97, 243)" >
     <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">GBP{metrics.totalCommission}</h5>
     <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Billing</p>
   </Card>
   </div>
   <div class="card4">
    <span class="hover-text4">Total Job Board Adverts</span>
   <Card >
     <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalAdverts}</h5>
     <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Adverts</p>
   </Card>
 </div>
   <div class="card5">
    <span class="hover-text5">Avg.Time to Fill Days</span>
   <Card >
     <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.avgTimeToFillDays}</h5>
     <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">TTF</p>
   </Card>
   </div>
   <div class="card6">
    <span class="hover-text6">Avg.Candidates</span>
   <Card >
     <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.avgCandidatesPerPlacement}</h5>
     <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight"> Candidates/Jobs</p>
   </Card>
 </div>
 {/if}
 </div>
 
 

 <style>
   .container {
     display: flex;
     flex-direction: row;
     padding: 20px;

     margin-top:300px;
     margin-left: 40px;
     width: 1400PX;
     height:150px ;
    
   }
  
   .card1{
   
     height: 20px;
     width: 200px;
     margin-left:5px;
    
    
   }
  
   .card2{
   
     height: 20px;
     width: 200px;
     margin-left: 50px;
    
   }
   .card3{
    
     height: 20px;
     width: 200px;
     margin-left: 30px;
    
   }
   .card4{
   
     height: 20px;
     width: 200px;
     margin-left:30px;
    
   }
   .card5{
    
     height: 20px;
   
     width: 200px;
     margin-left: 30px;
    }
   .card6{
   
     height: 20px;
     margin-left: 30px;
    width: 200px;
   }
   
  .hover-text {
      display: none; 
      position: absolute;
      background-color: rgba(0, 0, 0, 0.7);
      color: white;
      padding: 5px;
      border-radius: 5px;
      margin-top: -50px; 
      margin-left:50px;
      z-index: 1; 
  }

  .card1:hover .hover-text {
      display: block; 
  }
  .hover-text2 {
  
      display: none; 
      position: absolute;
      background-color: rgba(0, 0, 0, 0.7);
      color: white;
      padding: 5px;
      border-radius: 5px;
      margin-top: -50px; 
      margin-left:40px;
      z-index: 1; 
 

  }
  .card2:hover .hover-text2 {
      display: block; 
  }
  .hover-text3 {
  
  display: none; 
  position: absolute;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px;
  border-radius: 5px;
  margin-top: -50px; 
  margin-left:20px;
  z-index: 1; 


}
.card3:hover .hover-text3 {
  display: block; 
}
.hover-text4 {
  
  display: none; 
  position: absolute;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px;
  border-radius: 5px;
  margin-top: -50px;
  margin-left:10px;
  z-index: 1;
 

}
.card4:hover .hover-text4 {
  display: block;
}
.hover-text5 {
  
  display: none; 
  position: absolute;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px;
  border-radius: 5px;
  margin-top: -50px;
  margin-left:20px;
  z-index: 1;
 

}
.card5:hover .hover-text5 {
  display: block;
}
.hover-text6 {
  
  display: none; 
  position: absolute;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px;
  border-radius: 5px;
  margin-top:-50px;
  margin-left:40px;
  z-index: 1;


}
.card6:hover .hover-text6{
  display: block;

}
 </style>