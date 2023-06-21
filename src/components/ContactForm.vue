<template>
  <v-form ref="form" v-model="valid" lazy-validation>
    <v-card>
      <v-card-text>
        <v-text-field
          v-model="name"
          :rules="nameRules"
          label="Name"
          required
          clearable
        ></v-text-field>
        <v-text-field
          v-model="lastname"
          :rules="nameRules"
          label="Last Name"
          required
          clearable
        ></v-text-field>
        <v-text-field
          prepend-icon="mdi-email"
          v-model="email"
          :rules="emailRules"
          label="Contact e-mail"
          required
          clearable
        ></v-text-field>
        <v-autocomplete
          v-model="country"
          :items="countries"
          item-text="name"
          item-value="alpha2Code"
          :rules="[(v) => !!v || 'field is required']"
          label="Purpose of the trip"
          return-object
          required
          append-icon="mdi-airplane-search"
          hide-no-data
          :loading="loading"
          clearable
        ></v-autocomplete>
        <!-- Bagin of informations about the selected country -->
        <!-- End of informations about the selected country -->
        <v-checkbox
          v-model="checkbox"
          :rules="[(v) => !!v || 'You must agree to continue!']"
          label="I agree to the terms and conditions"
          required
          hide-details
        ></v-checkbox>
      </v-card-text>
      <v-card-actions>
        <v-btn :disabled="!valid" color="primary" @click="send" block>
          Send an inquiry
        </v-btn>
      </v-card-actions>
      <v-divider></v-divider>
      <v-btn color="info" small text @click="reset" block> reset </v-btn>
    </v-card>
  </v-form>
</template>

<script>
export default {
  name: "ContactForm",
  data: () => ({
    loading: false,
    valid: false,
    name: "",
    lastname: "",
    nameRules: [
      (v) => !!v || "Name is required",
      (v) => (v && v.length >= 3) || "Name must be more than 2 characters",
    ],
    email: "",
    emailRules: [
      (v) => !!v || "E-mail is required",
      (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
    ],
    country: null, //selected country
    countries: [], //list of available countries
    checkbox: false,
  }),
  async created() {
    this.loading = true;
    this.countries = await this.getCountries();
    this.loading = false;
  },
  methods: {
    async getCountries() {
      const countries = [];

      try {
        const {total_pages} = await (
            await fetch(`https://jsonmock.hackerrank.com/api/countries`)
        ).json();

        for (let i = 1; i <= total_pages; i++) {
          const resp = await (
              await fetch(
                  `https://jsonmock.hackerrank.com/api/countries?page=${i}`
              )
          ).json();
          countries.push(...resp.data);
        }
      } catch (e) {
        console.log(e);
        throw new Error("Something went wrong!");
      }

      return countries;
    },
    send() {
      this.valid = this.validation();
      if (this.valid) alert("The application has been sent!");
    },
    reset() {
      this.$refs.form.reset();
    },
    validation() {
      return this.$refs.form.validate();
    },
  },
};
</script>

