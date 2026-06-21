# CLAUDE.md — DeckNextLevel

> Instruções operacionais deste repositório. Leia e execute — não precisa reaprender a cada sessão.

## O que é este repositório
Coleção de **apresentações/decks da Next Level** publicadas como **GitHub Pages**.
Cada apresentação é uma página HTML estática servida diretamente, sem build.

- **Repo:** `Marcosgianoni/DeckNextLevel`
- **URL pública (Pages):** `https://marcosgianoni.github.io/DeckNextLevel/`
- **Branch publicada:** `main`, pasta `/` (root)
- **Logo compartilhada:** `nextlevel-logo.png` (referenciar como `../nextlevel-logo.png`)

## ⚡ Quando o usuário pedir uma apresentação nova — execute este fluxo
1. **Coletar:** nome do cliente, tema e dados (números, processos, datas, ROI, payback).
2. **Definir o slug** em kebab-case (ex.: `Cliente Exemplo Onboarding` → `cliente-exemplo-onboarding`).
3. **Ler o template:** `mcp__github__get_file_contents` no path `_template/index.html`.
4. **Criar a página** na branch `main`:
   - `mcp__github__create_or_update_file`
   - `path`: `<slug-cliente>/index.html`
   - `branch`: `main`
   - `content`: o HTML já com cliente/números/textos substituídos
   - `message`: commit claro (ex.: `Add <Cliente> presentation`)
   - **Criar** não precisa de `sha`. **Atualizar** precisa: faça `get_file_contents` antes para pegar o `sha` atual.
5. **Atualizar o portal:** `index.html` da raiz e o `README.md` para listar a nova apresentação (mesmo fluxo: `get_file_contents` → `sha` → `create_or_update_file`).
6. **Aguardar rebuild:** o Pages reconstrói sozinho em ~30–60s após o commit em `main`.
7. **Verificar:** `WebFetch` (ou Playwright) na URL pública para confirmar que está no ar.

## Modelo mental (como o deploy funciona)
- **Sem build/CI.** Não existe `.github/workflows`. O GitHub serve os HTML **exatamente como estão**.
- **Cada apresentação = uma subpasta** com um `index.html`. A URL espelha o caminho da pasta.
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

## Git neste ambiente
- Neste ambiente o `git push` normalmente **funciona** — pode usar git puro.
- ⚠️ Se em algum ambiente o `git push` estiver **bloqueado** (proxy retorna HTTP 403), publique usando as ferramentas do **GitHub MCP** (`mcp__github__*`), que falam direto com a API do GitHub.

| Operação | Ferramenta MCP |
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
2. **Repository access:** marcar `Marcosgianoni/DeckNextLevel`.
3. **Permissions → Contents:** `Read and write`.

## Detalhes que evitam dor de cabeça
- **Caminhos relativos sempre** (`../nextlevel-logo.png`). Evite caminhos absolutos (`/logo.png`), porque apontam para `marcosgianoni.github.io/logo.png`, fora do repo.
- **Página em branco / 404:** quase sempre é (a) faltou `index.html` na pasta certa, ou (b) caminho absoluto de asset quebrado. Use caminhos relativos.
- **Jekyll:** o Pages processa o site com Jekyll por padrão e **ignora pastas que começam com `_`** (por isso `_template/` não vira página). Se precisar servir uma pasta com `_`, crie um arquivo vazio `.nojekyll` na raiz para desligar o Jekyll.
- **Source do Pages:** mantenha **"Deploy from a branch"** (não "GitHub Actions"). O modo Actions só é necessário para builds (React, Vite, etc.).

---

## Como configurar o GitHub Pages num repositório NOVO
> Use quando o usuário quiser publicar páginas em **outro** repositório do zero. Conduza pelos cliques, um passo de cada vez.

**Pré-requisitos:** um repositório no GitHub e ao menos um `index.html` na raiz (ou dentro de uma subpasta).

1. **Ter conteúdo na `main`** — garantir um `index.html` na raiz. Teste mínimo:
   ```html
   <!doctype html>
   <html lang="pt-BR">
     <meta charset="utf-8">
     <title>Teste</title>
     <h1>Funcionou 🎉</h1>
   </html>
   ```
2. **Ativar o Pages:** Settings (engrenagem) → **Pages** (menu lateral) → **Build and deployment → Source: Deploy from a branch** → **Branch: `main`** + pasta **`/ (root)`** → **Save**.
3. **Aguardar o primeiro build** (~1 min; a primeira vez pode demorar alguns minutos). URL padrão: `https://<usuario>.github.io/<repo>/`.
4. **URLs:** raiz `index.html` → `https://<usuario>.github.io/<repo>/`; subpasta `clienteX/index.html` → `.../clienteX/`.
5. **Publicar mudanças:** todo commit em `main` dispara rebuild automático (~30–60s).

**Domínio próprio:** Settings → Pages → **Custom domain** (cria um `CNAME` no repo); configure o DNS (CNAME → `<usuario>.github.io`) e marque **Enforce HTTPS**.
