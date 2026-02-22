<script lang="ts">
  import {
    Star,
    PawPrint,
    CheckCircle2,
    ChevronLeft,
    ChevronRight,
    MessageSquare,
    Bone,
  } from "lucide-svelte";

  const reviews = [
    {
      img: "https://files.botsync.site/certificado-pet/print_01.jpeg",
      pet: "Billy",
      tutor: "João Silva",
      tag: "Cachorro",
    },
    {
      img: "https://files.botsync.site/certificado-pet/print_02.jpeg",
      pet: "Mel",
      tutor: "Ana Oliveira",
      tag: "Cachorro",
    },
    {
      img: "https://files.botsync.site/certificado-pet/print_03.jpeg",
      pet: "Thor",
      tutor: "Felipe Souza",
      tag: "Gato",
    },
    {
      img: "https://files.botsync.site/certificado-pet/print_04.jpeg",
      pet: "Luna",
      tutor: "Carla Santos",
      tag: "Coelho",
    },
  ];

  let scrollContainer: HTMLDivElement;

  function scroll(direction: "left" | "right") {
    const amount = 320;
    scrollContainer.scrollBy({
      left: direction === "left" ? -amount : amount,
      behavior: "smooth",
    });
  }
</script>

<svelte:head>
  <link
    href="https://fonts.googleapis.com/css2?family=Quicksand:wght@500;600;700&display=swap"
    rel="stylesheet"
  />
</svelte:head>

<section class="social-proof">
  <div class="container">
    <div class="text-center mb-8">
      <div class="badge-mini">
        <PawPrint size={14} fill="currentColor" />
        <span>Tutores Felizes</span>
      </div>
      <h2 class="section-title">Quem já registrou seu pet</h2>
      <p class="subtitle">
        Veja como ficaram as certidões de alguns dos nossos melhores amigos
      </p>
    </div>

    <div class="carousel-wrapper">
      <button class="nav-btn prev" on:click={() => scroll("left")}>
        <ChevronLeft size={24} />
      </button>

      <div class="carousel-track" bind:this={scrollContainer}>
        {#each reviews as review}
          <div class="proof-card">
            <div class="image-container">
              <img src={review.img} alt="Certificado do pet" />
              <div class="overlay">
                <span class="tag"><Bone size={12} /> {review.tag}</span>
              </div>
            </div>
            <div class="info">
              <div class="stars">
                {#each Array(5) as _}
                  <Star size={14} fill="#ffcc80" color="#ffcc80" />
                {/each}
              </div>
              <div class="verified">
                <CheckCircle2 size={14} />
                <span>Registro Oficial Enviado</span>
              </div>
            </div>
          </div>
        {/each}
      </div>

      <button class="nav-btn next" on:click={() => scroll("right")}>
        <ChevronRight size={24} />
      </button>
    </div>

    <div class="footer-stats">
      <div class="stat">
        <MessageSquare size={20} color="#f77f00" />
        <span>Suporte via WhatsApp</span>
      </div>
      <div class="divider-dot"></div>
      <div class="stat">
        <CheckCircle2 size={20} color="#f77f00" />
        <span>Entrega Garantida</span>
      </div>
    </div>
  </div>
</section>

<style>
  .social-proof {
    padding: 80px 0;
    background: #ffffff;
    overflow: hidden;
    font-family: "Quicksand", sans-serif;
  }

  .badge-mini {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background: #fff0d6;
    color: #f77f00;
    padding: 8px 18px;
    border-radius: 50px;
    font-size: 0.8rem;
    font-weight: 700;
    text-transform: uppercase;
    margin-bottom: 15px;
    border: 1px solid #ffcc80;
  }

  .section-title {
    color: #ff9f1c;
    font-size: clamp(2.2rem, 8vw, 3.5rem);
    margin-bottom: 10px;
    line-height: 1;
    font-weight: 800;
    letter-spacing: -1px;
  }

  .subtitle {
    color: #8d6e63;
    font-size: 1.1rem;
    max-width: 600px;
    margin: 0 auto;
    font-weight: 500;
  }

  .carousel-wrapper {
    position: relative;
    margin-top: 50px;
    padding: 0 40px;
  }

  .carousel-track {
    display: flex;
    gap: 25px;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scrollbar-width: none;
    padding: 20px 0;
  }

  .carousel-track::-webkit-scrollbar {
    display: none;
  }

  .proof-card {
    flex: 0 0 280px;
    scroll-snap-align: start;
    background: white;
    border-radius: 28px;
    overflow: hidden;
    box-shadow: 0 10px 30px rgba(74, 52, 46, 0.05);
    border: 1px solid #ffecb3;
    transition: transform 0.3s ease;
  }

  .proof-card:hover {
    transform: translateY(-8px);
  }

  .image-container {
    position: relative;
    aspect-ratio: 9/12;
    overflow: hidden;
    background: #fdfdfd;
  }

  .image-container img {
    width: 100%;
    height: 100%;
    object-fit: contain;
    padding: 10px;
  }

  .overlay {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    padding: 20px;
    background: linear-gradient(to top, rgba(255, 159, 28, 0.2), transparent);
  }

  .tag {
    background: rgba(255, 255, 255, 0.9);
    color: #f77f00;
    padding: 5px 12px;
    border-radius: 50px;
    font-size: 0.75rem;
    font-weight: 700;
    display: flex;
    align-items: center;
    gap: 5px;
    width: fit-content;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
  }

  .info {
    padding: 20px;
    text-align: left;
  }

  .stars {
    display: flex;
    gap: 2px;
    margin-bottom: 8px;
  }

  .info h3 {
    font-size: 1.15rem;
    color: #4a342e;
    margin-bottom: 5px;
    font-weight: 700;
  }

  .verified {
    display: flex;
    align-items: center;
    gap: 5px;
    color: #22c55e;
    font-size: 0.8rem;
    font-weight: 700;
  }

  .nav-btn {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    width: 45px;
    height: 45px;
    border-radius: 50%;
    background: white;
    border: none;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    z-index: 10;
    color: #ff9f1c;
    transition: all 0.3s;
  }

  .nav-btn:hover {
    background: #ff9f1c;
    color: white;
  }

  .prev {
    left: 0;
  }
  .next {
    right: 0;
  }

  .footer-stats {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 20px;
    margin-top: 50px;
  }

  .stat {
    display: flex;
    align-items: center;
    gap: 8px;
    color: #8d6e63;
    font-weight: 700;
    font-size: 0.9rem;
  }

  .divider-dot {
    width: 6px;
    height: 6px;
    background: #ffcc80;
    border-radius: 50%;
  }

  @media (max-width: 768px) {
    .carousel-wrapper {
      padding: 0;
    }
    .nav-btn {
      display: none;
    }
    .section-title {
      font-size: 2.2rem;
    }
    .proof-card {
      flex: 0 0 260px;
    }
    .footer-stats {
      flex-direction: column;
      gap: 10px;
    }
    .divider-dot {
      display: none;
    }
  }
</style>
