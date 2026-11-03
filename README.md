Pacote de habilidades de réplica para OpenCode
11 skills sequenciais para clonar qualquer app: do reconhecimento ao deploy — adaptadas do replica-skill (Jake Schincariol) para OpenCode.

Sala limpa sempre. Reconstrua funcionalidade e padrões UX, nunca código, ativos, cópia ou APIs privadas do original.

📦 Instalação
Opção 1: Global (disponível em todos os projetos)
# Windows (PowerShell)
Copy-Item -Path "F:\Replica Skill Opencode\replica-*" -Destination "$env:USERPROFILE\.config\opencode\skill\" -Recurse -Force

# Linux/macOS
cp -r /path/to/Replica\ Skill\ Opencode/replica-* ~/.config/opencode/skill/
Opção 2: Por projeto (isolado)
# Na raiz do seu projeto
mkdir -p .opencode/skill
cp -r /path/to/Replica\ Skill\ Opencode/replica-* .opencode/skill/
Opção 3: submódulo Git (recomendado algumas vezes)
cd seu-projeto
git submodule add https://github.com/SEU-USUARIO/replica-skill-opencode.git .opencode/skill/replica-pack
# Depois link cada skill:
for s in .opencode/skill/replica-pack/replica-*; do
  ln -s "$s" ".opencode/skill/$(basename $s)"
done
✅ Verificação
Reinicie o OpenCode (ou Ctrl+Rsem TUI) e teste:

/replica-recon --help
Deve mostrar uma descrição da habilidade. Se aparecer "Habilidade não encontrada", verifique o caminho em opencode config get skill.paths.

🚀 Como Usar (Fluxo Completo)
Execute em ordem — cada habilidade lê o que a anterior escreveu em replica/:

Unable to render rich display

No diagram type detected matching given configuration for text: recon → architect → design → build → backend → test → diff → entrepreneur → brand → launch → deploy


For more information, see https://docs.github.com/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams#creating-mermaid-diagrams

reconhecimento → arquitetura → design → construção → backend → teste → comparação → empreendedorismo → marca → lançamento → implantação
1. Reconhecimento —/replica-recon
/replica-recon "clone o Calendly"
# ou com URL:
/replica-recon "https://calendly.com"
Saída: replica/recon.md , replica/features.csv,replica/screens/

2. Arquitetura —/replica-architect
/replica-architect
Lê: recon.md , features.csv
Saída: replica/architecture.md ,replica/schema.sql

3. Sistema de projeto —/replica-design
/replica-design
# Verifica contraste WCAG AA:
python3 scripts/contrast.py replica/design/tokens.json
Lê: recon.md , replica/screens/
Saída: replica/design/tokens.json , tokens.css,components.md

4. Construir —/replica-build
/replica-build
Lê: recon.md , architecture.md, design/
Saída: Telas construídas, features.csvatualizadas, build-log.md,replica/clone-screens/

5. Backend —/replica-backend
/replica-backend
Ler: architecture.md
Saída: Migrations, auth, Stripe, email, jobs, backend.md,.env.example

6. Testes —/replica-test
/replica-test
# Roda Playwright:
npx playwright test
Ler: fluxos do recon.md
Saída: test-plan.md , bugs.md, specs eme2e/

7. Diferença/Paridade —/replica-diff
# Feature parity:
python3 scripts/parity.py replica/features.csv

# Layout diff (ignora cor — rebrand muda tudo):
python3 scripts/imgdiff.py replica/screens/S07.png replica/clone-screens/S07.png --out diff.png

# Relatório combinado:
python3 scripts/parity.py replica/features.csv --visual replica/diffs/*.json --markdown > replica/parity.md
Saída: parity.md , imagens diff, 5 principais lacunas

8. Empreendedor —/replica-entrepreneur
# 1. Colete reviews em replica/reviews.csv (source,url,date,rating,text)
# 2. Rode:
python3 scripts/reviews.py replica/reviews.csv --out replica/feedback.md
# 3. Skill gera:
/replica-entrepreneur
Saída: feedback.md , fixes.md(3 listas, plano fixo, ângulo), novas linhas emfeatures.csv

9. Marca —/replica-brand
# 1. Preencha replica/brand.json (avoid, domains, colors, name_candidates)
# 2. Sweep:
python3 scripts/sweep.py . --config replica/brand.json
# 3. Skill:
/replica-brand
Saída: brand.md , brand.json, tokens atualizados, strings reescritas, varredura limpa

10. Lançamento —/replica-launch
# 1. Preencha replica/launch/listing.json
# 2. Lint:
python3 scripts/listing.py replica/launch/listing.json
# 3. Skill:
/replica-launch
Saída: landing.md , pricing.md, listing.json(passa no lint),launch-plan.md

11. Implantar —/replica-deploy
# Preflight:
npx playwright test
python3 ../replica-diff/scripts/parity.py replica/features.csv
python3 ../replica-brand/scripts/sweep.py . --config replica/brand.json
python3 ../replica-launch/scripts/listing.py replica/launch/listing.json
npm run build

# Deploy:
/replica-deploy
Saída: deploy.md lista de verificação com, registros DNS, URL ativo

🛠 Scripts Python Incluídos
Roteiro	Habilidade	‐
contrast.py	replica-design	WCAG AA verifica nos tokens de cor
parity.py	replica-diff	Pontuação de paridade de recursos (obrigatório=3, recomendável=2, possível=1)
imgdiff.py	replica-diff	Diferença de layout PNG (mapas de borda, ignorar cor)
reviews.py	replica-entrepreneur	Classificação de avaliações por tema (pesos por classificação/recência)
sweep.py	replica-brand	Busca nome/domínio/cores do original no código
listing.py	replica-launch	Lint App Store / Google Play (limitações, regras contra plágio)
Todos: Python 3.8+, somente stdlib — pip install não necessário .

📁 Estrutura do Projeto Durante o Uso
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
⚖️ Regras de Ouro (Sala Limpa)
Fontes públicas + sua conta apenas. Nunca entre em conta alheia, nunca passe paywall.
Leitura, não raspagem. Velocidade humana, você presente.
Nenhum código fonte, nenhuma API privada. Documentos públicos OK.
Verifique os termos. Algumas proíbem o uso de conta para construção concorrente.
Capturas de tela são referência. Ficam em replica/screens/, nunca vão pro clone.
Suas palavras. Rótulos, estados vazios, e-mails — tudo escrito do zero.
Apenas tokens. Componentes nos com hexadecimal/pixel zero codificados.
Rebrand antes do lançamento. sweep.py deve sair limpo (saída 0).
Nome do original NUNCA no seu nome, listagem da loja, palavras-chave, anúncios.
Nenhuma prova falsa. Testemunhos inventados = infecção + risco legal.
🐛 Solução de problemas
Problema	…
"Habilidade não encontrada"	Verificar opencode config get skill.paths— deve incluir~/.config/opencode/skill
Falha no script Python	Usar python3(não python); verifique a codificação UTF-8 no CSV
parity.pyreivindicar colunas	features.csv—feature,area,priority,original,clone,notes
sweep.pyacha falsos positivos	Ajuste avoidem brand.json— nomes compostos viram regex flexível
listing.pyerro "menciona X"	Remova o nome do original de TODOS os campos da listagem
Dramaturgo não acho selecionadores	Use getByRole('button', { name: /.../i }), nunca classes CSS
📄 Licença
MIT — mesmo do original .

Adaptado para OpenCode por: [Seu Nome/Handle]
Baseado no trabalho de: Jake Schincariol ( opusjake.ai )

🤝 Contribuindo
Faça um fork deste repositório
Ramo Crie:git checkout -b feat/minha-melhoria
Teste com TDD (linha de base → habilidade → verificação → refatorar)
PR com: o que mudou, qual habilidade, cenário testado
⭐ Créditos
Jake Schincariol – réplica da habilidade original (Claude Code)
Equipe OpenCode — Plataforma excelente para agentes/habilidades
Comunidade — Testes, feedbacks, melhorias
📞 Suporte
Problemas: Problemas do GitHub
Discussões: Discussões do GitHub
Documentação do OpenCode: opencode.ai/v2/docs
"Reconstrói o que um aplicativo faz, nunca o que ele possui." — Jake Schincariol
