<script>
    import ParkingSlot from "./ParkingSlot.svelte";

    let { spots, selectedSpotId, onSelect } = $props();
</script>

<div class="parking-lot-container">
    <div class="parking-lot glass-morphism">
        <div class="header">
            <div class="lane-label">ZONE A</div>
            <div class="dots">
                <span></span><span></span><span></span>
            </div>
        </div>

        <div class="grid-layout">
            <div class="parking-row">
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
                <div class="road-markings">
                    {#each Array(6) as _}
                        <div class="dash"></div>
                    {/each}
                </div>
                <div class="direction-arrows">
                    <span class="entry-label">ENTRANCE</span>
                    <svg
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                        class="arrow-icon"
                    >
                        <path d="M5 12h14M12 5l7 7-7 7" />
                    </svg>
                </div>
            </div>

            <div class="parking-row bottom">
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
    </div>
</div>

<style>
    .parking-lot-container {
        padding: 4px;
        perspective: 1000px;
    }

    .parking-lot {
        background: rgba(15, 23, 42, 0.6);
        border-radius: var(--radius-xl);
        padding: 24px 16px;
        position: relative;
        box-shadow:
            0 20px 40px -10px rgba(0, 0, 0, 0.5),
            inset 0 0 20px rgba(255, 255, 255, 0.05);
    }

    .header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 24px;
        padding: 0 8px;
    }

    .lane-label {
        font-family: var(--font-heading);
        font-size: 0.75rem;
        font-weight: 700;
        letter-spacing: 0.2em;
        color: var(--text-dim);
    }

    .dots {
        display: flex;
        gap: 4px;
    }

    .dots span {
        width: 4px;
        height: 4px;
        border-radius: 50%;
        background: var(--surface-border);
    }

    .grid-layout {
        display: flex;
        flex-direction: column;
        gap: 0;
    }

    .parking-row {
        display: grid;
        grid-template-columns: repeat(6, 1fr);
        gap: 4px;
    }

    .road {
        height: 80px;
        background: rgba(0, 0, 0, 0.2);
        margin: 0 -16px;
        position: relative;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .road-markings {
        position: absolute;
        width: 100%;
        display: flex;
        justify-content: space-around;
        padding: 0 20px;
    }

    .dash {
        width: 24px;
        height: 2px;
        background: rgba(255, 255, 255, 0.05);
        border-radius: 2px;
    }

    .direction-arrows {
        display: flex;
        align-items: center;
        gap: 12px;
        color: var(--text-dim);
        opacity: 0.3;
    }

    .entry-label {
        font-size: 0.6rem;
        font-weight: 800;
        letter-spacing: 0.3em;
    }

    .arrow-icon {
        width: 14px;
        height: 14px;
    }

    .parking-row.bottom {
        transform: rotate(180deg);
    }

    .parking-row.bottom :global(.slot-id) {
        transform: rotate(180deg);
        top: auto;
        bottom: 8px;
    }

    .parking-row.bottom :global(.car-container) {
        transform: rotate(180deg);
    }
</style>
