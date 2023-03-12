<template>
  <div class="py-4 container-fluid">
    <div class="row">
      <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4">
        <mini-statistics-card
          title="Humidity"
          value="77.2%RH"
          :icon="{
            component: 'fa fa-tint',
            background: iconBackground,
          }"
          direction-reverse
        />
      </div>
      <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4">
        <mini-statistics-card
          title="Temperature"
          value="20°C"
          :icon="{
            component: ' fa fa-thermometer-empty',
            background: iconBackground,
          }"
          direction-reverse
        />
      </div>
      <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4">
        <mini-statistics-card
          title="CO2"
          value="2172 PPM"
          :icon="{
            component: 'fa fa-cloud',
            background: iconBackground,
          }"
          direction-reverse
        />
      </div>
      <div class="col-xl-3 col-sm-6 mb-xl-0">
        <mini-statistics-card
          title="Time"
          :value="formattedTime"
          :icon="{
            component: 'fa fa-clock-o',
            background: iconBackground,
          }"
          direction-reverse
        />
      </div>
    </div>

    <div class="row">
      <div class="col-12">
        <authors-table @show-form="showForm = $event" @action-form = "showForm2 = $event" @edit-form="showEditForm"/>
      </div>
      <pop-up-form :show="showForm" />
      <action-form :show="showForm2" />
      <edit-form :show="editForm.show" :rule="editForm.rule" />
    </div>
  </div>   
   
</template>
<script>
import MiniStatisticsCard from "@/examples/Cards/MiniStatisticsCard.vue";
import AuthorsTable from "./components/AuthorsTable";
import PopUpForm from "./components/PopUpForm";
import ActionForm from "./components/ActionForm.vue";
import EditForm from "./components/EditForm.vue";

export default {
  name: "dashboard-default",
  data() {
    return {
      editForm: {
        show: false,
        rule: null
      },
      iconBackground: "bg-gradient-success",  
      intervalId: null,
      showForm: false
    };
  },
  computed: {
    formattedTime() {
      const date = new Date()
      const hours = this.padZero(date.getHours())
      const minutes = this.padZero(date.getMinutes())
      const seconds = this.padZero(date.getSeconds())
      return `${hours}:${minutes}:${seconds}`
    },
  },
  methods: {
    showEditForm(show, rule) {
      this.editForm.show = show;
      this.editForm.rule = rule;
    },
    padZero(number) {
      return number.toString().padStart(2, '0')
    },
  },
  mounted() {
    // Update the time every second
    this.intervalId = setInterval(() => {
      this.$forceUpdate()
    }, 1000)
  },
  beforeUnmount() {
    clearInterval(this.intervalId)
  },
  components: {
    MiniStatisticsCard,
    AuthorsTable,
    PopUpForm,
    ActionForm,
    EditForm
  },
};
</script>
