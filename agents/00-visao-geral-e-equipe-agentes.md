---
tipo: governanca-agentes
projeto: VoluntarioPet
data: 2026-11-04
status: homologado
versao: 1.0.0
referencia: "[[01-levantamento-e-especificacao-de-requisitos.md]], [[02-diagrama-casos-de-uso.md]], [[03-estudo-e-decisao-tecnologica.md]]"
tags: [agentes, equipe, arquitetura, governanca, papéis, voluntariadopet]
---

# 🤖 Equipe Especializada de Agentes Autônomos — VoluntarioPet

Este documento estabelece a divisão de trabalho, fronteiras operacionais, interfaces de comunicação e atribuições dos agentes especializados que atuarão na concepção, implementação e validação da plataforma **VoluntarioPet**.

A divisão é orientada estritamente pelo escopo dos **4 Pilares Centrais** e pela stack tecnológica oficial (**TypeScript + React/Vite PWA + Tailwind/Radix + Supabase + Cloudflare Pages**).

---

## 1. Organograma e Especialização dos Agentes

```
                     ┌──────────────────────────────────────────────┐
                     │         ORQUESTRADOR / TECH LEAD             │
                     │  (Governança, Escopo e Deploy Cloudflare)    │
                     └──────────────────────┬───────────────────────┘
                                            │
        ┌───────────────────────────────────┼───────────────────────────────────┐
        │                                   │                                   │
        ▼                                   ▼                                   ▼
┌──────────────────┐               ┌──────────────────┐               ┌──────────────────┐
│ AGENTE 01:       │               │ AGENTE 02:       │               │ AGENTE 03:       │
│ DESIGN UI/UX     │ ────────────► │ FRONTEND & PWA   │ ◄───────────► │ BACKEND & DADOS  │
│ Acessibilidade   │  Design Specs │ Vite, Touch & PDF│  Schema/Types │ Supabase, RLS    │
└──────────────────┘               └──────────────────┘               └──────────────────┘
        │                                   │                                   │
        └───────────────────────────────────┼───────────────────────────────────┘
                                            │
                                            ▼
                                   ┌──────────────────┐
                                   │ AGENTE 04:       │
                                   │ QA & CONFORMIDADE│
                                   │ 3G, LGPD, Testes │
                                   └──────────────────┘
```

---

## 2. Matriz de Papéis e Responsabilidades

| Agente | Missão Central | Tecnologias Chave | Entregáveis Principais |
| :--- | :--- | :--- | :--- |
| **01. Agente de Design UI/UX & Acessibilidade** | Criar o Design System mobile-first com ergonomia para feira e idosos (WCAG 2.1 AA). | Figma/Pencil, Tailwind CSS tokens, Radix UI, Lucide Icons | Tokens de design, guia de componentes, wireframes e fluxos visuais de feira e pós-adoção. |
| **02. Agente Frontend & PWA** | Construir a aplicação web client-side reativa, offline-first e com assinatura digital instantânea. | React 19/18, Vite, TypeScript, `pdf-lib`, `react-signature-canvas`, Web Crypto | Telas mobile interativas, PWA com cache offline, módulo de assinatura e geração de PDF com Hash SHA-256. |
| **03. Agente Backend & Banco de Dados** | Modelar o banco PostgreSQL, blindar dados na LGPD com RLS e configurar o BaaS Supabase. | PostgreSQL, Supabase (SQL, Auth Magic Link, Storage, Triggers) | Scripts DDL de migração, políticas de Row Level Security (RLS), baldes de storage seguros e tipos TS. |
| **04. Agente QA & Conformidade** | Auditar tempos de resposta sob rede 3G simulada, integridade criptográfica e barreiras de segurança. | Vitest, Playwright, Lighthouse CLI, Axe-core, DevTools 3G | Relatórios de auditoria de performance, testes end-to-end de adoção e laudo de conformidade LGPD. |

---

## 3. Protocolo de Transição e Contratos de Interface

1. **Design ➔ Frontend:** O Agente de Design fornece a árvore de componentes em Tailwind CSS com especificações explícitas de estados (Loading, Erro, Offline, Sucesso) e tamanhos de toque mínimos (48x48 dp).
2. **Backend ➔ Frontend:** O Agente de Banco de Dados fornece o script SQL executado e os tipos TypeScript gerados via Supabase CLI (`Database['public']['Tables']`), garantindo que o Frontend nunca utilize tipos `any`.
3. **Frontend ➔ QA:** O Agente de Frontend disponibiliza os fluxos operacionais completos para o Agente de QA executar testes em rede móvel emulada (3G Regular) e validar o tempo de geração do PDF (< 300ms).
4. **Governança Geral:** Nenhum agente tem permissão de introduzir bibliotecas que adicionem custos mensais de servidores ou quebrem o limite de payload de 350 KB.
