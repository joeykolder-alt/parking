<script>
  let { id, status, isSelected, onClick } = $props();

  // Status can be 'available', 'occupied', 'banned'
</script>

<button 
  class="slot {status} {isSelected ? 'selected' : ''}" 
  onclick={onClick}
  disabled={status === 'occupied' || status === 'banned'}
  aria-label={`Parking Spot ${id} ${status}`}
>
  <div class="glow"></div>
  <div class="slot-content">
    <span class="slot-id">{id}</span>
    
    {#if status === 'occupied'}
      <div class="car-container animate-fade-in">
        <svg viewBox="0 0 24 24" fill="currentColor" class="car-icon">
          <path d="M18.92 6.01C18.72 5.42 18.16 5 17.5 5h-11c-.66 0-1.21.42-1.42 1.01L3 12v8c0 .55.45 1 1 1h1c.55 0 1-.45 1-1v-1h12v1c0 .55.45 1 1 1h1c.55 0 1-.45 1-1v-8l-2.08-5.99zM6.5 16c-.83 0-1.5-.67-1.5-1.5S5.67 13 6.5 13s1.5.67 1.5 1.5S7.33 16 6.5 16zm11 0c-.83 0-1.5-.67-1.5-1.5s.67-1.5 1.5-1.5 1.5.67 1.5 1.5-.67 1.5-1.5 1.5zM5 11l1.5-4.5h11L19 11H5z"/>
        </svg>
      </div>
    {:else if status === 'banned'}
       <div class="banned-indicator">
         <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="banned-icon">
           <circle cx="12" cy="12" r="10"/><line x1="4.93" y1="4.93" x2="19.07" y2="19.07"/>
         </svg>
       </div>
    {:else}
      <div class="available-indicator"></div>
    {/if}
  </div>
  
  <div class="marking-line left"></div>
  <div class="marking-line right"></div>
</button>

<style>
  .slot {
    width: 100%;
    aspect-ratio: 1/1.6;
    background-color: var(--surface-color);
    border: 1px solid var(--surface-border);
    border-top: none;
    border-bottom: none;
    position: relative;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0;
  }

  .glow {
    position: absolute;
    inset: 0;
    background: radial-gradient(circle at center, var(--primary-glow) 0%, transparent 70%);
    opacity: 0;
    transition: opacity 0.3s ease;
    pointer-events: none;
  }

  .slot.available:hover .glow {
    opacity: 0.4;
  }

  .slot.selected .glow {
    opacity: 0.8;
    background: radial-gradient(circle at center, rgba(245, 158, 11, 0.4) 0%, transparent 70%);
  }

  .marking-line {
    position: absolute;
    top: 10%;
    bottom: 10%;
    width: 2px;
    background: rgba(255, 255, 255, 0.15);
    border-radius: 4px;
  }

  .marking-line.left { left: 0; }
  .marking-line.right { right: 0; }

  .slot.selected {
    background: rgba(245, 158, 11, 0.05);
  }
  
  .slot.selected .marking-line {
    background: var(--accent);
    box-shadow: 0 0 8px var(--accent);
  }

  .slot.occupied {
    background: rgba(15, 23, 42, 0.4);
    cursor: not-allowed;
  }

  .slot.banned {
    background: rgba(239, 68, 68, 0.05);
    opacity: 0.5;
    cursor: not-allowed;
  }

  .slot-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 12px;
    z-index: 2;
  }

  .slot-id {
    font-size: 0.7rem;
    font-weight: 700;
    color: var(--text-dim);
    letter-spacing: 0.05em;
    position: absolute;
    top: 8px;
  }

  .selected .slot-id {
    color: var(--accent);
  }

  .car-container {
    color: var(--text-muted);
    filter: drop-shadow(0 4px 12px rgba(0, 0, 0, 0.3));
    transition: transform 0.3s ease;
  }

  .car-icon {
    width: 32px;
    height: 32px;
    transform: rotate(90deg);
  }

  .banned-icon {
    width: 20px;
    height: 20px;
    color: var(--error);
  }

  .available-indicator {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--success);
    box-shadow: 0 0 10px var(--success);
    opacity: 0.6;
  }

  .animate-fade-in {
    animation: fadeIn 0.4s ease-out;
  }

  @keyframes fadeIn {
    from { opacity: 0; transform: scale(0.9) rotate(90deg); }
    to { opacity: 1; transform: scale(1) rotate(90deg); }
  }
</style>
