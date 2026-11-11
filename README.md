<div align="center">

# Mosaic

**Sua presença digital não é uma lista de links.**

Um builder de páginas pessoais onde a página *é* o destino — não um índice que manda o visitante pra outro lugar.

</div>

---

> [!NOTE]
> **O Mosaic está em fase de descoberta e design.** O código deste repositório é um protótipo inicial; a versão real começa no Sprint 1, depois que o design estiver fechado. Este README descreve o que está sendo construído e por quê. Veja o [estado atual](#-estado-atual).

## O problema

Ferramentas como Linktree e Bento.me resolvem "juntar meus links num lugar só". Mas quem cria conteúdo cultural — reviews de jogo, recomendações de filme, listas de música — acaba com duas limitações:

1. **A página é só um índice.** O conteúdo de verdade mora em outro site; o visitante clica e sai.
2. **Todas as páginas se parecem.** Dá pra saber qual ferramenta a pessoa usou só olhando. A página parece da plataforma, não do usuário.

## A proposta

O Mosaic monta a página com **blocos modulares** (link, texto, imagem, embed, galeria, lista) num grid livre, com **tema próprio** — cor, tipografia, forma e densidade. O objetivo é que duas páginas feitas no Mosaic não pareçam do mesmo lugar.

**Público do MVP:** curadores culturais — gamers, cinéfilos, leitores, melômanos — que publicam opiniões espalhadas entre Instagram, Discord e Twitter e não têm um link único pra mandar quando alguém pergunta "o que você recomenda?".

### Funcionalidades do MVP

- [ ] Editor visual com canvas + painel lateral contextual
- [ ] 6 tipos de bloco genéricos e configuráveis
- [ ] Reordenação por drag-and-drop
- [ ] Editor de tema (cores, fonte, raio)
- [ ] Preview mobile / desktop
- [ ] Salvamento automático no navegador
- [ ] Compartilhamento por link — sem conta, sem cadastro
- [ ] Acessível por teclado, contraste WCAG AA

## Arquitetura

O MVP é **100% client-side**: sem backend e sem login. A decisão é intencional — foco total no front-end e no editor, que são o coração do produto.

```
Editor (Zustand) ──► storage.ts ──► localStorage
       │
       └──► encodeMosaic() ──► /m/<base64>  ──► decodeMosaic() ──► validação ──► Viewer
```

| Decisão | Por quê |
|---|---|
| Estado do mosaic serializado em JSON + base64 na URL | Compartilhar sem servidor. O link *é* o dado. |
| Persistência isolada em `src/lib/storage.ts` | Componentes nunca tocam no `localStorage`. Trocar por API no pós-MVP não exige refatorar a UI. |
| Schema versionado (`version: 1`) | URLs antigas continuam abrindo quando o formato mudar. |
| Blocos como *discriminated union* em TypeScript | O renderer usa `switch` exaustivo: um tipo novo sem componente é erro de compilação. |
| Tema via CSS custom properties + tokens no `@theme` (Tailwind v4) | Cada mosaic troca o visual inteiro sem gerar CSS novo. |

### Segurança

Como todo dado da página vem da URL — ou seja, do usuário —, nada é renderizado sem validação:

- `isSafeUrl()` aceita só `http:` e `https:` (bloqueia `javascript:` e `data:`)
- Embeds só de uma allowlist de domínios (YouTube, Spotify, …)
- Schema validado no `decodeMosaic()` antes de qualquer render
- `dangerouslySetInnerHTML` proibido por regra de ESLint

## Stack

| Camada | Ferramenta |
|---|---|
| UI | React + TypeScript |
| Build | Vite |
| Estilo e tokens | Tailwind CSS v4 |
| Estado | Zustand |
| Drag-and-drop | dnd-kit |
| Animação | Motion (`motion/react`), respeitando `prefers-reduced-motion` |
| Rotas | React Router |
| Testes | Vitest (funções puras em `src/lib/`) |
| Deploy | Vercel |

Design no Figma, documentação estratégica e ADRs no Notion.

## Estrutura de pastas (a partir do Sprint 1)

```
src/
├── components/
│   ├── ui/          # primitivos: Button, Input, Typography, Icon
│   └── layout/      # Header, Footer, PageWrapper
├── features/        # código agrupado por domínio
│   ├── builder/     # editor
│   ├── viewer/      # página pública /m/:encoded
│   └── landing/
├── hooks/
├── lib/             # funções puras e testadas: encode, decode, storage, isSafeUrl
├── types/           # Block, Mosaic, Theme
├── styles/          # globals.css com o bloco @theme
└── pages/
```

Organizar por *feature* em vez de por tipo de arquivo mantém tudo que muda junto no mesmo lugar.

## 📍 Estado atual

| Sprint | Foco | Estado |
|---|---|---|
| 0 | Descoberta e design: visão, persona, análise competitiva, identidade visual, wireframes, tokens | 🟡 Em andamento |
| 1 | Fundação técnica: TypeScript, estrutura, rotas, primitivos, Vitest | ⚪ |
| 2 | Sistema de blocos (renderização a partir de dados) | ⚪ |
| 3 | Builder visual (Zustand, dnd-kit, editor de tema) | ⚪ |
| 4 | Compartilhamento, segurança, acessibilidade e animação | ⚪ |
| 5 | Landing page, deploy e polimento final | ⚪ |
| 6 | **Pós-MVP:** backend e login seguro, links curtos | ⚪ |

**Já feito no Sprint 0:** Visão e Escopo, persona refinada. **Agora:** análise competitiva (Linktree, Bento.me, Beacons, Carrd, Read.cv).

O design vem antes do código de propósito: decisões visuais tomadas no meio do desenvolvimento viram retrabalho.

### Fora do MVP (v2)

Integrações com Steam, Letterboxd, Spotify e Goodreads · templates por perfil · analytics de cliques · upload de imagem · domínio próprio. Cada ideia nova vira issue com label `v2` em vez de entrar no sprint atual.

## Rodando localmente

```bash
git clone https://github.com/daniykt/Mosaic.git
cd Mosaic
npm install
npm run dev
```

Requer Node.js 20+.

## Sobre o projeto

O Mosaic é meu projeto de portfólio e de estudo. Estou usando ele pra praticar, num produto real e de ponta a ponta, o que um front-end faz no dia a dia: descoberta de produto, UI/UX, design system, arquitetura front-end e boas práticas de código.

**Danilo da Silva Pereira** — Front-end developer e web designer · estudante de Desenvolvimento de Software Multiplataforma na FATEC Matão

[LinkedIn](https://linkedin.com/in/-dsilva) · [GitHub](https://github.com/daniykt)