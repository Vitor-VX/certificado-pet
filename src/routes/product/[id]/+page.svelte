<script lang="ts">
    import {
        FileText,
        Download,
        Calendar,
        Clock,
        ShieldCheck,
        PawPrint,
        ExternalLink,
        AlertTriangle,
        Image as ImageIcon,
        Printer,
        MoreVertical,
        Compass,
    } from "lucide-svelte";
    import { onMount } from "svelte";
    import { fade, fly } from "svelte/transition";
    import { page } from "$app/stores";
    import { get } from "svelte/store";

    export let data;
    let id = "";
    let product = null;

    onMount(() => {
        id = get(page).params.id ?? "";
        getPage();
    });

    const getPage = async () => {
        try {
            const request = await fetch(
                `https://vxsoftware.space/api/v1/offers/certificate-pet/slug/${id}`,
            );
            const response = await request.json();

            if (response.success) {
                const data = response.data;

                product = {
                    id: data.id,
                    name: data.name,
                    type: data.type,
                    size: data.size,
                    createAt: new Date(data.createAt as string),
                    downloadUrl: data.downloadUrl,
                    previewUrl:
                        "https://files.botsync.site/certificado-pet/Billy.png",
                };
            }
        } catch (err) {
            console.error("Erro ao buscar o certificado:", err);
        }
    };

    const downloadFile = async () => {
        try {
            const response = await fetch(product.downloadUrl);
            const blob = await response.blob();
            const url = URL.createObjectURL(blob);

            const link = document.createElement("a");
            link.href = url;
            link.download = product.name + "." + product.type;
            document.body.appendChild(link);
            link.click();
            link.remove();

            URL.revokeObjectURL(url);
        } catch (err) {
            console.error("Erro ao baixar arquivo:", err);
            alert("Não foi possível baixar o arquivo. Tente novamente.");
        }
    };

    const daysRemaining = 6;
</script>

<svelte:head>
    <title>Download do Registro - Certidão de PET</title>
    <link
        href="https://fonts.googleapis.com/css2?family=Quicksand:wght@400;500;600;700&display=swap"
        rel="stylesheet"
    />
</svelte:head>

{#if product}
    <main class="download-page">
        <div class="container">
            <!-- TUTORIAL DE NAVEGADOR -->
            <div class="browser-notice" in:fly={{ y: -20, duration: 600 }}>
                <div class="notice-header">
                    <Compass size={24} />
                    <h3>Atenção: Para baixar com sucesso</h3>
                </div>
                <div class="notice-steps">
                    <div class="step">
                        <span class="number">1</span>
                        <p>
                            Toque nos <strong>três pontinhos</strong>
                            <MoreVertical size={16} class="inline-icon" /> no canto
                            superior.
                        </p>
                    </div>
                    <div class="step">
                        <span class="number">2</span>
                        <p>
                            Selecione <strong>"Abrir no Navegador"</strong> (Chrome
                            ou Safari).
                        </p>
                    </div>
                </div>
            </div>

            <header class="page-header">
                <div class="logo">
                    <PawPrint size={32} color="#ff9f1c" />
                    <span>Registro Pet</span>
                </div>
            </header>

            <div class="content" in:fly={{ y: 20, duration: 800 }}>
                <div class="product-card">
                    <div class="expiration-banner">
                        <Clock size={18} />
                        <span
                            >Este link expira em <strong
                                >{daysRemaining} dias</strong
                            >. Garanta seu download!</span
                        >
                    </div>

                    <div class="product-main">
                        <div class="file-preview-container">
                            {#if product.type === "pdf"}
                                <div class="file-icon pdf">
                                    <FileText size={80} />
                                    <span class="file-ext">PDF</span>
                                </div>
                            {:else}
                                <div class="file-icon image">
                                    <ImageIcon size={80} />
                                    <span class="file-ext">IMG</span>
                                </div>
                            {/if}

                            <div class="preview-overlay">
                                <img
                                    src={product.previewUrl}
                                    alt="Preview"
                                    class="mini-preview"
                                />
                            </div>
                        </div>

                        <div class="product-details">
                            <span class="category-tag">Documento Digital</span>
                            <h1>{product.name}</h1>
                            <div class="meta-info">
                                <span
                                    ><Calendar size={14} /> Gerado em {product.createAt.toLocaleDateString(
                                        "pt-BR",
                                    )}</span
                                >
                                <span
                                    ><ShieldCheck size={14} /> Arquivo verificado</span
                                >
                            </div>
                        </div>
                    </div>

                    <div class="action-section">
                        <a
                            href={product.downloadUrl}
                            class="btn-download"
                            on:click|preventDefault={downloadFile}
                        >
                            <Download size={22} />
                            Baixar Meu Certificado
                        </a>
                        <p class="file-size">
                            Tamanho do arquivo: {product.size}
                        </p>
                    </div>

                    <div class="instructions-grid">
                        <div class="instruction-item">
                            <div class="ins-icon"><Printer size={20} /></div>
                            <div class="ins-text">
                                <h4>Dica de Impressão</h4>
                                <p>
                                    Para um resultado profissional, utilize
                                    papel fotográfico ou couchê de gramatura
                                    180g.
                                </p>
                            </div>
                        </div>
                        <div class="instruction-item">
                            <div class="ins-icon">
                                <ExternalLink size={20} />
                            </div>
                            <div class="ins-text">
                                <h4>Compartilhe</h4>
                                <p>
                                    Poste uma foto do seu pet com a certidão e
                                    marque a gente!
                                </p>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="help-box">
                    <AlertTriangle size={20} color="#ff9f1c" />
                    <p>
                        Arquivos são excluídos permanentemente após 7 dias por
                        segurança. Salve-o agora no seu dispositivo.
                    </p>
                </div>
            </div>

            <footer class="page-footer">
                <p>&copy; {new Date().getFullYear()} Registro Pet Oficial.</p>
            </footer>
        </div>
    </main>
{/if}

<style>
    :root {
        --primary: #ff9f1c;
        --secondary: #f77f00;
        --text: #4a342e;
        --text-light: #8d6e63;
        --bg: #fffcf0;
        --white: #ffffff;
    }

    .download-page {
        min-height: 100vh;
        background: var(--bg);
        font-family: "Quicksand", sans-serif;
        color: var(--text);
        padding: 10px 0 40px;
    }

    .container {
        max-width: 800px;
        margin: 0 auto;
        padding: 0 20px;
    }

    .browser-notice {
        background: #ffcc80;
        border-radius: 20px;
        padding: 20px;
        margin-bottom: 30px;
        border: 2px solid #f77f00;
        box-shadow: 0 10px 20px rgba(247, 127, 0, 0.1);
    }

    .notice-header {
        display: flex;
        align-items: center;
        gap: 10px;
        margin-bottom: 15px;
        color: #4a342e;
    }

    .notice-header h3 {
        margin: 0;
        font-size: 1.1rem;
        font-weight: 800;
    }

    .notice-steps {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 15px;
    }

    .step {
        background: rgba(255, 255, 255, 0.4);
        padding: 12px;
        border-radius: 12px;
        display: flex;
        align-items: flex-start;
        gap: 10px;
    }

    .step .number {
        background: #f77f00;
        color: white;
        width: 24px;
        height: 24px;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-weight: 800;
        font-size: 0.8rem;
        flex-shrink: 0;
    }

    .step p {
        margin: 0;
        font-size: 0.85rem;
        font-weight: 600;
        line-height: 1.3;
    }

    .inline-icon {
        display: inline;
        vertical-align: middle;
        background: #f77f00;
        color: white;
        border-radius: 4px;
        padding: 1px;
    }

    .page-header {
        display: flex;
        justify-content: center;
        margin-bottom: 30px;
    }

    .logo {
        display: flex;
        align-items: center;
        gap: 10px;
        font-weight: 800;
        font-size: 1.5rem;
        color: var(--text);
    }

    .product-card {
        background: var(--white);
        border-radius: 40px;
        box-shadow: 0 20px 60px rgba(74, 52, 46, 0.06);
        border: 1px solid #ffecb3;
        overflow: hidden;
    }

    .expiration-banner {
        background: #fff9eb;
        padding: 15px;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 10px;
        color: #f77f00;
        font-size: 0.9rem;
        font-weight: 600;
        border-bottom: 1px solid #ffecb3;
    }

    .product-main {
        padding: 40px;
        display: flex;
        align-items: center;
        gap: 40px;
    }

    .file-preview-container {
        width: 180px;
        height: 240px;
        background: #fdfdfd;
        border-radius: 20px;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        border: 2px solid #f0f0f0;
        position: relative;
    }

    .file-icon.pdf {
        color: #ff6b6b;
    }
    .file-icon.image {
        color: #4dadf7;
    }

    .file-ext {
        font-weight: 800;
        font-size: 1.2rem;
        margin-top: 10px;
    }

    .preview-overlay {
        position: absolute;
        inset: 10px;
        background: white;
        border-radius: 12px;
        overflow: hidden;
        opacity: 0.9;
        border: 1px solid #eee;
    }

    .mini-preview {
        width: 100%;
        height: 100%;
        object-fit: contain;
    }

    .category-tag {
        background: #fff0d6;
        color: var(--secondary);
        padding: 6px 12px;
        border-radius: 50px;
        font-size: 0.75rem;
        font-weight: 700;
        text-transform: uppercase;
        letter-spacing: 1px;
    }

    .product-details h1 {
        font-size: 1.8rem;
        font-weight: 800;
        margin: 15px 0;
        line-height: 1.2;
    }

    .meta-info {
        display: flex;
        flex-direction: column;
        gap: 8px;
        color: var(--text-light);
        font-size: 0.9rem;
        font-weight: 600;
    }

    .meta-info span {
        display: flex;
        align-items: center;
        gap: 6px;
    }

    .action-section {
        padding: 0 40px 40px;
        text-align: center;
    }

    .btn-download {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 12px;
        background: linear-gradient(135deg, #ff9f1c 0%, #f77f00 100%);
        color: white;
        text-decoration: none;
        padding: 22px 40px;
        border-radius: 100px;
        font-size: 1.3rem;
        font-weight: 700;
        box-shadow: 0 15px 30px rgba(247, 127, 0, 0.25);
        transition: 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    .btn-download:hover {
        transform: translateY(-5px);
        box-shadow: 0 20px 40px rgba(247, 127, 0, 0.35);
    }

    .file-size {
        margin-top: 15px;
        font-size: 0.85rem;
        color: var(--text-light);
        font-weight: 600;
    }

    .instructions-grid {
        padding: 30px 40px;
        background: #fafafa;
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 30px;
        border-top: 1px solid #f0f0f0;
    }

    .instruction-item {
        display: flex;
        gap: 15px;
    }

    .ins-icon {
        width: 40px;
        height: 40px;
        background: white;
        border-radius: 12px;
        display: flex;
        align-items: center;
        justify-content: center;
        color: var(--primary);
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
        flex-shrink: 0;
    }

    .ins-text h4 {
        margin: 0 0 5px;
        font-size: 1rem;
        font-weight: 700;
    }
    .ins-text p {
        margin: 0;
        font-size: 0.85rem;
        color: var(--text-light);
        line-height: 1.4;
        font-weight: 500;
    }

    .help-box {
        margin-top: 30px;
        padding: 20px;
        background: #fff9eb;
        border-radius: 24px;
        display: flex;
        align-items: center;
        gap: 15px;
        border: 1px solid #ffecb3;
    }

    .help-box p {
        margin: 0;
        font-size: 0.85rem;
        line-height: 1.5;
        color: #8d6e63;
        font-weight: 600;
    }

    .page-footer {
        margin-top: 40px;
        text-align: center;
        color: var(--text-light);
        font-size: 0.85rem;
    }

    @media (max-width: 768px) {
        .notice-steps {
            grid-template-columns: 1fr;
        }
        .product-main {
            flex-direction: column;
            text-align: center;
            padding: 30px 20px;
        }
        .file-preview-container {
            width: 140px;
            height: 190px;
        }
        .product-details h1 {
            font-size: 1.5rem;
        }
        .instructions-grid {
            grid-template-columns: 1fr;
            padding: 30px 20px;
        }
        .action-section {
            padding: 0 20px 30px;
        }
        .btn-download {
            font-size: 1.1rem;
            padding: 18px;
        }
    }
</style>
