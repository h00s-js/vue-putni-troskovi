<template>
  <div id="app">
    <div id="options">
      Ime:<br>
      <input type="text" v-model="name"><br>
      Adresa rada:<br>
      <input type="text" v-model="addressWork"><br>
      Adresa stanovanja:<br>
      <input type="text" v-model="addressHome"><br>
      Udaljenost:<br>
      <input type="text" v-model="distance"><br>
      Mjesec:<br>
      <input type="text" v-model="formMonth"><br>
      Godina:<br>
      <input type="text" v-model="formYear"><br>
      Preskoči dane:<br>
      <input type="text" v-model="skipDays"><br>
      Datum podnošenja:<br>
      <input type="text" v-model="date"><br><br>
      <button @click="calculateFillTable">Generiraj!</button>
      <hr>
    </div>
    <div id="generated-form">
      <div class="centered">
        <p>IZVJEŠĆE O PRIJEĐENOJ UDALJENOSTI<br>
        PRI DOLASKU NA POSAO I ODLASKU S POSLA</p>
      </div>

      <p>
        Ime i prezime zaposlenika: <strong>{{ name }}</strong><br>
        Adresa rada: {{ addressWork }}<br>
        Adresa stanovanja: {{ addressHome }}<br>
      </p>

      <table>
        <thead>
          <tr>
            <th>Datum</th>
            <th>Broj prijeđenih<br>kilometara pri dolasku</th>
            <th>Broj prijeđenih<br>kilometara pri odlasku</th>
            <th>Prijevozno sredstvo</th>
            <th>Potpis</th>
          </tr>
        </thead>
        <tfoot>
          <tr>
            <td><strong>UKUPNO:</strong></td>
            <td>{{ totalDistance.toFixed(2) }}</td>
            <td>{{ totalDistance.toFixed(2) }}</td>
            <td>{{ (totalDistance * 2).toFixed(2) }}</td>
            <td></td>
          </tr>
        </tfoot>
        <tbody v-html="table">
        </tbody>
      </table>

      <p>Za točnost i istinitost podataka iz ovog izvješća zaposlenik jamči potpisom pod punom krivičnom i materijalnom odgovornošću. Naknada po prijeđenom kilometru: 0,75 kn (stavak 6. ili st.8. čl. 67.)</p>
      <p><strong>Na ime naknade troška prijevoza potražujem: {{ cost }}kn</strong></p>
      <p>Datum podnošenja izvješćaa: {{ date }}</p>

      <p class="right">_________________<br>(potpis zaposlenika)</p>

      <p><strong>Odobreni iznos za isplatu _________________ kn</strong></p>

      <p class="right">____________________<br>(za Školu odobrava)</p>

    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      name: '',
      addressWork: '',
      addressHome: '',
      distance: 0,
      totalDistance: 0,
      date: this.getDate(),
      formMonth: this.getMonth(),
      formYear: this.getYear(),
      skipDays: '',
      cost: '',
      table: ''
    }
  },
  methods: {
    getDate() {
      let today = new Date();
      let dd = today.getDate();
      let mm = today.getMonth() + 1;
      let yyyy = today.getFullYear();

      if (dd < 10) {
          dd = '0'+dd
      } 

      if (mm<10) {
          mm = '0'+mm
      } 

      today = dd + '.' + mm + '.' + yyyy + '.';
      return today;      
    },
    getMonth() {
      let today = new Date();
      return today.getMonth() + 1;
    },
    getYear() {
      let today = new Date();
      return today.getFullYear();
    },
    calculateFillTable() {
      this.table = '';
      this.totalDistance = 0;
      let noOfDays = new Date(this.formYear, this.formMonth-1, 0).getDate();
      let skipDays = JSON.parse('[' + this.skipDays + ']');
      for (let i = 1; i <= noOfDays; i++) {
        let day = new Date(this.formYear, this.formMonth-1, i).getDay();
        if (day != 0 && day != 6 && !skipDays.includes(i)) {
          this.table += '<tr><td>' + i + '.' + this.formMonth + '.' + this.formYear + '</td>';
          this.table += '<td>' + this.distance + '</td>';
          this.table += '<td>' + this.distance + '</td>';
          this.table += '<td>automobil</td><td></td></tr>';
          this.totalDistance += parseFloat(this.distance);
        }
      }
      this.cost = (this.totalDistance * 2 * 0.75).toFixed(2);
    }
  }
}
</script>

<style>
@media print {
  #options {
    display: none;
  }
}

.centered {
  text-align: center;
}

.right {
  text-align: right;
}

table {
  border-collapse: collapse;
}

table, th, td {
  border: 1px solid black;
  padding: 0.05em 1em 0.05em 1em;
  text-align: center;
}

p {
  line-height: 1.2;
}
</style>
