<template>
  <div
    class="account-tabs-tab account-tab-info account-info"
    v-if="!loadingData || !profileInfo"
  >
    <h2 class="account-tab-title">
      <span>Account info</span>
    </h2>
    <div class="account-info__form">
      <div>
        <TextInputOutlined
          :inputValue="profileInfo.name"
          label="Name"
          :error="wasSubmitted && !profileInfo.name"
          :disabled="!editMode"
          @update="profileInfo.name = $event"
        />
        <TextInputOutlined
          :inputValue="profileInfo.surname"
          :error="wasSubmitted && !profileInfo.surname"
          label="Surname"
          :disabled="!editMode"
          @update="profileInfo.surname = $event"
        />
      </div>
      <div>
        <TextInputOutlined
          :inputValue="profileInfo.email"
          :disabled="!editMode"
          :error="wasSubmitted && !profileInfo.email"
          label="Email address"
          @update="profileInfo.email = $event"
        />
      </div>
      <div>
        <TextInputOutlined
          :inputValue="profileInfo.city"
          :disabled="!editMode"
          :error="wasSubmitted && !profileInfo.city"
          @update="profileInfo.city = $event"
          label="City"
        />
      </div>
      <SwitchInput v-if="editMode" />
      <button v-if="editMode" @click="saveChanges">Save changes</button>
    </div>
  </div>
  <MiddleLoader v-else />
</template>

<script>
import CitySelect from "@/components/other/CitySelect";
import MiddleLoader from "@/components/Loaders/MiddleLoader";
import SwitchInput from "@/components/other/SwitchInput";
import TextInputOutlined from "@/components/other/TextInputOutlined";
export default {
  name: "Account",
  layout: "account",
  components: { CitySelect, MiddleLoader, SwitchInput, TextInputOutlined },
  data() {
    return {
      loadingData: false,
      profileInfo: this.$store.state.auth.userData,
      wasSubmitted: false
    };
  },
  async mounted() {
    await this.$store.dispatch("auth/setUserData");
  },
  methods: {
    async saveChanges() {
      this.wasSubmitted = true;
      if (
        this.profileInfo.email &&
        this.profileInfo.name &&
        this.profileInfo.surname &&
        this.profileInfo.city
      ) {
        this.loadingData = true;
        await this.$store.dispatch("auth/editProfileNameSurnameEmail", {
          name: this.profileInfo.name,
          surname: this.profileInfo.surname,
          email: this.profileInfo.email,
          city: this.profileInfo.city
        });
        await this.$store.dispatch("auth/toggleEdit", false);
        this.loadingData = false;
      }
    }
  },
  computed: {
    userData() {
      this.profileInfo = this.$store.state.auth.userData;
      return this.$store.state.auth.userData;
    },
    editMode() {
      return this.$store.state.auth.editMode;
    }
  },
  beforeDestroy() {
    this.$store.dispatch("auth/toggleEdit", false);
  }
};
</script>
