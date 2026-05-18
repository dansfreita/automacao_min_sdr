# Agente SDR Autônomo B2B (n8n + Gemini AI)

Este repositório contém a arquitetura e os fluxos lógicos de um agente de prospecção autônoma (SDR) focado em operações B2B. O sistema orquestra a mineração de dados, enriquecimento de contexto e negociação persuasiva utilizando Inteligência Artificial Generativa e ferramentas Low-Code.

Projeto desenvolvido como Trabalho de Conclusão de Curso (Engenharia da Computação) visando colaborar com o acesso a operações de *Outbound Marketing* para micro e pequenas empresas com baixo custo marginal.

## Stack Tecnológico

* **Orquestrador:** [n8n](https://n8n.io/) (Self-hosted via Easypanel / Docker)
* **Inteligência Cognitiva:** Google Gemini API
* **Mineração de Dados:** Apify (Google Maps Scraper)
* **Memória de Longo Prazo / RAG:** Supabase (pgvector)
* **Memória de Curto Prazo (Buffer):** Redis Cloud
* **Interface de Mensageria:** Evolution API (WhatsApp)
* **Exposição de Rede:** Cloudflare Tunnels

## Arquitetura do Sistema

O fluxo de dados é dividido em três módulos principais:
1.  **Módulo BDR (Mineração):** Extração de leads via Apify, cruzamento de dados e elaboração de mensagem de abertura hiper-personalizada.
2.  **Módulo Orquestrador (Triagem):** Agrupamento de mensagens assíncronas via Redis e triagem sintática para identificar se a resposta é humana ou automática.
3.  **Módulo SDR (Negociação e Closing):** Agente cognitivo com arquitetura RAG (busca de contexto no Supabase) para contornar objeções e realizar o agendamento direto no Google Agenda.
