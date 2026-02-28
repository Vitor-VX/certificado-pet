<script lang="ts">
  import {
    User,
    Phone,
    Mail,
    Calendar,
    MapPin,
    Check,
    Camera,
    ImageIcon,
    CheckCircle2,
    PawPrint,
    Bone,
    Lock,
    Dog,
    ChevronRight,
  } from "lucide-svelte";
  import {
    checkoutStore,
    updateCustomerData,
    updatePersonData,
  } from "$lib/stores/checkoutStore";
  import { track } from "$lib/track/meta";
  import { fade } from "svelte/transition";

  export let onNext: () => void;

  let customerData = {
    name: "",
    whatsapp: "",
    email: "",
  };

  let confirmWhatsapp = false;

  const themes = [
    {
      id: "pet_modern",
      name: "Estilo Moderno",
      preview: "https://files.botsync.site/certificado-pet/Billy.png",
    },
    {
      id: "pet_classic",
      name: "Estilo Clássico",
      preview: "https://files.botsync.site/certificado-pet/Billy.png",
    },
  ];

  $: ({ selectedProduct, people, totalAmount, selectedExtras } =
    $checkoutStore);

  $: hasPhotoExtra = selectedExtras.some(
    (e) => e.id === "with_photo" && e.selected,
  );
  $: hasFullCollection = selectedExtras.some(
    (e) => e.id === "collection" && e.selected,
  );
  $: isWithPhoto = hasPhotoExtra || hasFullCollection;

  function onlyNumbers(value: string) {
    return value.replace(/\D/g, "");
  }

  function handleThemeSelect(index: number, themeId: string) {
    updatePersonData(index, { selectedTheme: themeId });
  }

  function sanitizeString(str: string) {
    if (!str) return "";
    return str
      .toLowerCase()
      .trim()
      .split(" ")
      .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
      .join(" ");
  }

  function formatDisplayDate(value: string) {
    let v = value.replace(/\D/g, "").slice(0, 8);
    if (v.length >= 5) return `${v.slice(0, 2)}/${v.slice(2, 4)}/${v.slice(4)}`;
    if (v.length >= 3) return `${v.slice(0, 2)}/${v.slice(2)}`;
    return v;
  }

  function handlePersonUpdate(index: number, field: string, value: any) {
    let finalValue = value;
    if (field === "startDate") finalValue = formatDisplayDate(value);
    updatePersonData(index, { [field]: finalValue });
  }

  function formatPhone(value: string) {
    return value
      .replace(/\D/g, "")
      .replace(/(\d{2})(\d)/, "($1) $2")
      .replace(/(\d{5})(\d{4})/, "$1-$2");
  }

  function handleSubmit() {
    if (!customerData.email || !customerData.name || !customerData.whatsapp) {
      alert("Por favor, preencha todos os campos obrigatórios.");
      return;
    }

    const isMissingData = people.some((p) => {
      return (
        !p.petName?.trim() ||
        !p.race?.trim() ||
        !p.tutorName?.trim() ||
        !p.cityName?.trim() ||
        !p.stateName?.trim() ||
        !p.startDate?.trim() ||
        !p.photo?.trim()
      );
    });

    if (isMissingData) {
      alert("Preencha todos os dados do seu pet e escolha um modelo.");
      return;
    }

    if (!confirmWhatsapp) {
      alert("Por favor, confirme que seu WhatsApp está correto.");
      return;
    }

    people.forEach((p, index) => {
      const fullLocation = `${sanitizeString(p.cityName)} - ${p.stateName.toUpperCase().trim()}`;
      updatePersonData(index, {
        ...p,
        cityName: fullLocation,
      });
    });

    track("initiate_checkout", { value: totalAmount });
    updateCustomerData({
      ...customerData,
      name: sanitizeString(customerData.name),
      whatsapp: onlyNumbers(customerData.whatsapp),
    });
    onNext();
  }

  function handleFileUpload(index: number, e: Event) {
    const target = e.target as HTMLInputElement;
    if (target.files && target.files[0]) {
      const reader = new FileReader();
      reader.onload = (event) =>
        updatePersonData(index, { photo: event.target?.result as string });
      reader.readAsDataURL(target.files[0]);
    }
  }
</script>

<div class="step-customer">
  <div class="container">
    <header class="main-header">
      <div class="header-icon">
        <Dog size={32} color="#ff9f1c" />
      </div>
      <div class="header-text">
        <h1>Personalize a Certidão</h1>
        <p>Preencha os dados do seu melhor amigo abaixo</p>
      </div>
    </header>

    <div class="form-flow">
      {#each people as person, index}
        <section class="card-section">
          <div class="pet-counter-badge">Pet {index + 1}</div>

          <div class="section-title">
            <PawPrint size={20} />
            <span>Dados do Registro</span>
          </div>

          <div class="input-grid">
            <div class="form-group">
              <label>Nome do Pet</label>
              <input
                type="text"
                placeholder="Ex: Billy"
                value={person.petName || ""}
                on:input={(e) =>
                  handlePersonUpdate(index, "petName", e.currentTarget.value)}
              />
            </div>
            <div class="form-group">
              <label>Raça</label>
              <input
                type="text"
                placeholder="Ex: SRD, Poodle..."
                value={person.race || ""}
                on:input={(e) =>
                  handlePersonUpdate(index, "race", e.currentTarget.value)}
              />
            </div>
          </div>

          <div class="input-grid">
            <div class="form-group">
              <label>Nascimento ou Adoção</label>
              <input
                type="text"
                maxlength="10"
                placeholder="DD/MM/AAAA"
                value={person.startDate || ""}
                on:input={(e) =>
                  handlePersonUpdate(index, "startDate", e.currentTarget.value)}
              />
            </div>
            <div class="form-group">
              <label>Cidade / UF</label>
              <div class="location-inputs">
                <input
                  type="text"
                  placeholder="Cidade"
                  value={person.cityName || ""}
                  on:input={(e) =>
                    handlePersonUpdate(
                      index,
                      "cityName",
                      e.currentTarget.value,
                    )}
                />
                <input
                  type="text"
                  maxlength="2"
                  placeholder="UF"
                  class="uf-input"
                  value={person.stateName || ""}
                  on:input={(e) =>
                    handlePersonUpdate(
                      index,
                      "stateName",
                      e.currentTarget.value,
                    )}
                />
              </div>
            </div>
          </div>

          <div class="form-group">
            <label>Nome do Tutor</label>
            <input
              type="text"
              placeholder="Nome do responsável"
              value={person.tutorName || ""}
              on:input={(e) =>
                handlePersonUpdate(index, "tutorName", e.currentTarget.value)}
            />
          </div>

          <!-- <div class="form-group">
            <label>Escolha o Modelo</label>
            <div class="theme-selector">
              {#each themes as theme}
                <button
                  class="theme-card"
                  class:active={person.selectedTheme === theme.id}
                  on:click={() => handleThemeSelect(index, theme.id)}
                >
                  <div class="preview-box">
                    <img src={theme.preview} alt={theme.name} />
                    {#if person.selectedTheme === theme.id}
                      <div class="check-overlay" in:fade>
                        <CheckCircle2 size={24} fill="#ff9f1c" color="white" />
                      </div>
                    {/if}
                  </div>
                  <span>{theme.name}</span>
                </button>
              {/each}
            </div>
          </div> -->

          {#if !isWithPhoto}
            <div class="form-group">
              <label>Foto do Pet</label>
              <div class="upload-container">
                {#if person.photo}
                  <div class="photo-preview">
                    <img src={person.photo} alt="Preview" />
                    <button
                      class="btn-remove"
                      on:click={() => updatePersonData(index, { photo: "" })}
                      >×</button
                    >
                  </div>
                {:else}
                  <label class="upload-placeholder">
                    <Camera size={28} />
                    <span>Anexar Foto</span>
                    <input
                      type="file"
                      accept="image/*"
                      on:change={(e) => handleFileUpload(index, e)}
                    />
                  </label>
                {/if}
              </div>
            </div>
          {/if}
        </section>
      {/each}

      <section class="card-section">
        <div class="section-title">
          <Mail size={20} />
          <span>Seus Dados de Contato</span>
        </div>
        <div class="form-group">
          <label>Seu Nome Completo</label>
          <input
            type="text"
            bind:value={customerData.name}
            placeholder="Para identificação do pedido"
          />
        </div>
        <div class="input-grid">
          <div class="form-group">
            <label>WhatsApp</label>
            <input
              type="text"
              value={customerData.whatsapp}
              on:input={(e) =>
                (customerData.whatsapp = formatPhone(e.currentTarget.value))}
              placeholder="(00) 00000-0000"
            />
          </div>
          <div class="form-group">
            <label>E-mail</label>
            <input
              type="email"
              bind:value={customerData.email}
              placeholder="seu@email.com"
            />
          </div>
        </div>

        <label class="confirm-box">
          <input type="checkbox" bind:checked={confirmWhatsapp} />
          <div class="check-custom"><Check size={14} strokeWidth={4} /></div>
          <span
            >Confirmo que os dados estão corretos para o envio via WhatsApp.</span
          >
        </label>
      </section>

      <section class="summary-footer card-section">
        <div class="summary-content">
          <div class="summary-info">
            <h3>Resumo do Registro</h3>
            <p>{selectedProduct?.name} • Digital</p>
          </div>
          <div class="summary-price">
            <span class="total-label">Total a pagar:</span>
            <span class="total-value"
              >R$ {totalAmount.toFixed(2).replace(".", ",")}</span
            >
          </div>
        </div>

        <button class="btn-submit" on:click={handleSubmit}>
          <Bone size={20} />
          Finalizar e Gerar Certidão
          <ChevronRight size={20} />
        </button>

        <div class="secure-tag">
          <Lock size={12} />
          <span>Pagamento processado em ambiente 100% seguro</span>
        </div>
      </section>
    </div>
  </div>
</div>

<style>
  .step-customer {
    padding: 40px 0 80px;
    font-family: "Quicksand", sans-serif;
    color: #4a342e;
  }

  .main-header {
    display: flex;
    align-items: center;
    gap: 15px;
    margin-bottom: 40px;
  }

  .header-icon {
    width: 60px;
    height: 60px;
    background: #fff9eb;
    border-radius: 18px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .header-text h1 {
    font-size: 1.8rem;
    font-weight: 800;
    margin: 0;
    color: #4a342e;
  }
  .header-text p {
    margin: 0;
    color: #8d6e63;
    font-weight: 500;
  }

  .form-flow {
    display: flex;
    flex-direction: column;
    gap: 25px;
    max-width: 800px;
  }

  .card-section {
    position: relative;
    background: white;
    border-radius: 24px;
    padding: 30px;
    border: 1px solid #f0f0f0;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.02);
  }

  .section-title {
    display: flex;
    align-items: center;
    gap: 10px;
    color: #ff9f1c;
    font-weight: 700;
    margin-bottom: 25px;
    font-size: 1.1rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-top: 10px;
  }

  .input-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
  }

  .location-inputs {
    display: grid;
    grid-template-columns: 1fr 80px;
    gap: 10px;
  }

  .form-group {
    margin-bottom: 20px;
  }
  .form-group label {
    display: flex;
    font-size: 0.9rem;
    font-weight: 700;
    margin-bottom: 8px;
    color: #4a342e;
  }

  input {
    width: 100%;
    padding: 14px 18px;
    border: 1.5px solid #f0f0f0;
    border-radius: 14px;
    font-family: inherit;
    font-size: 1rem;
    background: #fafafa;
    transition: 0.2s;
    box-sizing: border-box;
  }

  input:focus {
    outline: none;
    border-color: #ff9f1c;
    background: white;
    box-shadow: 0 0 0 4px rgba(255, 159, 28, 0.1);
  }

  .pet-counter-badge {
    position: absolute;
    top: -12px;
    right: 30px;
    background: #ff9f1c;
    color: white;
    padding: 4px 15px;
    border-radius: 50px;
    font-size: 0.85rem;
    font-weight: 700;
    box-shadow: 0 4px 10px rgba(255, 159, 28, 0.3);
  }

  /* THEME SELECTOR */
  .theme-selector {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    gap: 15px;
  }

  .theme-card {
    background: #fafafa;
    border: 1.5px solid #f0f0f0;
    border-radius: 18px;
    padding: 10px;
    cursor: pointer;
    transition: 0.3s;
    text-align: center;
  }

  .theme-card:hover {
    border-color: #ffcc80;
  }
  .theme-card.active {
    border-color: #ff9f1c;
    background: #fffcf5;
  }

  .preview-box {
    width: 100%;
    aspect-ratio: 1;
    border-radius: 12px;
    overflow: hidden;
    margin-bottom: 8px;
    position: relative;
    background: #eee;
  }

  .preview-box img {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }
  .check-overlay {
    position: absolute;
    inset: 0;
    background: rgba(255, 255, 255, 0.4);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .theme-card span {
    font-size: 0.85rem;
    font-weight: 700;
    color: #4a342e;
  }

  .upload-container {
    width: 120px;
  }
  .upload-placeholder {
    width: 100%;
    aspect-ratio: 1;
    border: 2px dashed #ddd;
    border-radius: 18px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    color: #aaa;
    font-size: 0.75rem;
    font-weight: 700;
    transition: 0.2s;
  }
  .upload-placeholder:hover {
    border-color: #ff9f1c;
    color: #ff9f1c;
  }
  .upload-placeholder input {
    display: none;
  }
  .photo-preview {
    position: relative;
    width: 100%;
    aspect-ratio: 1;
    border-radius: 18px;
    overflow: hidden;
  }
  .photo-preview img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  .btn-remove {
    position: absolute;
    top: 5px;
    right: 5px;
    background: #ff4d4d;
    color: white;
    border: none;
    border-radius: 50%;
    width: 20px;
    height: 20px;
    cursor: pointer;
  }

  /* CONFIRMATION */
  .confirm-box {
    display: flex;
    align-items: center;
    gap: 12px;
    cursor: pointer;
    margin-top: 10px;
  }
  .confirm-box input {
    display: none;
  }
  .check-custom {
    width: 22px;
    height: 22px;
    border: 2px solid #ddd;
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    transition: 0.2s;
  }
  .confirm-box input:checked + .check-custom {
    background: #22c55e;
    border-color: #22c55e;
  }
  .confirm-box span {
    font-size: 0.85rem;
    font-weight: 600;
    color: #8d6e63;
    line-height: 1.4;
  }

  /* SUMMARY FOOTER */
  .summary-footer {
    background: #fffcf5;
    border: 2px solid #ffecb3;
  }
  .summary-content {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    margin-bottom: 25px;
  }
  .summary-info h3 {
    margin: 0;
    font-size: 1.2rem;
    font-weight: 800;
  }
  .summary-info p {
    margin: 5px 0 0;
    color: #8d6e63;
    font-weight: 600;
    font-size: 0.9rem;
  }
  .summary-price {
    text-align: right;
  }
  .total-label {
    display: block;
    font-size: 0.8rem;
    font-weight: 700;
    color: #8d6e63;
    text-transform: uppercase;
  }
  .total-value {
    font-size: 2rem;
    font-weight: 800;
    color: #f77f00;
  }

  .btn-submit {
    width: 100%;
    padding: 20px;
    border-radius: 100px;
    border: none;
    background: linear-gradient(135deg, #ff9f1c 0%, #f77f00 100%);
    color: white;
    font-size: 1.2rem;
    font-weight: 800;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 12px;
    box-shadow: 0 10px 25px rgba(247, 127, 0, 0.2);
    transition: 0.3s;
  }
  .btn-submit:hover {
    transform: translateY(-3px);
    box-shadow: 0 15px 30px rgba(247, 127, 0, 0.3);
  }
  .secure-tag {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 6px;
    margin-top: 15px;
    color: #bfa59d;
    font-size: 0.75rem;
    font-weight: 600;
  }

  @media (max-width: 768px) {
    .input-grid {
      grid-template-columns: 1fr;
      gap: 0;
    }
    .card-section {
      padding: 20px;
    }
    .summary-content {
      flex-direction: column;
      align-items: flex-start;
      gap: 15px;
    }
    .summary-price {
      text-align: left;
    }
    .btn-submit {
      font-size: 1.1rem;
    }
  }
</style>
