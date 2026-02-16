<script lang="ts">
  import { PawPrint, Bone, Heart, Sparkles } from "lucide-svelte";
  import { onMount } from "svelte";

  export let onStartCheckout: () => void;

  const carrosel = ["https://files.botsync.site/certificado-pet/Billy.png"];

  let current = 0;
  let interval: any;

  function next() {
    current = (current + 1) % carrosel.length;
  }

  onMount(() => {
    interval = setInterval(next, 3500);
    return () => clearInterval(interval);
  });
</script>

<svelte:head>
  <link
    href="https://fonts.googleapis.com/css2?family=Quicksand:wght@400;500;600;700&display=swap"
    rel="stylesheet"
  />
</svelte:head>

<section class="hero">
  <div class="container">
    <div class="hero-content">
      <div class="badge">🐾 Registro Oficial do seu amiguinho</div>

      <h1 class="hero-title">
        <span class="title-icon">
          <PawPrint size={44} fill="currentColor" />
        </span>
        Certidão do seu Pet
        <span class="title-icon">
          <PawPrint size={44} fill="currentColor" />
        </span>
      </h1>

      <p class="hero-subtitle">
        Eternize a história do seu companheiro com um documento exclusivo. Um
        registro cheio de amor para celebrar a vida de quem te dá carinho todos
        os dias.
      </p>

      <div class="video-preview">
        <div class="video-frame">
          <div class="video-thumbnail carousel">
            {#each carrosel as image, i}
              <img
                src={image}
                alt="Prévia da Certidão Pet"
                class:active={i === current}
              />
            {/each}

            <!-- <div class="dots">
              {#each carrosel as _, i}
                <span
                  class:active={i === current}
                  on:click={() => (current = i)}
                />
              {/each}
            </div> -->
          </div>
        </div>
      </div>

      <div class="cta-section">
        <button class="btn btn-primary btn-large" on:click={onStartCheckout}>
          <Bone size={24} />
          Criar Certidão Agora
        </button>
        <div class="price-container">
          <p class="price-info">
            De <span class="price-old">R$ 29,90</span> por apenas
            <span class="price">R$ 12,90</span>
          </p>
        </div>
      </div>
    </div>
  </div>
</section>

<style>
  .hero {
    background: linear-gradient(180deg, #fffcf0 0%, #ffffff 100%);
    color: #4a342e;
    padding: 60px 0 100px 0;
    text-align: center;
    position: relative;
    overflow: hidden;
    font-family: "Quicksand", sans-serif;
  }

  .hero::before,
  .hero::after {
    position: absolute;
    color: #ffe8d6;
    opacity: 0.8;
    z-index: 0;
  }

  .hero::before {
    content: "🐾";
    top: 10%;
    left: 8%;
    font-size: 2.5rem;
    animation: floating 4s ease-in-out infinite;
  }

  .hero::after {
    content: "🦴";
    bottom: 15%;
    right: 8%;
    font-size: 2.5rem;
    animation: floating 5s ease-in-out infinite;
  }

  @keyframes floating {
    0% {
      transform: translateY(0px) rotate(0deg);
    }
    50% {
      transform: translateY(-25px) rotate(15deg);
    }
    100% {
      transform: translateY(0px) rotate(0deg);
    }
  }

  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
    position: relative;
    z-index: 1;
  }

  .badge {
    background: #fff0d6;
    color: #f77f00;
    padding: 10px 24px;
    border-radius: 50px;
    font-weight: 700;
    font-size: 0.85rem;
    display: inline-flex;
    align-items: center;
    margin-bottom: 25px;
    text-transform: uppercase;
    letter-spacing: 1px;
    border: 2px solid #ffcc80;
  }

  .hero-title {
    color: #ff9f1c;
    font-size: clamp(2.2rem, 8vw, 3.8rem);
    font-weight: 800;
    margin-bottom: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 15px;
    line-height: 1.1;
    letter-spacing: -1px;
  }

  .title-icon {
    color: #ffbf69;
    display: flex;
    align-items: center;
  }

  .hero-subtitle {
    font-size: 1.25rem;
    color: #6d4c41;
    max-width: 650px;
    margin: 0 auto 48px;
    line-height: 1.6;
    font-weight: 600;
  }

  .video-frame {
    display: inline-block;
    padding: 15px;
    background: white;
    border-radius: 30px;
    box-shadow: 0 20px 50px rgba(255, 159, 28, 0.15);
    margin-bottom: 48px;
    border: 1px solid #ffecb3;
  }

  .video-thumbnail {
    position: relative;
    max-width: 320px;
    margin: 0 auto;
    border-radius: 20px;
    overflow: hidden;
    cursor: pointer;
    aspect-ratio: 9/12;
    background: #fdfdfd;
  }

  .video-thumbnail img {
    width: 100%;
    height: 100%;
    object-fit: contain;
    opacity: 0;
    position: absolute;
    inset: 0;
    transform: scale(1.05);
    transition:
      opacity 0.8s ease,
      transform 0.8s ease;
  }

  .video-thumbnail img.active {
    opacity: 1;
    transform: scale(1);
    position: relative;
  }

  .btn-large {
    padding: 20px 45px;
    font-size: 1.3rem;
    font-weight: 700;
    border-radius: 50px;
    background: linear-gradient(135deg, #ff9f1c 0%, #f77f00 100%);
    box-shadow: 0 10px 25px rgba(247, 127, 0, 0.3);
    color: white;
    border: none;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    gap: 12px;
    font-family: "Quicksand", sans-serif;
    transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  }

  .btn-large:hover {
    transform: scale(1.05) translateY(-5px);
    box-shadow: 0 15px 35px rgba(247, 127, 0, 0.4);
  }

  .price-info {
    font-size: 1.1rem;
    color: #8d6e63;
    font-weight: 600;
    margin-top: 25px;
  }

  .price-old {
    text-decoration: line-through;
    opacity: 0.6;
    font-size: 1rem;
    margin: 0 5px;
  }

  .price {
    font-size: 2rem;
    font-weight: 800;
    color: #f77f00;
    display: block;
    margin-top: 5px;
  }

  .dots {
    position: absolute;
    bottom: 15px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 10px;
    z-index: 5;
  }

  .dots span {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background: rgba(0, 0, 0, 0.1);
    cursor: pointer;
    transition: all 0.3s ease;
  }

  .dots span.active {
    background: #ff9f1c;
    width: 25px;
    border-radius: 10px;
  }

  @media (max-width: 768px) {
    .hero {
      padding: 40px 0 60px 0;
    }
    .hero-title {
      gap: 10px;
    }
    .title-icon {
      transform: scale(0.8);
    }
    .hero-subtitle {
      font-size: 1.1rem;
      padding: 0 15px;
    }
    .video-thumbnail {
      max-width: 280px;
    }
    .btn-large {
      width: 90%;
      font-size: 1.1rem;
      padding: 18px;
    }
  }
</style>
