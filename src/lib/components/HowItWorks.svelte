<script lang="ts">
  import {
    LayoutList,
    FileSignature,
    QrCode,
    Smartphone,
    PawPrint,
    Bone,
  } from "lucide-svelte";

  export let onStartCheckout: () => void;
</script>

<section class="how-it-works">
  <div class="container">
    <h2 class="text-center section-title">Passo a passo para o registro</h2>

    <div class="steps">
      <div class="step">
        <div class="step-number">
          <PawPrint size={14} fill="white" />
          <span>1</span>
        </div>
        <div class="step-icon">
          <LayoutList size={32} color="#ff9f1c" />
        </div>
        <h3>Escolha o plano</h3>
        <p>
          Selecione quantas certidões você deseja criar para seus pets ou para
          presentear.
        </p>
      </div>

      <div class="step-connector"></div>

      <div class="step">
        <div class="step-number">
          <PawPrint size={14} fill="white" />
          <span>2</span>
        </div>
        <div class="step-icon">
          <FileSignature size={32} color="#ff9f1c" />
        </div>
        <h3>Personalize tudo</h3>
        <p>
          Informe o nome do pet, raça, data de nascimento, nome do tutor e a
          cidade/estado.
        </p>
      </div>

      <div class="step-connector"></div>

      <div class="step">
        <div class="step-number">
          <PawPrint size={14} fill="white" />
          <span>3</span>
        </div>
        <div class="step-icon">
          <QrCode size={32} color="#ff9f1c" />
        </div>
        <h3>Pagamento Pix</h3>
        <p>
          Finalize com Pix de forma instantânea e 100% segura pelo nosso
          sistema.
        </p>
      </div>

      <div class="step-connector"></div>

      <div class="step">
        <div class="step-number">
          <PawPrint size={14} fill="white" />
          <span>4</span>
        </div>
        <div class="step-icon">
          <Smartphone size={32} color="#ff9f1c" />
        </div>
        <h3>Receba na hora</h3>
        <p>
          A certidão digital chega direto no seu WhatsApp em poucos minutos,
          pronta para salvar.
        </p>
      </div>
    </div>

    <div class="text-center mt-8">
      <button class="btn btn-primary btn-large" on:click={onStartCheckout}>
        <Bone size={20} />
        Quero registrar meu pet agora
      </button>
    </div>
  </div>
</section>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Quicksand:wght@500;600;700&display=swap");

  .how-it-works {
    padding: 100px 0;
    background: #ffffff;
    background-image: linear-gradient(#fffcf0 1px, transparent 1px),
      linear-gradient(90deg, #fffcf0 1px, transparent 1px);
    background-size: 40px 40px;
    font-family: "Quicksand", sans-serif;
  }

  .section-title {
    color: #ff9f1c;
    font-size: clamp(2rem, 6vw, 3rem);
    font-weight: 800;
    margin-bottom: 80px;
    letter-spacing: -1px;
  }

  .steps {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    max-width: 1100px;
    margin: 0 auto;
    position: relative;
  }

  .step {
    flex: 1;
    text-align: center;
    position: relative;
    padding: 0 10px;
    z-index: 1;
  }

  .step-number {
    position: absolute;
    top: -15px;
    left: 50%;
    transform: translateX(-50%);
    background: #ff9f1c;
    color: white;
    padding: 8px 14px;
    border-radius: 20px;
    display: flex;
    align-items: center;
    gap: 6px;
    font-weight: 700;
    font-size: 14px;
    box-shadow: 0 4px 10px rgba(255, 159, 28, 0.3);
  }

  .step-icon {
    background: #fff9eb;
    padding: 24px;
    border-radius: 24px;
    margin: 20px auto 24px;
    width: fit-content;
    border: 2px solid #ffecb3;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .step:hover .step-icon {
    transform: rotate(5deg) scale(1.05);
    background: #fff0d6;
    border-color: #ff9f1c;
  }

  .step-connector {
    height: 2px;
    background: #ffcc80;
    background-image: linear-gradient(to right, #ffcc80 50%, transparent 50%);
    background-size: 10px 1px;
    flex: 1;
    margin-top: 65px;
    opacity: 0.6;
  }

  .step h3 {
    font-size: 1.25rem;
    color: #4a342e;
    font-weight: 700;
    margin-bottom: 12px;
  }

  .step p {
    color: #6d4c41;
    font-size: 0.95rem;
    line-height: 1.5;
    font-weight: 500;
  }

  .btn-large {
    padding: 20px 40px;
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
    transition: all 0.3s ease;
  }

  .btn-large:hover {
    transform: scale(1.02);
    box-shadow: 0 15px 30px rgba(247, 127, 0, 0.4);
  }

  @media (max-width: 992px) {
    .step-connector {
      display: none;
    }
  }

  @media (max-width: 768px) {
    .how-it-works {
      padding: 60px 0;
    }

    .steps {
      flex-direction: column;
      gap: 50px;
    }

    .step {
      width: 100%;
    }

    .step-number {
      position: relative;
      top: 0;
      margin-bottom: -10px;
      display: inline-flex;
    }

    .step-icon {
      margin-top: 0;
    }

    .btn-large {
      width: 90%;
      padding: 18px;
    }
  }
</style>
