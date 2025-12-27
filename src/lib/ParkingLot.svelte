<script>
    import ParkingSlot from "./ParkingSlot.svelte";

    let { spots, selectedSpotId, onSelect } = $props();
</script>

<div class="parking-lot">
    <div class="row">
        {#each spots.slice(0, 6) as spot}
            <ParkingSlot
                id={spot.id}
                status={spot.status}
                isSelected={selectedSpotId === spot.id}
                onClick={() => onSelect(spot.id)}
            />
        {/each}
    </div>

    <div class="road">
        <div class="lane-marker"></div>
        <span class="road-label">ENTRY</span>
        <div class="lane-marker"></div>
    </div>

    <div class="row">
        {#each spots.slice(6, 12) as spot}
            <ParkingSlot
                id={spot.id}
                status={spot.status}
                isSelected={selectedSpotId === spot.id}
                onClick={() => onSelect(spot.id)}
            />
        {/each}
    </div>
</div>

<style>
    .parking-lot {
        background: #161e2e;
        padding: 24px;
        border-radius: var(--radius-lg);
        box-shadow: inset 0 0 40px rgba(0, 0, 0, 0.5);
        display: flex;
        flex-direction: column;
        gap: 20px;
    }

    .row {
        display: grid;
        grid-template-columns: repeat(6, 1fr);
        gap: 12px;
    }

    .road {
        height: 60px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0 10px;
        opacity: 0.5;
        position: relative;
    }

    .road-label {
        font-size: 0.8rem;
        letter-spacing: 4px;
        color: var(--color-text-muted);
        font-weight: 800;
    }

    .lane-marker {
        height: 2px;
        flex-grow: 1;
        background: linear-gradient(
            90deg,
            transparent 0%,
            var(--color-text-muted) 50%,
            transparent 100%
        );
        opacity: 0.3;
    }
</style>
