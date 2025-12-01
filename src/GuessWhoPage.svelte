<script lang="ts">
    import {DATA} from './data';

    // State
    let selectedCharacter: string | null = null;
    let hiddenCharacters: Set<string> = new Set();

    function handleCharacterClick(id: string) {
        if (!selectedCharacter) {
            selectedCharacter = id;
        } else {
            if (hiddenCharacters.has(id)) {
                hiddenCharacters.delete(id);
            } else {
                hiddenCharacters.add(id);
            }
            // Force Svelte to update
            hiddenCharacters = new Set(hiddenCharacters);
        }
    }
</script>

<div class="bg-neutral-950 flex flex-col items-center">
    {#if selectedCharacter}
        <div class="flex items-center gap-4 mb-6 p-4 bg-zinc-800 rounded-xl shadow max-w-md">
            <img class="w-16 h-16 rounded-lg bg-zinc-900" src={`/images/characters/${selectedCharacter}.png`}
                 alt={selectedCharacter}/>
            <div>
                <div class="text-zinc-300">Your character:</div>
                <div class="font-bold text-lg text-teal-400">
                    {DATA.find(c => c.id === selectedCharacter)?.name}
                </div>
            </div>
        </div>
    {:else}
        <div class="text-lg text-zinc-400 mb-6">Select your character</div>
    {/if}

    <div class="grid grid-cols-9 xl:grid-cols-[repeat(16,minmax(0,1fr))] mx-24 gap-4">
        {#each DATA.filter(v => !v.id.startsWith("traveler_")) as char}
            <button
                    type="button"
                    class="flex flex-col items-center rounded-lg overflow-hidden transition cursor-pointer bg-zinc-800 focus:outline-none
                    {selectedCharacter === char.id ? 'ring-4 ring-teal-400' : ''}"
                    on:click={() => handleCharacterClick(char.id)}
                    aria-pressed={hiddenCharacters.has(char.id)}
                    aria-label={char.name}
                    title={char.name}
            >
                <img class="transition w-full aspect-square object-contain bg-zinc-900 {hiddenCharacters.has(char.id) ? 'grayscale brightness-50 opacity-50' : ''}" src={`/images/characters/${char.id}.png`}
                     alt={char.name}/>
                <span class="text-xs text-zinc-200 mt-1 select-none transition {hiddenCharacters.has(char.id) ? 'grayscale brightness-50 opacity-50' : ''}">
                    {char.shortName || char.name}
                </span>
            </button>
        {/each}
    </div>
</div>
