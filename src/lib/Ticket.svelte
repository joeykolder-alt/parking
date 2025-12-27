<script>
    let { spotId, duration, onClose } = $props();

    // Create a fake QR code pattern
    const qrRows = Array(8)
        .fill(0)
        .map(() =>
            Array(8)
                .fill(0)
                .map(() => Math.random() > 0.5),
        );
</script>

<div class="ticket-container">
    <div class="ticket">
        <div class="ticket-header">
            <h3>Parking Pass</h3>
            <span class="brand">SuperApp</span>
        </div>

        <div class="ticket-body">
            <div class="info-row">
                <label>SPOT</label>
                <span class="value huge">{spotId}</span>
            </div>

            <div class="info-grid">
                <div class="info-item">
                    <label>DURATION</label>
                    <span class="value">{duration} hr</span>
                </div>
                <div class="info-item">
                    <label>COST</label>
                    <span class="value">${duration * 5}.00</span>
                </div>
            </div>

            <div class="qr-code">
                {#each qrRows as row}
                    <div class="qr-row">
                        {#each row as cell}
                            <div
                                class="qr-cell"
                                style:opacity={cell ? 1 : 0.1}
                            ></div>
                        {/each}
                    </div>
                {/each}
            </div>
        </div>

        <div class="ticket-footer">
            <div class="cutout left"></div>
            <div class="cutout right"></div>
            <div class="dashed-line"></div>
        </div>

        <div class="actions">
            <button class="done-btn" onclick={onClose}>Done</button>
        </div>
    </div>
</div>

<style>
    .ticket-container {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: var(--color-bg);
        display: flex;
        align-items: center;
        justify-content: center;
        z-index: 200;
        padding: 20px;
    }

    .ticket {
        background: #fff;
        color: #000;
        width: 100%;
        max-width: 340px;
        border-radius: 20px;
        overflow: hidden;
        box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5);
        animation: popIn 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    @keyframes popIn {
        from {
            transform: scale(0.8);
            opacity: 0;
        }
        to {
            transform: scale(1);
            opacity: 1;
        }
    }

    .ticket-header {
        background: var(--color-primary);
        padding: 20px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        color: white;
    }

    .brand {
        font-weight: 800;
        letter-spacing: 1px;
        opacity: 0.8;
    }

    .ticket-body {
        padding: 30px;
        display: flex;
        flex-direction: column;
        gap: 20px;
    }

    .info-row {
        text-align: center;
    }

    .huge {
        font-size: 3rem;
        font-weight: 800;
        color: var(--color-bg);
        display: block;
        line-height: 1;
    }

    .info-grid {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 20px;
    }

    .info-item {
        display: flex;
        flex-direction: column;
        gap: 5px;
    }

    label {
        font-size: 0.7rem;
        color: #64748b;
        font-weight: 700;
        letter-spacing: 1px;
    }

    .value {
        font-weight: 700;
        font-size: 1.1rem;
    }

    .qr-code {
        width: 120px;
        height: 120px;
        margin: 10px auto;
        display: flex;
        flex-direction: column;
    }

    .qr-row {
        display: flex;
        flex: 1;
    }

    .qr-cell {
        flex: 1;
        background: #000;
    }

    .ticket-footer {
        position: relative;
        height: 20px;
        background: #fff;
    }

    .cutout {
        position: absolute;
        top: -10px;
        width: 20px;
        height: 20px;
        background: var(--color-bg);
        border-radius: 50%;
    }

    .left {
        left: -10px;
    }
    .right {
        right: -10px;
    }

    .dashed-line {
        position: absolute;
        top: 0;
        left: 10px;
        right: 10px;
        border-top: 2px dashed #cbd5e1;
    }

    .actions {
        padding: 20px;
        background: #f8fafc;
    }

    .done-btn {
        width: 100%;
        padding: 15px;
        background: var(--color-bg);
        color: #fff;
        border-radius: 12px;
        font-weight: bold;
    }
</style>
