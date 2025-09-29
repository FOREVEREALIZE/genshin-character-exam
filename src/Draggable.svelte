<script lang="ts">
    import {useDraggable} from "@dnd-kit-svelte/core";
    import {type Character, Element} from "./data";

    const { item, showAnswers = false, inDrawer = false, isCorrect = false }: { item: Character; showAnswers?: boolean; inDrawer?: boolean; isCorrect?: boolean } = $props();

    // (Unused color mapping kept if future element-based coloring desired)
    const COLORS: { [key: string]: string } = {
        [Element.Anemo]: "bg-teal-600",
        [Element.Geo]: "bg-yellow-400",
        [Element.Electro]: "bg-purple-800",
        [Element.Dendro]: "bg-lime-600",
        [Element.Hydro]: "bg-blue-800",
        [Element.Pyro]: "bg-red-800",
        [Element.Cryo]: "bg-cyan-600",
    };

    const {transform, listeners, attributes, node, isDragging} = useDraggable({id: item.id});

    let style = $state('contain: layout paint style;');
    $effect(() => {
        if (transform.current) {
            const {x, y} = transform.current;
            style = `transform: translate3d(${x}px, ${y}px, 0); will-change: transform; z-index: 50; contain: layout paint style;`;
        } else {
            style = 'contain: layout paint style;';
        }
    });

    let answerClass = $state('');
    $effect(() => {
        if (!showAnswers) {
            answerClass = 'bg-neutral-800';
        } else if (inDrawer) {
            answerClass = 'bg-neutral-700 grayscale';
        } else {
            answerClass = isCorrect ? 'bg-green-700' : 'bg-red-700';
        }
    });
</script>

<div
        class={`flex flex-col items-center aspect-square justify-center relative rounded-lg overflow-clip cursor-grab active:cursor-grabbing shadow ${answerClass} ${isDragging.current ? 'opacity-70 scale-105' : 'hover:scale-110 hover:rotate-2 transition-transform duration-150'}`}
        {style}
        bind:this={node.current}
        {...listeners.current}
        {...attributes.current}
>
    <img src={`/images/characters/${item.icon || item.id}.png`} alt={item.name}
         class="w-full h-full pointer-events-none select-none object-cover" draggable={false} loading="lazy" decoding="async" />
    <span class="text-white absolute bottom-1 left-1 right-1 bg-neutral-900/90 text-center px-1 py-0.5 rounded-md text-[10px] leading-tight pointer-events-none select-none">
        {item.shortName || item.name}
    </span>
</div>
