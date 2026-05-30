# Manual de Integração e Credenciais (n8n)

Este documento detalha o passo a passo para obter os acessos, criar credenciais e conectar as principais ferramentas utilizadas nos fluxos do n8n deste projeto.

---

## 1. Google Gemini API (Inteligência Cognitiva)

O Gemini é o "cérebro" do agente, responsável por ler o contexto e redigir mensagens ou contornar objeções.

**Como obter a chave:**
1. Acesse o [Google AI Studio](https://aistudio.google.com/app/apikey).
2. Faça login com sua conta do Google.
3. Clique em **Create API Key** e copie o valor gerado.

**Como conectar no n8n:**
- Adicione um nó do **Google Gemini** no fluxo.
- Em **Credential to connect with**, clique em *Create New Credential*.
- Dê um nome à credencial (ex: `Gemini API`) e cole a sua API Key.

---

## 2. Apify (Mineração de Dados)

O Apify executa o *Google Maps Scraper* para encontrar leads com base em buscas específicas.

**Como obter o token:**
1. Crie uma conta no [Apify](https://apify.com/).
2. Vá até o painel **Settings > Integrations**.
3. Em **Personal API token**, gere e copie o seu token de acesso.
4. Adicione o ator [Google Maps Scraper](https://apify.com/compass/google-maps-scraper) aos seus "Saved Actors".

**Como conectar no n8n:**
- No nó de HTTP Request ou no nó oficial do Apify (se instalado), vá na criação de credenciais.
- Selecione o tipo de autenticação (Header `Authorization: Bearer SEU_TOKEN`) e insira o token copiado.

---

## 3. Supabase (Banco de Dados Vetorial / RAG)

O Supabase armazena o histórico dos leads prospectados e gerencia a memória de longo prazo do agente via PostgreSQL e extensão `pgvector`.

**Como obter as credenciais:**
1. Crie um projeto gratuito no [Supabase](https://supabase.com/).
2. Acesse as **Project Settings > API**.
3. Copie a **Project URL** e a chave **anon / public** ou **service_role** (recomendado para backend).
4. No menu **Database**, ative a extensão `vector` caso vá utilizar RAG.

**Como conectar no n8n:**
- Adicione o nó do **Supabase**.
- Crie uma credencial inserindo a `Project URL` e a `Service Role Secret`.
- (Opcional) Para buscar similaridade com vetores, instale a integração do n8n-nodes-langchain e utilize o nó `Supabase Vector Store`.

---

## 4. Redis Cloud (Buffer e Memória de Curto Prazo)

O Redis atua controlando a frequência de mensagens para não disparar gatilhos de spam no WhatsApp e servindo como cache rápido.

**Como obter as credenciais:**
1. Crie uma conta no [Redis Enterprise Cloud](https://redis.com/try-free/).
2. Crie uma base de dados (Subscription) gratuita.
3. Na aba **Configuration**, anote o **Public Endpoint** (host:porta) e o **Default User Password**.

**Como conectar no n8n:**
- Utilize o nó **Redis**.
- Crie a credencial inserindo o Host, a Porta e a Senha obtidos no painel.

---

## 5. Evolution API (WhatsApp)

A Evolution API gerencia as instâncias de WhatsApp de forma não-oficial, enviando e recebendo mensagens.

**Como obter o acesso:**
1. Com a Evolution API já hospedada (via Easypanel ou VPS própria), acesse o *Manager* (interface visual) ou via requisições Postman.
2. Crie uma nova instância (ex: `SDR_01`) e leia o QR Code pelo WhatsApp do telefone que servirá como agente.
3. Copie a **Global API Key** nas configurações globais da sua Evolution.

**Como conectar no n8n:**
- O projeto geralmente utiliza nós de **HTTP Request** para conversar com a Evolution.
- Nos nós de HTTP, configure a URL (`https://sua-evolution.com/message/sendText/SDR_01`).
- Na aba de Headers, adicione `apikey` e cole sua chave global.
- Para receber mensagens, configure o **Webhook** na Evolution apontando para a URL do Webhook do n8n.
