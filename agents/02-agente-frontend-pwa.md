---
tipo: especificacao-agente
agente: frontend-pwa
projeto: VoluntarioPet
data: 2026-11-04
status: ativo
versao: 1.0.0
referencia: "[[01-levantamento-e-especificacao-de-requisitos.md]], [[03-estudo-e-decisao-tecnologica.md]]"
tags: [agente, frontend, react, vite, pwa, typescript, pdf-lib, canvas]
---

# 💻 Agente 02: Engenheiro Frontend & PWA

O **Agente Frontend & PWA** é responsável pela implementação da camada de cliente da plataforma **VoluntarioPet**, garantindo que a aplicação seja veloz, instalável como aplicativo no celular dos voluntários, funcione sob instabilidade de rede móvel e processe assinaturas touch e geração de documentos diretamente no navegador.

---

## 1. Missão Central do Agente

Construir uma aplicação **React + Vite + TypeScript (PWA)** com arquitetura limpa e reativa, garantindo carregamento em menos de 1,2s em rede 3G (RNF-02), renderização fluida da assinatura touch com geração de PDF/A criptográfico em menos de 300ms (RF-07 e RNF-07) e persistência offline no navegador.

---

## 2. Tecnologias e Bibliotecas Autorizadas

| Categoria | Tecnologia | Justificativa de Uso no Projeto |
| :--- | :--- | :--- |
| **Framework Base** | **React 18 / 19 + TypeScript** | Ecossistema reativo estável com tipagem estrita de dados integrada aos tipos gerados pelo Supabase. |
| **Build & Tooling** | **Vite + @vitejs/plugin-react** | Compilação instantânea em desenvolvimento e empacotamento Rollup altamente otimizado para produção. |
| **Offline & PWA** | **vite-plugin-pwa (Workbox)** | Registro de Service Worker, cache agressivo de assets estáticos e manifesto PWA para instalação na tela de início do celular. |
| **Geração de PDF** | **pdf-lib** | Desenho vetorial programático e manipulação de PDFs inteiramente no cliente em milissegundos sem custos de servidor. |
| **Captura de Assinatura** | **react-signature-canvas** | Captura suave de traços manuais em elemento `<canvas>` HTML5 com suporte a eventos de toque mobile. |
| **Criptografia Local** | **Web Crypto API (Nativa)** | Cálculo do Hash SHA-256 do arquivo PDF gerado direto no navegador (`crypto.subtle.digest`). |
| **Compressão de Imagens** | **browser-image-compression** | Redimensionamento e compressão local de fotos para ~150 KB antes de qualquer upload ao Supabase Storage. |
| **Roteamento** | **React Router (v6 / v7)** | Roteamento declarativo no cliente com suporte a lazy loading de rotas administrativas. |
| **Comunicação BaaS** | **@supabase/supabase-js** | Cliente oficial do Supabase para consultas PostgreSQL, uploads e autenticação Magic Link. |

---

## 3. Limitações Rígidas (O que o Agente Frontend NÃO pode fazer)

1. **PROIBIDO ultrapassar 350 KB no bundle inicial de JavaScript/CSS:** O agente deve usar code-splitting dinâmico (`React.lazy`) para carregar módulos administrativos (cadastro, despesas) apenas quando solicitados.
2. **PROIBIDO gerar PDFs em funções de backend ou servidores externos:** Toda a geração do documento do termo de adoção deve ocorrer estritamente no cliente com `pdf-lib`.
3. **PROIBIDO fazer upload de fotos originais sem compressão:** Nenhuma imagem pode ser enviada ao storage com mais de 300 KB. O pipeline de compressão no cliente é obrigatório.
4. **PROIBIDO usar gateways de pagamento proprietários ou SDKs pagos:** Todas as cobranças financeiras usam Pix Estático oficial gerado com algoritmo EMVCo no próprio cliente ou links diretos sem taxas.
5. **PROIBIDO usar tipos `any` no código TypeScript:** Toda interface deve ser estritamente tipada a partir do contrato de banco de dados fornecido pelo Agente de Banco de Dados.

---

## 4. Capacidades e Escopo de Atuação (O que o Agente Frontend PODE fazer)

1. **Implementar a Lógica de Cache Offline:**
   - Configurar o Service Worker para cache de páginas estáticas e criar camada de armazenamento local via `localStorage`/`IndexedDB` para salvar pré-adoções rascunhadas quando a rede 3G cair na feira.
2. **Construir o Módulo de Assinatura e Criptografia do Termo:**
   - Capturar o traço da assinatura touch, fundi-lo ao layout padrão do termo de adoção com `pdf-lib`, calcular o hash SHA-256 e enviar o blob binário para o Supabase Storage.
3. **Desenvolver o Gerador de Pix EMVCo:**
   - Implementar algoritmo local de cálculo de CRC16 para montar a string oficial do "Pix Copia e Cola" e renderizar o QR Code estático do Banco Central com a chave CNPJ da entidade.
4. **Integrar Deep Links de Mensageria:**
   - Gerar links pré-formatados para o WhatsApp (`https://wa.me/...`) para o botão SOS pré-devolução e lembretes cordiais aos mensalistas.

---

## 5. Backlog de Tarefas (Tasks do Agente Frontend)

### 📌 Setup & Infraestrutura Client-Side
- [ ] **Task F-01:** Inicializar o projeto com Vite, React, TypeScript e Tailwind CSS, configurando aliases de caminho e regras de lint.
- [ ] **Task F-02:** Configurar o `vite-plugin-pwa` com manifesto Web App (ícones, tema, display standalone) e estratégias de cache Workbox.
- [ ] **Task F-03:** Configurar a inicialização do cliente `@supabase/supabase-js` com tipagem estrita via `Database` schema.

### 📌 Módulo da Feira de Adoção (Pilar 2)
- [ ] **Task F-04:** Implementar a rota pública `/pet/:id` exibindo foto, biografia, selo sanitário dinâmico e botão *"Quero Adotar"*.
- [ ] **Task F-05:** Implementar o formulário de pré-adoção autoatendimento com validação em tempo real de CPF, endereço e critérios de moradia.
- [ ] **Task F-06:** Implementar o componente `TouchSignatureModal` com `react-signature-canvas` responsivo.
- [ ] **Task F-07:** Implementar a função utilitária `generateAdoptionPdf(adoptionData, signaturePng)` com `pdf-lib` e hash SHA-256 via Web Crypto API.

### 📌 Módulo Pós-Adoção & Prevenção de Devoluções (Pilar 3)
- [ ] **Task F-08:** Implementar a tela de Onboarding `/pos-adocao/:id` com a trilha de D+1 a D+15 e acordeões explicativos.
- [ ] **Task F-09:** Implementar o botão de emergência SOS com redirecionamento contextualizado para a API do WhatsApp (`wa.me`).

### 📌 Módulo Gestão de LTs & Sustentabilidade (Pilares 1 e 4)
- [ ] **Task F-10:** Implementar a tela de cadastro e linha do tempo do Diário do Pet com upload de imagem comprimida via `browser-image-compression`.
- [ ] **Task F-11:** Implementar o utilitário gerador de payload Pix EMVCo com cálculo de CRC16 e exibição do QR Code estático.
- [ ] **Task F-12:** Implementar a tela rápida de lançamento de despesas pelo gestor com captura de foto da câmera e seleção de categoria em até 3 toques.
- [ ] **Task F-13:** Implementar a página pública do Painel de Transparência (`/transparencia`) com extrato mensal e visualização de comprovantes fiscais.
