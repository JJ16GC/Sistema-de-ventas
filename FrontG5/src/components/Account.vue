<template>
  <div id="Historial">
    <div class="container">
      <h2>
        Titular Cuenta:
        <span>{{ username }}</span>
      </h2>
      <h2>
        Saldo:
        <span>${{ accountByUsername.balance }} COP</span>
      </h2>
      <h2>
        Último Movimiento:
        <span>
          {{ accountByUsername.lastChange.substring(0, 10) }}
          {{ accountByUsername.lastChange.substring(11, 19) }}
        </span>
      </h2>
    </div>
    <h2>Transacciones</h2>
    <div class="container-table">
      <table>
        <tr>
          <th>Fecha</th>
          <th>Hora</th>
          <th>Origen</th>
          <th>Destino</th>
          <th>Valor</th>
        </tr>
        <tr v-for="transaction in transactionByUsername" :key="transaction.id">
          <td>{{ transaction.date.substring(0, 10) }}</td>
          <td>{{ transaction.date.substring(11, 19) }}</td>
          <td>{{ transaction.usernameOrigin }}</td>
          <td>{{ transaction.usernameDestiny }}</td>
          <td>${{ transaction.value }} COP</td>
        </tr>
      </table>
    </div>
      <h2>Ventas</h2>
    <div class="container-table">
      <table>
        <tr>
          <th>Fecha</th>
          <th>Hora</th>
          <th>Cantidad</th>
          <th>Precio total</th>
        </tr>
        <tr v-for="ventas in ventasByUsername" :key="ventas.id">
          <td>{{ ventas.lastChange.substring(0, 10) }}</td>
          <td>{{ ventas.lastChange.substring(11, 19) }}</td>
          <td>{{ ventas.cantidad }}</td>
          <td>${{ ventas.precio }} COP</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
import gql from "graphql-tag";

export default {
  name: "Account",

  data: function() {
    return {
      username: localStorage.getItem("username") || "none",
      transactionByUsername: [],
      ventasByUsername: [],
      accountByUsername: {
        balance: "",
        lastChange: "",
      },
    };
  },

  apollo: {
    transactionByUsername: {
      query: gql`
        query($username: String!) {
          transactionByUsername(username: $username) {
            id
            usernameOrigin
            usernameDestiny
            value
            date
          }
        }
      `,
      variables() {
        return {
          username: this.username,
        };
      },
    },

    ventasByUsername: {
      query: gql`
        query($username: String!) {
          ventasByUsername(username: $username) {
            id
            nombre
            cantidad
            precio
            lastChange
          }
        }
      `,
      variables() {
        return {
          username: this.username,
        };
      },
    },

    accountByUsername: {
      query: gql`
        query($username: String!) {
          accountByUsername(username: $username) {
            balance
            lastChange
          }
        }
      `,
      variables() {
        return {
          username: this.username,
        };
      },
    },
  },

  created: function() {
    this.$apollo.queries.transactionByUsername.refetch();
    this.$apollo.queries.ventasByUsername.refetch();
    this.$apollo.queries.accountByUsername.refetch();
  },
};
</script>

<style>
#Historial {
   height: 100%;
  width: 100%;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  flex-direction: column;
}
#Historial .container-table {
  
  height: auto;
  width: 50%;
  min-height: 200px;
  max-height: 250px;
  overflow-x: hidden;
}
#Historial table {
  width: 100%;
  border-collapse: collapse;
  border: 1px solid rgba (0, 0, 0, 0.3);
}
#Historial table td,
#Historial table th {
  border: 1px solid #ddd;
  padding: 8px;
}
#Historial table tr:nth-child(even) {
  background-color: #f2f2f2;
}
#Historial table tr:hover {
  background-color: #ddd;
}
#Historial table th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: crimson;
  color: white;
}
#Historial > h2 {
  color: #283747;
  font-size: 25px;
}
#Historial .container {
  padding: 30px;
  border: 3px solid rgba (0, 0, 0, 0.3);
  border-radius: 20px;
  margin: 5% 0 1% 0;
}
#Historial .container h2 {
  font-size: 25px;
  color: #283747;
}
#Historial .container span {
  color: crimson;
  font-weight: bold;
}
</style>