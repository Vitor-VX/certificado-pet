<script lang="ts">
  import { PawPrint, Timer, ShieldCheck, CheckCircle2 } from "lucide-svelte";
</script>

<section class="benefits">
  <div class="container">
    <h2 class="text-center section-title">
      Por que registrar seu melhor amigo?
    </h2>

    <div class="grid grid-3">
      <div class="benefit-card card">
        <div class="benefit-icon icon-orange">
          <PawPrint size={40} />
        </div>
        <h3>Totalmente Único</h3>
        <p>
          Cada documento é personalizado com o nome do pet, raça, data de
          nascimento ou adoção e uma foto especial.
        </p>
      </div>

      <div class="benefit-card card">
        <div class="benefit-icon icon-yellow">
          <Timer size={40} />
        </div>
        <h3>Receba em segundos</h3>
        <p>
          A certidão digital do seu amiguinho é enviada direto no seu
          <strong>WhatsApp</strong> instantes após o pagamento.
        </p>
      </div>

      <div class="benefit-card card">
        <div class="benefit-icon icon-green">
          <ShieldCheck size={40} />
        </div>
        <h3>Compra 100% Segura</h3>
        <p>
          Pagamento via Pix com confirmação automática e total garantia de
          entrega da nossa plataforma.
        </p>
      </div>
    </div>
  </div>
</section>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Quicksand:wght@500;600;700&display=swap");

  .benefits {
    padding: 100px 0;
    background: #fffcf0;
    position: relative;
    font-family: "Quicksand", sans-serif;
  }

  .section-title {
    color: #ff9f1c;
    font-size: clamp(2rem, 6vw, 3rem);
    font-weight: 800;
    margin-bottom: 60px;
    position: relative;
    letter-spacing: -1px;
  }

  .section-header::after {
    content: "";
    display: block;
    width: 60px;
    height: 4px;
    background: #ffcc80;
    margin: 15px auto 0;
    border-radius: 10px;
  }

  .benefit-card {
    text-align: center;
    border: 1px solid #ffecb3;
    padding: 45px 30px;
    background: #ffffff;
    display: flex;
    flex-direction: column;
    align-items: center;
    border-radius: 30px;
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  }

  .benefit-card:hover {
    border-color: #ff9f1c;
    transform: translateY(-12px);
    box-shadow: 0 20px 40px rgba(255, 159, 28, 0.1);
  }

  .benefit-icon {
    margin-bottom: 24px;
    width: 85px;
    height: 85px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 24px;
    transition: transform 0.5s ease;
  }

  .benefit-card:hover .benefit-icon {
    transform: rotate(10deg) scale(1.1);
  }

  .icon-orange {
    background: #fff0d6;
    color: #ff9f1c;
  }

  .icon-yellow {
    background: #fef9c3;
    color: #facc15;
  }

  .icon-green {
    background: #dcfce7;
    color: #22c55e;
  }

  .benefit-card h3 {
    color: #4a342e;
    font-size: 1.4rem;
    font-weight: 700;
    margin-bottom: 16px;
  }

  .benefit-card p {
    color: #6d4c41;
    font-size: 1rem;
    line-height: 1.6;
    margin-bottom: 0;
    font-weight: 500;
  }

  @media (max-width: 768px) {
    .benefits {
      padding: 60px 0;
    }

    .section-title {
      font-size: 1.8rem;
      margin-bottom: 40px;
      padding: 0 20px;
    }

    .benefit-card {
      padding: 35px 20px;
      margin: 0 10px;
    }
  }
</style>
