<script lang="ts">
    import {useDroppable, type UseDroppableArguments} from '@dnd-kit-svelte/core';
    import type {Snippet} from 'svelte';

    interface DroppableProps extends UseDroppableArguments {
        children: Snippet;
        class?: string;
        boxStyle?: string;
    }

    let {children, class: className, boxStyle, ...rest}: DroppableProps = $props();

    const droppable = useDroppable(rest);
</script>

<div class={[className]} style={boxStyle} bind:this={droppable.node.current}>
    {@render children()}
</div>
