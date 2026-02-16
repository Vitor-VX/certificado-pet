<script lang="ts">
  import { checkoutStore, toggleExtra } from "$lib/stores/checkoutStore";
  import { Bone, Sparkles, Check } from "lucide-svelte";

  export let onNext: () => void;

  $: ({ selectedExtras, totalAmount } = $checkoutStore);

  $: hasFullCollection = selectedExtras.some(
    (e) => e.id === "collection" && e.selected,
  );
</script>

<svelte:head>
  <link
    href="https://fonts.googleapis.com/css2?family=Quicksand:wght@500;600;700&display=swap"
    rel="stylesheet"
  />
</svelte:head>

<div class="step-extras">
  <div class="step-header">
    <h2>Turbine o registro do seu amiguinho</h2>
    <p>Adicione extras opcionais para deixar a certidão ainda mais completa</p>
  </div>

  <div class="extras-list">
    {#each selectedExtras as extra}
      {@const includedInCollection =
        hasFullCollection && extra.id === "with_photo"}

      <div
        class="extra-card card {includedInCollection ? 'disabled' : ''}"
        class:selected={extra.selected || includedInCollection}
      >
        <div class="extra-content">
          <div class="extra-info">
            <h3>
              {extra.name}

              {#if includedInCollection}
                <span class="included-badge">Incluso no Pacote</span>
              {/if}
            </h3>

            <p>{extra.description}</p>
          </div>

          <div class="extra-price">
            {#if includedInCollection}
              <span class="free">Grátis</span>
            {:else}
              + R$ {extra.price.toFixed(2).replace(".", ",")}
            {/if}
          </div>
        </div>

        <label class="extra-checkbox">
          <input
            type="checkbox"
            checked={includedInCollection || extra.selected}
            disabled={includedInCollection}
            on:change={() => toggleExtra(extra.id)}
          />

          <span class="checkmark">
            <Check size={14} strokeWidth={4} />
          </span>

          <span class="checkbox-text">
            {#if includedInCollection}
              Já incluso
            {:else}
              {extra.selected ? "Remover" : "Adicionar ao pedido"}
            {/if}
          </span>
        </label>
      </div>
    {/each}
  </div>

  <div class="step-footer">
    <div class="total-display">
      <span class="label">Valor atual:</span>
      <span class="amount">R$ {totalAmount.toFixed(2).replace(".", ",")}</span>
    </div>

    <button class="btn-continue" on:click={onNext}>
      Continuar
      <Bone size={18} />
    </button>
  </div>
</div>

<style>
  .step-extras {
    max-width: 650px;
    margin: 0 auto;
    font-family: "Quicksand", sans-serif;
    padding: 0 15px;
  }

  .step-header {
    text-align: center;
    margin-bottom: 40px;
  }

  .step-header h2 {
    color: #ff9f1c;
    font-size: clamp(2rem, 6vw, 2.8rem);
    font-weight: 800;
    margin-bottom: 12px;
    letter-spacing: -1px;
  }

  .step-header p {
    color: #6d4c41;
    font-size: 1.1rem;
    font-weight: 500;
  }

  .extras-list {
    margin-bottom: 40px;
  }

  .extra-card {
    background: white;
    border-radius: 24px;
    padding: 24px;
    margin-bottom: 16px;
    border: 2px solid #fff9eb;
    transition: all 0.3s ease;
    box-shadow: 0 4px 15px rgba(74, 52, 46, 0.03);
  }

  .extra-card.selected {
    border-color: #ffcc80;
    background: #fffcf5;
  }

  .extra-card.disabled {
    opacity: 0.7;
    background: #f9f9f9;
  }

  .extra-content {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    gap: 16px;
    margin-bottom: 20px;
  }

  .extra-info h3 {
    font-size: 1.15rem;
    color: #4a342e;
    font-weight: 700;
    margin-bottom: 6px;
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    gap: 8px;
  }

  .included-badge {
    font-size: 0.7rem;
    background: #dcfce7;
    color: #166534;
    padding: 4px 10px;
    border-radius: 50px;
    font-weight: 700;
    text-transform: uppercase;
  }

  .extra-info p {
    color: #8d6e63;
    font-size: 0.95rem;
    line-height: 1.4;
    font-weight: 500;
  }

  .extra-price {
    font-weight: 800;
    color: #ff9f1c;
    font-size: 1.1rem;
    white-space: nowrap;
  }

  .extra-price .free {
    color: #22c55e;
  }

  .extra-checkbox {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    cursor: pointer;
    background: #fdfdfd;
    padding: 8px 16px;
    border-radius: 50px;
    border: 1px solid #eee;
    transition: 0.2s;
  }

  .extra-card.selected .extra-checkbox {
    background: #ff9f1c;
    border-color: #ff9f1c;
    color: white;
  }

  .extra-checkbox input {
    display: none;
  }

  .checkmark {
    width: 20px;
    height: 20px;
    border: 2px solid #ddd;
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: white;
    color: transparent;
    transition: all 0.2s;
  }

  .extra-checkbox input:checked + .checkmark {
    background: white;
    border-color: white;
    color: #ff9f1c;
  }

  .checkbox-text {
    font-size: 0.9rem;
    font-weight: 700;
  }

  .step-footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 30px 0;
    border-top: 2px dashed #ffecb3;
    margin-top: 20px;
  }

  .total-display {
    display: flex;
    flex-direction: column;
  }

  .total-display .label {
    font-size: 0.9rem;
    color: #8d6e63;
    font-weight: 600;
  }

  .total-display .amount {
    font-size: 1.8rem;
    font-weight: 800;
    color: #f77f00;
  }

  .btn-continue {
    padding: 18px 35px;
    font-size: 1.2rem;
    font-weight: 700;
    border-radius: 50px;
    background: linear-gradient(135deg, #ff9f1c 0%, #f77f00 100%);
    box-shadow: 0 10px 25px rgba(247, 127, 0, 0.2);
    color: white;
    border: none;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 10px;
    transition: all 0.3s ease;
  }

  .btn-continue:hover {
    transform: translateY(-3px);
    box-shadow: 0 15px 30px rgba(247, 127, 0, 0.3);
  }

  @media (max-width: 768px) {
    .extra-content {
      flex-direction: column;
      gap: 10px;
    }

    .extra-price {
      align-self: flex-start;
    }

    .step-footer {
      flex-direction: column;
      gap: 20px;
      text-align: center;
    }

    .btn-continue {
      width: 100%;
      justify-content: center;
    }
  }
</style>
