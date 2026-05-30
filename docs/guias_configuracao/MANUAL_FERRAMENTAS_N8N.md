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
> 📺 **Vídeo Tutorial de Ajuda:** Se você tiver dúvidas de como criar credenciais da nuvem do Google (como OAuth2 para o Google Calendar ou sheets), assista a este guia: [Configurando Credenciais Google para n8n](https://www.youtube.com/watch?v=FBGtpWMTppw&time_continue=0&embeds_referring_euri=https%3A%2F%2Fdocs.n8n.io%2F)

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

## 3. Supabase (Banco de Dados / RAG)

O Supabase armazena a nossa base de dados. Dependendo da parte do fluxo, você pode usar os nós comuns do próprio **Supabase** ou nós diretos de **PostgreSQL** (para consultas complexas e RAG). Abaixo estão as duas formas de conectar:

### 3.1. Nós Comuns do Supabase (Data API)
1. Acesse o seu projeto no [Supabase](https://supabase.com/).
2. No menu lateral, vá em **Project Settings** (engrenagem).
3. Clique em **API** (ou *Data API*).
4. Na seção *Project URL*, copie a sua **URL**.
5. Em *Project API keys* (ou *legacy anon, service_role API keys*), copie a chave do tipo **service_role** 
6. No n8n, crie a credencial do nó Supabase colando a URL e a API Key (Service Role) copiadas.

### 3.2. Nó do PostgreSQL (Para Consultas SQL Diretas e RAG)
Para buscas de similaridade vetorial (RAG) que precisam de queries complexas, usamos o nó nativo de PostgreSQL.

**Como obter as credenciais (PostgreSQL):**
1. Crie ou acesse seu projeto no [Supabase](https://supabase.com/).
2. No painel inicial do seu projeto, clique no botão **Connect** na parte superior da tela.
3. Clique em **ORM** e, em seguida, em **Configure ORM**.
4. Você verá uma string de conexão `DATABASE_URL` parecida com esta:
   `postgresql://postgres.[sua-ref-aqui]:[SUA-SENHA]@aws-0-sa-east-1.pooler.supabase.com:6543/postgres?pgbouncer=true`
5. Desta string, extraia as informações necessárias: o **Host** (tudo após o `@` e antes de `:6543`), a **Porta** (`6543`), o **User** (`postgres.[sua-ref-aqui]`) e o **Database Name** (`postgres`).

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

**Como criar a Tabela e Função de Busca Vetorial (RAG):**
Para que o seu agente SDR (baseado no Gemini) consiga buscar informações de contexto através de similaridade semântica, precisamos preparar o banco do Supabase para suportar vetores com a exata dimensão que a IA do Google gera (3072 dimensões).
Siga este passo a passo:
1. Abra o painel do seu projeto no **Supabase**.
2. No menu lateral esquerdo, clique em **SQL Editor**.
3. Clique em **New Query** e cole exatamente o script abaixo no editor de texto:

```sql
-- 1. Habilita a extensão pgvector
create extension if not exists vector with schema extensions;

-- 2. Cria a tabela com a dimensão do Gemini (3072)
create table documents (
  id bigserial primary key,
  content text, 
  metadata jsonb, 
  embedding extensions.vector(3072) -- Ajustado de 1536 para 3072 (Padrão Gemini)
);

-- 3. Função de busca ajustada para 3072 dimensões
create or replace function match_documents (
  query_embedding extensions.vector(3072), -- Ajustado aqui também
  match_count int default null,
  filter jsonb DEFAULT '{}'
) returns table (
  id bigint,
  content text,
  metadata jsonb,
  similarity float
)
language plpgsql
as $$
#variable_conflict use_column
begin
  return query
  select
    documents.id,
    documents.content,
    documents.metadata,
    1 - (documents.embedding <=> query_embedding) as similarity
  from documents
  where metadata @> filter
  order by documents.embedding <=> query_embedding
  limit match_count;
end;
$$;
```
4. Clique no botão verde **Run** (ou pressione `Cmd+Enter` / `Ctrl+Enter`).
Pronto! Com isso o seu banco está preparado e já inclui a função `match_documents` que o n8n vai acionar.


---

## 4. Redis Cloud (Buffer e Memória de Curto Prazo)

O Redis funciona **acumulando as mensagens recebidas**. Como pode ser observado no fluxo *Prospector RAG*, quando um lead envia várias mensagens curtas seguidas (algo comum no WhatsApp), o Redis armazena temporariamente esse histórico e o consolida. Assim, ele entrega o contexto completo para a inteligência artificial interpretar de uma só vez, impedindo que o agente responda cada frase individualmente e evitando bloqueios por spam.

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
