<template>

  <main class="card-details">

    <div v-show="!revert" class="card-details-figure">
      <div class="card-details-figure-front-bind-infos">
        <img class="card-details-figure-front-bind-infos-image" src="./assets/bg-card-front.png" alt="">
        <div class="card-details-figure-front-bind-infos-group">
          <img src="./assets/card-logo.svg" alt="">
          <p class="card-details-figure-front-bind-infos-group-card-number">{{state.cardNumber}}</p>
          <p class="card-details-figure-front-bind-infos-group-month">{{state.month}} /</p>
          <p class="card-details-figure-front-bind-infos-group-year">{{state.year}}</p>
          <p class="card-details-figure-front-bind-infos-group-cardholder-name"> {{state.cardholderName}}</p>
        </div>
      </div>
      <div class="card-details-figure-back-bind-infos">
        <img class="card-details-figure-back-bind-infos-image" src="./assets/bg-card-back.png" alt="">
        <p class="card-details-figure-back-bind-infos-cvc">{{state.cvc}}</p>
      </div>
    </div>

    <div v-show="revert" class="card-details-figure revert">
      <div class="card-details-figure-front-bind-infos">
        <img class="card-details-figure-front-bind-infos-image" src="./assets/bg-card-front.png" alt="">
        <div class="card-details-figure-front-bind-infos-group">
          <img src="./assets/card-logo.svg" alt="">
          <p class="card-details-figure-front-bind-infos-group-card-number">{{state.cardNumber}}</p>
          <p class="card-details-figure-front-bind-infos-group-month">{{state.month}} /</p>
          <p class="card-details-figure-front-bind-infos-group-year">{{state.year}}</p>
          <p class="card-details-figure-front-bind-infos-group-cardholder-name"> {{state.cardholderName}}</p>
        </div>
      </div>
      <div class="card-details-figure-back-bind-infos">
        <img class="card-details-figure-back-bind-infos-image" src="./assets/bg-card-back.png" alt="">
        <p class="card-details-figure-back-bind-infos-cvc">{{state.cvc}}</p>
      </div>
    </div>

    <form v-if="!validatedData" class="card-details-data" novalidate @submit.prevent="validateForm()">

      <div class="form-group front-card" :class="{ error: v$.cardholderName.$errors.length }">
        <label for="">nome do titular</label>
        <input type="text" placeholder="e.g. Jane Appleseed" maxlength="25" v-model="state.cardholderName">
        <div v-if="v$.cardholderName.$error" class="error-msg">Can't be blank</div>
      </div>

      <div class="form-group front-card" :class="{ error: v$.cardholderName.$errors.length }">
        <label for="">número do cartão</label>
        <input type="text" placeholder="e.g. 1234 5678 9123 0000" v-model="state.cardNumber" v-mask="'#### #### #### ####'" masked="true">
        <div v-if="v$.cardNumber.$error" class="error-msg">Can't be blank</div>
      </div>


      <div class="row">
        <div class="form-group-date front-card" :class="{ error: v$.cardholderName.$errors.length }" >
          <label for="">Exp. Date (MM/YY)</label>
          <div class="form-group-date-input">
            <input type="text" max="30" placeholder="MM" v-model="state.month" v-mask="'##'" masked="true">
            <input type="text" placeholder="YY" v-model="state.year" v-mask="'##'" masked="true">
          </div>
          <div v-if="v$.cardNumber.$error" class="error-msg">Can't be blank</div>
        </div>
  
        <div class="form-group" :class="{ error: v$.cardholderName.$errors.length }">
          <label for="">cvc</label>
          <input type="text" placeholder="e.g. 123" v-model="state.cvc" v-mask="'###'" masked="true" @click="toggleBackSideCard">
          <div v-if="v$.cvc.$error" class="error-msg">Can't be blank</div>
        </div>
      </div>

      <input id="data-confirm" class="default-button-styled" type="submit" value="Confirmar">

    </form>
      
    <div v-if="validatedData" class="card-details-gratification">
      <img src="./assets/icon-complete.svg" alt="">
      <div class="card-details-gratification-text-group">
        <h3>Thank you!</h3>
        <p>We've added your card details</p>
      </div>
      <button class="default-button-styled" @click="toggleValidatedData()" >Continue</button>
    </div>

  </main>
</template>

<script>
import { reactive, watchEffect, nextTick, ref } from 'vue';
import useVuelidate from '@vuelidate/core'
import { required } from '@vuelidate/validators'

export default {
  name: 'App',
  components: {
  },
  setup(){
    const validatedData = ref(false);
    const revert = ref(false)
    let cardFigureBackElement = '';
    let cardFigureBackElementRevert = '';
    let cardFigureFrontElement = '';
    let cardFigureFrontElementRevert = '';
    let cardDetailsFigure = '';

    const state = reactive({
      cardholderName: null,
      cardNumber: null,
      month: null,
      year: null,
      cvc: null,
    })

    const rules = {
      cardholderName: { required }, // Matches state.cardholderName
      cardNumber: { required }, // Matches state.cardNumber
      month: { required },
      year: { required },
      cvc: { required },
    }

    watchEffect(() => revert.value, (newValue) => console.log(newValue))

    function toggleBackSideCard(){
      cardFigureBackElement.classList.add("playing");
      cardFigureFrontElement.classList.add("playing");
      cardFigureBackElementRevert.classList.remove("revert");
      cardFigureFrontElementRevert.classList.remove("revert");

      
      setTimeout(function(){ 
        revert.value = true
      }, 1000);

    }

    function toggleFrontSideCard(){
      cardFigureFrontElementRevert.classList.add("revert");
      cardFigureBackElementRevert.classList.add("revert");
      cardFigureBackElement.classList.remove("playing");
      cardFigureFrontElement.classList.remove("playing");
      
      setTimeout(function(){ 
        revert.value = false
      }, 1000);
  
    }

    function toggleValidatedData(){
      return this.validatedData = !this.validatedData
    }

    async function validateForm(){
      const isFormCorrect = await this.v$.$validate();
      if(isFormCorrect){
        this.toggleValidatedData()
      }
    }

    nextTick(() =>{
      function getElementsFront(){
        const inputFrontCardElement = document.querySelectorAll('.front-card input');
        cardFigureBackElement = document.querySelector('.card-details-figure-back-bind-infos');
        cardFigureBackElementRevert = document.querySelector('.card-details-figure.revert .card-details-figure-back-bind-infos');
        cardFigureFrontElement = document.querySelector('.card-details-figure-front-bind-infos');
        cardFigureFrontElementRevert = document.querySelector('.card-details-figure.revert .card-details-figure-front-bind-infos');
        cardDetailsFigure = document.querySelector('card-details-figure');
  
        inputFrontCardElement.forEach(element => {
          element.addEventListener('click', (e) => {
            toggleFrontSideCard();
          })
        })
      }

      getElementsFront();
    });

    const v$ = useVuelidate(rules, state)

    return{
      validatedData,
      revert,
      toggleBackSideCard,
      toggleFrontSideCard,
      state,
      v$,
      validateForm,
      toggleValidatedData
    }
  },
}
</script>

<style src="./App.scss" lang="scss" />

