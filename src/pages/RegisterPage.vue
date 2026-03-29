<script setup>

    import { watch, ref, onBeforeMount } from 'vue';
    import { Notyf } from 'notyf';
    import { useRouter } from 'vue-router';
    import { useGlobalStore } from '../stores/global.js';
    import api from '../api.js';


    const firstName = ref("");
    const lastName = ref("");
    const mobileNum = ref("");
    const email = ref("");
    const password = ref("");
    const confirmPass = ref("");
    const isEnabled = ref(false);

    const notyf = new Notyf();

    const router = useRouter()

    const {user} = useGlobalStore();

    
    watch([email,password,confirmPass,firstName,lastName,mobileNum], (currentValue, oldValue) => {
        
        if(currentValue.every(input => input !== "") && currentValue[1] === currentValue[2]){
            isEnabled.value = true
        } else {
            isEnabled.value = false
        }
    });

    async function handleSubmit(){

        

        try {
            
            await api.post('/users/checkEmail', {
                email: email.value
            })


            let response = await api.post('/users/register', {
                firstName: firstName.value,
                lastName: lastName.value,
                email: email.value,
                mobileNo: mobileNum.value,
                password: password.value
            })

            
            if(response.status === 201) {

                
                notyf.success(response.data.message);

                
                firstName.value = "";
                lastName.value = "";
                mobileNum.value = "";
                email.value = "";
                password.value = "";
                confirmPass.value = "";

                router.push({path: '/login'})

            } else {

                
                notyf.error("Registration Failed. Please contact administrator.");
            }
            

            
            
        } catch (e) {

            if(
                e.response.status === 404 || 
                e.response.status === 401 || 
                e.response.status === 400 ||
                e.response.status === 409
            ){
               
                notyf.error(e.response.data.message);
            } else {
                
                console.error(e);
                notyf.error("Registration Failed. Please contact administrator.");
            }
            
        }
    }

    
    onBeforeMount(() => {
        
        if(user.token){
            router.push({path: '/courses'})
        }
    })
</script>

<template>
  <div class="auth-layout">
    <!-- Left panel -->
    <div class="auth-panel-left d-none d-lg-flex">
      <div class="auth-panel-left__inner">
        <i class="bi bi-mortarboard-fill auth-panel-left__icon"></i>
        <h2 class="auth-panel-left__title">Join the Community</h2>
        <p class="auth-panel-left__sub">Create your account and start booking courses today.</p>
        <div class="auth-panel-left__links mt-4">
          <span class="text-white-50">Already have an account?</span>
          <router-link to="/login" class="auth-switch-link ms-2">Sign in</router-link>
        </div>
      </div>
    </div>

    <!-- Right panel / form -->
    <div class="auth-panel-right">
      <div class="auth-form-wrap">
        <div class="mb-4">
          <h1 class="auth-form-title">Create account</h1>
          <p class="auth-form-sub">Fill in your details to get started.</p>
        </div>

        <form @submit.prevent="handleSubmit">
          <div class="row g-3 mb-3">
            <div class="col-6">
              <label for="fName" class="form-label">First Name</label>
              <input type="text" class="form-control" id="fName" placeholder="John" v-model="firstName" />
            </div>
            <div class="col-6">
              <label for="lName" class="form-label">Last Name</label>
              <input type="text" class="form-control" id="lName" placeholder="Doe" v-model="lastName" />
            </div>
          </div>

          <div class="mb-3">
            <label for="mobile" class="form-label">Mobile Number</label>
            <div class="input-icon-wrap">
              <i class="bi bi-phone input-icon"></i>
              <input type="text" class="form-control ps-icon" id="mobile" placeholder="+63 9XX XXX XXXX" v-model="mobileNum" />
            </div>
          </div>

          <div class="mb-3">
            <label for="emailInput" class="form-label">Email address</label>
            <div class="input-icon-wrap">
              <i class="bi bi-envelope input-icon"></i>
              <input type="email" class="form-control ps-icon" id="emailInput" placeholder="you@example.com" v-model="email" />
            </div>
          </div>

          <div class="mb-3">
            <label for="passwordInput" class="form-label">Password</label>
            <div class="input-icon-wrap">
              <i class="bi bi-lock input-icon"></i>
              <input type="password" class="form-control ps-icon" id="passwordInput" placeholder="••••••••" v-model="password" />
            </div>
          </div>

          <div class="mb-4">
            <label for="cpasswordInput" class="form-label">Confirm Password</label>
            <div class="input-icon-wrap">
              <i class="bi bi-lock-fill input-icon"></i>
              <input type="password" class="form-control ps-icon" id="cpasswordInput" placeholder="••••••••" v-model="confirmPass" />
            </div>
          </div>

          <div class="d-grid">
            <button type="submit" class="btn btn-primary btn-auth" :disabled="!isEnabled">Create Account</button>
          </div>
        </form>

        <p class="auth-bottom-link d-lg-none mt-4 text-center">
          Already have an account? <router-link to="/login">Sign in</router-link>
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.auth-layout {
  min-height: calc(100vh - 60px);
  display: flex;
}

.auth-panel-left {
  width: 42%;
  background: linear-gradient(145deg, #1d4ed8 0%, #2563eb 60%, #3b82f6 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 3rem;
}

.auth-panel-left__inner {
  color: #fff;
  max-width: 320px;
}

.auth-panel-left__icon {
  font-size: 3rem;
  opacity: 0.9;
}

.auth-panel-left__title {
  font-size: 1.8rem;
  font-weight: 700;
  margin-top: 1rem;
  line-height: 1.2;
}

.auth-panel-left__sub {
  opacity: 0.75;
  margin-top: 0.5rem;
  font-size: 1rem;
}

.auth-switch-link {
  color: #fff;
  font-weight: 600;
  text-decoration: underline;
}

.auth-panel-right {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
  background: var(--app-bg);
}

.auth-form-wrap {
  width: 100%;
  max-width: 440px;
  background: var(--surface);
  border: 1px solid var(--border-soft);
  border-radius: var(--radius);
  box-shadow: var(--shadow-soft);
  padding: 2.5rem;
}

.auth-form-title {
  font-size: 1.6rem;
  font-weight: 700;
  color: var(--text-main);
}

.auth-form-sub {
  color: var(--text-muted);
  margin-top: 0.25rem;
}

.input-icon-wrap {
  position: relative;
}

.input-icon {
  position: absolute;
  left: 0.85rem;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-muted);
  pointer-events: none;
}

.ps-icon {
  padding-left: 2.4rem;
}

.btn-auth {
  padding: 0.65rem;
  font-size: 1rem;
}
</style>