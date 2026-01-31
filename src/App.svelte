<script>
  import ParkingLot from "./lib/ParkingLot.svelte";
  import BookingModal from "./lib/BookingModal.svelte";
  import Ticket from "./lib/Ticket.svelte";
  import { onMount } from "svelte";

  // Mock Data
  let spots = $state([
    { id: "A1", status: "available" },
    { id: "A2", status: "occupied" },
    { id: "A3", status: "available" },
    { id: "A4", status: "available" },
    { id: "A5", status: "banned" },
    { id: "A6", status: "available" },
    { id: "B1", status: "available" },
    { id: "B2", status: "available" },
    { id: "B3", status: "occupied" },
    { id: "B4", status: "occupied" },
    { id: "B5", status: "available" },
    { id: "B6", status: "available" },
  ]);

  let selectedSpotId = $state(null);
  let showModal = $state(false);

  let currentTicket = $state(null);
  let authCode = $state("");
  let token = $state("");

  function handleSelect(id) {
    const spot = spots.find((s) => s.id === id);
    if (!spot || spot.status !== "available") return;

    selectedSpotId = id;
    showModal = true;
  }

  function handleConfirm(duration) {
    if (!selectedSpotId) return;

    if (!token) {
      authenticate().then(() => {
        pay(duration);
      });
    } else {
      pay(duration);
    }
  }

  function authenticate() {
    return new Promise((resolve, reject) => {
      if (typeof my === "undefined") {
        console.warn("MiniApp environment not detected");
        resolve();
        return;
      }

      my.getAuthCode({
        scopes: ["auth_base", "USER_ID"],
        success: (res) => {
          authCode = res.authCode;

          fetch("https://its.mouamle.space/api/auth-with-superQi", {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              token: authCode,
            }),
          })
            .then((res) => res.json())
            .then((data) => {
              token = data.token;
              my.alert({ content: "Login successful" });
              resolve();
            })
            .catch((err) => {
              let errorDetails = "";
              if (err && typeof err === "object") {
                errorDetails = JSON.stringify(err, null, 2);
              } else {
                errorDetails = String(err);
              }
              my.alert({ content: "Error: " + errorDetails });
              reject(err);
            });
        },
        fail: (res) => {
          console.log(res.authErrorScopes);
          reject(res);
        },
      });
    });
  }

  function pay(duration) {
    if (typeof my === "undefined") {
      completeBooking(duration);
      return;
    }

    fetch("https://its.mouamle.space/api/payment", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Authorization: token,
      },
    })
      .then((res) => res.json())
      .then((data) => {
        my.tradePay({
          paymentUrl: data.url,
          success: (res) => {
            my.alert({
              content: "Payment successful",
            });
            completeBooking(duration);
          },
        });
      })
      .catch((err) => {
        my.alert({
          content: "Payment failed",
        });
      });
  }

  function completeBooking(duration) {
    const idx = spots.findIndex((s) => s.id === selectedSpotId);
    if (idx !== -1) {
      spots[idx].status = "occupied";
    }

    currentTicket = { spotId: selectedSpotId, duration };
    showModal = false;
    selectedSpotId = null;
  }

  function handleCloseTicket() {
    currentTicket = null;
  }

  function handleScan() {
    if (typeof my === "undefined") {
      console.log("Simulating QR Scan");
      selectedSpotId = "MALL-PARKING-A1";
      showModal = true;
      return;
    }
    my.scan({
      type: "qr",
      success: (res) => {
        if (res.code) {
          selectedSpotId = res.code;
          showModal = true;
        }
      },
    });
  }

  onMount(() => {
    authenticate();
  });
</script>

<main>
  <div class="background-glow"></div>

  <header class="animate-fade-in">
    <div class="top-bar">
      <button class="icon-btn" aria-label="Back">
        <svg
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2.5"
          class="icon"><path d="M15 18l-6-6 6-6" /></svg
        >
      </button>
      <div class="title-group">
        <h1>Elite Parking</h1>
        <p class="subtitle">Intelligent Spot Finder</p>
      </div>
      <div class="profile-group">
        <button class="scan-btn" onclick={handleScan} aria-label="Scan QR Code">
          <svg
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            class="icon"
          >
            <path
              d="M3 7V5a2 2 0 012-2h2m10 0h2a2 2 0 012 2v2m0 10v2a2 2 0 01-2 2h-2M7 21H5a2 2 0 01-2-2v-2M7 12h10M12 7v10"
            />
          </svg>
        </button>
        <div class="avatar"></div>
      </div>
    </div>

    <div class="stats-grid">
      <div class="stat-card glass-morphism">
        <span class="stat-label">Available</span>
        <span class="stat-value text-success"
          >{spots.filter((s) => s.status === "available").length}</span
        >
      </div>
      <div class="stat-card glass-morphism">
        <span class="stat-label">Reserved</span>
        <span class="stat-value text-accent"
          >{spots.filter((s) => s.status === "occupied").length}</span
        >
      </div>
      <div class="stat-card glass-morphism">
        <span class="stat-label">Floor</span>
        <span class="stat-value">P1</span>
      </div>
    </div>
  </header>

  <section class="floor-layout animate-fade-in">
    <div class="floor-nav">
      <button class="floor-chip active">Floor 01</button>
      <button class="floor-chip">Floor 02</button>
      <button class="floor-chip">Floor 03</button>
      <button class="floor-chip">Premium</button>
    </div>

    <ParkingLot {spots} {selectedSpotId} onSelect={handleSelect} />
  </section>

  <div class="bottom-actions animate-fade-in">
    <div class="legend">
      <div class="legend-item">
        <span class="dot available"></span> Available
      </div>
      <div class="legend-item"><span class="dot occupied"></span> Occupied</div>
      <div class="legend-item"><span class="dot selected"></span> Selected</div>
    </div>
  </div>

  {#if showModal}
    <div
      class="modal-overlay"
      onclick={() => {
        showModal = false;
        selectedSpotId = null;
      }}
    >
      <div class="modal-container" onclick={(e) => e.stopPropagation()}>
        <BookingModal
          spotId={selectedSpotId}
          onCancel={() => {
            showModal = false;
            selectedSpotId = null;
          }}
          onConfirm={handleConfirm}
        />
      </div>
    </div>
  {/if}

  {#if currentTicket}
    <div class="modal-overlay">
      <div class="modal-container">
        <Ticket
          spotId={currentTicket.spotId}
          duration={currentTicket.duration}
          onClose={handleCloseTicket}
        />
      </div>
    </div>
  {/if}
</main>

<style>
  main {
    flex: 1;
    display: flex;
    flex-direction: column;
    padding: 20px 0;
    position: relative;
    z-index: 1;
  }

  .background-glow {
    position: absolute;
    top: -100px;
    right: -100px;
    width: 300px;
    height: 300px;
    background: radial-gradient(
      circle,
      var(--primary-glow) 0%,
      transparent 70%
    );
    z-index: -1;
    pointer-events: none;
  }

  header {
    padding: 0 24px;
    margin-bottom: 24px;
  }

  .top-bar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 24px;
  }

  .title-group h1 {
    font-size: 1.4rem;
    letter-spacing: -0.02em;
  }

  .subtitle {
    font-size: 0.8rem;
    color: var(--text-muted);
    margin: 4px 0 0 0;
  }

  .icon-btn {
    width: 40px;
    height: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: var(--surface-color);
    border-radius: 12px;
    border: 1px solid var(--surface-border);
    color: var(--text-main);
  }

  .profile-group {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .scan-btn {
    width: 44px;
    height: 44px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(135deg, var(--primary), var(--secondary));
    border-radius: 14px;
    color: white;
    box-shadow: 0 8px 16px -4px var(--primary-glow);
  }

  .avatar {
    width: 44px;
    height: 44px;
    background: var(--surface-color-lighter);
    border-radius: 14px;
    border: 2px solid var(--surface-border);
    background-image: url("https://api.dicebear.com/7.x/avataaars/svg?seed=Lucky");
    background-size: cover;
  }

  .icon {
    width: 20px;
    height: 20px;
  }

  .stats-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
  }

  .stat-card {
    padding: 12px;
    border-radius: var(--radius-md);
    display: flex;
    flex-direction: column;
    gap: 4px;
    border: 1px solid rgba(255, 255, 255, 0.05);
  }

  .stat-label {
    font-size: 0.65rem;
    color: var(--text-dim);
    text-transform: uppercase;
    font-weight: 700;
    letter-spacing: 0.05em;
  }

  .stat-value {
    font-size: 1.1rem;
    font-weight: 700;
    font-family: var(--font-heading);
  }

  .text-success {
    color: var(--success);
  }
  .text-accent {
    color: var(--accent);
  }

  .floor-layout {
    padding: 0 12px;
    flex: 1;
  }

  .floor-nav {
    display: flex;
    gap: 8px;
    padding: 0 12px;
    margin-bottom: 16px;
    overflow-x: auto;
    scrollbar-width: none;
  }

  .floor-nav::-webkit-scrollbar {
    display: none;
  }

  .floor-chip {
    padding: 8px 16px;
    border-radius: 20px;
    background: var(--surface-color);
    color: var(--text-muted);
    font-size: 0.8rem;
    font-weight: 600;
    white-space: nowrap;
    border: 1px solid var(--surface-border);
  }

  .floor-chip.active {
    background: var(--primary);
    color: white;
    border-color: var(--primary);
    box-shadow: 0 4px 12px var(--primary-glow);
  }

  .bottom-actions {
    padding: 20px 24px;
  }

  .legend {
    display: flex;
    justify-content: center;
    gap: 20px;
  }

  .legend-item {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 0.75rem;
    color: var(--text-muted);
    font-weight: 500;
  }

  .dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
  }

  .dot.available {
    background: var(--success);
    box-shadow: 0 0 8px var(--success);
  }
  .dot.occupied {
    background: var(--text-dim);
  }
  .dot.selected {
    background: var(--accent);
    box-shadow: 0 0 8px var(--accent);
  }

  .modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.8);
    backdrop-filter: blur(8px);
    z-index: 100;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 20px;
  }

  .modal-container {
    width: 100%;
    max-width: 400px;
    animation: modalSlideUp 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  }

  @keyframes modalSlideUp {
    from {
      transform: translateY(20px);
      opacity: 0;
    }
    to {
      transform: translateY(0);
      opacity: 1;
    }
  }
</style>
