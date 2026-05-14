# leandromanique.com

Landing pessoal canônica de Leandro Manique, founder da ZEITH Co. + área de artigos. HTML estático, zero build, zero framework.

## Estrutura

```
leandromanique-com/
├── index.html                       (landing pessoal single-page)
├── imagens/
│   ├── leandro-canonica.png         (3000x3750, hero + OG image + Wikimedia)
│   ├── leandro-palestrando.png      (E-E-A-T sobre)
│   ├── leandro-praca-germania.png   (secundaria)
│   └── leandro-miami.png            (lifestyle)
├── artigos/
│   ├── index.html                   (lista de artigos, ordem cronologica reversa)
│   ├── post.html                    (renderer universal de artigo)
│   ├── posts.json                   (manifesto dos artigos)
│   └── posts/
│       ├── presenca-canonica-em-ias.md
│       ├── trafego-pago-x-outbound.md
│       └── saturacao-publicitaria-redes-sociais.md
├── favicon.svg
├── vercel.json                      (rewrites /artigos/[slug] + headers seguranca)
├── robots.txt
├── sitemap.xml
└── README.md                        (este arquivo)
```

## Como subir um artigo novo (3 passos, ~2 minutos)

### Passo 1, criar o arquivo Markdown

Em `artigos/posts/`, criar `slug-do-artigo.md` (slug em kebab-case, sem acento). Estrutura mínima:

```markdown
# Título do artigo (vira o h1 da página)

Primeiro parágrafo, geralmente o lead que sintetiza o argumento.

## Subtítulo da primeira seção

Mais parágrafos. Pode usar **negrito**, *itálico*, [links](https://exemplo.com), listas, blockquotes, code blocks, tabelas GFM.

| Coluna A | Coluna B |
|---|---|
| valor 1 | valor 2 |
```

### Passo 2, adicionar entrada em `posts.json`

Abrir `artigos/posts.json` e adicionar uma entrada nova no início do array (ordem não importa, lista é ordenada por data automaticamente):

```json
{
  "slug": "slug-do-artigo",
  "title": "Título do artigo (igual ao h1 do .md)",
  "date": "2026-05-14",
  "excerpt": "Resumo em 1-2 frases que aparece no card da lista e como og:description no compartilhamento social."
}
```

Importantes:
- `slug` deve bater EXATAMENTE com o nome do arquivo `.md` (sem extensão)
- `date` em formato `YYYY-MM-DD`
- `excerpt` ≤ 200 caracteres pra ficar bom em OG cards de WhatsApp/LinkedIn

### Passo 3, deploy

```powershell
cd C:\Users\maniq\Downloads\Zeith_Workspace\projetos\leandromanique-com
git add artigos/
git commit -m "novo artigo: <título curto>"
git push
```

Vercel auto-deploy em ~30 segundos. Live em `https://leandromanique.com/artigos/slug-do-artigo`.

## Como editar um artigo já publicado

1. Editar o `.md` correspondente em `artigos/posts/`
2. Se mudou o título ou excerpt, atualizar também o `posts.json`
3. Git push, Vercel re-deploy automático

Se mudou só o conteúdo do `.md`, **não precisa** mexer no JSON. Mas atualize `date` se quiser que apareça mais acima na lista.

## Como remover um artigo

1. Deletar a entrada do `posts.json`
2. Deletar o arquivo `.md` correspondente em `artigos/posts/`
3. Atualizar `sitemap.xml` removendo a entrada `<url>`
4. Git push

## Schema.org canônico injetado

### Landing principal
- **Person** `@id: https://leandromanique.com/#person`
- **Organization** `@id: https://zeithco.com/#organization` (cross-link)
- **WebSite + AboutPage + BreadcrumbList**
- **FAQPage** com 5 perguntas-chave (alinhadas com baseline D0)

### Cada artigo (gerado dinamicamente em runtime)
- **BlogPosting** com headline, datePublished, wordCount, author, publisher, image, mainEntityOfPage, isPartOf Blog

### Lista de artigos
- **Blog** entity com author + publisher apontando pro Person canonical

Cross-references: `Person.worksFor` → `Organization`, `BlogPosting.author` → `Person`, `BlogPosting.isPartOf` → `Blog`.

## Stack técnica

- **HTML/CSS vanilla** (mesma identidade visual em toda página)
- **JS vanilla** pra renderizar lista + markdown
- **marked.js 12.0.0** (CDN) pra parsear Markdown a HTML
- **DOMPurify 3.0.9** (CDN) pra sanitizar HTML antes de inserir (anti-XSS)
- **URLs limpas** via Vercel rewrites: `/artigos/meu-artigo` → `post.html?slug=meu-artigo`
- **Schema BlogPosting** JSON-LD injetado em runtime no `<head>` do post.html
- **Lista chronological reversa** automática (ordenada por `date` no JSON)
- **Mobile-first** breakpoint @768px

## Deploy Vercel, passo a passo

### Pré-requisitos
- Conta Vercel (já tem, `leandro-6813`)
- Domínio `leandromanique.com` (você já tem)

### Deploy via CLI Vercel

```powershell
cd C:\Users\maniq\Downloads\Zeith_Workspace\projetos\leandromanique-com
npx vercel --prod
```

Quando perguntar:
- Set up and deploy? Y
- Which scope? Leandro Manique
- Link to existing project? N
- What's your project's name? `leandromanique-com`
- In which directory is your code located? `./`
- Want to override settings? N

Após primeiro deploy, configurar domínio:
1. Vercel Dashboard → projeto `leandromanique-com` → Settings → Domains
2. Add `leandromanique.com` e `www.leandromanique.com`
3. Atualizar DNS do `leandromanique.com` apontando para Vercel:
   - `A` record: `76.76.21.21`
   - `CNAME` (www): `cname.vercel-dns.com`
4. Aguardar propagação DNS (5min, 24h)
5. HTTPS é automático via Vercel

### Pós-deploy, validações obrigatórias

- [ ] Validator Schema, https://validator.schema.org/?url=https://leandromanique.com/, zero erros
- [ ] Rich Results, https://search.google.com/test/rich-results?url=https://leandromanique.com/, elegibilidade Person + FAQPage
- [ ] Rich Results no artigo, https://search.google.com/test/rich-results?url=https://leandromanique.com/artigos/presenca-canonica-em-ias, elegibilidade BlogPosting
- [ ] Lighthouse mobile, rodar 3x, mediana >=90
- [ ] Google Search Console, adicionar propriedade + submeter sitemap + request-indexing das 5 URLs principais
- [ ] Bing Webmaster Tools, idem

## Conexão com método Optimize (cliente ZTH-SELF)

Esta landing + área de artigos é parte da Fase D1 do plano de auto-aplicação do Optimize ao founder:
- Plano mestre, `.claude/plans/precisamos-realizar-o-show-merry-ripple.md`
- Pasta do cliente, `projetos/zeith-showcase/optimize/clientes/leandro-manique/`
- Entidade canônica, `entidade-canonica.json`

Quando o Wikidata Q-id da Pessoa e da Empresa estiverem criados (D2 e D3), atualizar:
- `Person.sameAs` no index.html, adicionar `https://www.wikidata.org/wiki/Q<id-pessoa>`
- `Person.identifier` no index.html, adicionar PropertyValue `wikidata`
- `Person.image.url` no index.html, trocar pra `https://commons.wikimedia.org/wiki/Special:FilePath/<arquivo>` após upload Wikimedia (D4)

## Otimizações pendentes pós-deploy inicial

1. **Converter PNGs pra WebP**, arquivos atuais somam ~33MB. Meta, <500KB por imagem. Usar sharp ou squoosh.cli:
   ```
   npx @squoosh/cli --webp '{"quality": 82}' imagens/leandro-canonica.png
   ```
   Lighthouse mobile vai bater 90 só com isso.

2. **OG image dedicada por artigo**, hoje todos os artigos usam `leandro-canonica.png` como og:image. Ideal: criar OG 1200x630 por artigo (texto sobreposto). Pode esperar.

3. **RSS feed**, gerar `artigos/feed.xml` a partir do `posts.json`. Útil pra quem quer assinar via leitor RSS.

4. **Busca client-side**, se a lista passar de 30 artigos, adicionar campo de busca no `index.html` filtrando localmente.

## Identidade visual aplicada

Paleta puxada de `zeithco.com/sobre.html`:
- bg, `#0a0e14`
- text, `#f0ede8`
- accent, `#4B69A6`
- border, `#1c2028`

Tipografia:
- Display, Space Grotesk 600
- Body, DM Sans 300/400
- Mono, JetBrains Mono 400/500

Padrões aplicados em toda página: nav fixed top com blur, max-width 760-880px, label mono caps com linha lateral, noise overlay 0.03 opacity.

## Conteúdo dos 3 artigos atuais

| Slug | Título | Data | Tema |
|---|---|---|---|
| `presenca-canonica-em-ias` | Por que ser encontrado pelas IAs vale mais que aparecer no Google | 2026-05-14 | ZEITH Showcase, AEO/GEO, Wikidata, Schema |
| `trafego-pago-x-outbound` | Tráfego pago x outbound, quando encontrar o cliente certo custa mais do que falar com ele | 2026-05-12 | Zeith Outbound, ICP B2B, prospecção orientada por dados |
| `saturacao-publicitaria-redes-sociais` | A saturação publicitária nas redes sociais e o retorno à lógica dos canais de comunicação | 2026-05-06 | Crítica do mídia paga social, retorno do e-mail/WhatsApp como canais |
