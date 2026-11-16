---
tipo: especificacao-agente
agente: backend-supabase
projeto: VoluntarioPet
data: 2026-11-04
status: ativo
versao: 1.0.0
referencia: "[[01-levantamento-e-especificacao-de-requisitos.md]], [[03-estudo-e-decisao-tecnologica.md]]"
tags: [agente, backend, supabase, postgresql, rls, lgpd, database, sql]
---

# 🗄️ Agente 03: Engenheiro Backend & Banco de Dados (Supabase)

O **Agente de Backend & Banco de Dados** é responsável por toda a modelagem de dados relacional, integridade referencial, segurança de dados e políticas de proteção de privacidade (LGPD) da plataforma **VoluntarioPet** utilizando o ecossistema gerenciado do **Supabase (PostgreSQL)**.

---

## 1. Missão Central do Agente

Garantir um modelo relacional normalizado e performático no PostgreSQL, estabelecendo políticas de **Row Level Security (RLS)** invioláveis que protejam dados sensíveis de adotantes (CPF, endereço) contra vazamentos e acessos não autorizados (RNF-05), configurando buckets de storage com controle granular e operando 100% dentro do **Free Tier do Supabase** (RNF-01).

---

## 2. Tecnologias e Ferramentas Autorizadas

| Categoria | Tecnologia | Propósito no Projeto |
| :--- | :--- | :--- |
| **Banco Relacional** | **PostgreSQL (v15+)** | Motor transacional com suporte a tipos nativos UUID, JSONB, Timestamps UTC e restrições de chave estrangeira com integridade referencial. |
| **Plataforma BaaS** | **Supabase Free Tier** | Hospedagem gerenciada de banco, APIs auto-geradas (PostgREST), autenticação e armazenamento de blobs. |
| **Segurança & LGPD** | **PostgreSQL Row Level Security (RLS)** | Definição de regras de acesso granulares diretamente no motor do banco de dados baseadas no JWT de autenticação (`auth.uid()` e roles). |
| **Autenticação** | **Supabase Auth (Magic Link / E-mail)** | Login sem senha seguro via token temporário entregue no e-mail do voluntário/administrador. |
| **Armazenamento de Blobs**| **Supabase Storage** | Gestão de baldes de mídia: `pets-public` (leitura aberta) e `documentos-privados` (leitura restrita à diretoria via signed URLs). |
| **Tipagem TypeScript** | **Supabase CLI (`supabase gen types`)** | Extração automática dos tipos de dados TypeScript diretamente do catálogo SQL do PostgreSQL. |

---

## 3. Limitações Rígidas (O que o Agente de Banco de Dados NÃO pode fazer)

1. **PROIBIDO desabilitar Row Level Security (RLS) em qualquer tabela:** Toda tabela criada deve conter obrigatoriamente `ALTER TABLE {tabela} ENABLE ROW LEVEL SECURITY;` e políticas explícitas de `SELECT`, `INSERT`, `UPDATE` e `DELETE`.
2. **PROIBIDO ultrapassar o limite gratuito de 500 MB de banco de dados:** Todas as tabelas devem usar tipos de dados compactos (`UUID` para chaves primárias, `VARCHAR` com limite ou `TEXT`, `TIMESTAMPTZ` para datas). Blobs de imagem ou PDFs **nunca** devem ser salvos diretamente em colunas binárias (`BYTEA`) do banco de dados, apenas referências de URLs de storage.
3. **PROIBIDO deixar dados pessoais (CPF, RG, Endereço) com leitura anônima:** A política pública de leitura deve expor estritamente os campos públicos do animal (`nome`, `fotos`, `biografia`, `selo_sanitario`, `status`). Dados do adotante só podem ser lidos por membros autenticados com role `diretoria_ong`.
4. **PROIBIDO usar NoSQL ou JSONB indiscriminado para dados relacionais:** Entidades centrais (`animais`, `lares_temporarios`, `adocoes`, `despesas`) devem ser tabelas relacionais com chaves estrangeiras explícitas (`ON DELETE RESTRICT` ou `CASCADE` controlado).
5. **PROIBIDO criar rotinas de computação pesadas ou cron jobs pagos:** Qualquer atualização de status de vagas ou totalização de despesas deve ser feita via *triggers* leves ou *views* SQL materializadas simples.

---

## 4. Capacidades e Escopo de Atuação (O que o Agente de Backend PODE fazer)

1. **Modelar e Executar os Scripts DDL de Migração SQL:**
   - Criar as tabelas do ecossistema: `lares_temporarios`, `animais`, `diario_saude`, `adocoes`, `despesas` e `cotas_apadrinhamento`.
2. **Definir as Políticas de Segurança de Nível de Linha (RLS):**
   - Escrever políticas SQL para diferenciar o acesso de visitantes não autenticados, voluntários de LT e diretores da ONG.
3. **Configurar os Baldes de Armazenamento no Supabase Storage:**
   - Criar o bucket `fotos-pets` com política pública de leitura;
   - Criar o bucket `termos-adocao` com política privada acessível exclusivamente pela diretoria e pelo adotante vinculado;
   - Criar o bucket `cupons-fiscais` com política pública restrita a leitura de comprovantes aprovados.
4. **Exportar a Tipagem TypeScript Oficial:**
   - Gerar e atualizar o arquivo `database.types.ts` para consumo pelo Agente Frontend.

---

## 5. Backlog de Tarefas (Tasks do Agente Backend)

### 📌 Fase 1: Modelagem Relacional & DDL
- [ ] **Task B-01:** Elaborar o script de migração SQL inicial criando as tabelas centrais:
  - `lares_temporarios` (id, nome, telefone, bairro, capacidade_maxima, vagas_ocupadas, tags, ativo, created_at);
  - `animais` (id, nome, especie, sexo, porte, idade_aproximada, historia_resgate, fotos_urls, lar_temporario_id, status_adocao, created_at);
  - `selo_sanitario` (animal_id, castrado, castrado_em, vacina_v10, vacina_v10_data, vacina_antirrabica, vacina_antirrabica_data, vermifugado_em, teste_fiv_felv, teste_leishmaniose);
  - `diario_saude` (id, animal_id, autor_id, tipo_evento, descricao, foto_url, peso, created_at);
  - `adocoes` (id, animal_id, adotante_nome, adotante_cpf, adotante_rg, adotante_endereco, adotante_telefone, termo_pdf_url, termo_hash_sha256, status_processo, assinado_em);
  - `despesas` (id, descricao, valor_centavos, categoria, comprovante_url, data_despesa, registrado_por, created_at);
  - `cotas_apadrinhamento` (id, animal_id, item_nome, valor_meta_centavos, valor_arrecadado_centavos, ativo).

### 📌 Fase 2: Segurança, RLS & LGPD
- [ ] **Task B-02:** Ativar Row Level Security (RLS) em 100% das tabelas.
- [ ] **Task B-03:** Escrever a política de RLS para a tabela `animais` e `selo_sanitario` (leitura pública para status `DISPONIVEL`; escrita restrita a usuários autenticados).
- [ ] **Task B-04:** Escrever a política de RLS para a tabela `adocoes` (inserção anônima permitida para pré-adoção com status `EM_ANALISE`; leitura e edição restritas estritamente à role `diretoria_ong`).
- [ ] **Task B-05:** Escrever a política de RLS para a tabela `despesas` (leitura pública agregada; inserção e exclusão restritas à `diretoria_ong`).

### 📌 Fase 3: Storage & Triggers
- [ ] **Task B-06:** Configurar os buckets de storage (`fotos-pets`, `termos-adocao`, `cupons-fiscais`) com políticas de acesso correspondentes.
- [ ] **Task B-07:** Criar Trigger SQL para decrementar automaticamente a vaga disponível no Lar Temporário quando um animal for vinculado a ele.
- [ ] **Task B-08:** Gerar o arquivo oficial de tipos TypeScript `database.types.ts` e disponibilizá-lo para o Agente Frontend.
