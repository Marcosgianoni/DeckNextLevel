# CLAUDE.md — DeckNextLevel

> Instruções operacionais deste repositório. Leia e execute — não precisa reaprender a cada sessão.

## O que é este repositório
Coleção de **apresentações/decks da Next Level** publicadas como **GitHub Pages**.
Cada apresentação é uma página HTML estática servida diretamente, sem build.

- **Repo:** `Marcosgianoni/DeckNextLevel`
- **URL pública:** `https://marcosgianoni.github.io/DeckNextLevel/` (domínio nativo do GitHub Pages; cada deck em `/<slug>/`)
- **Sem portal na raiz** — cada deck é compartilhado pela URL direta. O site institucional vive em outro domínio (`nextltech.com.br`).
- **Branch publicada:** `main`, pasta `/` (root)
- **Logo compartilhada:** `nextlevel-logo.png` (referenciar como `../nextlevel-logo.png`)

## ⚡ Quando o usuário pedir uma apresentação nova — execute este fluxo
1. **Coletar:** nome do cliente, tema e dados (números, processos, datas, ROI, payback).
2. **Definir de onde vem o HTML** (não há template fixo no repo):
   - **Modo A — o usuário traz o HTML pronto:** use o arquivo/conteúdo que ele passar; ajuste só os caminhos relativos (`../nextlevel-logo.png`) se necessário.
   - **Modo B — criar do zero aqui:** monte o HTML aplicando o **padrão visual da Next Level** (ver seção "Padrão visual Next Level").
3. **Definir o slug** em kebab-case (ex.: `Cliente Exemplo Onboarding` → `cliente-exemplo-onboarding`).
4. **Criar a página** na branch `main`:
   - `mcp__github__create_or_update_file`
   - `path`: `<slug-cliente>/index.html`
   - `branch`: `main`
   - `content`: o HTML final (do Modo A ou B), com cliente/números/textos preenchidos
   - `message`: commit claro (ex.: `Add <Cliente> presentation`)
   - **Criar** não precisa de `sha`. **Atualizar** precisa: faça `get_file_contents` antes para pegar o `sha` atual.
5. **Atualizar o `README.md`** para listar a nova apresentação (não há portal na raiz — o deck é compartilhado pela URL direta).
6. **Aguardar rebuild:** o Pages reconstrói sozinho em ~30–60s após o commit em `main`.
7. **Verificar:** `WebFetch` (ou Playwright) na URL pública para confirmar que está no ar.

## Padrão visual Next Level
> Brand Book v1.0 (2026). Use no **Modo B** (criar HTML do zero). Marca: **Next Level Tech** · slogan **"Tecnologia. No próximo nível."**

### Paleta de cores (use só estas)
| Cor | Hex | Papel |
|---|---|---|
| **Eclipse** | `#080F17` | Fundo institucional padrão (o "palco") |
| **Voltage Blue** | `#3DA3EC` | Marca / destaque / CTA — cor de ação **única** |
| **Stardust** | `#A3B0C5` | Texto secundário, linhas, detalhes |
| **Pure White** | `#FFFFFF` | Texto principal sobre Eclipse |

- Voltage Blue é o **único** destaque — nunca recolorir a marca com outras cores.
- Tons derivados só em hover, gradientes, glow e estados de UI.

### Tipografia
- **Títulos e corpo:** `SansBeamBody` → fallback `"Helvetica Neue", Arial, sans-serif`.
- **Técnico/tags/números:** `JetBrains Mono` (Google Fonts) → fallback `Consolas, "Courier New", monospace`.
- Hierarquia:
  - **Display:** SansBeamBody Heavy · 36–96px · letter-spacing −2%
  - **Headline:** Bold · 22–28px · −1.5%
  - **Subhead:** Medium · 14–18px
  - **Corpo:** Regular · 16–18px (web) · line-height 1.5–1.6
  - **Tag técnica:** JetBrains Mono Medium · MAIÚSCULAS · letter-spacing 0.18em (ex.: `// SEÇÃO 03`)

### Apresentações / decks (formato padrão)
- **16:9**, fundo **Eclipse**, destaque **Voltage**, SansBeamBody em todo o conteúdo.
- **1 ideia por slide.** Hierarquia: tag mono → headline → bullets curtos ou 1 visual. Densidade é inimiga da marca.
- **CTA:** botão **pill** (`border-radius: 100px`) em Voltage Blue com texto Eclipse. Cards: cantos 6–8px.
- **Glow:** radial gradient em Voltage com 12–18% de opacidade, sempre ao fundo (nunca em primeiro plano).
- **Grafismos** (sutis, nunca decorativos): malha de pontos/linhas ao fundo; filetes finos com gradiente p/ Voltage marcando seções; tags mono com prefixo `//`.

### Ícones
- Bibliotecas oficiais: **Lucide** ou **Phosphor (Light)**. Traço 1.5–2px, geometria limpa.
- Só contorno, em **Voltage Blue** ou **Pure White**. Nunca preenchidos, coloridos ou cartoon.

### Logo
- **Assets de uso (raiz)** — referenciar como `../nextlevel-logo.png` a partir das subpastas:
  - `nextlevel-logo.png` — logo completo (M + "Next Level Tech"), sem fundo, alta resolução.
  - `nextlevel-icon.png` — só o símbolo M, sem fundo (favicon / usos reduzidos).
- **Variantes oficiais (`brand/`)** — fontes para outros contextos, não usar direto nos decks:
  - `brand/logo-full-eclipse.png` · `brand/logo-icon-eclipse.png` — versões com fundo Eclipse embutido.
  - `brand/Next-Level-Brandkit.pdf` — brand book completo (v1.0), fonte de verdade da marca.
- Tamanhos mínimos em tela: logo completo **90px** de largura; símbolo **24px** de altura. Respiro mínimo: **2X** ao redor.
- **Nunca:** distorcer, recolorir, rotacionar, aplicar sombra/brilho/3D, reduzir opacidade, ou usar sobre fundo "sujo".

### Voz e tom (textos)
Direta, técnica, honesta, próxima ("você"). Frases curtas, verbos no presente. Sem clichês de marketing ("disruptivo", "jornada", "360°"). Prefira números e fatos (ex.: "Lead time caiu de 12 para 2 dias").

## Modelo mental (como o deploy funciona)
- **Sem build/CI.** Não existe `.github/workflows`. O GitHub serve os HTML **exatamente como estão**.
- **Cada apresentação = uma subpasta** com um `index.html`. A URL espelha o caminho da pasta.
- **Sem portal na raiz.** Não há `index.html` na raiz — cada deck é compartilhado pela URL direta.
- **Domínio nativo do GitHub Pages** — sem `CNAME`/domínio próprio. O site é uma *project page*, servido em `https://marcosgianoni.github.io/DeckNextLevel/` (o nome do repo entra no caminho).

```
DeckNextLevel/
├── nextlevel-logo.png    → asset compartilhado (referenciado como ../nextlevel-logo.png)
└── <slug-cliente>/
    └── index.html        → https://marcosgianoni.github.io/DeckNextLevel/<slug-cliente>/
```

**Regra de ouro da URL:** o caminho da pasta é a URL (depois do nome do repo).
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
- **Caminhos relativos sempre** (`../nextlevel-logo.png`). Evite caminhos absolutos (`/logo.png`): como o site é uma *project page*, `/logo.png` aponta para `marcosgianoni.github.io/logo.png` (fora do repo). O relativo resolve certo dentro de `/DeckNextLevel/`.
- **Página em branco / 404:** quase sempre é (a) faltou `index.html` na pasta certa, ou (b) caminho absoluto de asset quebrado. Use caminhos relativos.
- **Jekyll:** o Pages processa o site com Jekyll por padrão e **ignora pastas que começam com `_`**. Se precisar servir uma pasta com `_`, crie um arquivo vazio `.nojekyll` na raiz para desligar o Jekyll.
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
