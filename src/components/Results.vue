<template >
  <ion-card v-for="key in Object.keys(this.info)" :key="key">
      <ion-card-header>
        <ion-card-subtitle>{{ value }} {{key }}</ion-card-subtitle>
        <ion-card-title>{{ (info[key]*value).toFixed(2) }} €</ion-card-title>
      </ion-card-header>
    </ion-card>
</template>

<script>
import { IonCard, IonCardHeader, IonCardSubtitle, IonCardTitle } from '@ionic/vue';

export default {
  name : 'Results',

  data(){
    return{
      info : {}
    }
  },

  props : {
    data : Object,
    value : Number
  },

  components : {
    IonCard, 
    IonCardHeader, 
    IonCardSubtitle, 
    IonCardTitle,
  },

  updated(){
    this.info = this.data.rates
  }
}
</script>