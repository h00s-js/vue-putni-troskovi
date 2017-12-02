<template>
  <div id="app" class="container">
    <div id="options" class="box rounded mb-3">
      <div class="row mb-3">
        <div class="col-4 col-md-4">
          Prezime i ime:<br>
          <input type="text" v-model="name"><br>
        </div>

        <div class="col-4 col-md-4">
          Adresa prebivališta:<br>
          <input type="text" v-model="address"><br>
        </div>

        <div class="col-4 col-md-4">
        </div>
      </div>

      <div class="row mb-3">
        <div class="col-4 col-md-4">
          Udaljenost pri dolasku (km):<br>
          <input type="text" v-model="distanceIn"><br>
        </div>

        <div class="col-4 col-md-4">
          Udaljenost pri odlasku (km):<br>
          <input type="text" v-model="distanceOut"><br>
        </div>

        <div class="col-4 col-md-4">
        </div>
      </div>

      <div class="row mb-3">
        <div class="col-4 col-md-4">
          Mjesec:<br>
          <input type="text" v-model="month"><br>
        </div>

        <div class="col-4 col-md-4">
          Godina:<br>
          <input type="text" v-model="year"><br>
        </div>

        <div class="col-4 col-md-4">
          Preskoči dane:<br>
          <input type="text" v-model="skipDays"><br>
        </div>
      </div>

      Datum podnošenja:<br>
      <input type="text" v-model="date"><br><br>
      <button class="btn btn-primary" @click="calculateFillTable">Generiraj!</button>
      <hr>
    </div>
    <div id="generated-form">
      <div class="centered">
        <h6 class="mb-0"><b>IZVJEŠĆE O PRIJEĐENOJ UDALJENOSTI PRI DOLASKU NA POSAO<br>
        I ODLASKU S POSLA za mjesec {{ month }}/{{ year }}</b></h6>
        <small>(stavak 12. čl. 67. TKU za službenike i namještenike u javnim službama)</small>
      </div>

      <p class="mt-4">
        Prezime i ime zaposlenika: <strong>{{ name }}</strong><br>
        Adresa prebivališta-boravišta: {{ address }}<br>
      </p>

      <table>
        <thead>
          <tr>
            <th></th>
            <th>Datum</th>
            <th>Broj KM<br>pri dolasku</th>
            <th>Broj KM<br> pri odlasku</th>
            <th>Prijevozno sredstvo</th>
            <th>Potpis</th>
          </tr>
        </thead>
        <tfoot>
          <tr>
            <td></td>
            <td><strong>Ukupno KM:</strong></td>
            <td>{{ totalDistanceIn.toFixed(2) }}</td>
            <td>{{ totalDistanceOut.toFixed(2) }}</td>
            <td><b>{{ (totalDistanceIn + totalDistanceOut).toFixed(2) }} km x 0,75</b></td>
            <td><b>= {{ this.cost }} kn</b></td>
          </tr>
          <tr>
            <td></td>
            <td><strong>Ukupno<br>DANA rada:</strong></td>
            <td>{{ workingDays }}</td>
            <td>{{ workingDays }}</td>
            <td></td>
            <td></td>
          </tr>
        </tfoot>
        <tbody v-html="table">
        </tbody>
      </table>

      <p class="mt-3">Za točnost i istinitost podataka iz ovog izvješća zaposlenik jamči pod punom krivičnom i materijalnom odgovornošću.</p>

      <p>Datum podnošenja izvješća: {{ date }}</p>

      <p class="right">Potpis zaposlenika:</p>
      <p></p>
      <p class="right">_________________</p>

    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      name: '',
      address: '',
      distanceIn: 0,
      distanceOut: 0,
      totalDistanceIn: 0,
      totalDistanceOut: 0,
      workingDays: 0,
      date: this.getDate(),
      month: this.getMonth(),
      year: this.getYear(),
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
      this.totalDistanceIn = 0;
      this.totalDistanceOut = 0;
      let noOfDays = new Date(this.year, this.month, 0).getDate();
      let skipDays = JSON.parse('[' + this.skipDays + ']');
      let workingDays = 1;
      for (let i = 1; i <= noOfDays; i++) {
        let day = new Date(this.year, this.month+1, i).getDay();
        if (day != 0 && day != 6 && !skipDays.includes(i)) {
          this.table += '<tr><td>' + workingDays + '</td><td>' + i +  '.' + this.month + '.' + this.year + '</td>';
          this.table += '<td>' + this.distanceIn + '</td>';
          this.table += '<td>' + this.distanceOut + '</td>';
          this.table += '<td>automobil</td><td></td></tr>';
          this.totalDistanceIn += parseFloat(this.distanceIn);
          this.totalDistanceOut += parseFloat(this.distanceOut);
          workingDays++;
        }
      }
      this.cost = ((this.totalDistanceIn + this.totalDistanceOut) * 0.75).toFixed(2);
      this.workingDays = workingDays - 1;
    }
  }
}
</script>

<style>
@import './assets/css/bootstrap.min.css';
@import './assets/css/style.css';
</style>
