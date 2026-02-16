<script lang="ts">
  import { PawPrint, Bone } from "lucide-svelte";
  import { products, selectProduct } from "$lib/stores/checkoutStore";

  export let onNext: () => void;
</script>

<svelte:head>
  <link
    href="https://fonts.googleapis.com/css2?family=Quicksand:wght@400;500;600;700&display=swap"
    rel="stylesheet"
  />
</svelte:head>

<div class="step-order">
  <div class="step-header">
    <h2>Escolha o registro perfeito</h2>
    <p>
      Cada documento é único e feito com todo o carinho para celebrar a vida do
      seu amiguinho.
    </p>
  </div>

  <div class="products-grid">
    {#each products as product}
      <div class="product-card card">
        <div class="product-icon">
          <PawPrint size={32} fill="#ff9f1c" color="#ff9f1c" />
        </div>

        <h3>{product.name}</h3>
        <p class="product-description">{product.description}</p>

        <div class="pricing">
          {#if product.oldPrice}
            <span class="price-old"
              >De R$ {product.oldPrice.toFixed(2).replace(".", ",")}</span
            >
          {/if}
          <div class="price-wrapper">
            <span class="currency">R$</span>
            <span class="price"
              >{product.price.toFixed(2).replace(".", ",")}</span
            >
          </div>
        </div>

        <button
          class="product-btn"
          on:click={() => {
            selectProduct(product);
            onNext();
          }}
        >
          <Bone size={18} />
          Registrar meu Pet
        </button>
      </div>
    {/each}
  </div>
</div>

<style>
  .step-order {
    max-width: 900px;
    margin: 0 auto;
    padding: 20px 0;
    font-family: "Quicksand", sans-serif;
  }

  .step-header {
    text-align: center;
    margin-bottom: 50px;
  }

  .step-header h2 {
    color: #ff9f1c;
    font-size: clamp(2.2rem, 8vw, 3.2rem);
    font-weight: 800;
    margin-bottom: 12px;
    letter-spacing: -1px;
  }

  .step-header p {
    color: #6d4c41;
    font-size: 1.15rem;
    font-weight: 500;
    max-width: 550px;
    margin: 0 auto;
    line-height: 1.5;
  }

  .products-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
    gap: 30px;
    padding: 0 20px;
  }

  .product-card {
    background: white;
    border-radius: 30px;
    text-align: center;
    position: relative;
    border: 2px solid #fff9eb;
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    padding: 40px 24px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    box-shadow: 0 10px 30px rgba(74, 52, 46, 0.03);
  }

  .product-card:hover {
    border-color: #ffcc80;
    transform: translateY(-10px);
    box-shadow: 0 20px 40px rgba(255, 159, 28, 0.1);
  }

  .product-icon {
    background: #fff9eb;
    padding: 16px;
    border-radius: 20px;
    margin: 0 auto 24px;
    width: fit-content;
    transition: transform 0.3s ease;
  }

  .product-card:hover .product-icon {
    transform: scale(1.1) rotate(-5deg);
  }

  .product-card h3 {
    font-size: 1.5rem;
    color: #4a342e;
    margin-bottom: 12px;
    font-weight: 700;
  }

  .product-description {
    color: #8d6e63;
    margin-bottom: 24px;
    font-size: 1rem;
    font-weight: 500;
    min-height: 45px;
  }

  .pricing {
    margin-bottom: 32px;
  }

  .price-old {
    display: block;
    color: #bfa2a2;
    font-size: 0.95rem;
    text-decoration: line-through;
    margin-bottom: 4px;
    font-weight: 500;
  }

  .price-wrapper {
    color: #f77f00;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 4px;
  }

  .currency {
    font-size: 1.2rem;
    font-weight: 600;
  }

  .price {
    font-size: 2.5rem;
    font-weight: 800;
  }

  .product-btn {
    padding: 18px 30px;
    font-size: 1.2rem;
    font-weight: 700;
    border-radius: 50px;
    background: linear-gradient(135deg, #ff9f1c 0%, #f77f00 100%);
    box-shadow: 0 8px 20px rgba(247, 127, 0, 0.2);
    color: white;
    border: none;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
    font-family: "Quicksand", sans-serif;
    transition: all 0.3s ease;
  }

  .product-btn:hover {
    transform: scale(1.02);
    box-shadow: 0 12px 25px rgba(247, 127, 0, 0.3);
  }

  @media (max-width: 768px) {
    .step-header h2 {
      font-size: 2.2rem;
    }

    .products-grid {
      grid-template-columns: 1fr;
      padding: 0 15px;
    }

    .product-card {
      padding: 30px 20px;
    }

    .product-btn {
      width: 100%;
    }
  }
</style>
