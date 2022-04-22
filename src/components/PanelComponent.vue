<template>
  <div class="m-2 my-4 p-2 pt-4 border rounded shadow containerpanel">
    <div class="mb-3">
      <label>Number of pages: </label>
      <div class="mt-1 d-flex justify-content-center">
        <button @click="numPages--">-</button>
        <input type="number" min="1" name="numPages" v-model="numPages"><br>
        <button @click="numPages++">+</button>
        <button v-b-modal.modal-pages class="bg-info text-light rounded-circle ms-2">i</button>
      </div>
    </div>
    <div>
      <label>Number of languages: </label>
      <div class="mt-1 d-flex justify-content-center">
        <button @click="numLanguages--">-</button>
        <input type="number" min="1" name="numLanguages" v-model="numLanguages">
        <button @click="numLanguages++">+</button>
        <button v-b-modal.modal-languages class="bg-info text-light rounded-circle ms-2">i</button>
      </div>
    </div>
    <p class="small text-secondary m-2">* Minimum 1 page and 1 language (30€ extra)</p>
    <p>Price extras web: {{ extraWeb }}</p>

    <b-modal id="modal-pages" title="Pages information" hide-footer>
      <p class="my-4">Set here the number of pages of your website</p>
    </b-modal>
    <b-modal id="modal-languages" title="Languages information" hide-footer>
      <p class="my-4">Set here the number of languages of your website</p>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'PanelComponent',
  data(){
    return {
      numPages: 1,
      numLanguages: 1
    }
  },
  methods: {
    
  },
  computed: {
    extraWeb(){
      let extraWeb = this.numPages * this.numLanguages * 30;
      this.$emit('changeWebHome', this.numPages, this.numLanguages);
      return extraWeb;
    }
  },
  mounted() {
    this.numPages = JSON.parse(localStorage.getItem("numPages"));
    this.numLanguages = JSON.parse(localStorage.getItem("numLanguages"));
  },
  watch: {
    numPages: {
      handler(){
        if(this.numPages < 1) this.numPages = 1;
        localStorage.setItem("numPages", JSON.stringify(this.numPages));
      }
    },
    numLanguages: {
      handler(){
        if(this.numLanguages < 1) this.numLanguages = 1;
        localStorage.setItem("numLanguages", JSON.stringify(this.numLanguages));
      }
    }
  }
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  input{
    width: 50px;
  }
  @media (max-width: 400px) {
    input{
      width: 30px;
    }
  }
</style>
