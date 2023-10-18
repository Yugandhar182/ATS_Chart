<script>


    export let appData; // Export appData from main.svelte

    import Calendar from './DateRangePicker.svelte';
    import Reject from './RejectedReasons.svelte';
    import Metric from './metric.svelte';
    import TimeToFill from './TimeToFill.svelte';
    import Placement from "./PlacementByUser.svelte"
  </script>
  
  <div class="main-container">
    <Calendar/>
    <Metric {appData}/>
    <TimeToFill {appData}/>
    <Placement {appData}/>
    <Reject {appData}/> 
    </div>
 
    <style>
        .main-container {
           background-color: rgba(207, 218, 236, 0.5);;
            width: 1520px;
            height: 1800px;
            margin-left:0px;
            margin-top:-165px;
        }
    
     
      
    </style>