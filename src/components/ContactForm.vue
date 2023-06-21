<template>
  <v-form ref="form" v-model="valid" lazy-validation>
    <v-card>
      <v-card-text>
        <v-text-field
            v-model="name"
            :rules="nameRules"
            clearable
            label="Name"
            required
        ></v-text-field>
        <v-text-field
            v-model="lastname"
            :rules="nameRules"
            clearable
            label="Last Name"
            required
        ></v-text-field>
        <v-text-field
            v-model="email"
            :rules="emailRules"
            clearable
            label="Contact e-mail"
            prepend-icon="mdi-email"
            required
        ></v-text-field>
        <v-autocomplete
            v-model="country"
            :items="countries"
            :loading="loading"
            :rules="[(v) => !!v || 'field is required']"
            append-icon="mdi-airplane-search"
            clearable
            hide-no-data
            item-text="name"
            item-value="alpha2Code"
            label="Purpose of the trip"
            required
            return-object
        ></v-autocomplete>
        <!-- Bagin of informations about the selected country -->
        <v-card v-if="this.country">
          <v-container>
            <v-row no-gutters>
              <v-col>
                <v-sheet class="pa-2 ma-2">
                  <v-card-item v-if="this.country.capital">
                    <v-card-title>{{ this.country.capital }}</v-card-title>
                    <v-card-text>Capital</v-card-text>
                  </v-card-item>
                  <v-card-item v-else>
                    <v-card-title>No capital</v-card-title>
                    <v-card-text>Capital</v-card-text>
                  </v-card-item>
                </v-sheet>
              </v-col>
              <v-col>
                <v-sheet class="pa-2 ma-2"
                >
                  <v-card-item>
                    <v-card-title>{{ this.country.language[0] }}</v-card-title>
                    <v-card-text>Language</v-card-text>
                  </v-card-item>
                </v-sheet
                >
              </v-col>
            </v-row>
            <v-row no-gutters>
              <v-col>
                <v-sheet class="pa-2 ma-2">
                  <v-card-item>
                    <v-card-title>{{ this.country.population }}</v-card-title>
                    <v-card-text>Population</v-card-text>
                  </v-card-item>
                </v-sheet>
              </v-col>
              <v-col>
                <v-sheet class="pa-2 ma-2">
                  <v-card-item>
                    <v-card-title>{{ this.country.region }}</v-card-title>
                    <v-card-text>Region</v-card-text>
                  </v-card-item>
                </v-sheet
                >
              </v-col>
            </v-row>
          </v-container>
        </v-card>
        <!-- End of informations about the selected country -->
        <v-checkbox
            v-model="checkbox"
            :rules="[(v) => !!v || 'You must agree to continue!']"
            hide-details
            label="I agree to the terms and conditions"
            required
        ></v-checkbox>
      </v-card-text>
      <v-card-actions>
        <v-btn :disabled="!valid" color="primary" @click="send" block>
          Send an inquiry
        </v-btn>
      </v-card-actions>
      <v-divider></v-divider>
      <v-btn block color="info" small text @click="reset"> reset</v-btn>
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

