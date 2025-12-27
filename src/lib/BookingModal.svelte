<script>
    let { spotId, onConfirm, onCancel } = $props();
    let duration = $state(1);
    let price = $derived(duration * 5); // $5 per hour
</script>

<div class="modal-backdrop">
    <div class="modal">
        <div class="header">
            <h2>Reserve Spot <span class="highlight">{spotId}</span></h2>
            <button class="close-btn" onclick={onCancel}>&times;</button>
        </div>

        <div class="content">
            <div class="duration-control">
                <label>Duration</label>
                <div class="stepper">
                    <button onclick={() => duration > 1 && duration--}>-</button
                    >
                    <span>{duration} hr</span>
                    <button onclick={() => duration++}>+</button>
                </div>
            </div>

            <div class="price-tag">
                <span>Total</span>
                <span class="amount">${price}.00</span>
            </div>
        </div>

        <button class="confirm-btn" onclick={() => onConfirm(duration)}>
            Confirm Booking
        </button>
    </div>
</div>

<style>
    .modal-backdrop {
        position: fixed;
        bottom: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.6);
        backdrop-filter: blur(4px);
        display: flex;
        align-items: flex-end;
        justify-content: center;
        z-index: 100;
    }

    .modal {
        background: var(--color-surface);
        width: 100%;
        max-width: 480px;
        border-radius: 24px 24px 0 0;
        padding: 24px;
        box-shadow: 0 -10px 40px rgba(0, 0, 0, 0.5);
        animation: slideUp 0.3s ease-out;
    }

    @keyframes slideUp {
        from {
            transform: translateY(100%);
        }
        to {
            transform: translateY(0);
        }
    }

    .header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 24px;
    }

    h2 {
        margin: 0;
        font-size: 1.5rem;
        color: var(--color-text-main);
    }

    .highlight {
        color: var(--color-selected);
    }

    .close-btn {
        font-size: 1.5rem;
        color: var(--color-text-muted);
        padding: 8px;
    }

    .content {
        background: rgba(0, 0, 0, 0.2);
        padding: 20px;
        border-radius: var(--radius-md);
        margin-bottom: 24px;
    }

    .duration-control {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 20px;
    }

    .stepper {
        display: flex;
        align-items: center;
        gap: 15px;
        background: var(--color-bg);
        padding: 8px;
        border-radius: var(--radius-md);
    }

    .stepper button {
        width: 30px;
        height: 30px;
        background: var(--color-surface);
        color: var(--color-text-main);
        border-radius: 8px;
        font-weight: bold;
    }

    .price-tag {
        display: flex;
        justify-content: space-between;
        align-items: center;
        font-size: 1.2rem;
        font-weight: bold;
    }

    .amount {
        color: var(--color-primary);
    }

    .confirm-btn {
        width: 100%;
        padding: 18px;
        background: var(--color-primary);
        color: #fff;
        font-size: 1.1rem;
        font-weight: bold;
        border-radius: var(--radius-md);
        box-shadow: 0 4px 20px var(--color-primary-glow);
        transition: transform 0.2s;
    }

    .confirm-btn:active {
        transform: scale(0.98);
    }
</style>
