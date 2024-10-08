<script lang="ts">
    // Function imports
    import { findCurrentSessionIndex } from "$lib/functions/findCurrentSessionIndex";
    import { calculateOffset } from "$lib/functions/calculateOffset";
    import { afterUpdate } from "svelte";

    // Store imports
    import { currentSessionIndex } from "$lib/stores/currentSessionIndex";

    export let nextEventSessions: { [key: string]: string };

    let sessionListEl: HTMLElement;

    const decreaseSessionIndex = () => {
        if ($currentSessionIndex > 0) {
            currentSessionIndex.update((index) => index - 1);
        }
    }

    const increaseSessionIndex = () => {
        if ($currentSessionIndex < Object.keys(nextEventSessions).length - 1) {
            currentSessionIndex.update((index) => index + 1);
        }
    }

    $: if (nextEventSessions) {
        const index = findCurrentSessionIndex(nextEventSessions);
        currentSessionIndex.set(index);
    }

    afterUpdate(() => {
        const offset = calculateOffset($currentSessionIndex, sessionListEl);
        sessionListEl.style.transform = `translateX(${offset}px)`;
    })
</script>

<style>
    .session-selection {
        margin: 10px 0;

        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: center;

        font-size: 24px;

        position: relative;
        overflow: hidden;
    }

    .all-sessions {
        transition: transform 0.3s ease;
        width: 280px;

        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: center;

        transform: translateX(0px);
    }

    .session-selection:after {
        position: absolute;
        bottom: 0;
        left: 0;
        height: 100%;
        width: 100%;
        content: "";
        background-image: linear-gradient(90deg, #111 15%, transparent 25%, transparent 75%, #111 85%);
    }

    .session.selected {
        color: var(--main-text-color);
        font-weight: bold;
    }

    .session {
        margin: 0 10px;
        color: var(--secondary-text-color);
    }

    @media only screen and (max-width: 768px) {
        .session-selection {
            font-size: 18px;
        }

        .all-sessions {
            width: 200px;
        }
    }
</style>

<div class="session-selection" data-nosnippet>
    <button on:click={decreaseSessionIndex} aria-label="Decrease Session Index">
        ←
    </button>
    <div class="all-sessions" bind:this={sessionListEl}>
        {#each Object.keys(nextEventSessions) as sessionName, sessionIndex}
            <span class="session" class:selected={sessionIndex === $currentSessionIndex}>{sessionName.toUpperCase()}</span>
        {/each}
    </div>
    <button on:click={increaseSessionIndex} aria-label="Increase Session Index">
        →
    </button>
</div>
