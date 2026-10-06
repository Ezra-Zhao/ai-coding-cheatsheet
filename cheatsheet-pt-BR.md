# Guia rápido de assistentes de IA para programar (Português BR)

> Verificado em 2026-10-06: cada comando, atalho e opção foi conferido na documentação oficial. O que não deu para verificar foi removido.
> Documentação oficial: Claude Code <https://docs.anthropic.com/en/docs/claude-code> · Codex <https://developers.openai.com/codex> · Cursor <https://docs.cursor.com>
> Tudo muda rápido; na dúvida, vale a documentação oficial.

---

## 1. Claude Code (assistente da Anthropic no terminal)

### 1.1 Iniciar e gerenciar sessões

| Comando | Descrição | Exemplo |
|---|---|---|
| `claude` | Inicia uma sessão interativa | `claude` |
| `claude "pergunta"` | Inicia já com um prompt | `claude "explica a estrutura do projeto"` |
| `claude -p "pergunta"` | Uma resposta só e sai (`--print` é a mesma coisa) | `claude -p "o que essa função faz?"` |
| `cat f.log \| claude -p "analisa"` | Analisa o que vier pelo pipe | `cat err.log \| claude -p "acha o erro"` |
| `claude -c` | Continua a última sessão deste diretório | `claude -c` |
| `claude -r "nome" "continua"` | Retoma uma sessão pelo nome ou ID | `claude -r "auth-refactor" "continua"` |
| `claude update` | Atualiza para a versão mais nova | `claude update` |
| `claude doctor` | Diagnostica instalação e configuração | `claude doctor` |
| `claude auth login` | Faz login na sua conta | `claude auth login` |
| `claude --version` | Mostra a versão | `claude --version` |

### 1.2 Parâmetros comuns na inicialização

| Parâmetro | Descrição | Exemplo |
|---|---|---|
| `--model <nome>` | Escolhe o modelo | `claude --model sonnet` |
| `--permission-mode <modo>` | Modo de permissão: `plan` / `acceptEdits` / `auto` / `manual` / `dontAsk` / `bypassPermissions` | `claude --permission-mode plan` |
| `--dangerously-skip-permissions` | Pula todas as confirmações (só em ambiente confiável) | — |
| `--allowedTools "Bash(git *)" "Read"` | Essas ferramentas rodam sem pedir permissão | `claude --allowedTools "Bash(git *)" "Read"` |
| `--add-dir <caminho>` | Diretórios extras de trabalho | `claude --add-dir ../shared-lib` |
| `--agent <nome>` | Inicia com um subagente específico | `claude --agent reviewer` |

### 1.3 Atalhos e comandos slash na sessão

| Entrada | Descrição |
|---|---|
| `Shift+Tab` | Alterna o modo de permissão (inclui o modo plan) |
| `Esc` | Interrompe a execução |
| `/help` | Lista todos os comandos (a lista mais confiável) |
| `/init` | Gera um rascunho de CLAUDE.md para o projeto |
| `/model` | Troca de modelo |
| `/context` | Mostra o uso do contexto e os arquivos de memória carregados |
| `/doctor` | Diagnostica na sessão e pode corrigir a configuração |
| `/cd <diretório>` | Troca o diretório de trabalho da sessão |
| `/usage` (apelido `/cost`) | Mostra seu consumo |
| `/review` | Revisa código (skill embutida) |
| `/debug` `/batch` `/loop` `/verify` `/run` | Skills embutidas: depurar, tarefas em lote, loops, verificar, executar |

### 1.4 CLAUDE.md: a memória do projeto

| Local | Alcance |
|---|---|
| `./CLAUDE.md` ou `./.claude/CLAUDE.md` | Do projeto, vai para o repo e o time inteiro usa |
| `~/.claude/CLAUDE.md` | Pessoal, vale para todos os seus projetos |
| `./CLAUDE.local.md` | Preferências locais, coloca no `.gitignore` |
| `.claude/rules/*.md` | Regras por caminho de arquivo (frontmatter `paths`) |
| `AGENTS.md` | Também é lido, pode conviver com o CLAUDE.md |

Dicas:

- Escreva instruções concretas e verificáveis ("antes de commitar rode `npm test`"), nada de frase vazia
- No máximo 200 linhas por arquivo; procedimento longo vira skill
- Use `@caminho` para importar outros arquivos dentro do CLAUDE.md
- `/init` gera o rascunho e você refina na mão

### 1.5 Skills e MCP

- Skill é um arquivo `SKILL.md`: pessoal em `~/.claude/skills/<nome>/SKILL.md`, do projeto em `.claude/skills/<nome>/SKILL.md`
- Chama com `/<nome>` na sessão; o Claude também ativa sozinho quando faz sentido
- Skills embutidas: `/doctor` `/code-review` `/batch` `/debug` `/loop` `/claude-api` `/verify` `/run`
- `claude mcp`: gerencia servidores MCP (ver, adicionar, autenticar) para plugar ferramentas externas
- `claude plugin`: gerencia plugins

### 1.6 Fluxo de trabalho

1. Mudança grande, primeiro no plan: inicie com `--permission-mode plan` ou troque com `Shift+Tab`; a IA propõe o plano e você aprova antes de mexer no código
2. Avance em passos pequenos e confira o `git diff` em cada um
3. Antes de mexer no código, peça para ela repetir o que entendeu: "me diz quais arquivos você vai alterar"
4. Transforme rotina repetitiva em skill e pare de explicar toda vez
5. Uma tarefa, uma sessão nova; não acumula tudo na mesma

---

## 2. Codex CLI (agente da OpenAI no terminal)

Instalação: `npm install -g @openai/codex`

### 2.1 Comandos

| Comando | Descrição | Exemplo |
|---|---|---|
| `codex` | Inicia uma sessão interativa | `codex` |
| `codex exec "tarefa"` | Executa sem interface, ideal para scripts e CI | `codex exec "conserta os testes quebrados"` |
| `codex resume` | Retoma uma sessão anterior | `codex resume` |
| `codex mcp` | Gerencia servidores MCP | `codex mcp` |
| `codex cloud` | Manda a tarefa rodar na nuvem | `codex cloud` |
| `codex --image img.png` | Anexa uma imagem ao primeiro prompt (ex. print de um erro) | `codex --image err.png "analisa esse erro"` |
| `codex --search` | Ativa busca na web nesta execução | `codex --search "pesquisa o uso atual da API"` |
| `codex completion` | Gera autocomplete para o seu shell | `codex completion` |

### 2.2 Aprovação e sandbox (o diferencial do Codex: "o que pode mexer" e "quando te pergunta" são controles separados)

| Parâmetro | Descrição | Exemplo |
|---|---|---|
| `-a, --ask-for-approval <política>` | Quando ele para para te perguntar: `on-request` (padrão) / `never` | `codex -a never exec "roda os testes e conserta"` |
| `-s, --sandbox <modo>` | O que ele pode mexer: `read-only` / `workspace-write` / `danger-full-access` | `codex -s read-only "explica esse código"` |
| `--dangerously-bypass-approvals-and-sandbox` | Libera tudo, sem aprovação nem sandbox (só em ambiente isolado e confiável) | — |
| `--profile <nome>` | Usa um perfil de configuração | `codex --profile work` |

Dentro da sessão, `/permissions` mostra e ajusta os limites daquela execução.

### 2.3 AGENTS.md e configuração

- Coloque um `AGENTS.md` na raiz do projeto: comandos de build, convenções, proibições; o Codex lê sozinho toda vez
- Configuração do usuário em `~/.codex/config.toml`; a do projeto em `.codex/config.toml` (precisa confiar no projeto)
- Chaves comuns: `model`, `approval_policy = "on-request"`, `sandbox_mode`

### 2.4 Plugin para a IDE

- Tem plugin para VS Code: conversa no editor, vê diffs e compartilha sua seleção
- Plugin e CLI usam o mesmo `AGENTS.md`, a mesma configuração e os mesmos servidores MCP

---

## 3. Cursor (IDE nativa de IA)

### 3.1 Atalhos principais (Mac: Cmd, Windows/Linux: Ctrl)

| Atalho | Descrição |
|---|---|
| `Cmd/Ctrl+K` | Edição inline: seleciona o código e diz como mudar |
| `Cmd/Ctrl+L` | Abre o chat: sua seleção entra sozinha no contexto |
| `Cmd/Ctrl+I` | Abre o Agent/Composer: alterações automáticas em vários arquivos |
| `Cmd/Ctrl+Shift+L` | Adiciona a seleção ao chat atual |
| `Cmd/Ctrl+Enter` | Enviar; se o agente estiver ocupado dá para encadear perguntas |
| `Tab` | Aceita a sugestão da IA (Cursor Tab, ativa nas configurações) |
| `Esc` | Fecha a sugestão / interrompe a IA |

### 3.2 Menções @ (para dar contexto à IA)

| Sintaxe | Descrição |
|---|---|
| `@Files` / `@Folders` | Referencia arquivos / pastas |
| `@Codebase` | Busca semântica no código inteiro |
| `@Docs` | Referencia documentação |
| `@Web` | Busca na web |
| `@Git` | Referencia histórico e diffs do git |
| `@<arquivo>` | Mencionar o arquivo pelo nome também vale |

### 3.3 Project Rules (`.cursor/rules/`)

- Uma regra por arquivo `.mdc`: `description` + `globs` (em quais arquivos vale) + instruções
- Quatro formas de ativar: Always (sempre) / Auto Attached (entra sozinha por globs) / Agent Requested (a IA decide) / Manual (só com @)
- Suas regras pessoais ficam em Configurações → Rules
- As do time vão para o repo; as só suas, não

### 3.4 Modos Agent / Plan

- Ask: só pergunta, não mexe em nada; bom para entender o código primeiro
- Agent: edita arquivos e roda comandos sozinho; você confirma cada diff antes de aceitar
- Plan: propõe o plano de implementação e executa quando você aprova
- Troca em cima da caixa de texto; a interface muda direto, vale a documentação oficial

### 3.5 Autocomplete com Tab

- Procura "Cursor Tab" nas configurações para ativar; `Tab` aceita, `Esc` rejeita
- Se atrapalhar, desliga ou deixa só em certas linguagens

---

## 4. Geral: .gitignore / prompts / segurança

### 4.1 .gitignore: o essencial

```gitignore
.env*             # chaves e tokens
*.pem *.key       # chaves privadas e certificados
node_modules/     # dependências
dist/ build/      # saída de build
.DS_Store
CLAUDE.local.md   # memória local de IA, não commita
```

O Cursor ainda tem o `.cursorignore`: ali vão os arquivos que você não quer que a IA indexe.

### 4.2 Como pedir bem (prompts)

1. Primeiro o objetivo, depois o como: "quero rate limiting no login, mexe nesses 3 arquivos; me diz seu plano primeiro"
2. Peça algo verificável: "quando terminar rode os testes e cola o resultado"
3. Devagar e sempre: uma tarefa por vez
4. Dê a lista negra: "não mexe no schema do banco, não instala dependência nova"
5. Em tarefa complexa, ela propõe o plano e você aprova antes de mexer no código

### 4.3 Segurança: linhas vermelhas

- Chaves, tokens, chaves privadas e dados de clientes nunca vão para o chat
- `--dangerously-skip-permissions` / `--dangerously-bypass-approvals-and-sandbox` só em ambiente isolado e confiável
- API keys vão em variáveis de ambiente (ex. `ANTHROPIC_API_KEY`), nunca no código nem no prompt
- Comando destrutivo ou de deploy que a IA sugerir: olha antes de dar Enter
- Em repo público não suba `.env`, endereços internos nem dados reais de usuários

---

MIT License · Copyright (c) 2026 Guangyi Zhao
