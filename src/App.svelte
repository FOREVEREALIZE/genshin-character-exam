<script lang="ts">
    import GuessWhoPage from "./GuessWhoPage.svelte";
    import TablePage from "./TablePage.svelte";

    let currentPage: 'table' | 'guess_who' = 'table';
</script>

<div class="bg-neutral-950 min-h-screen">
    <nav class="w-full flex items-center justify-center gap-4 p-4 *:cursor-pointer">
        <button onclick={() => currentPage = 'table'} class="text-sm bg-blue-600 hover:bg-blue-500 active:bg-blue-700 transition-colors text-white px-3 py-1 rounded font-medium">
            Table match
        </button>
        <button onclick={() => currentPage = 'guess_who'} class="text-sm bg-green-600 hover:bg-green-500 active:bg-green-700 transition-colors text-white px-3 py-1 rounded font-medium">
            Guess Who
        </button>
    </nav>

    {#if currentPage === 'guess_who'}
        <GuessWhoPage />
    {:else if currentPage === 'table'}
        <TablePage />
    {/if}
</div>
