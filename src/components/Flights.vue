<template>
  <div>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px;  ">Flight Center</h2>
    <h3>Most recent landed flights over the world</h3>
    <div v-if="noUpdate">
       <img src="../assets/spinner.gif" width="100">  
        <h4 class="noFlight">No flight is landed over last few seconds</h4> 
    </div>
    <div class="container" id="app">
    <div class="row">
      <div v-for="(flight,index) in flights" class="col-md-6 col-lg-4 col-xl-4" style="margin: 16px 0px;">
        <div class="card">
          <div class="card-body">
            <h4 class="card-title"><i class="fas fa-plane-departure"></i> Departure: {{ flight.departure.iataCode }}  </h4>
            <h4 class="card-title"><i class="fas fa-plane-arrival"></i>  Arrival  : {{ flight.arrival.iataCode }} </h4>
            <h4 class="card-title status"><span v-if="index==0"> Just</span> {{ flight.status }} </h4>
            
             <transition name="fade">
                 <h5 > Flight No:  {{flight.flight.number}} </h5>
             </transition>

             <h5 class="card-title">Airline: {{ flight.airline.iataCode }}  </h5>
             <h5 class="card-subtitle mb-2">Lat: {{ flight.geography.latitude }} <i class="fas fa-globe-asia"></i></h5>
             <h5 class="card-subtitle mb-2"> Long:{{ flight.geography.longitude }}  <i class="fas fa-globe-asia"></i></h5>
          </div>
        </div>
      </div>
    </div>
  </div>
  </div>
</template>

<script>
import { HubConnection, HubConnectionBuilder } from '@aspnet/signalr'
import * as signalR from '@aspnet/signalr'
import axios from "axios";

export default {
  name: "Flights",
  data: function() {
    return {
      apiBaseUrl: "http://localhost:7071/api",
      hubConnection: HubConnection,
      flights: [],
      options:{},
      noUpdate:true
    };
  },
  created: function() {
    this.hubConnection = new signalR.HubConnectionBuilder()
        .withUrl(`${this.apiBaseUrl}`,{withCredentials:true})
        .build();
      
      this.hubConnection.on('landedFlight', this.landedFlight);
      this.hubConnection
              .start()
              .then(() => {
                console.log("connection started");
              })
              .catch(e => {
                console.error(
                  `An error occurred while connecting to operations hub. Details ${e}`
                );
              });
  },
  methods: {
    landedFlight(updatedFlight) {
      if (updatedFlight && updatedFlight.flight)
      {
      const flightNumber= updatedFlight.flight.number;
      if (this.flights.length==0 || this.flights[0].flight.number!=flightNumber) {
        this.flights.unshift(updatedFlight);
        this.noUpdate=false;
     } 
      else
      {
        this.noUpdate=true;
      }
        
    }}
  }
};
</script>

<style lang="scss" scoped>
.noFlight{
  color: blue;
}
.container .row div:first-child {
  .card {
    background-color: azure;
    border: 2px solid gray;
    .status{
        color: red;
      }
      .card-title{
        i{
          margin-right: 8px; 
        }
      }
  }
} 

</style>

