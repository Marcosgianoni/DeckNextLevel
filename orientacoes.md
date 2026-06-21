# 📘 Guia: Como publicamos páginas no GitHub Pages (Next Level)

Material de handoff para outro Claude executar. Duas partes:
**(A)** como publicamos neste repositório hoje, e
**(B)** como ensinar o usuário a configurar o GitHub Pages num repositório novo.

---

## Parte A — Como o deploy funciona neste repositório

### Visão geral (o modelo mental)
- O site é hospedado pelo **GitHub Pages** a partir da branch **`main`**, pasta **`/` (root)**.
- **Não há build/CI.** Não existe `.github/workflows`. O GitHub serve os arquivos HTML **exatamente como estão** no repositório.
- **Cada apresentação = uma subpasta** com um `index.html` dentro. A URL espelha o caminho da pasta.
- O `index.html` da raiz é o **portal** que lista as apresentações.

```
DeckNextLevel/
├── index.html            → https://marcosgianoni.github.io/DeckNextLevel/
├── nextlevel-logo.png    → asset compartilhado (referenciado como ../nextlevel-logo.png)
├── _template/index.html  → template base (NÃO é publicado como página útil)
└── <slug-cliente>/
    └── index.html        → https://marcosgianoni.github.io/DeckNextLevel/<slug-cliente>/
```

**Regra de ouro da URL:** o caminho da pasta é a URL.
`cliente-exemplo-onboarding/index.html` → `https://marcosgianoni.github.io/DeckNextLevel/cliente-exemplo-onboarding/`

### Passo a passo para publicar uma página nova

> ⚠️ **IMPORTANTÍSSIMO sobre git neste ambiente:** se o `git push` estiver **bloqueado** (o proxy retorna HTTP 403), você **TEM** que publicar usando as ferramentas do **GitHub MCP** (`mcp__github__*`), que falam direto com a API do GitHub. Não dependa de `git push` quando ele falhar — use o MCP.

1. **Coletar do usuário:** nome do cliente, tema, e os dados (números, processos, datas, ROI, payback).

2. **Definir o slug** em kebab-case (ex.: `Cliente Exemplo Onboarding` → `cliente-exemplo-onboarding`).

3. **Ler o template e pegar o conteúdo base:**
   - `mcp__github__get_file_contents` no path `_template/index.html`.

4. **Criar o arquivo da apresentação** na branch `main`:
   - `mcp__github__create_or_update_file`
   - `path`: `<slug-cliente>/index.html`
   - `branch`: `main`
   - `content`: o HTML já com cliente/números/textos substituídos
   - `message`: mensagem de commit clara (ex.: `Add <Cliente> presentation`)
   - Para **criar** não precisa de `sha`. Para **atualizar** um arquivo existente, primeiro faça `get_file_contents` para pegar o `sha` atual e passe-o.

5. **Atualizar o `index.html` da raiz e o `README.md`** para listar a nova apresentação (mesmo fluxo: `get_file_contents` → pegar `sha` → `create_or_update_file`).

6. **Aguardar o rebuild:** o GitHub Pages reconstrói sozinho em ~30–60s após o commit em `main`.

7. **Verificar o deploy:** `WebFetch` (ou Playwright) na URL pública para confirmar que está no ar.

### Tabela de operações git via MCP (cola isso na memória)

| Operação | Ferramenta |
|---|---|
| Criar / atualizar arquivo | `mcp__github__create_or_update_file` (update precisa de `sha`) |
| Ler conteúdo + SHA | `mcp__github__get_file_contents` |
| Listar pasta | `mcp__github__get_file_contents` com `path` da pasta |
| Deletar arquivo | `mcp__github__delete_file` |
| Criar branch | `mcp__github__create_branch` |

### Convenção de branches
- **`main`** = branch publicada (é o que o Pages serve).
- Cada apresentação é uma **pasta** em `main`, **não** uma branch.
- Branches `apresentacao/<slug>` existem só para rascunho/revisão antes de mergear em `main`.

### Se o MCP retornar 403 (permissão)
Peça ao usuário para:
1. Ir em `github.com/settings/installations` → app **Claude**.
2. **Repository access:** marcar o repositório `Marcosgianoni/DeckNextLevel`.
3. **Permissions → Contents:** `Read and write`.

---

## Parte B — Como ensinar o usuário a configurar GitHub Pages num repositório NOVO

> Use isto quando o usuário quiser publicar páginas num **outro** repositório do zero. Conduza-o pelos cliques, um passo de cada vez, e confirme cada etapa.

### Pré-requisitos
- Um repositório no GitHub (público, ou privado em conta/plano que permita Pages).
- Pelo menos um `index.html` na raiz (ou um `index.html` dentro de uma subpasta).

### Passo 1 — Ter conteúdo na branch `main`
Garanta que existe um `index.html` na raiz do repo na branch `main`. Um teste mínimo:

```html
<!doctype html>
<html lang="pt-BR">
  <meta charset="utf-8">
  <title>Teste</title>
  <h1>Funcionou 🎉</h1>
</html>
```

### Passo 2 — Ativar o GitHub Pages (modo "Deploy from a branch")
Diga ao usuário, literalmente:
1. Abra o repositório no GitHub.
2. Clique em **Settings** (engrenagem, no topo).
3. No menu lateral esquerdo, clique em **Pages**.
4. Em **Build and deployment → Source**, escolha **Deploy from a branch**.
5. Em **Branch**, selecione **`main`** e a pasta **`/ (root)`**.
6. Clique em **Save**.

### Passo 3 — Aguardar o primeiro build
- Aparece um aviso "Your site is live at ..." em ~1 minuto (a primeira vez pode levar alguns minutos).
- A URL padrão é: `https://<usuario>.github.io/<nome-do-repo>/`

### Passo 4 — Entender as URLs
- Arquivo na raiz `index.html` → `https://<usuario>.github.io/<repo>/`
- Subpasta `clienteX/index.html` → `https://<usuario>.github.io/<repo>/clienteX/`
- **Caminhos relativos** funcionam (ex.: `../logo.png` sobe um nível). Evite caminhos que começam com `/` se o site não estiver na raiz do domínio, porque `/logo.png` aponta para `<usuario>.github.io/logo.png`, não para dentro do repo.

### Passo 5 — Publicar mudanças
- Todo commit em `main` dispara um rebuild automático (~30–60s). Não precisa de mais nada.
- Se o ambiente bloquear `git push` (como o nosso costuma fazer), use as ferramentas `mcp__github__*` em vez de git puro.

### Detalhes que evitam dor de cabeça
- **Domínio próprio:** Settings → Pages → **Custom domain**. Isso cria um arquivo `CNAME` no repo; configure o DNS (CNAME apontando para `<usuario>.github.io`) e marque **Enforce HTTPS**.
- **Página em branco / 404:** quase sempre é (a) faltou `index.html` na pasta certa, ou (b) caminho absoluto de asset (`/arquivo.png`) quebrado. Use caminhos relativos.
- **Jekyll:** o Pages processa o site com Jekyll por padrão e **ignora pastas que começam com `_`** (por isso `_template/` não vira página). Se um dia você tiver pastas com `_` que PRECISAM ser servidas, adicione um arquivo vazio chamado **`.nojekyll`** na raiz para desligar o Jekyll.
- **Mudou para "GitHub Actions" sem querer?** Para sites de arquivos estáticos simples como este, mantenha **"Deploy from a branch"**. O modo Actions só é necessário para builds (React, Vite, etc.).
