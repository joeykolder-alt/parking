<script>
    let { spotId, onCancel, onConfirm } = $props();
    let durations = [
        { label: "1 Hour", value: 1, price: "5.00" },
        { label: "2 Hours", value: 2, price: "9.00" },
        { label: "4 Hours", value: 4, price: "15.00" },
        { label: "All Day", value: 12, price: "25.00" },
    ];
    let selectedDuration = $state(durations[0]);
</script>

<div class="booking-card glass-morphism">
    <div class="header">
        <div class="spot-badge">
            <span class="label">SPOT</span>
            <span class="value">{spotId}</span>
        </div>
        <h3>Booking Details</h3>
    </div>

    <div class="content">
        <p class="section-title">Select Duration</p>
        <div class="duration-grid">
            {#each durations as d}
                <button
                    class="duration-item {selectedDuration.value === d.value
                        ? 'active'
                        : ''}"
                    onclick={() => (selectedDuration = d)}
                >
                    <span class="duration-label">{d.label}</span>
                    <span class="duration-price">${d.price}</span>
                </button>
            {/each}
        </div>

        <div class="summary-card">
            <div class="summary-row">
                <span>Parking Fee</span>
                <span>${selectedDuration.price}</span>
            </div>
            <div class="summary-row">
                <span>Service Fee</span>
                <span>$0.50</span>
            </div>
            <div class="divider"></div>
            <div class="summary-row total">
                <span>Total Amount</span>
                <span
                    >${(parseFloat(selectedDuration.price) + 0.5).toFixed(
                        2,
                    )}</span
                >
            </div>
        </div>
    </div>

    <div class="actions">
        <button class="secondary-btn" onclick={onCancel}>Cancel</button>
        <button
            class="primary-btn"
            onclick={() => onConfirm(selectedDuration.value)}
        >
            Proceed to Pay
            <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2.5"
                class="icon"><path d="M5 12h14M12 5l7 7-7 7" /></svg
            >
        </button>
    </div>
</div>

<style>
    .booking-card {
        background: var(--surface-color);
        border-radius: var(--radius-lg);
        padding: 24px;
        border: 1px solid var(--glass-border);
        box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
    }

    .header {
        display: flex;
        align-items: center;
        gap: 16px;
        margin-bottom: 24px;
    }

    .spot-badge {
        display: flex;
        flex-direction: column;
        align-items: center;
        background: var(--accent);
        color: var(--bg-color);
        padding: 8px 12px;
        border-radius: 12px;
        min-width: 60px;
    }

    .spot-badge .label {
        font-size: 0.6rem;
        font-weight: 800;
    }
    .spot-badge .value {
        font-size: 1.2rem;
        font-weight: 900;
        line-height: 1;
    }

    h3 {
        font-size: 1.2rem;
        color: var(--text-main);
    }

    .section-title {
        font-size: 0.75rem;
        font-weight: 700;
        color: var(--text-dim);
        text-transform: uppercase;
        letter-spacing: 0.05em;
        margin-bottom: 12px;
    }

    .duration-grid {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 10px;
        margin-bottom: 24px;
    }

    .duration-item {
        background: var(--surface-color-lighter);
        border: 1px solid var(--surface-border);
        padding: 12px;
        border-radius: 14px;
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        gap: 4px;
        text-align: left;
    }

    .duration-item.active {
        border-color: var(--primary);
        background: rgba(99, 102, 241, 0.1);
        box-shadow: 0 0 15px var(--primary-glow);
    }

    .duration-label {
        font-size: 0.85rem;
        font-weight: 600;
        color: var(--text-main);
    }
    .duration-price {
        font-size: 0.75rem;
        color: var(--text-muted);
    }
    .active .duration-label {
        color: var(--primary);
    }

    .summary-card {
        background: rgba(0, 0, 0, 0.2);
        border-radius: 16px;
        padding: 16px;
        margin-bottom: 24px;
    }

    .summary-row {
        display: flex;
        justify-content: space-between;
        font-size: 0.85rem;
        color: var(--text-muted);
        margin-bottom: 8px;
    }

    .divider {
        height: 1px;
        background: var(--surface-border);
        margin: 12px 0;
    }

    .total {
        color: var(--text-main);
        font-weight: 700;
        font-size: 1rem;
        margin-bottom: 0;
    }

    .actions {
        display: flex;
        gap: 12px;
    }

    .secondary-btn {
        flex: 1;
        padding: 14px;
        background: transparent;
        border: 1px solid var(--surface-border);
        color: var(--text-muted);
        border-radius: 14px;
        font-weight: 600;
    }

    .primary-btn {
        flex: 2;
        padding: 14px;
        background: var(--primary);
        color: white;
        border-radius: 14px;
        font-weight: 700;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 8px;
        box-shadow: 0 8px 20px -6px var(--primary-glow);
    }

    .icon {
        width: 18px;
        height: 18px;
    }
</style>
