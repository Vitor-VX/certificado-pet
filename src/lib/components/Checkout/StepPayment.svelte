<script lang="ts">
  import { onDestroy, onMount } from "svelte";
  import {
    Copy,
    CircleCheck as CheckCircle,
    Clock,
    Lock,
    Sparkles,
    MessageSquare,
    Mail,
    ShieldCheck,
    AlertCircle,
    PawPrint,
    Bone,
    Dog,
  } from "lucide-svelte";
  import {
    checkoutStore,
    setPaymentStatus,
    setPixData,
  } from "$lib/stores/checkoutStore";
  import { track } from "$lib/track/meta";

  export let onComplete: () => void;

  $: ({
    selectedProduct,
    selectedExtras,
    people,
    customerData,
    totalAmount,
    paymentStatus,
    pixCode,
    pixQrCode,
  } = $checkoutStore);

  let copySuccess = false;
  let paymentInterval: any = null;

  function startPaymentWatcher() {
    if (paymentInterval) return;
    paymentInterval = setInterval(async () => {
      await checkPayment();
    }, 20000);
  }

  function stopPaymentWatcher() {
    if (paymentInterval) {
      clearInterval(paymentInterval);
      paymentInterval = null;
      localStorage.removeItem("order-payment");
    }
  }

  onMount(() => {
    generatePixPayment();
    startPaymentWatcher();
  });

  onDestroy(() => {
    stopPaymentWatcher();
  });

  async function generatePixPayment() {
    setPaymentStatus("generating");

    const upsell = selectedExtras
      .filter((el) => el.selected)
      .map((el) => el.id);

    const certificates = people.map((el) => ({
      petName: el.petName,
      race: el.race,
      startDate: el.startDate,
      tutorName: el.tutorName,
      city: el.cityName,
      photo: el.photo,
      theme: "vintage",
    }));

    // console.log(certificates);
    const planSelected = selectedProduct.id;

    try {
      const request = await fetch(
        "https://vxsoftware.space/api/v1/offers/certificate-pet/orders/create",
        {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            product: {
              plan: planSelected,
              extras: upsell,
              certificates,
            },
            name: customerData.name,
            whatsapp: customerData.whatsapp,
            email: customerData.email,
          }),
        },
      );

      const response = await request.json();
      const { payment, token } = response.data;
      const { qrCode, qrCodeBase64 } = payment;

      setPixData(qrCode, `data:imagepng;base64,${qrCodeBase64}`);
      setPaymentStatus("waiting");
      localStorage.setItem("order-payment", token);
    } catch (err) {
      console.error("Erro ao gerar pagamento:", err);
    }
  }

  async function checkPayment() {
    const token = localStorage.getItem("order-payment");
    if (!token) return;

    const res = await fetch(
      "https://vxsoftware.space/api/v1/offers/certificate-pet/orders/current",
      {
        headers: { Authorization: `Bearer ${token}` },
      },
    );

    const data = await res.json();
    if (data.data.status === "approved") {
      stopPaymentWatcher();
      setPaymentStatus("paid");
      track("purchase", { value: totalAmount });
    }
  }

  function copyPixCode() {
    navigator.clipboard.writeText(pixCode).then(() => {
      copySuccess = true;
      setTimeout(() => (copySuccess = false), 2000);
    });
  }
</script>

<svelte:head>
  <link
    href="https://fonts.googleapis.com/css2?family=Quicksand:wght@400;500;600;700&display=swap"
    rel="stylesheet"
  />
</svelte:head>

<div class="step-payment">
  <div class="container">
    <div class="content-wrapper">
      <div class="payment-section">
        {#if paymentStatus !== "paid"}
          <div class="section-header">
            <h2>
              {paymentStatus === "generating"
                ? "Preparando o registro..."
                : "Falta pouco para o registro!"}
            </h2>
            <p>Realize o pagamento para liberarmos a certidão do seu pet.</p>
          </div>
        {/if}

        {#if paymentStatus === "generating"}
          <div class="loading-state">
            <div class="paw-loader">
              <PawPrint size={48} fill="#ff9f1c" color="#ff9f1c" />
            </div>
            <h3>Gerando QR Code Pix...</h3>
          </div>
        {:else if paymentStatus === "waiting"}
          <div class="payment-content">
            <div class="qr-section card">
              <div class="badge-pix">
                <Sparkles size={14} />
                Pix Instantâneo
              </div>
              <img src={pixQrCode} alt="QR Code PIX" class="qr-code" />
              <div class="pix-code-section">
                <label>Pix Copia e Cola</label>
                <div class="code-container">
                  <input type="text" value={pixCode} readonly />
                  <button
                    class="copy-btn"
                    on:click={copyPixCode}
                    class:success={copySuccess}
                  >
                    {#if copySuccess}<CheckCircle size={18} />{:else}<Copy
                        size={18}
                      />{/if}
                    {copySuccess ? "Copiado" : "Copiar"}
                  </button>
                </div>
              </div>

              <div class="instructions">
                <h4>Como pagar:</h4>
                <ul>
                  <li>
                    Acesse o app do seu banco e escolha <strong>Pix</strong>.
                  </li>
                  <li>
                    Escaneie o QR Code ou use o <strong>Copia e Cola</strong>.
                  </li>
                  <li>
                    Valor exato: <span class="highlight"
                      >R$ {totalAmount.toFixed(2).replace(".", ",")}</span
                    >
                  </li>
                </ul>
              </div>
            </div>

            <div class="status-waiting">
              <div class="loader-dots">
                <span></span><span></span><span></span>
              </div>
              <h4>Aguardando confirmação...</h4>
              <p>O sistema identifica seu pagamento automaticamente.</p>
            </div>
          </div>
        {:else if paymentStatus === "paid"}
          <div class="success-container animate-in">
            <div class="success-card card">
              <div class="icon-header">
                <CheckCircle size={64} color="#22c55e" strokeWidth={1.5} />
              </div>

              <h2>Registro Confirmado!</h2>

              <p class="main-msg">
                O registro do seu pet foi realizado com sucesso. Já iniciamos o
                processo de envio da sua certidão personalizada.
              </p>

              <div class="delivery-steps">
                <div class="delivery-item">
                  <div class="step-icon">
                    <MessageSquare size={24} color="#25D366" />
                  </div>
                  <div class="step-text">
                    <strong>Entrega principal via WhatsApp</strong>
                    <span>
                      Sua certidão foi enviada para o número
                      <b>{customerData.whatsapp}</b>. Verifique suas conversas e
                      também a pasta de mensagens arquivadas.
                    </span>
                  </div>
                </div>

                <div class="delivery-item">
                  <div class="step-icon">
                    <Mail size={24} color="#ff9f1c" />
                  </div>
                  <div class="step-text">
                    <strong>Entrega alternativa por e-mail</strong>
                    <span>
                      Caso não seja possível realizar a entrega via WhatsApp,
                      enviaremos automaticamente para o e-mail
                      <b>{customerData.email}</b>. É importante verificar também
                      sua
                      <strong>caixa de spam</strong> e garantir que seu e-mail tenha
                      espaço disponível.
                    </span>
                  </div>
                </div>
              </div>

              <div class="guarantee-box">
                <div class="guarantee-header">
                  <ShieldCheck size={20} />
                  <span>Garantia Total de Entrega ou Reembolso</span>
                </div>

                <p>
                  Trabalhamos para garantir que você receba sua certidão com
                  segurança. Caso não seja possível realizar a entrega nem pelo
                  WhatsApp nem pelo e-mail, seja por motivos como <strong
                    >caixa de entrada cheia</strong
                  >,
                  <strong>indisponibilidade do provedor</strong> ou outros
                  problemas técnicos, realizaremos o
                  <strong>reembolso automático e integral em até 1 dia</strong>.
                </p>

                <p>
                  Se houver qualquer dificuldade no recebimento do reembolso ou
                  se precisar de suporte, basta entrar em contato com nosso <strong
                    >suporte oficial no Instagram</strong
                  >, e nossa equipe resolverá rapidamente para você.
                </p>
              </div>

              <div class="support-info">
                <AlertCircle size={16} />
                <span>
                  Nosso suporte está disponível para garantir que você tenha uma
                  experiência segura e sem riscos.
                </span>
              </div>
            </div>
          </div>
        {/if}
      </div>

      <div class="summary-side">
        <div class="order-summary card">
          <div class="secure-checkout">
            <Lock size={14} /> Checkout 100% Seguro
          </div>
          <h3>Resumo do Pedido</h3>
          <div class="summary-details">
            <div class="item">
              <span class="label">Plano:</span>
              <span class="val">{selectedProduct?.name}</span>
            </div>
            <div class="item">
              <span class="label">Pet:</span>
              <span class="val">{people[0]?.petName || "Seu amiguinho"}</span>
            </div>
          </div>
          <div class="summary-divider"></div>
          <div class="summary-total">
            <span>Total:</span>
            <span class="total-price"
              >R$ {totalAmount.toFixed(2).replace(".", ",")}</span
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .step-payment {
    max-width: 1000px;
    margin: 0 auto;
    padding-bottom: 50px;
    font-family: "Quicksand", sans-serif;
  }
  .content-wrapper {
    display: grid;
    grid-template-columns: 1.6fr 1fr;
    gap: 30px;
  }
  .section-header h2 {
    color: #4a342e;
    font-size: 2rem;
    font-weight: 800;
    margin-bottom: 8px;
  }
  .section-header p {
    color: #8d6e63;
    font-weight: 500;
  }
  .loading-state {
    text-align: center;
    padding: 50px 0;
  }
  .support-info {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-top: 25px;
    color: #a0aec0;
    font-size: 0.75rem;
    justify-content: center;
  }
  .paw-loader {
    animation: bounce 1s infinite alternate;
    margin-bottom: 20px;
  }
  @keyframes bounce {
    from {
      transform: translateY(0);
    }
    to {
      transform: translateY(-15px);
    }
  }

  .qr-section {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 30px;
    background: white;
    border: 2px solid #ffecb3;
    border-radius: 30px;
    text-align: center;
  }
  .badge-pix {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    background: #fff9eb;
    color: #ff9f1c;
    padding: 6px 16px;
    border-radius: 50px;
    font-size: 0.8rem;
    font-weight: 700;
    margin-bottom: 20px;
    text-transform: uppercase;
  }
  .qr-code {
    width: 220px;
    height: 220px;
    border: 1px solid #eee;
    border-radius: 20px;
    margin-bottom: 25px;
    padding: 10px;
    background: white;
  }
  .code-container {
    display: flex;
    gap: 10px;
    margin-top: 10px;
  }
  .code-container input {
    flex: 1;
    padding: 14px;
    border: 1.5px solid #eee;
    border-radius: 14px;
    font-size: 0.85rem;
    color: #4a342e;
    background: #fafafa;
  }
  .copy-btn {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 0 20px;
    background: #ff9f1c;
    color: white;
    border-radius: 14px;
    cursor: pointer;
    border: none;
    font-weight: 700;
    transition: 0.2s;
  }
  .copy-btn.success {
    background: #22c55e;
  }
  .instructions {
    text-align: left;
    margin-top: 30px;
    background: #fffdf9;
    padding: 20px;
    border-radius: 20px;
    width: 100%;
    box-sizing: border-box;
  }
  .instructions h4 {
    font-size: 0.95rem;
    color: #4a342e;
    margin-bottom: 12px;
  }
  .instructions ul {
    list-style: none;
    padding: 0;
    font-size: 0.9rem;
    color: #6d4c41;
  }
  .instructions li {
    margin-bottom: 10px;
    position: relative;
    padding-left: 25px;
  }
  .instructions li::before {
    content: "🐾";
    position: absolute;
    left: 0;
    font-size: 0.8rem;
  }
  .highlight {
    color: #f77f00;
    font-weight: 800;
  }

  .status-waiting {
    padding: 20px;
    text-align: center;
  }
  .loader-dots span {
    display: inline-block;
    width: 8px;
    height: 8px;
    background: #ffcc80;
    border-radius: 50%;
    margin: 0 4px;
    animation: dots 1.4s infinite;
  }
  .loader-dots span:nth-child(2) {
    animation-delay: 0.2s;
  }
  .loader-dots span:nth-child(3) {
    animation-delay: 0.4s;
  }
  @keyframes dots {
    0%,
    100% {
      opacity: 0.3;
    }
    50% {
      opacity: 1;
    }
  }

  .success-card {
    padding: 40px;
    text-align: center;
    border: 2px solid #e6fffa;
    background: #ffffff;
    border-radius: 30px;
  }
  .icon-header {
    margin-bottom: 20px;
  }
  .success-card h2 {
    color: #1a202c;
    font-size: 2rem;
    margin-bottom: 10px;
  }
  .main-msg {
    color: #4a5568;
    margin-bottom: 30px;
  }
  .delivery-steps {
    display: grid;
    gap: 20px;
    text-align: left;
    margin-bottom: 40px;
  }
  .delivery-item {
    display: flex;
    gap: 15px;
    align-items: flex-start;
    padding: 15px;
    background: #f8fafc;
    border-radius: 15px;
  }
  .step-text strong {
    display: block;
    color: #4a342e;
    font-size: 1rem;
  }
  .step-text span {
    font-size: 0.85rem;
    color: #8d6e63;
    line-height: 1.4;
  }
  .guarantee-box {
    background: #fef2f2;
    border: 1px solid #fee2e2;
    padding: 20px;
    border-radius: 15px;
    text-align: left;
  }
  .guarantee-header {
    display: flex;
    align-items: center;
    gap: 8px;
    color: #dc2626;
    font-weight: 700;
    margin-bottom: 8px;
    font-size: 0.9rem;
    text-transform: uppercase;
  }
  .guarantee-box p {
    font-size: 0.85rem;
    color: #991b1b;
    margin: 0;
    line-height: 1.5;
  }
  .support-info {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-top: 25px;
    color: #a0aec0;
    font-size: 0.75rem;
    justify-content: center;
  }
  .order-summary {
    position: sticky;
    top: 20px;
    padding: 30px;
    background: white;
    border-radius: 20px;
  }
  .secure-checkout {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    font-size: 0.8rem;
    color: #166534;
    margin-bottom: 20px;
    background: #f0fff4;
    padding: 6px;
    border-radius: 6px;
    font-weight: 600;
  }
  .summary-total {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-weight: 700;
    margin-top: 20px;
  }
  .total-price {
    font-size: 2rem;
    font-weight: 800;
    color: #f77f00;
  }

  @media (max-width: 768px) {
    .content-wrapper {
      grid-template-columns: 1fr;
    }
    .summary-side {
      order: -1;
    }
    .code-container {
      flex-direction: column;
    }
    .copy-btn {
      padding: 15px;
    }
    .success-card {
      padding: 20px;
    }
  }
</style>
