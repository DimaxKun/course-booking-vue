<script setup>

	import { useGlobalStore } from '../stores/global';
    import { watch, ref, onBeforeMount } from 'vue';
    import { Notyf } from 'notyf';

    import api from '../api';

  	import { useRouter } from 'vue-router';

  	const router = useRouter()

	const {getUserDetails, user} = useGlobalStore();

    const email = ref("");
    const password = ref("");
    const isEnabled = ref(false);

    const notyf = new Notyf();

    watch([email,password], (currentValue, oldValue) => {

        if(currentValue.every(input => input !== "")){
            isEnabled.value = true
        } else {
            isEnabled.value = false
        }
    });

    
    async function handleSubmit(e){

        
        e.preventDefault();

        try {

            let res = await api.post('/users/login', {
                email: email.value,
                password: password.value
            })

            
            console.log(res)

            
            if(res.data.access){
                
                notyf.success("Login Successful");

                
                localStorage.setItem("token",res.data.access);

                
                getUserDetails(res.data.access);

                email.value = "";
		        password.value = "";

		        
				router.push({path: '/courses'})
			}
        } catch(e) {

           
            if(
                e.response?.status === 404 || 
                e.response?.status === 401 || 
                e.response?.status === 400
            ){
               
                notyf.error(e.response?.data?.message || "Login failed.");
            } else {
               
                console.error(e);
                notyf.error(e.response?.data?.message || "Login failed. Please try again.");
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
        <h2 class="auth-panel-left__title">Course Booking System</h2>
        <p class="auth-panel-left__sub">Opportunities for everyone, everywhere.</p>
        <div class="auth-panel-left__links mt-4">
          <span class="text-white-50">Don't have an account?</span>
          <router-link to="/register" class="auth-switch-link ms-2">Sign up</router-link>
        </div>
      </div>
    </div>

    <!-- Right panel / form -->
    <div class="auth-panel-right">
      <div class="auth-form-wrap">
        <div class="mb-4">
          <h1 class="auth-form-title">Welcome back</h1>
          <p class="auth-form-sub">Sign in to your account to continue.</p>
        </div>

        <form @submit="handleSubmit">
          <div class="mb-3">
            <label for="emailInput" class="form-label">Email address</label>
            <div class="input-icon-wrap">
              <i class="bi bi-envelope input-icon"></i>
              <input type="email" class="form-control ps-icon" id="emailInput" placeholder="you@example.com" v-model="email" />
            </div>
          </div>
          <div class="mb-4">
            <div class="d-flex justify-content-between">
              <label for="passwordInput" class="form-label">Password</label>
              <router-link to="/reset-password" class="auth-forgot">Forgot password?</router-link>
            </div>
            <div class="input-icon-wrap">
              <i class="bi bi-lock input-icon"></i>
              <input type="password" class="form-control ps-icon" id="passwordInput" placeholder="••••••••" v-model="password" />
            </div>
          </div>

          <div class="d-grid mt-2">
            <button type="submit" class="btn btn-primary btn-auth" :disabled="!isEnabled">Login</button>
          </div>
        </form>

        <p class="auth-bottom-link d-lg-none mt-4 text-center">
          Don't have an account? <router-link to="/register">Sign up</router-link>
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
  max-width: 400px;
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

.auth-forgot {
  font-size: 0.85rem;
  color: var(--primary);
}

.btn-auth {
  padding: 0.65rem;
  font-size: 1rem;
}
</style>