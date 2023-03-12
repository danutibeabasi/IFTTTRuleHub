<template>
    <div class="card">
      <div class="pop-up-overlay" v-if="show">
        <div class="pop-up-form">
          <form class="p-3">
            <h6 class="mb-4">Add Rules</h6>
            <div class="form-group mb-4">
                <label for="condition-name" class="form-label">Condition Name</label>
                <input id="condition-name" type="text" class="form-control" v-model="rules.condition" required />
              </div>
            <div class="row">
              <div class="col-md-8 form-group mb-4">
                <label for="condition-name" class="form-label">Value</label>
                <input id="condition-name" type="number" class="form-control" v-model="rules.value" required />
              </div>
              <div class="col-md-4 form-group mb-4">
              <label for="condition-units" class="form-label">Units</label>
              <select id="condition-units" class="form-select" v-model="rules.units" required>
                <option value="">Select a unit</option>
                <option value="hour(s)">Hour</option>
                <option value="minute(s)">Minute</option>
                <option value="celsius">°C</option>
              </select>
            </div>
            </div>
            <div class="form-group mb-4">
            <label for="action" class="form-label">Action</label>
            <select id="action" class="form-select" v-model="rules.action" required>
                <option value="">Select an action</option>
                <option v-for="action in actions" :value="action.name" :key="action.id">{{ action.name }}</option>
            </select>
            </div>
            <div class="form-group mb-4">
            <label for="date" class="form-label">Date</label>
            <input id="date" type="date" class="form-control" v-model="rules.date" />
            </div>
            <div class="form-group mb-4">
            <label for="time" class="form-label">Time</label>
            <input id="time" type="time" class="form-control" v-model="rules.time" />
            </div>
            <div class="form-group mb-4">
              <div class="form-check">
                <input id="is-active" type="checkbox" class="form-check-input" v-model="rules.isActive" />
                <label for="is-active" class="form-check-label">Is Active</label>
              </div>
            </div>
            <div class="d-flex justify-content-end">
              <soft-button color="secondary" variant="gradient" class="me-2" @click="closeForm" formnovalidate>Cancel</soft-button>
                <soft-button variant="gradient" class="me-2" @click="addNewRule">Save</soft-button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </template>
  
  <style>
  .pop-up-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 9999;
  }
  
  .pop-up-form {
    background-color: white;
    padding: 20px;
    border-radius: 10px;
    max-width: 500px;
    width: 100%;
    position: relative;
  }
  
  .close {
    position: absolute;
    font-size: 25px;
    top: 10px;
    right: 10px;
    cursor: pointer;
  }
  </style>
  
  <script>
  import SoftButton from "@/components/SoftButton.vue";
  import axios from "axios";
  export default {
    name: "ConditionForm",
    props: {
      show: {
        type: Boolean,
        default: false,
      },
    },
    components: {
      SoftButton,
    },
    async mounted(){
    let result = await axios.get("http://localhost:3000/actions");
    console.log(result.data);
    this.actions = result.data;
  },
    data() {
      return {
        rules:{
            id: "",
            condition: "",
            action: "",
            time: "",
            date: "",
            value: "",
            units: "",
            isActive: "",
        }
        
      };
    },
    methods: {
      closeForm() {
          this.$emit('show-form', false);
      },
      async addNewRule() {
        if (this.rules.condition && this.rules.action && this.rules.time && this.rules.date && this.rules.value && this.rules.units) {
          try {
            let valueToSend = this.rules.value;
            let originalValue = this.rules.value;
            
            if (this.rules.units === 'hour(s)') {
              valueToSend = this.rules.value * 60; // Convert hours to minutes
            } else if (this.rules.units === 'celsius') {
              valueToSend = this.rules.value; // No conversion needed
            }

            if (!this.rules.isActive) {
              this.rules.isActive = false;
            }

            const result_2 = await axios.post("http://localhost:3000/rules", {
              condition: this.rules.condition,
              action: this.rules.action,
              time: this.rules.time,
              date: this.rules.date,
              value: valueToSend,
              units: this.rules.units,
              originalValue: originalValue,
              isActive: this.rules.isActive,
            });

            if (result_2.status === 201) {
              // Emit an event to the parent component to close the form
              this.$emit('show-form', false);
            }
          } catch (error) {
            // Handle errors
            console.log(error);
          }
        }
      }
    }
};
</script> 