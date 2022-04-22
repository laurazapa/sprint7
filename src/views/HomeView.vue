<template>
  <div class="home container mb-5">
    <div class="row">
      <b-col cols="6">
        <h2>Services</h2>
        <div class="mb-3">
          <input type="text" placeholder="project name" v-model="projectName" class="m-1">
          <input type="text" placeholder="client name" v-model="clientName" class="m-1">
        </div>
        <div>
          <div class="mb-2">
            <input class="m-1" type="checkbox" name="web" id="webservice" @change="toggleService('web')" :checked="availableServices.filter( x => x.service == 'web')[0].selected">
            <label for="web">Web page (500€)</label>
            <PanelComponent v-if="availableServices.filter( x => x.service == 'web')[0].selected" @changeWebHome="changePriceWeb" />
          </div>
          <div class="mb-2">
            <input class="m-1" type="checkbox" name="seo" id="seoservice" @change="toggleService('seo')" :checked="availableServices.filter( x => x.service == 'seo')[0].selected">
            <label for="seo">SEO services (300€)</label>
          </div>
          <div class="mb-2">
            <input class="m-1" type="checkbox" name="ads" id="adsservice" @change="toggleService('ads')" :checked="availableServices.filter( x => x.service == 'ads')[0].selected">
            <label for="ads">Google Ads (200€)</label>
          </div>
          <h5 class="mt-3">Total price: {{ totalPrice }}</h5>
          <b-button @click="saveBudget" class="m-1">Save budget</b-button>
        </div>
      </b-col>
      <b-col cols="6">
        <BudgetsListComponent :budgetsList="savedBudgets" />
      </b-col>
    </div>
    <div>
      <router-link :to="{ name: 'welcome' }"><b-button class="m-1 bg-primary">Welcome page</b-button></router-link>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import PanelComponent from '@/components/PanelComponent.vue'
import BudgetsListComponent from '@/components/BudgetsListComponent.vue'

export default {
  name: 'HomeView',
  components: {
    PanelComponent,
    BudgetsListComponent
  },
  data(){
    return {
      availableServices: [
        {
          service: 'web',
          price: 500,
          selected: false
        },
        {
          service: 'seo',
          price: 300,
          selected: false
        },
        {
          service: 'ads',
          price: 200,
          selected: false
        }
      ],
      extraPriceWeb: 30,
      numPages: 0,
      numLanguages: 0,
      projectName: '',
      clientName: '',
      savedBudgets: []
    }
  },
  computed: {
    totalPrice(){
      let total = this.availableServices.filter( x => x.selected == true).map( x => x.price ).reduce( (x, y) => (x + y), 0);
      if(this.availableServices.filter( x => x.service == 'web')[0].selected){
        total += this.extraPriceWeb;
      }
      return total;
    }
  },
  methods: {
    toggleService(serviceSelected){
      this.availableServices.find( x => x.service == serviceSelected).selected = !this.availableServices.find( x => x.service == serviceSelected).selected;
    },
    changePriceWeb(pages, languages){
      this.numPages = pages;
      this.numLanguages = languages;
      this.extraPriceWeb = parseFloat(pages * languages * 30);
    },
    saveBudget(){

      if(this.clientName=='' || this.projectName== '' || this.availableServices.filter( x => x.selected).length == 0){
        alert('You must include the project name and the client name, as well as to select at least one service.');
        return;
      }

      //to add date formated ad yyyy-mm-dd hh:mm:ss
      function padTo2Digits(num) {
        return num.toString().padStart(2, '0');
      }

      function formatDate(date) {
        return (
          [
            date.getFullYear(),
            padTo2Digits(date.getMonth() + 1),
            padTo2Digits(date.getDate()),
          ].join('-') +
          ' ' +
          [
            padTo2Digits(date.getHours()),
            padTo2Digits(date.getMinutes()),
            padTo2Digits(date.getSeconds()),
          ].join(':')
        );
      }

      // save the budget in an object and push it to the savedBudgets array
      this.savedBudgets.push(
        {
          projectName: this.projectName,
          clientName: this.clientName,
          services: structuredClone(this.availableServices),
          numPages: this.numPages,
          numLanguages: this.numLanguages,
          totalPrice: this.totalPrice,
          date: formatDate(new Date())
        }
      );
      this.projectName= '';
      this.clientName= '';
      this.extraPriceWeb= 30;
      this.numPages= 0;
      this.numLanguages= 0;
      localStorage.setItem("numPages", JSON.stringify(1));
      localStorage.setItem("numLanguages", JSON.stringify(1));
      for(let service of this.availableServices){
        service.selected = false;
      }
    }
  },
  mounted() {
    this.projectName = JSON.parse(localStorage.getItem("projectName"));
    this.clientName = JSON.parse(localStorage.getItem("clientName"));
    if(localStorage.getItem("availableServices")){
      this.availableServices = JSON.parse(localStorage.getItem("availableServices"));
    };
    this.extraPriceWeb = JSON.parse(localStorage.getItem("extraPriceWeb"));
    if(localStorage.getItem("savedBudgets")){
      this.savedBudgets = JSON.parse(localStorage.getItem("savedBudgets"));
    };
  },
  watch: {
    projectName(newValue) {
      localStorage.setItem("projectName", JSON.stringify(newValue));
    },
    clientName(newValue) {
      localStorage.setItem("clientName", JSON.stringify(newValue));
    },
    availableServices: {
      handler(){
        localStorage.setItem("availableServices", JSON.stringify(this.availableServices));
      },
      deep: true
    },
    extraPriceWeb(newValue) {
      localStorage.setItem("extraPriceWeb", JSON.stringify(newValue));
    },
    savedBudgets: {
      handler(){
        localStorage.setItem("savedBudgets", JSON.stringify(this.savedBudgets));
      },
      deep: true
    },
  }
  
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  @media (max-width: 400px) {
    input{
      width: 150px;
    }
  }
</style>
