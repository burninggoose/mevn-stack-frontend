
<template>
  <div id="app">
    <AddClientWindow
      @toggleModal="toggleCreateClientModal"
      v-if="showCreateClientModal"
      @loadClients="loadClients"
    />
    <EditClientWindow
      @toggleModal="toggleEditClientModal"
      v-if="editClientModal.active"
      :client="editClientModal.client"
      @loadClients="loadClients"
    />
    <Alerts/>
    <div class="container">
      <div class="row">
        <div class="col text-left">
          <h1>Clients</h1>
        </div>
        <div class="col text-right">
          <button
            style="margin-top: 10px"
            class="btn btn-success align-bottom"
            @click="toggleCreateClientModal"
          >Add client</button>
        </div>
      </div>
      <div class="row">
        <div class="col-12">
          <table class="table">
            <thead>
              <tr>
                <td>Name</td>
                <td>Email</td>
                <td>Phone</td>
                <td>Providers</td>
                <td/>
                <td colspan="2">Actions</td>
              </tr>
              <tr/>
            </thead>
            <tbody>
              <ClientRow
                v-for="client in clients"
                :client="client"
                @loadClients="loadClients"
                @editClient="toggleEditClientModal"
                :key="client.id"
              />
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import ClientRow from "./components/ClientRow";
import AddClientWindow from "./components/AddClientWindow";
import EditClientWindow from "./components/EditClientWindow";
import Alerts from "./components/Alerts";

export default {
  components: { ClientRow, AddClientWindow, EditClientWindow, Alerts },
  name: "app",
  data() {
    return {
      clients: null,
      providers: null,
      showCreateClientModal: false,
      editClientModal: {
        active: false,
        client: null
      }
    };
  },
  methods: {
    loadClients: function() {
      axios.get("http://127.0.0.1:3000/client").then(response => {
        this.clients = response.data.clients;
        this.providers = response.data.providers;
        for (let i in this.clients) {
          for (let k in this.clients[i].providers) {
            for (let z in this.providers) {
              if (this.clients[i].providers[k].id == this.providers[z].id) {
                this.clients[i].providers[k] = this.providers[z];
              }
            }
          }
        }
      });
    },
    toggleEditClientModal: function(client) {
      this.editClientModal.client = client;
      this.editClientModal.active = !this.editClientModal.active;
    },
    toggleCreateClientModal: function() {
      this.showCreateClientModal = !this.showCreateClientModal;
    }
  },
  mounted() {
    this.loadClients();
  }
};
</script>

