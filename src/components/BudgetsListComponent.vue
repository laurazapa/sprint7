<template>
  <div>
     <h2>Saved budgets</h2>
     <div>
      <div class="mb-3">
        <b-button @click="sortBudgets('name')" class="m-1">Sort by name</b-button>
        <b-button @click="sortBudgets('date')" class="m-1">Sort by date</b-button>
        <b-button @click="sortBudgets('reset')" class="m-1">Reset</b-button>
        <div class="mt-2">
          <input type="text" @keyup="filterBudgets" v-model="filterInput" placeholder="Search...">
        </div>
      </div>
      <div v-if="filterInput">
        <div v-for="(budget, index) of newArray" :key="index">
          <h3>{{ budget.projectName }}</h3>
          <p>Client: {{ budget.clientName }}</p>
          <p>Date: {{ budget.date }}</p>
          <h5>Selected services:</h5>
          <div>
            <div v-for="(service, index) of budget.services" :key="index">
              <div v-if="service.selected">
                - {{ service.service }}
                <span v-if="service.service=='web'">(pages: {{ budget.numPages }}; languages: {{ budget.numLanguages }})</span>
              </div>
            </div>
          </div>
          <p>Total price: {{ budget.totalPrice }}</p>
          <hr>
        </div>
      </div>
      <div v-else>
        <div v-for="(budget, index) of budgetsList" :key="index">
          <h3>{{ budget.projectName }}</h3>
          <p>Client: {{ budget.clientName }}</p>
          <p>Date: {{ budget.date }}</p>
          <h5>Selected services:</h5>
          <div>
            <div v-for="(service, index) of budget.services" :key="index">
              <div v-if="service.selected">
                - {{ service.service }}
                <span v-if="service.service=='web'">(pages: {{ budget.numPages }}; languages: {{ budget.numLanguages }})</span>
              </div>
            </div>
          </div>
          <p>Total price: {{ budget.totalPrice }}</p>
          <hr>
        </div>
      </div>
      
        
     </div>
  </div>
</template>

<script>
export default {
  name: 'BudgetsListComponent',
  data(){
    return {
      newArray: [],
      filterInput: ''
    }
  },
  props: ['budgetsList'],
  methods: {
    sortBudgets(type){
      if(type == 'name'){
        function compare(a, b) {
          if ( a.projectName < b.projectName ){
            return -1;
          }
          if ( a.projectName > b.projectName ){
            return 1;
          }
          return 0;
        }
        this.$props.budgetsList.sort( compare );
      }
      if(type == 'date'){
        function compare(a, b) {
          if ( a.date < b.date ){
            return 1;
          }
          if ( a.date > b.date ){
            return -1;
          }
          return 0;
        }
        this.$props.budgetsList.sort( compare );
      }
      if(type == 'reset'){
        function compare(a, b) {
          if ( a.date < b.date ){
            return -1;
          }
          if ( a.date > b.date ){
            return 1;
          }
          return 0;
        }
        this.$props.budgetsList.sort( compare );
      }
    },
    filterBudgets(){
      if(this.filterInput){
        this.newArray = this.$props.budgetsList.filter( (x) => x.projectName.includes(this.filterInput));
      }else{
        this.newArray = [];
      }
      // save new array filtered in localstorage
      localStorage.setItem("arrayFiltered", JSON.stringify(this.newArray));
      localStorage.setItem("filterInput", JSON.stringify(this.filterInput));
    }
  },
  mounted() {
    if(localStorage.getItem("arrayFiltered")){
      this.newArray = JSON.parse(localStorage.getItem("arrayFiltered"));
      this.filterInput = JSON.parse(localStorage.getItem("filterInput"));
    };
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h3{
    color: darkcyan;
  }
  @media (max-width: 400px) {
    input{
      width: 120px;
    }
  }
</style>
