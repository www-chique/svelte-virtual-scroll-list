<script>
    import {flip} from "svelte/animate"
    import VirtualScroll from "../src/VirtualScroll.svelte"
    import {createSequenceGenerator, randomInteger} from "./mock"
    import TestItem from "./TestItem.svelte"

    const getItemId = createSequenceGenerator()
    const getNotificationId = createSequenceGenerator()

    let items = []
    addItems(1000)

    let list
    let notifications = {}

    function addItems(count = 10) {
        let new_items = []
        for (let i = 0; i < count; i++)
            new_items.push({uniqueKey: getItemId(), height: randomInteger(20, 260)})
        items = [...items, ...new_items]
    }

    function addNotification(e) {
        const id = getNotificationId()
        notifications[id] = e
        setTimeout(() => {
            delete notifications[id]
            notifications = notifications
        }, 5000)
    }
</script>
<div class="vs">
    <VirtualScroll
            bind:this={list}
            data={items}
            key="uniqueKey"
            let:data
            on:bottom={() => addNotification("bottom")}
            on:top={() => addNotification("top")}
    >
        <div slot="header">
            This is a header set via slot
        </div>
        <TestItem {...data}/>
        <div slot="footer">
            This is a footer set via slot
        </div>
    </VirtualScroll>
</div>
<button on:click={() => list.scrollToOffset(0)}>To top</button>
<button on:click={list.scrollToBottom}>To bottom</button>
<div>
    {#each Object.entries(notifications) as [id, action] (id)}
        <div animate:flip>{action} </div>
    {/each}
</div>


<style>
    .vs {
        height: 300px;
    }

    .vs :global(.virtual-scroll-item) {
        padding: 4px 0;
    }
</style>
