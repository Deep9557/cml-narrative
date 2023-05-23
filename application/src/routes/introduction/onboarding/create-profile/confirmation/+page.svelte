<!--
 /src/routes/introduction/onboarding/create-profile/confirmation/+page.svelte
 +page.svelte
 cml-narrative
 
 Created by Ian Thompson on January 10th 2023
 icthomp@g.clemson.edu
 
 https://idealab.sites.clemson.edu
 
--->

<script lang="ts">

	
    import type { UserData } from "$lib/types/UserData";
	import { agentData, accessToken } from "$lib/utils/stores/store";
    import { goto } from "$app/navigation";
    import  axios from "axios";

    let agentName: string
    let data: any;

    agentData.subscribe(value => {
        agentName = value.agentName,
        data = value;
    })


    function confirmProfile () {
        data['password']='test'; //TBD: 
        axios.post('https://cml-backend-deep9557.vercel.app/api/auth/signup', {
            data
        }).then(res=>{
            console.log(res);
            if(res!=null && 200==res.request?.status) {
                //1. save access token received in response (in data.accessToken object in response)
                accessToken.set(res.data?.accessToken);
                //2/ redirect to other page.
                goto("/introduction/welcome?page=1")
            } else {
                console.log('something breaking..')
            }
        }).catch(err=>{
            console.error(err);
        });
        
	}

</script>

<div class="flex flex-col w-full h-full justify-center items-center space-y-10 bg-gray-50">
    <img src="/img/icons/profile.png" alt="" class="h-1/5">
    <h1 class="text-5xl text-lapiz-blue">Agent {agentName}</h1>
    <button  on:click={confirmProfile} class="bg-lapiz-blue text-white text-3xl px-7 py-3 rounded-md shadow hover:shadow-lg">CONFIRM</button>
</div>