# Fluxos do n8n (JSON)

Esta pasta contém os arquivos de exportação dos fluxos construídos no n8n para este projeto.

## Como importar um fluxo no seu n8n

Siga o passo a passo abaixo para importar qualquer um dos arquivos `.json` presentes nesta pasta para a sua instância do n8n:

### Método 1: Importação via Arquivo (Recomendado)
1. Baixe o arquivo `.json` desejado desta pasta para o seu computador.
2. Abra o painel do seu n8n no navegador.
3. No menu lateral ou no canto superior direito, clique em **Add Workflow** (ou abra um fluxo vazio já existente).
4. Na tela do fluxo, clique no ícone de menu (os três pontinhos `...` no canto superior direito) e selecione **Import from File...**
5. Encontre e selecione o arquivo `.json` que você baixou no passo 1.
6. O fluxo será montado automaticamente na sua tela! Lembre-se de salvar o fluxo em seguida.

### Método 2: Importação via Copiar e Colar (Copy/Paste)
1. Abra o arquivo `.json` desejado aqui mesmo no GitHub.
2. Clique no ícone de **Copy raw contents** (copiar o conteúdo bruto do arquivo).
3. Abra a tela de um fluxo em branco no seu n8n.
4. Simplesmente pressione `Ctrl+V` (ou `Cmd+V` no Mac) clicando em qualquer lugar no espaço em branco.
5. Os nós serão instantaneamente renderizados na sua tela.

> **⚠️ Atenção:** Após a importação, você notará que os nós de integração externa (como Google Gemini, Apify, Supabase, etc.) apresentarão um aviso de erro. Isso é esperado! Basta clicar em cada nó afetado e selecionar a **sua própria credencial** na seção "Credential to connect with". Caso tenha dúvidas sobre credenciais, consulte o documento `MANUAL_FERRAMENTAS_N8N.md` localizado na pasta `docs/`.
