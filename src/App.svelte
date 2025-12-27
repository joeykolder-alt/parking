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
    { id: "A5", status: "banned" }, // Disabled/staff
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

  let currentTicket = $state(null); // { id, duration }
  let authCode = $state("");
  let token = $state("");

  function handleSelect(id) {
    // Cannot select if occupied (handled by disabled in button generally, but good to check)
    const spot = spots.find((s) => s.id === id);
    if (!spot || spot.status !== "available") return;

    selectedSpotId = id;
    showModal = true;
  }

  function handleConfirm(duration) {
    if (!selectedSpotId) return;

    // Logic: Authenticate -> Pay -> Show Ticket
    // For now, we assume user is authenticated at mount or we verify it here
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
        resolve(); // Mock success for web dev
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
      // Mock payment for web
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
    // Update spot status
    const idx = spots.findIndex((s) => s.id === selectedSpotId);
    if (idx !== -1) {
      spots[idx].status = "occupied";
    }

    currentTicket = { spotId: selectedSpotId, duration };

    // Close modal
    showModal = false;
    selectedSpotId = null;
  }

  function handleCloseTicket() {
    currentTicket = null;
  }

  function handleScan() {
    if (typeof my === "undefined") {
      // Mock functionality for browser testing
      console.log("Simulating QR Scan");
      selectedSpotId = "MALL-PARKING-A1";
      showModal = true;
      return;
    }
    my.scan({
      type: "qr",
      success: (res) => {
        // Use the scanned code (Park Name/ID) to initiate booking
        if (res.code) {
          selectedSpotId = res.code;
          showModal = true;
        }
      },
    });
  }

  onMount(() => {
    // Optional: Try to silent login on mount
    authenticate();
  });
</script>

<main>
  <header>
    <div class="top-bar">
      <span class="back-arrow">←</span>
      <h1>Parking</h1>
      <button class="scan-btn" onclick={handleScan} aria-label="Scan QR Code">
        <span>📷</span>
      </button>
      <div class="user-avatar"></div>
    </div>

    <div class="status-summary">
      <div class="status-pill status-free">
        <span class="dot"></span> Available
      </div>
      <div class="status-pill status-busy">
        <span class="dot"></span> Occupied
      </div>
    </div>
  </header>

  <div class="content-area">
    <div class="floor-selector">
      <button class="active">Level 1</button>
      <button>Level 2</button>
      <button>Level 3</button>
    </div>

    <ParkingLot {spots} {selectedSpotId} onSelect={handleSelect} />
  </div>

  {#if showModal}
    <BookingModal
      spotId={selectedSpotId}
      onCancel={() => {
        showModal = false;
        selectedSpotId = null;
      }}
      onConfirm={handleConfirm}
    />
  {/if}

  {#if currentTicket}
    <Ticket
      spotId={currentTicket.spotId}
      duration={currentTicket.duration}
      onClose={handleCloseTicket}
    />
  {/if}
</main>

<style>
  header {
    padding: 20px 24px;
    background: linear-gradient(
      180deg,
      rgba(30, 41, 59, 1) 0%,
      rgba(15, 23, 42, 0) 100%
    );
  }

  .top-bar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
  }

  h1 {
    font-size: 1.2rem;
    margin: 0;
    font-weight: 600;
  }

  .back-arrow {
    font-size: 1.5rem;
    color: var(--color-text-muted);
  }

  .scan-btn {
    background: rgba(255, 255, 255, 0.1);
    border-radius: 50%;
    width: 36px;
    height: 36px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 10px;
    margin-left: auto;
  }

  .user-avatar {
    width: 36px;
    height: 36px;
    background: linear-gradient(135deg, #6366f1, #a855f7);
    border-radius: 50%;
    box-shadow: 0 0 10px rgba(168, 85, 247, 0.4);
  }

  .status-summary {
    display: flex;
    gap: 15px;
    justify-content: center;
  }

  .status-pill {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 0.8rem;
    color: var(--color-text-muted);
    background: rgba(255, 255, 255, 0.05);
    padding: 6px 12px;
    border-radius: 20px;
  }

  .dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
  }

  .status-free .dot {
    background: var(--color-primary);
    box-shadow: 0 0 8px var(--color-primary);
  }
  .status-busy .dot {
    background: var(--color-booked);
  }

  .content-area {
    padding: 20px;
  }

  .floor-selector {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    overflow-x: auto;
    padding-bottom: 5px;
  }

  .floor-selector button {
    padding: 8px 16px;
    border-radius: 20px;
    background: var(--color-surface);
    color: var(--color-text-muted);
    font-size: 0.9rem;
    font-weight: 500;
    white-space: nowrap;
  }

  .floor-selector button.active {
    background: var(--color-primary);
    color: #fff;
    box-shadow: 0 4px 12px var(--color-primary-glow);
  }
</style>
