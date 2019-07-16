<template>
  <div>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px;  ">Flight Center</h2>
    <h3>Most recent landed flights in Sydney</h3>
    <div class="container" id="app">
    <div class="row">
      <div v-for="flight in flights" class="col-md-6 col-lg-4 col-xl-3" style="margin: 16px 0px;">
        <div class="card">
          <div class="card-body">
            <h4 class="card-title">{{ flight.departure.iataCode }} <i class="fas fa-plane"></i> {{ flight.arrival.iataCode }}</h4>

             <transition name="fade">
                 <p v-if="show"> Flight No:  {{flight.flight.number}} </p>
             </transition>

             <h4 class="card-title">Airline: {{ flight.airline.iataCode }}  </h4>
             <h4 class="card-title">{{ flight.status }}  </h4>

             <!-- <h4 class="card-subtitle mb-2" :key="flight.price">${{ flight.price }}</h4> -->
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
      show: true,
      hubConnection: HubConnection,
      flights: [],
      options:{}
    };
  },
  created: function() {
    let _this=this;
    let defaultFlight = {
      geography: {
        latitude: 43.5033,
        longitude: -79.1297,
        altitude: 7833.36,
        direction: 70
      },
      speed: {
        horizontal: 833.4,
        isGround: 0,
        vertical: 0
      },
      departure: {
        iataCode: "YHM",
        icaoCode: "CYHM"
      },
      arrival: {
        iataCode: "YQM",
        icaoCode: "CYQM"
      },
      aircraft: {
        icaoCode: "B763",
        regNumber: "CGYAJ",
        icao24: "C08412"
      },
      airline: {
        iataCode: "W8",
        icaoCode: "CJT"
      },
      flight: {
        iataNumber: "W8620",
        icaoNumber: "CJT620",
        number: "620"
      },
      system: {
        updated: 1513148168,
        squawk: "0000"
      },
      status: "en-route"
    };
    this.flights.push(defaultFlight);
    this.hubConnection = new signalR.HubConnectionBuilder()
        .withUrl(`${this.apiBaseUrl}`,{withCredentials:true})
        .build();
      
     
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

    
    // this.getConnectionInfo().then(function(info) {

    //   let accessToken = info.accessToken;
    //   const options = {
    //     accessTokenFactory: function() {
    //       if (accessToken) {
    //         const _accessToken = accessToken
    //         accessToken = null
    //         return _accessToken
    //       } else {
    //         return getConnectionInfo().then(function(info) {
    //           return info.accessToken
    //         })
    //       }
    //     }
    //   }

    //   _this.hubConnection = new signalR.HubConnectionBuilder()
    //     .withUrl(info.url, options)
    //     .build();
      
    //   _this.hubConnection.on('flightUpdated', _this.flightUpdated);
    //   _this.hubConnection
    //           .start()
    //           .then(() => {
    //             console.log("connection started");
    //           })
    //           .catch(e => {
    //             console.error(
    //               `An error occurred while connecting to operations hub. Details ${e}`
    //             );
    //           });

    // })
  },
  methods: {
    getFlights() {
      return axios
        .get(`${this.apiBaseUrl}/api/GetFlights`, { crossdomain: true })
        .then(function(resp) {
          return resp.data;
        })
        .catch(function() {
          return {};
        });
    },
    getConnectionInfo2() {
      return axios
        .get(`${this.apiBaseUrl}negotiate`, { crossdomain: true })
        .then(function(resp) {
          return resp.data;
        })
        .catch(function() {
          return {};
        });
    },
    flightUpdated(updatedFlight) {
      this.flights.push(updatedFlight);
    }
  }
};
</script>

<style lang="scss" scoped>
.container .row div:first-child {
  .card {
    background-color: blueviolet;
  }
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-active {
  opacity: 0;
}
</style>

<!-- Add "scoped" attribute to limit CSS to this component only -->
