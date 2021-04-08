<template>
  <AuthLayout>
    <AuthAreaTemplate class="signup">
      <template #header>{{locale.account_registration}}</template>
      <template #content>
        <form class="main-form" novalidate @submit.prevent="tryRegistrate">
          <InlineAlert class="alert error" v-if="error">{{locale.email_already_in_use}}</InlineAlert>

          <div class="double-inputs">
            <FormInput
              class="form-inp"
              name="first_name"
              :placeholder="locale.enter_first_name"
              :underlineError="true"
              :errors="validationErrors.signupForm.first_name"
              v-model="$v.signupForm.first_name.$model"
              @input="validate('signupForm', 'first_name')"
            >{{locale.first_name}}</FormInput>

            <FormInput
              class="form-inp"
              name="last_name"
              :placeholder="locale.enter_last_name"
              :underlineError="true"
              :errors="validationErrors.signupForm.last_name"
              v-model="$v.signupForm.last_name.$model"
              @input="validate('signupForm', 'last_name')"
            >{{locale.last_name}}</FormInput>
          </div>

          <FormInput
            class="form-inp"
            type="email"
            name="email"
            :placeholder="locale.enter_email"
            :underlineError="true"
            :errors="validationErrors.signupForm.email"
            v-model="$v.signupForm.email.$model"
            @input="validate('signupForm', 'email')"
          >{{locale.email}}</FormInput>

          <PasswordInput
            class="form-inp"
            name="password"
            :pwdHidden="pwdHidden"
            :placeholder="locale.enter_password"
            :underlineError="true"
            :errors="validationErrors.signupForm.password"
            v-model="$v.signupForm.password.$model"
            @input="validate('signupForm', 'password')"
            @pwd-visibility-changed="togglePwdHidden"
          >{{locale.password}}</PasswordInput>

          <PasswordInput
            class="form-inp"
            name="re_password"
            :pwdHidden="pwdHidden"
            :placeholder="locale.confirm_password"
            :underlineError="true"
            :errors="validationErrors.signupForm.re_password"
            v-model="$v.signupForm.re_password.$model"
            @input="validate('signupForm', 're_password')"
            @pwd-visibility-changed="togglePwdHidden"
          >{{locale.confirm_password}}</PasswordInput>

          <CountrySelect class="form-inp" v-model="signupForm.country">{{locale.choose_country}}</CountrySelect>

          <LocalizationSelect class="form-inp">{{locale.choose_language}}</LocalizationSelect>

          <FormButton class="signup-button">{{locale.sign_up}}</FormButton>
        </form>
        <GoogleAuthButton class="signup-button">{{locale.signup_with_google}}</GoogleAuthButton>
        <div class="login-prompt">
          {{locale.already_have_account}}
          <router-link class="smooth-link" :to="{name: 'login'}">{{locale.click_to_enter}}</router-link>
        </div>
      </template>
    </AuthAreaTemplate>
  </AuthLayout>
</template>

<script>
import { mapGetters, mapActions } from "vuex";

import AuthLayout from "@/components/auth/AuthLayout";
import AuthAreaTemplate from "@/components/auth/AuthAreaTemplate";
import FormInput from "@/components/common/inputs/FormInput";
import PasswordInput from "@/components/common/inputs/PasswordInput";
import CountrySelect from "@/components/common/selects/CountrySelect";
import LocalizationSelect from "@/components/common/selects/LocalizationSelect";
import FormButton from "@/components/common/buttons/FormButton";
import GoogleAuthButton from "@/components/common/buttons/GoogleAuthButton";
import InlineAlert from "@/components/common/InlineAlert";

export default {
  name: "SignUp",
  components: {
    AuthLayout,
    AuthAreaTemplate,
    FormInput,
    PasswordInput,
    CountrySelect,
    LocalizationSelect,
    FormButton,
    GoogleAuthButton,
    InlineAlert
  },
  validationSchemas: ["signupForm"],
  validations: {},
  data() {
    return {
      signupForm: {
        first_name: "",
        last_name: "",
        email: "",
        password: "",
        re_password: "",
        country: "gb"
      },
      error: false,
      pwdHidden: true
    };
  },
  computed: {
    ...mapGetters(["localization"])
  },
  methods: {
    ...mapActions(["registrateAccount"]),
    togglePwdHidden(val) {
      this.pwdHidden = val;
    },
    tryRegistrate() {
      if (!this.validate("signupForm")) return;

      let v = this;

      this.registrateAccount({
        email: this.signupForm.email,
        password: this.signupForm.password,
        first_name: this.signupForm.first_name,
        last_name: this.signupForm.last_name,
        country: this.signupForm.country,
        localization: this.localization
      })
        .then(res =>
          v.$router.push({
            name: "signup_confirmation",
            params: { state: "sended", email: v.signupForm.email }
          })
        )
        .catch(err => {
          v.signupForm.error = true;
          v.signupForm.email = "";
          v.signupForm.password = "";
          v.signupForm.re_password = "";
        });
    }
  }
};
</script>

<style lang="scss" scoped>
.signup {
  max-width: 450px;

  .alert {
    margin-bottom: 25px;
    width: 100%;
  }

  .main-form {
    width: 100%;

    .form-inp {
      margin-bottom: 30px;
    }

    > .form-inp {
      &:last-of-type {
        margin-bottom: 50px;
      }
    }
  }

  .double-inputs {
    display: flex;

    & > *:first-child {
      margin-right: 15px;
    }
  }

  .signup-button {
    width: 100%;
    margin-bottom: 15px;
  }

  .login-prompt {
    color: #2e2f31;
    font-size: 14px;
    font-weight: 500;
    text-align: center;
  }

  @media screen and (max-width: 1023px) {
    .double-inputs {
      display: block;

      & > *:first-child {
        margin-right: 0;
      }
    }
  }
}
</style>