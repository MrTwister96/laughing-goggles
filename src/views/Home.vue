<template>
  <div class="home mx-auto" style="width: 95%;">
    <v-row justify="center">
      <v-col cols="12" sm="12" md="6">
        <v-card>
          <v-card-title>Covid Opname</v-card-title>

          <v-card-text>
            <v-form ref="form" v-model="valid">
              <v-row>
                <v-col>
                  <v-text-field v-model="form.name" label="Naam"  :rules="rules.nameRules"></v-text-field>
                </v-col>
                <v-col>
                  <v-text-field v-model="form.surname" label="Van"  :rules="rules.surnameRules"></v-text-field>
                </v-col>
              </v-row>

              <v-row>
                <v-col>
                  <v-text-field v-model="form.cell" label="Cell No." :rules="rules.cellRules" type="number"></v-text-field>
                </v-col>
                <v-col>
                  <v-text-field v-model="form.temperature" label="Temperatuur"  :rules="rules.tempRules" type="number"></v-text-field>
                </v-col>
              </v-row>

              <v-row>
                <v-col cols="12" sm="12" md="6">
                  <v-radio-group v-model="form.question1"  label="Het U enige Covid simtome ?" :rules="rules.qRules">
                    <v-radio label="Nee" value="false"></v-radio>
                    <v-radio label="Ja" value="true"></v-radio>
                  </v-radio-group>
                </v-col>
                <v-col cols="12" sm="12" md="6">
                  <v-radio-group v-model="form.question2" label="Was U in aanraaking met enige iemand gekom wat Covid het ?" :rules="rules.qRules">
                    <v-radio label="Nee" value="false"></v-radio>
                    <v-radio label="Ja" value="true"></v-radio>
                  </v-radio-group>
                </v-col>
              </v-row>
            </v-form>
          </v-card-text>

          <v-card-actions>
            <v-btn color="success" @click="submit">
              Submit
            </v-btn>
          </v-card-actions>

        </v-card>
      </v-col>
    </v-row>
    

    <v-dialog
      v-model="success"
      width="170"
      persistent
    >
      <v-card height="190">
          <sweetalert-icon icon="success" class="pt-11"></sweetalert-icon>
      </v-card>
    </v-dialog>

    <v-dialog
      v-model="error"
      width="170"
      persistent
    >
      <v-card height="190">
          <sweetalert-icon icon="error" class="pt-11"></sweetalert-icon>
      </v-card>
    </v-dialog>

    <v-dialog
      v-model="loading"
      width="170"
      persistent
    >
      <v-card height="180">
          <sweetalert-icon icon="loading" class="pl-8 pt-12" ></sweetalert-icon>
      </v-card>
    </v-dialog>

  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios';
import SweetalertIcon from 'vue-sweetalert-icons';

export default {
  name: 'Home',
  components: { SweetalertIcon },
  data () {
    return {
      success: false,
      error: false,
      loading: false,
      valid: false,
      form: {
        name: "",
        surname: "",
        cell: "",
        question1: "",
        question2: "",
        temperature: ""
      },
      rules: {
        nameRules: [
          v => !!v || 'Naam is nodig',
          v => v.length <= 20 || 'Naam moet minder as 20 karakters bevat',
        ],
        surnameRules: [
          v => !!v || 'Van is nodig',
          v => v.length <= 20 || 'Van moet minder as 20 karakters bevat',
        ],
        cellRules: [
          v => !!v || 'Cell is nodig',
          v => v.length == 10 || 'Cell moet 10 karakters bevat',
        ],
        qRules: [
          v => !!v || 'Jy moet n opsie kies'
        ],
        tempRules: [
          v => !!v || 'Temperatuur is nodig',
          v => !isNaN(v) || 'Temperatuur moet n getal wees. bv 35.9 of 36',
        ]
        
      }
    }
  },
  watch: {
    success (val) {
      if (!val) return

      setTimeout(() => (this.success = false), 1500)
    },
    error (val) {
      if (!val) return

      setTimeout(() => (this.error = false), 1500)
    },
  },
  methods: {
    submit(){
      if(this.$refs.form.validate()) {
        this.loading = true

        axios.post('https://covidapi.ptype.app/entries/', {
          name: this.form.name,
          surname: this.form.surname,
          cell: this.form.cell,
          question1: this.form.question1,
          question2: this.form.question2,
          temperature: this.form.temperature,
        })
          .then(response => {
            if (response.data.status == true){
              this.loading = false
              this.success = true
              this.$refs.form.reset()
            } else {
              this.loading = false
              this.error = true
            }
          })
          .catch(e => {
            this.loading = false
            this.error = true
          })
      }
    }
  }
}
</script>
