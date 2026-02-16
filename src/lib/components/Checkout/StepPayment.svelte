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
      breed: el.breed,
      startDate: el.startDate,
      tutorName: el.tutorName,
      city: el.cityName,
      state: el.stateName,
      photo: el.photo,
      theme: el.selectedTheme,
    }));

    try {
      // const request = await fetch(
      //   "https://vxsoftware.space/api/v1/offers/certificate/orders/create",
      //   {
      //     method: "POST",
      //     headers: { "Content-Type": "application/json" },
      //     body: JSON.stringify({
      //       product: {
      //         plan: "single",
      //         extras: upsell,
      //         certificates,
      //       },
      //       name: customerData.name,
      //       whatsapp: customerData.whatsapp,
      //       email: customerData.email,
      //     }),
      //   },
      // );

      // const response = await request.json();
      // const { payment, token } = response.data;
      // const { qrCode, qrCodeBase64 } = payment;

      setPixData("123", `data:image/png;base64,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`);
      setPaymentStatus("waiting");
      // localStorage.setItem("order-payment", token);
    } catch (err) {
      console.error("Erro ao gerar pagamento:", err);
    }
  }

  async function checkPayment() {
    const token = localStorage.getItem("order-payment");
    if (!token) return;

    const res = await fetch(
      "https://vxsoftware.space/api/v1/offers/certificate/orders/current",
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
                Obrigado por registrar seu melhor amigo conosco.
              </p>

              <div class="delivery-steps">
                <div class="delivery-item">
                  <div class="step-icon">
                    <MessageSquare size={24} color="#25D366" />
                  </div>
                  <div class="step-text">
                    <strong>Confira seu WhatsApp</strong>
                    <span
                      >Enviamos a certidão agora mesmo para o número <b
                        >{customerData.whatsapp}</b
                      >.</span
                    >
                  </div>
                </div>
                <div class="delivery-item">
                  <div class="step-icon">
                    <Mail size={24} color="#ff9f1c" />
                  </div>
                  <div class="step-text">
                    <strong>Cópia por E-mail</strong>
                    <span
                      >Também enviamos uma cópia de segurança para <b
                        >{customerData.email}</b
                      >.</span
                    >
                  </div>
                </div>
              </div>

              <div class="guarantee-box">
                <div class="guarantee-header">
                  <ShieldCheck size={20} />
                  <span>Garantia de Entrega</span>
                </div>
                <p>
                  Fique tranquilo! Caso ocorra qualquer imprevisto técnico no
                  envio, você conta com <strong
                    >reembolso integral automático</strong
                  >.
                </p>
              </div>

              <div class="support-info">
                <AlertCircle size={16} />
                <span
                  >Precisa de ajuda? Entre em contato com nosso suporte.</span
                >
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
    border: 2px solid #dcfce7;
    background: #ffffff;
    border-radius: 35px;
  }
  .success-card h2 {
    color: #166534;
    font-size: 1.8rem;
    font-weight: 800;
    margin-bottom: 10px;
  }
  .main-msg {
    color: #4a342e;
    font-weight: 500;
    margin-bottom: 30px;
  }

  .delivery-steps {
    display: grid;
    gap: 15px;
    text-align: left;
    margin-bottom: 30px;
  }
  .delivery-item {
    display: flex;
    gap: 15px;
    align-items: center;
    padding: 20px;
    background: #fffdf5;
    border-radius: 20px;
    border: 1px solid #ffecb3;
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
    border-radius: 20px;
    text-align: left;
  }
  .guarantee-header {
    display: flex;
    align-items: center;
    gap: 8px;
    color: #dc2626;
    font-weight: 700;
    margin-bottom: 8px;
    font-size: 0.85rem;
    text-transform: uppercase;
  }
  .guarantee-box p {
    font-size: 0.85rem;
    color: #991b1b;
    margin: 0;
    line-height: 1.5;
    font-weight: 500;
  }

  .order-summary {
    position: sticky;
    top: 20px;
    padding: 30px;
    background: white;
    border-radius: 30px;
    border: 1px solid #ffecb3;
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
    padding: 10px;
    border-radius: 12px;
    font-weight: 700;
  }
  .summary-details {
    margin-bottom: 20px;
  }
  .item {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
    font-size: 0.95rem;
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
      padding: 16px;
    }
  }
</style>
