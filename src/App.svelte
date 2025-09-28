<script lang="ts">
    import {
        DndContext,
        KeyboardSensor,
        MouseSensor,
        TouchSensor,
        type DragEndEvent,
        useSensor,
        useSensors
    } from "@dnd-kit-svelte/core";
    import {type Character, Weapon, DATA, Element} from "./data";
    import Droppable from "./Droppable.svelte";
    import Draggable from "./Draggable.svelte";

    const sensors = useSensors(useSensor(TouchSensor), useSensor(KeyboardSensor), useSensor(MouseSensor));

    // Arrays to iterate (explicit ordering)
    const ELEMENTS: Element[] = [Element.Anemo, Element.Geo, Element.Electro, Element.Dendro, Element.Hydro, Element.Pyro, Element.Cryo];
    const WEAPONS: Weapon[] = [Weapon.Sword, Weapon.Claymore, Weapon.Polearm, Weapon.Bow, Weapon.Catalyst];

    // Map character id -> location id (either 'drawer' or `${element}:${weapon}`)
    let location = $state<Record<string,string>>(Object.fromEntries(DATA.filter(v => !v.id.startsWith("traveler_")).map(c => [c.id, 'drawer'])));

    function cellId(element: Element, weapon: Weapon){
        return `${element}:${weapon}`;
    }

    function handleDragEnd(event: DragEndEvent){
        const {active, over} = event;
        if (!over) return; // dropped outside
        const overId = over.id as string;
        const character = DATA.find(c => c.id === active.id);
        if (!character) return;
        if (overId === 'drawer') {
            location[character.id] = 'drawer';
            location = {...location};
            return;
        }
        // (Optional rule enforcement could be re-enabled here.)
        location[character.id] = overId;
        location = {...location};
    }

    // Groups map (recomputed only when location changes via reactive effect)
    let groups = $state<Record<string, Character[]>>({});
    $effect(() => {
        const map: Record<string, Character[]> = Object.create(null);
        for (const c of DATA) {
            const loc = location[c.id];
            (map[loc] ||= []).push(c);
        }
        groups = map;
    });

    function itemsInDrawer(): Character[] {
        return groups['drawer'] || [];
    }

    function itemsInCell(element: Element, weapon: Weapon): Character[] {
        return groups[cellId(element, weapon)] || [];
    }

    const ELEMENT_LABEL: Record<Element,string> = {
        [Element.Anemo]: 'Anemo',
        [Element.Geo]: 'Geo',
        [Element.Electro]: 'Electro',
        [Element.Dendro]: 'Dendro',
        [Element.Hydro]: 'Hydro',
        [Element.Pyro]: 'Pyro',
        [Element.Cryo]: 'Cryo'
    };

    const WEAPON_LABEL: Record<Weapon,string> = {
        [Weapon.Sword]: 'Sword',
        [Weapon.Claymore]: 'Claymore',
        [Weapon.Polearm]: 'Polearm',
        [Weapon.Bow]: 'Bow',
        [Weapon.Catalyst]: 'Catalyst'
    };
</script>

<DndContext
        {sensors}
        onDragEnd={handleDragEnd}
>
    <main class="flex flex-col items-center justify-start w-full min-h-screen bg-neutral-950 gap-8 py-6">
        <!-- Drawer -->
        <section class="w-full max-w-5xl">
            <h2 class="text-white mb-2 font-semibold tracking-wide">Drawer (Unplaced Characters)</h2>
            <Droppable id="drawer" class="grid gap-2" boxStyle="grid-template-columns: repeat(auto-fill,minmax(72px,1fr)); min-height:90px; border:1px dashed #555; padding:8px; border-radius:8px;">
                {#each itemsInDrawer() as item (item.id)}
                    <Draggable item={item} />
                {/each}
            </Droppable>
        </section>

        <!-- Grid Table -->
        <div class="overflow-x-auto w-full max-w-6xl">
            <table class="text-white border-collapse">
                <thead>
                <tr>
                    <th class="p-2 text-left"></th>
                    {#each ELEMENTS as el}
                        <th class="px-4 py-2 text-center whitespace-nowrap border border-white/30">{ELEMENT_LABEL[el]}</th>
                    {/each}
                </tr>
                </thead>
                <tbody>
                {#each WEAPONS as weapon}
                    <tr>
                        <td class="px-4 py-2 font-medium border border-white/30 whitespace-nowrap">{WEAPON_LABEL[weapon]}</td>
                        {#each ELEMENTS as el}
                            <td class="align-top p-1 border border-white/30">
                                <Droppable id={cellId(el, weapon)} class="grid gap-1" boxStyle="grid-template-columns: repeat(auto-fill,minmax(56px,1fr)); min-width:120px; min-height:60px;">
                                    {#each itemsInCell(el, weapon) as item (item.id)}
                                        <Draggable item={item} />
                                    {/each}
                                </Droppable>
                            </td>
                        {/each}
                    </tr>
                {/each}
                </tbody>
            </table>
        </div>
    </main>
</DndContext>

<style>
    table { width: 100%; }
</style>
