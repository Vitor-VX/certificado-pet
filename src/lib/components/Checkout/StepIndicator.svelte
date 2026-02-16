<script lang="ts">
  import { PawPrint, Check } from "lucide-svelte";
  export let currentStep: number;

  const steps = ["Pedido", "Extras", "Dados", "Pagamento"];
</script>

<svelte:head>
  <link
    href="https://fonts.googleapis.com/css2?family=Quicksand:wght@500;600;700&display=swap"
    rel="stylesheet"
  />
</svelte:head>

<div class="step-indicator">
  {#each steps as step, index}
    <div
      class="step-item"
      class:active={index === currentStep}
      class:completed={index < currentStep}
    >
      <div class="step-circle">
        {#if index < currentStep}
          <Check size={20} strokeWidth={3} />
        {:else if index === currentStep}
          <PawPrint size={20} fill="white" />
        {:else}
          {index + 1}
        {/if}
      </div>
      <span class="step-label">{step}</span>
    </div>

    {#if index < steps.length - 1}
      <div class="step-line" class:completed={index < currentStep}></div>
    {/if}
  {/each}
</div>

<style>
  .step-indicator {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 20px auto 50px;
    padding: 0 10px;
    max-width: 600px;
    font-family: "Quicksand", sans-serif;
  }

  .step-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    position: relative;
    z-index: 2;
  }

  .step-circle {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    background: #ffffff;
    color: #ffcc80;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 18px;
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    box-shadow: 0 4px 10px rgba(74, 52, 46, 0.05);
    border: 2px solid #fff9eb;
  }

  .step-item.active .step-circle {
    background: linear-gradient(135deg, #ff9f1c 0%, #f77f00 100%);
    border-color: #ff9f1c;
    color: white;
    transform: scale(1.1);
    box-shadow: 0 8px 20px rgba(247, 127, 0, 0.25);
  }

  .step-item.completed .step-circle {
    background: #ffcc80;
    border-color: #ffcc80;
    color: white;
  }

  .step-label {
    font-size: 0.85rem;
    color: #8d6e63;
    font-weight: 600;
    white-space: nowrap;
  }

  .step-item.active .step-label {
    color: #f77f00;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    font-size: 0.75rem;
  }

  .step-line {
    flex: 1;
    height: 4px;
    background: #fff9eb;
    margin: -25px 10px 0;
    border-radius: 10px;
    position: relative;
    z-index: 1;
  }

  .step-line.completed {
    background: #ffecb3;
  }

  .step-line::after {
    content: "";
    position: absolute;
    left: 0;
    top: 0;
    height: 100%;
    width: 0;
    background: #ffcc80;
    transition: width 0.4s ease;
    border-radius: 10px;
  }

  .step-line.completed::after {
    width: 100%;
  }

  @media (max-width: 768px) {
    .step-indicator {
      margin-bottom: 40px;
    }

    .step-circle {
      width: 40px;
      height: 40px;
      font-size: 16px;
    }

    .step-label {
      font-size: 0.75rem;
    }

    .step-line {
      margin-top: -24px;
    }
  }
</style>
