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
      apiBaseUrl: "http://localhost:7071",
      show: true,
      hubConnection: HubConnection,
      flights: [],
      options:{}
    };
  },
  created: function() {
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

    this.hubConnection.on('flightUpdated', this.flightUpdated);
 
    this.getConnectionInfo().then(function(info) {
      let accessToken = info.accessToken;
      const options = {
        accessTokenFactory: function() {
          if (accessToken) {
            const _accessToken = accessToken
            accessToken = null
            return _accessToken
          } else {
            return getConnectionInfo().then(function(info) {
              return info.accessToken
            })
          }
        } 
      }

    this.hubConnection = new signalR.HubConnectionBuilder()
       .withUrl(info.url, options, {
        withCredentials: true
      })
      .build();

     
      // startConnection(connection)
      
      this.hubConnection
            .start()
            .then(() => {
              console.log("connection started");
              // this.hubConnection.invoke(
              //   "broadcastMessage",
              //   "_SYSTEM_",
              //   this.username + " JOINED"
              // );
            })
            .catch(e => {
              console.error(
                `An error occurred while connecting to operations hub. Details ${e}`
              );
            });
      
    })
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
    getConnectionInfo() {
      return axios
        .get(`${this.apiBaseUrl}/api/SignalRInfo`, { crossdomain: true })
        .then(function(resp) {
          return resp.data;
        })
        .catch(function() {
          return {};
        });
    },
    flightUpdated(updatedFlight) {
      // const flight = data.flights.find(f => f.id === updatedFlight.id)
      // if (flight) {
      //   Vue.set(flight, 'from', updatedFlight.from)
      //   Vue.set(flight, 'to', updatedFlight.to)
      //   Vue.set(flight, 'price', updatedFlight.price)
      // } else {
      this.flights.push(updatedFlight);
    }
    // onConnected: function(connection) {
    //   console.log("connection started");
    //   connection.send("broadcastMessage", "_SYSTEM_", this.username + "JOINED");
    // }
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
