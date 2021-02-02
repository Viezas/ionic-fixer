<template>
  <ion-title>Convertir :</ion-title>
    <p>- Selectionnez une ou plusieurs devises à convertir depuis l'euros (si aucune devise n'est fournie, le $ américain est la devise par defaut)</p>
    <p>- Entrez la valeur à convertir</p>
    <p>- Si vous souhaitez connaitre la valeur de la devise à une différente époque, sélectionnez une date valide</p>
    <ion-item>
      <ion-label>Devises :</ion-label>
      <ion-select multiple v-model="symbols" autofocus placeholder="Dollar">
          <ion-select-option v-for="key in Object.keys(symbolsArr)" :key="key" :value="key">{{ symbolsArr[key] }}</ion-select-option>
      </ion-select>
    </ion-item>

    <ion-item>
      <ion-label>Valeur<span>* </span>:</ion-label>
      <ion-input clear-input type='number' min="0" placeholder="Somme d'argent" v-model="value" autofocus></ion-input>
    </ion-item>

    <ion-item>
      <ion-label>Date :</ion-label>
      <ion-input clear-input type='date' v-model="date" autofocus></ion-input>
    </ion-item>
    <small>*champ obligatoire</small>
    <br>
    <ion-button type="submit" @click.prevent="convert">Rechercher</ion-button>
</template>

<script>
import { IonLabel, IonItem, IonInput, IonSelect, IonSelectOption, IonTitle, IonButton, toastController } from '@ionic/vue';

window.toastController = toastController;

async function toaster(message) {
  const toast = await toastController.create({
    color: 'danger',
    duration: 2000,
    message: message,
    showCloseButton: true
  });
  await toast.present()
  return false;
}

export default ({
  name: 'Search',
  data(){
    return{
      symbolsArr : {},
      symbols : [],
      value : 1,
      date : null,
    }
  },
  components : {
    IonLabel, 
    IonItem, 
    IonInput,
    IonSelect, 
    IonSelectOption,
    IonTitle,
    IonButton,
  },

  methods : {
    convert() {
      this.value = parseInt(this.value)
      if (typeof this.value !== "number") {
        this.value = 1
        toaster("Impossible de convertir une valeur non numérique ou égale à 0")
      }

      if (this.value <= 0) {
        this.value = 1
        toaster("Impossible de convertir une valeur égale à 0 ou négative")
      }

      if (this.symbols.length === 0) {
          this.symbols = ["USD"]
      }

      if (!this.date) {
        fetch(`http://data.fixer.io/api/latest?access_key=${process.env.VUE_APP_FIXER_API_KEY}&symbols=${this.symbols}`)
        .then(response => {
          response.json()
          .then(data => {
            this.$emit("transfer-data", data, this.value)
            this.value = 1
            this.symbols = []
          })
        })
        .catch(error =>{
            console.log(error.response)
        })
        return
      }
    
      fetch(`http://data.fixer.io/api/${this.date}?access_key=${process.env.VUE_APP_FIXER_API_KEY}&symbols=${this.symbols}`)
      .then(response => {
          response.json()
          .then(data => {
            if (data.success === false) {
              toaster("Impossible d'obtenir une conversion pour l'époque sélectionnée")
            }
            this.$emit("transfer-data", data, this.value)
            this.value = 1
            this.date = null
            this.symbols = []
          })
        })
        .catch(error =>{
            console.log(error.response)
        })

    },
  },

  mounted() {
    fetch(`http://data.fixer.io/api/symbols?access_key=${process.env.VUE_APP_FIXER_API_KEY}`)
    .then(response => {
      response.json()
      .then(data => {
        this.symbolsArr = data.symbols
      })
    })
    .catch(error =>{
        console.log(error.response)
    })
  }
});
</script>

<style scoped>
span, small{
  color: red;
}
ion-button{
  margin: 2vh 0 3vh;
}
</style>