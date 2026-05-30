# Manual de Instalação, Ferramentas e Credenciais (n8n)

Este documento detalha passo a passo como preparar o seu ambiente n8n para rodar os fluxos JSON exportados deste projeto, ensinando a instalar nós extras, criar credenciais e conectar todas as ferramentas.

---

## 0. Instalando Nós da Comunidade (Community Nodes)

Os fluxos utilizam integrações criadas pela comunidade (Apify e Evolution API) que não vêm instaladas por padrão no n8n. Você deve instalá-las antes de importar os fluxos, caso contrário, os nós aparecerão com erro (desconhecidos).

**Passo a passo para instalação:**
1. Abra o seu n8n. No menu lateral esquerdo, clique em **Settings** (Configurações).
2. No menu de configurações, vá até a aba **Community Nodes**.
3. Clique em **Install a community node**.
4. **Para a Evolution API:** No campo "npm Package Name", digite `n8n-nodes-evolution-api` e clique em **Install**.
5. **Para o Apify:** Repita o processo, digite `n8n-nodes-apify` e clique em **Install**.
6. Após a instalação de ambos, concorde com os termos de segurança e aguarde. Recomendamos recarregar a aba do navegador ao final.

---

## 1. Google Cloud / Gemini API (Inteligência Cognitiva)

O Gemini é o "cérebro" do agente SDR, responsável por redigir mensagens, extrair contexto e contornar objeções de forma humanizada.

**Como obter a chave:**
1. Acesse o [Google AI Studio](https://aistudio.google.com/app/apikey).
2. Faça login com sua conta do Google.
3. Clique em **Create API Key**, escolha um projeto e copie a chave gerada.

**Como conectar no n8n (Nos fluxos):**
1. No seu fluxo importado, clique no nó do **Google Gemini** (ou Google Vertex AI).
2. Na seção **Credential to connect with**, clique no menu e selecione **Create New Credential**.
3. Dê um nome (ex: `Minha Chave Gemini`) e cole a API Key no campo correspondente.
4. Clique em **Save**.
> 📺 **Vídeo Tutorial de Ajuda:** Se você tiver dúvidas de como criar credenciais da nuvem do Google (como OAuth2 para o Google Calendar ou Vertex), assista a este guia: [Configurando Credenciais Google para n8n](https://www.youtube.com/watch?v=FBGtpWMTppw&time_continue=0&embeds_referring_euri=https%3A%2F%2Fdocs.n8n.io%2F)

---

## 2. Apify (Mineração de Dados)

Utilizamos o ator *Google Maps Scraper* do Apify para buscar informações e contatos de leads.

**Como obter o token:**
1. Acesse o [Apify Console](https://console.apify.com/) e faça login.
2. No menu, vá até **Settings > Integrations**.
3. Na seção **Personal API token**, gere e copie o seu token longo.

**Como conectar no n8n (Nos fluxos):**
1. Abra o nó do **Apify** no seu fluxo.
2. Em **Credential to connect with**, clique em **Create New Credential**.
3. Insira o seu Token da API que você acabou de copiar e clique em **Save**.
*(Nota: Certifique-se de que o ID do ator do Google Maps Scraper esteja corretamente referenciado no campo do nó).*

---

## 3. Supabase via Nó do PostgreSQL (Banco de Dados / RAG)

O Supabase armazena a nossa base de dados. Nos fluxos deste projeto, nós não usamos o nó oficial "Supabase" do n8n, mas sim o nó nativo do **PostgreSQL**, pois ele permite escrever *Queries* (consultas SQL) diretamente no banco do Supabase, facilitando a manipulação e inserção da extensão vetorial.

**Como obter as credenciais:**
1. Crie ou acesse seu projeto no [Supabase](https://supabase.com/).
2. No menu lateral do seu projeto, vá até a engrenagem **Project Settings** e depois clique em **Database**.
3. Na seção *Connection Parameters*, desmarque a opção "Use connection pooling" (se quiser a URL direta) ou utilize a porta `6543` para pooler. Você verá os dados: Host, Database Name, Port, User e Password.

**Como conectar no n8n (Nos fluxos):**
1. Procure pelos nós do tipo **PostgreSQL** nos seus fluxos importados (eles farão os `SELECT` ou `INSERT`).
2. Clique no nó e em **Credential to connect with**, escolha **Create New Credential** > **PostgreSQL**.
3. Preencha os campos exatamente com os dados do Supabase:
   - **Host:** Ex: `aws-0-sa-east-1.pooler.supabase.com`
   - **Database:** `postgres`
   - **User:** `postgres.[sua-ref]`
   - **Password:** A senha que você criou ao iniciar o projeto no Supabase.
   - **Port:** Geralmente `5432` ou `6543`.
4. Clique em **Save** para finalizar. 

---

## 4. Redis Cloud (Buffer e Memória de Curto Prazo)

O Redis impede que o agente envie 10 mensagens simultâneas e acabe bloqueando a sua instância de WhatsApp por spam.

**Como obter as credenciais:**
1. Acesse o [Redis Enterprise Cloud](https://redis.com/try-free/) e crie uma Database gratuita.
2. Na aba **Configuration** da sua Database, anote o **Public Endpoint** (o texto antes dos dois pontos é o Host, e os números depois são a Porta) e o **Default User Password**.

**Como conectar no n8n (Nos fluxos):**
1. No fluxo orquestrador, clique no nó do **Redis**.
2. Selecione **Create New Credential**.
3. Cole o Host, a Porta e a Senha que você pegou no painel do Redis Cloud e salve.

---

## 5. Evolution API (WhatsApp)

A Evolution gerencia a sessão do seu número de WhatsApp. 

**Como conectar no n8n (Nos fluxos):**
1. Se você estiver usando o nó comunitário (*Community Node*) da Evolution, clique nele.
2. Em **Credential to connect with**, escolha criar uma nova credencial.
3. Você precisará de duas coisas da sua instalação da Evolution:
   - **Evolution API URL:** O link de onde a sua API está hospedada (ex: `https://api.sua-evolution.com`).
   - **Global API Key:** A chave mestra configurada no seu arquivo `.env` da Evolution API.
4. Salve e teste. Dentro do nó, você precisará especificar o nome exato da **Instância** (ex: `SDR_01`) que está conectada no seu WhatsApp.
