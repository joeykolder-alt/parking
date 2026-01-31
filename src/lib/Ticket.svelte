<script>
    let { spotId, duration, onClose } = $props();
    const entryTime = new Date().toLocaleTimeString([], {
        hour: "2-digit",
        minute: "2-digit",
    });
    const expiresAt = new Date(
        Date.now() + duration * 60 * 60 * 1000,
    ).toLocaleTimeString([], { hour: "2-digit", minute: "2-digit" });
</script>

<div class="ticket-container">
    <div class="ticket glass-morphism animate-ticket">
        <div class="ticket-header">
            <div class="logo">
                <div class="logo-icon">P</div>
                <span>PARKING PASS</span>
            </div>
            <div class="status-badge">ACTIVE</div>
        </div>

        <div class="main-info">
            <div class="info-block">
                <span class="label">ENTRY TIME</span>
                <span class="value">{entryTime}</span>
            </div>
            <div class="info-block text-right">
                <span class="label">EXPIRES AT</span>
                <span class="value">{expiresAt}</span>
            </div>
        </div>

        <div class="divider">
            <div class="cutout left"></div>
            <div class="dashed-line"></div>
            <div class="cutout right"></div>
        </div>

        <div class="ticket-details">
            <div class="row">
                <div class="detail">
                    <span class="label">SPOT NO</span>
                    <span class="value highlight">{spotId}</span>
                </div>
                <div class="detail">
                    <span class="label">LEVEL</span>
                    <span class="value">01</span>
                </div>
            </div>
            <div class="row">
                <div class="detail">
                    <span class="label">VEHICLE</span>
                    <span class="value">SEDAN</span>
                </div>
                <div class="detail">
                    <span class="label">PLATE</span>
                    <span class="value">ABC - 123</span>
                </div>
            </div>
        </div>

        <div class="qr-section">
            <div class="qr-placeholder">
                <div class="qr-dots">
                    {#each Array(25) as _}
                        <div class="qr-dot"></div>
                    {/each}
                </div>
                <div class="qr-corner top-left"></div>
                <div class="qr-corner top-right"></div>
                <div class="qr-corner bottom-left"></div>
                <div class="qr-corner bottom-right"></div>
            </div>
            <p class="qr-hint">Scan at exit gate</p>
        </div>
    </div>

    <button class="close-btn" onclick={onClose}> Confirm & Close </button>
</div>

<style>
    .ticket-container {
        width: 100%;
        display: flex;
        flex-direction: column;
        gap: 24px;
        align-items: center;
    }

    .ticket {
        width: 100%;
        background: #fff;
        color: #000;
        border-radius: 24px;
        overflow: hidden;
        position: relative;
    }

    .ticket-header {
        background: #000;
        color: #fff;
        padding: 20px 24px;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .logo {
        display: flex;
        align-items: center;
        gap: 10px;
        font-weight: 800;
        font-size: 0.8rem;
        letter-spacing: 0.1em;
    }

    .logo-icon {
        width: 24px;
        height: 24px;
        background: var(--primary);
        display: flex;
        align-items: center;
        justify-content: center;
        border-radius: 6px;
        font-size: 0.9rem;
    }

    .status-badge {
        font-size: 0.65rem;
        font-weight: 800;
        padding: 4px 8px;
        border: 1px solid rgba(255, 255, 255, 0.3);
        border-radius: 4px;
    }

    .main-info {
        padding: 24px;
        display: flex;
        justify-content: space-between;
    }

    .info-block .label {
        display: block;
        font-size: 0.65rem;
        font-weight: 700;
        color: #666;
        margin-bottom: 4px;
    }

    .info-block .value {
        font-size: 1.4rem;
        font-weight: 900;
        font-family: var(--font-heading);
    }

    .text-right {
        text-align: right;
    }

    .divider {
        position: relative;
        height: 20px;
        display: flex;
        align-items: center;
    }

    .cutout {
        position: absolute;
        width: 20px;
        height: 20px;
        background: #000; /* Match modal background */
        border-radius: 50%;
        z-index: 2;
    }

    .cutout.left {
        left: -10px;
    }
    .cutout.right {
        right: -10px;
    }

    .dashed-line {
        width: 100%;
        height: 1px;
        border-top: 2px dashed #eee;
        margin: 0 15px;
    }

    .ticket-details {
        padding: 12px 24px;
        display: flex;
        flex-direction: column;
        gap: 20px;
    }

    .row {
        display: flex;
        justify-content: space-between;
    }

    .detail .label {
        display: block;
        font-size: 0.6rem;
        font-weight: 700;
        color: #999;
        margin-bottom: 2px;
    }

    .detail .value {
        font-size: 0.95rem;
        font-weight: 700;
    }

    .value.highlight {
        color: var(--primary);
        font-size: 1.1rem;
    }

    .qr-section {
        padding: 24px;
        display: flex;
        flex-direction: column;
        align-items: center;
        background: #fbfbfb;
    }

    .qr-placeholder {
        width: 120px;
        height: 120px;
        position: relative;
        display: flex;
        align-items: center;
        justify-content: center;
        margin-bottom: 12px;
    }

    .qr-dots {
        display: grid;
        grid-template-columns: repeat(5, 1fr);
        gap: 4px;
    }

    .qr-dot {
        width: 4px;
        height: 4px;
        background: #000;
        border-radius: 1px;
    }

    .qr-corner {
        position: absolute;
        width: 20px;
        height: 20px;
        border: 2px solid #000;
    }

    .top-left {
        top: 0;
        left: 0;
        border-right: none;
        border-bottom: none;
    }
    .top-right {
        top: 0;
        right: 0;
        border-left: none;
        border-bottom: none;
    }
    .bottom-left {
        bottom: 0;
        left: 0;
        border-right: none;
        border-top: none;
    }
    .bottom-right {
        bottom: 0;
        right: 0;
        border-left: none;
        border-top: none;
    }

    .qr-hint {
        font-size: 0.7rem;
        font-weight: 600;
        color: #999;
        text-transform: uppercase;
        letter-spacing: 0.1em;
    }

    .close-btn {
        width: 100%;
        padding: 16px;
        background: var(--primary);
        color: white;
        border-radius: 16px;
        font-weight: 700;
        font-size: 1rem;
        box-shadow: 0 10px 25px -10px var(--primary-glow);
    }

    .animate-ticket {
        animation: ticketPop 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    @keyframes ticketPop {
        0% {
            transform: scale(0.8);
            opacity: 0;
        }
        100% {
            transform: scale(1);
            opacity: 1;
        }
    }
</style>
