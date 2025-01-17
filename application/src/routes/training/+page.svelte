<!--
 /src/routes/training/+page.svelte
 +page.svelte
 cml-narrative
 
 Created by Ian Thompson on January 16th 2023
 icthomp@g.clemson.edu
 
 https://idealab.sites.clemson.edu
 
--->

<script lang="ts">
	import DialogBox from "$lib/components/dialog/DialogBox.svelte";
	import SlimDialogBox from "$lib/components/dialog/SlimDialogBox.svelte";
    import ProjectorView from "$lib/components/scene/ProjectorView.svelte";
    import { fade, fly } from 'svelte/transition';

	import { goto } from "$app/navigation";
    import Scene from "$lib/components/scene/Scene.svelte";
	import { NavigationDirection } from "$lib/types/Enums";
	import type { Line } from "$lib/types/Script";
	import Technology from "$lib/components/sequences/training/Technology.svelte";
	import Training2 from "$lib/components/sequences/training/Training2.svelte";
	import TrainingText from "$lib/components/sequences/training/TrainingText.svelte";

    /** @type {import('./$types').PageData} */
    export let data;

    let line: Line

    let shouldDarken: boolean = false

    /**
     * We declared the line variable above. This variable is "reactive" and will change on
     * each goto() call (implemented in handleDialogEvent()) as script data is returned 
     * from the "server".
     * 
     * Because the page doesn't "reload" like normal, (due to the way SvelteKit handles 
     * navigation and hydrates data on the page), when line data is changed, Svelte doesn't
     * know the data has changed to update the DOM. We tell Svelte using the $: syntax that
     * line is a reactive element and will change in the future. 
     * 
     * This solution was inspired by the following thread on StackOverflow:
     * https://stackoverflow.com/questions/74221733/sveltekit-call-load-function-in-page-server-when-params-change
    */
    $: (
        line = data.line
    )

    $: {
        if (line.id == 7 || line.id == 8 || line.id == 9) {
            shouldDarken = true
        } else {
            shouldDarken = false
        }
    }
    
    console.log("DATA: ", data);

    /**
    * Handles an emitted dialogEvent as sent from a DialogControl component and progresses the script as such
    * @param event can be destructured to obtain which way the dialog in a script should progress
    */
    const handleDialogEvent = async (event) => {
        var state: NavigationDirection = event.detail.state

        handleNavigation(state)
    }

    /**
     * Check the keycode that has been emitted from a Keydown Event on the Window to determine how we should navigate the user 
     * through the scene.
     * 
     * Event keys were found by using the following site below:
     * 
     * https://www.toptal.com/developers/keycode
     * 
     * @param event Keyboard Event emitted from  the Window 
     * 
     */
    const handleKeydownEvent = (event: KeyboardEvent) => {
        switch (event.key) {
            case "ArrowRight":
            case " ":
                handleNavigation(NavigationDirection.forward)
                break;
            case "ArrowLeft":
                handleNavigation(NavigationDirection.backward)
            default:
                break;
        }
    }

    /**
     * Determine the state of the DialogEvent that was emitted. Then, we will navigate
     * the user to the appropriate url with appropriate querystring which represents
     * which line in the script should be returned to the user.
    */
    const handleNavigation = (direction: NavigationDirection) => {
        if (direction == NavigationDirection.forward) {
            if (line.id == 5){
                goto("/activities/harmful-or-helpful")
            } else if (line.id == 11) { 
                goto("/activities/what-do-you-think-an-algorithm-is")
            } else if (line.id == 14) {
                goto("/activities/what-do-you-think-machine-learning-is")
            } else if (line.id == 19) {
                goto("/")
            } else {
                goto(`/training?page=${line.id + 1}`)

            }
        } else if (direction == NavigationDirection.backward) {
            if (line.id == 1) {
                goto(`/introduction/bot-buddy?page=24`)

            } else {
                goto(`/training?page=${line.id - 1}`)
            }
        }
    }

</script>

<svelte:window on:keydown|preventDefault={handleKeydownEvent} />

<Scene background="/img/backgrounds/Spark_Lab.jpg" darken={shouldDarken}>
    <div class={`w-full h-full ${shouldDarken ? "brightness-40" : ""}`} slot="content">
        {#if line.id <= 14}
            <ProjectorView>
                {#if line.id == 1 || line.id == 2}
                    <Technology />
                {:else if line.id == 3}
                    <Training2 />
                {:else if line.id == 4 || line.id == 5}
                    <TrainingText>
                        <p class="text-4xl">
                            These technologies can be helpful, but they can also be harmful to people. 
                        </p>
                        <p class="text-4xl">
                            You need to decide which technologies are helpful or harmful. 
                        </p>
                    </TrainingText>
                {:else if line.id == 6 || line.id == 10 || line.id == 11 || line.id == 12}
                    <TrainingText>
                        <p class="text-5xl text-center font-bold">
                            Algorithm
                        </p>
                    </TrainingText>
                {:else if line.id == 13 || line.id == 14}
                    <TrainingText>
                        <p class="text-5xl text-center font-bold">
                            Machine Learning
                        </p>
                    </TrainingText>
                {/if}
            </ProjectorView>
        {:else}
        <div class=""></div>
        {/if}
    </div>
    <div class="w-full" slot="dialog">
        {#if line.id != 3}
            <SlimDialogBox speaker={line.speaker} dialog={line.dialog} avatar={line.avatar} on:dialogEvent{handleDialogEvent}></SlimDialogBox>
        {/if}

    </div>
</Scene>