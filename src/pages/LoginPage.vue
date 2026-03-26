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
	<div class="container-fluid page-shell">
	    <h1 class="my-4 text-primary text-center section-title">Welcome!</h1>
      <p class="text-center section-subtitle mb-4">Login to continue booking your next course.</p>
	    <div class="row d-flex justify-content-center">
	        <div class="col-md-6 col-lg-5 auth-card mx-auto p-4 p-md-5">
	            <form v-on:submit="handleSubmit">
	                <div class="mb-3">
	                    <label for="emailInput" class="form-label">Email Address</label>
	                    <input type="email" class="form-control" id="emailInput" v-model="email" />
	                </div>
	                <div class="mb-3">
	                    <label for="passwordInput" class="form-label">Password</label>
	                    <input type="password" class="form-control" id="passwordInput" v-model="password" />
	                </div>
	                <div class="d-grid mt-5">
	                	<button type="submit" class="btn btn-primary btn-block"  v-if="isEnabled">Login</button>
                		<button type="submit" class="btn btn-danger btn-block" disabled v-else>Login</button>
	                </div>
	            </form>
	        </div>
	    </div>
    </div>
</template>