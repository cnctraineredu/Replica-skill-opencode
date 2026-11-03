# Replica Skill Pack para OpenCode

**11 skills sequenciais** para clonar qualquer app: do reconhecimento ao deploy — adaptadas do [replica-skill](https://github.com/Jakeschincariol/replica-skill) (Jake Schincariol) para OpenCode.

> **Clean-room always.** Reconstrói funcionalidade e padrões UX, nunca código, assets, copy ou APIs privadas do original.

---

## 📦 Instalação

### Opção 1: Global (disponível em todos os projetos)

```bash
# Windows (PowerShell)
Copy-Item -Path "F:\Replica Skill Opencode\replica-*" -Destination "$env:USERPROFILE\.config\opencode\skill\" -Recurse -Force

# Linux/macOS
cp -r /path/to/Replica\ Skill\ Opencode/replica-* ~/.config/opencode/skill/
```

### Opção 2: Por projeto (isolado)

```bash
# Na raiz do seu projeto
mkdir -p .opencode/skill
cp -r /path/to/Replica\ Skill\ Opencode/replica-* .opencode/skill/
```

### Opção 3: Git submodule (recomendado para times)

```bash
cd seu-projeto
git submodule add https://github.com/SEU-USUARIO/replica-skill-opencode.git .opencode/skill/replica-pack
# Depois link cada skill:
for s in .opencode/skill/replica-pack/replica-*; do
  ln -s "$s" ".opencode/skill/$(basename $s)"
done
```

---

## ✅ Verificação

Reinicie o OpenCode (ou `Ctrl+R` no TUI) e teste:

```
/replica-recon --help
```

Deve mostrar a descrição da skill. Se aparecer "Skill not found", verifique o caminho em `opencode config get skill.paths`.

---

## 🚀 Como Usar (Fluxo Completo)

Execute **em ordem** — cada skill lê o que a anterior escreveu em `replica/`:

```mermaid
recon → architect → design → build → backend → test → diff → entrepreneur → brand → launch → deploy
```

### 1. Reconhecimento — `/replica-recon`
```bash
/replica-recon "clone o Calendly"
# ou com URL:
/replica-recon "https://calendly.com"
```
**Saída:** `replica/recon.md`, `replica/features.csv`, `replica/screens/`

---

### 2. Arquitetura — `/replica-architect`
```bash
/replica-architect
```
**Lê:** `recon.md`, `features.csv`  
**Saída:** `replica/architecture.md`, `replica/schema.sql`

---

### 3. Design System — `/replica-design`
```bash
/replica-design
# Verifica contraste WCAG AA:
python3 scripts/contrast.py replica/design/tokens.json
```
**Lê:** `recon.md`, `replica/screens/`  
**Saída:** `replica/design/tokens.json`, `tokens.css`, `components.md`

---

### 4. Build — `/replica-build`
```bash
/replica-build
```
**Lê:** `recon.md`, `architecture.md`, `design/`  
**Saída:** Telas construídas, `features.csv` atualizado, `build-log.md`, `replica/clone-screens/`

---

### 5. Backend — `/replica-backend`
```bash
/replica-backend
```
**Lê:** `architecture.md`  
**Saída:** Migrations, auth, Stripe, email, jobs, `backend.md`, `.env.example`

---

### 6. Testes — `/replica-test`
```bash
/replica-test
# Roda Playwright:
npx playwright test
```
**Lê:** flows do `recon.md`  
**Saída:** `test-plan.md`, `bugs.md`, specs em `e2e/`

---

### 7. Diff/Parity — `/replica-diff`
```bash
# Feature parity:
python3 scripts/parity.py replica/features.csv

# Layout diff (ignora cor — rebrand muda tudo):
python3 scripts/imgdiff.py replica/screens/S07.png replica/clone-screens/S07.png --out diff.png

# Relatório combinado:
python3 scripts/parity.py replica/features.csv --visual replica/diffs/*.json --markdown > replica/parity.md
```
**Saída:** `parity.md`, diff images, top 5 gaps

---

### 8. Empreendedor — `/replica-entrepreneur`
```bash
# 1. Colete reviews em replica/reviews.csv (source,url,date,rating,text)
# 2. Rode:
python3 scripts/reviews.py replica/reviews.csv --out replica/feedback.md
# 3. Skill gera:
/replica-entrepreneur
```
**Saída:** `feedback.md`, `fixes.md` (3 listas, fix plan, angle), novas rows em `features.csv`

---

### 9. Marca — `/replica-brand`
```bash
# 1. Preencha replica/brand.json (avoid, domains, colors, name_candidates)
# 2. Sweep:
python3 scripts/sweep.py . --config replica/brand.json
# 3. Skill:
/replica-brand
```
**Saída:** `brand.md`, `brand.json`, tokens atualizados, strings reescritas, **sweep limpo**

---

### 10. Launch — `/replica-launch`
```bash
# 1. Preencha replica/launch/listing.json
# 2. Lint:
python3 scripts/listing.py replica/launch/listing.json
# 3. Skill:
/replica-launch
```
**Saída:** `landing.md`, `pricing.md`, `listing.json` (passa no lint), `launch-plan.md`

---

### 11. Deploy — `/replica-deploy`
```bash
# Preflight:
npx playwright test
python3 ../replica-diff/scripts/parity.py replica/features.csv
python3 ../replica-brand/scripts/sweep.py . --config replica/brand.json
python3 ../replica-launch/scripts/listing.py replica/launch/listing.json
npm run build

# Deploy:
/replica-deploy
```
**Saída:** `deploy.md` com checklist, DNS records, live URL

---

## 🛠 Scripts Python Incluídos

| Script | Skill | Função |
|--------|-------|--------|
| `contrast.py` | `replica-design` | WCAG AA check nos tokens de cor |
| `parity.py` | `replica-diff` | Feature parity score (must=3, should=2, could=1) |
| `imgdiff.py` | `replica-diff` | Layout diff PNG (edge maps, ignora cor) |
| `reviews.py` | `replica-entrepreneur` | Rank de reviews por tema (pesos por rating/recência) |
| `sweep.py` | `replica-brand` | Busca nome/domínio/cores do original no código |
| `listing.py` | `replica-launch` | Lint App Store / Google Play (limites, copycat rules) |

**Todos:** Python 3.8+, **stdlib only** — `pip install` **não necessário**.

---

## 📁 Estrutura do Projeto Durante o Uso

```
seu-projeto/
├── .opencode/skill/replica-*    # Skills instaladas
├── replica/
│   ├── recon.md                 # Recon map
│   ├── features.csv             # Feature matrix (original/clone/skip)
│   ├── screens/                 # Screenshots do original (ref)
│   ├── clone-screens/           # Screenshots do clone (para diff)
│   ├── architecture.md          # Stack, schema, API, build order
│   ├── design/
│   │   ├── tokens.json          # Design tokens (roles, não cores)
│   │   ├── tokens.css           # CSS custom properties
│   │   └── components.md        # Specs de cada componente
│   ├── build-log.md             # Log por tela
│   ├── schema.sql               # Database schema
│   ├── backend.md               # Checklist backend
│   ├── test-plan.md             # Casos F01-H1, F01-E1, F01-N1...
│   ├── bugs.md                  # Bug reports (S1-S4)
│   ├── e2e/                     # Playwright specs
│   ├── diffs/                   # JSON outputs do imgdiff.py
│   ├── parity.md                # Relatório combinado
│   ├── reviews.csv              # Reviews coletados (source,url,date,rating,text)
│   ├── feedback.md              # Output do reviews.py
│   ├── fixes.md                 # 3 listas + fix plan + angle
│   ├── brand.md                 # Nome, checks, palette, logo brief, voice
│   ├── brand.json               # Config para sweep.py e listing.py
│   ├── launch/
│   │   ├── landing.md           # Landing page sections
│   │   ├── pricing.md           # Pricing table + Stripe products
│   │   ├── listing.json         # App Store / Play Store metadata
│   │   └── launch-plan.md       # Waitlist, PH/HN, primeiros usuários
│   └── deploy.md                # Checklist preflight + DNS + live URL
└── src/                         # Seu código (Next.js, etc.)
```

---

## ⚖️ Regras de Ouro (Clean Room)

1. **Fontes públicas + sua conta apenas.** Nunca logue em conta alheia, nunca passe paywall.
2. **Leitura, não scraping.** Velocidade humana, você presente.
3. **Nenhum código fonte, nenhuma API privada.** Docs públicas OK.
4. **Cheque os termos.** Alguns proíbem usar a conta para construir concorrente.
5. **Screenshots são referência.** Ficam em `replica/screens/`, nunca vão pro clone.
6. **Suas palavras.** Labels, empty states, emails — tudo escrito do zero.
7. **Tokens only.** Zero hex/pixels hardcoded nos componentes.
8. **Rebrand antes de launch.** `sweep.py` deve sair limpo (exit 0).
9. **Nome do original NUNCA** no seu nome, store listing, keywords, ads.
10. **Nenhuma prova falsa.** Testemoniais inventados = rejeição + risco legal.

---

## 🐛 Troubleshooting

| Problema | Solução |
|----------|---------|
| "Skill not found" | Verifique `opencode config get skill.paths` — deve incluir `~/.config/opencode/skill` |
| Script Python falha | Use `python3` (não `python`); verifique encoding UTF-8 no CSV |
| `parity.py` reclama colunas | `features.csv` precisa: `feature,area,priority,original,clone,notes` |
| `sweep.py` acha false positives | Ajuste `avoid` em `brand.json` — nomes compostos viram regex flexível |
| `listing.py` erro "mentions X" | Remova o nome do original de TODOS os campos do listing |
| Playwright não acha seletores | Use `getByRole('button', { name: /.../i })`, nunca CSS classes |

---

## 📄 Licença

MIT — mesmo do [original](https://github.com/Jakeschincariol/replica-skill/blob/main/LICENSE).

> **Adaptado para OpenCode por:** [Seu Nome/Handle]  
> **Baseado no trabalho de:** Jake Schincariol ([opusjake.ai](https://opusjake.ai))

---

## 🤝 Contribuindo

1. Fork este repo
2. Crie branch: `git checkout -b feat/minha-melhoria`
3. Teste com TDD (baseline → skill → verify → refactor)
4. PR com: o que mudou, qual skill, cenário testado

---

## ⭐ Créditos

- **Jake Schincariol** — [replica-skill original](https://github.com/Jakeschincariol/replica-skill) (Claude Code)
- **OpenCode team** — Platform excelente para agents/skills
- **Comunidade** — Testes, feedbacks, melhorias

---

## 📞 Suporte

- **Issues:** [GitHub Issues](https://github.com/SEU-USUARIO/replica-skill-opencode/issues)
- **Discussões:** [GitHub Discussions](https://github.com/SEU-USUARIO/replica-skill-opencode/discussions)
- **OpenCode Docs:** [opencode.ai/v2/docs](https://opencode.ai/v2/docs/)

---

> **"It rebuilds what an app does, never what it owns."** — Jake Schincariol