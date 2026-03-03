# agent-browser

CLI de automação de navegador headless para agentes de IA. CLI rápida em Rust com fallback para Node.js.

## Instalação

### Instalação Global (recomendada)

Instala o binário nativo em Rust para máximo desempenho:

```bash
npm install -g agent-browser
agent-browser install  # Baixar o Chromium
```

Esta é a opção mais rápida -- os comandos são executados diretamente pelo CLI nativo em Rust com overhead de processamento sub-milissegundo.

### Início Rápido (sem instalar)

Execute diretamente com `npx` se quiser testar sem instalar globalmente:

```bash
npx agent-browser install   # Baixar o Chromium (apenas na primeira vez)
npx agent-browser open example.com
```

> **Nota:** O `npx` passa pelo Node.js antes de chegar ao CLI Rust, por isso é visivelmente mais lento que a instalação global. Para uso regular, instale globalmente.

### Instalação no Windows

No Windows, o `agent-browser` utiliza um binário nativo para melhor performance. Se você encontrar o erro `Daemon failed to start (socket: ...)`, siga estas etapas:

#### 1. Configure a Variável de Ambiente

O CLI pode ter dificuldade em localizar o motor do navegador (`daemon.js`) dependendo de como foi instalado. Defina o caminho base do projeto:

```powershell
# Temporário na sessão atual:
$env:AGENT_BROWSER_HOME="C:\Caminho\Para\agent-browser"

# Permanente para o usuário:
[System.Environment]::SetEnvironmentVariable('AGENT_BROWSER_HOME', 'C:\Caminho\Para\agent-browser', 'User')
```

#### 2. Execute com Permissão

Certifique-se de que o terminal tem permissões de escrita na pasta `%USERPROFILE%\.agent-browser`, onde os arquivos de controle do daemon são criados.

---

### Instalação de Projeto (dependência local)

Para projetos que desejam fixar a versão no `package.json`:

```bash
npm install agent-browser
npx agent-browser install
```

Em seguida, use via `npx` ou scripts do `package.json`:

```bash
npx agent-browser open example.com
```

### Homebrew (macOS)

```bash
brew install agent-browser
agent-browser install  # Baixar o Chromium
```

### A partir do Código-Fonte

```bash
git clone https://github.com/vercel-labs/agent-browser
cd agent-browser
pnpm install
pnpm build
pnpm build:native   # Requer Rust (https://rustup.rs)
pnpm link --global  # Torna o agent-browser disponível globalmente
agent-browser install
```

### Dependências no Linux

No Linux, instale as dependências do sistema:

```bash
agent-browser install --with-deps
# ou manualmente: npx playwright install-deps chromium
```

## Início Rápido

```bash
agent-browser open example.com
agent-browser snapshot                    # Obter árvore de acessibilidade com referências (refs)
agent-browser click @e2                   # Clicar por ref a partir do snapshot
agent-browser fill @e3 "teste@exemplo.com" # Preencher por ref
agent-browser get text @e1                # Obter texto por ref
agent-browser screenshot page.png
agent-browser close
```

### Seletores Tradicionais (também suportados)

```bash
agent-browser click "#submit"
agent-browser fill "#email" "teste@exemplo.com"
agent-browser find role button click --name "Submit"
```

## Comandos

### Comandos Principais

```bash
agent-browser open <url>              # Navegar para URL (aliases: goto, navigate)
agent-browser click <sel>             # Clicar no elemento (--new-tab para abrir em nova aba)
agent-browser dblclick <sel>          # Clique duplo no elemento
agent-browser focus <sel>             # Focar no elemento
agent-browser type <sel> <text>       # Digitar no elemento
agent-browser fill <sel> <text>       # Limpar e preencher
agent-browser press <key>             # Pressionar tecla (Enter, Tab, Control+a) (alias: key)
agent-browser keyboard type <text>    # Digitar com teclas reais (sem seletor, foco atual)
agent-browser keyboard inserttext <text>  # Inserir texto sem eventos de tecla (sem seletor)
agent-browser keydown <key>           # Segurar tecla
agent-browser keyup <key>             # Soltar tecla
agent-browser hover <sel>             # Passar o mouse sobre o elemento
agent-browser select <sel> <val>      # Selecionar opção de dropdown
agent-browser check <sel>             # Marcar checkbox
agent-browser uncheck <sel>           # Desmarcar checkbox
agent-browser scroll <dir> [px]       # Rolar (up/down/left/right, --selector <sel>)
agent-browser scrollintoview <sel>    # Rolar elemento para a visualização (alias: scrollinto)
agent-browser drag <src> <tgt>        # Arrastar e soltar
agent-browser upload <sel> <files>    # Enviar arquivos
agent-browser screenshot [path]       # Tirar print (--full para página inteira)
agent-browser screenshot --annotate   # Print anotado com números nos elementos interativos
agent-browser pdf <path>              # Salvar como PDF
agent-browser snapshot                # Árvore de acessibilidade com refs (melhor para IA)
agent-browser eval <js>               # Executar JavaScript (-b para base64)
agent-browser connect <port>          # Conectar ao navegador via CDP
agent-browser close                   # Fechar o navegador (aliases: quit, exit)
```

### Obter Informações

```bash
agent-browser get text <sel>          # Obter conteúdo de texto
agent-browser get html <sel>          # Obter innerHTML
agent-browser get value <sel>         # Obter valor de input
agent-browser get attr <sel> <attr>   # Obter atributo
agent-browser get title               # Obter título da página
agent-browser get url                 # Obter URL atual
agent-browser get count <sel>         # Contar elementos correspondentes
agent-browser get box <sel>           # Obter caixa delimitadora (bounding box)
agent-browser get styles <sel>        # Obter estilos computados
```

### Verificar Estado

```bash
agent-browser is visible <sel>        # Verificar se está visível
agent-browser is enabled <sel>        # Verificar se está habilitado
agent-browser is checked <sel>        # Verificar se está marcado
```

### Localizar Elementos (Locators Semânticos)

```bash
agent-browser find role <role> <action> [value]       # Por regra ARIA (role)
agent-browser find text <text> <action>               # Por conteúdo de texto
agent-browser find label <label> <action> [value]     # Por etiqueta (label)
agent-browser find placeholder <ph> <action> [value]  # Por placeholder
agent-browser find alt <text> <action>                # Por texto alternativo (alt)
agent-browser find title <text> <action>              # Por atributo title
agent-browser find testid <id> <action> [value]       # Por data-testid
agent-browser find first <sel> <action> [value]       # Primeira correspondência
agent-browser find last <sel> <action> [value]        # Última correspondência
agent-browser find nth <n> <sel> <action> [value]     # N-ésima correspondência
```

**Ações:** `click`, `fill`, `type`, `hover`, `focus`, `check`, `uncheck`, `text`

**Opções:** `--name <name>` (filtrar role pelo nome acessível), `--exact` (exigir correspondência exata de texto)

**Exemplos:**
```bash
agent-browser find role button click --name "Enviar"
agent-browser find text "Entrar" click
agent-browser find label "E-mail" fill "teste@teste.com"
```

### Esperar (Wait)

```bash
agent-browser wait <selector>         # Esperar elemento ficar visível
agent-browser wait <ms>               # Esperar tempo determinado (milissegundos)
agent-browser wait --text "Bem-vindo" # Esperar texto aparecer
agent-browser wait --url "**/dash"    # Esperar padrão de URL
agent-browser wait --load networkidle # Esperar estado de carregamento
agent-browser wait --fn "window.ready === true"  # Esperar condição JS
```

**Estados de carregamento:** `load`, `domcontentloaded`, `networkidle`

### Controle do Mouse

```bash
agent-browser mouse move <x> <y>      # Mover mouse
agent-browser mouse down [button]     # Pressionar botão (left/right/middle)
agent-browser mouse up [button]       # Soltar botão
agent-browser mouse wheel <dy> [dx]   # Roda do mouse (scroll wheel)
```

### Configurações do Navegador

```bash
agent-browser set viewport <w> <h>    # Definir tamanho da visualização
agent-browser set device <name>       # Emular dispositivo ("iPhone 14")
agent-browser set geo <lat> <lng>     # Definir geolocalização
agent-browser set offline [on|off]    # Alternar modo offline
agent-browser set headers <json>      # Cabeçalhos HTTP extras
agent-browser set credentials <u> <p> # Autenticação básica HTTP
agent-browser set media [dark|light]  # Emular esquema de cores (dark/light mode)
```

### Cookies e Armazenamento

```bash
agent-browser cookies                 # Obter todos os cookies
agent-browser cookies set <name> <val> # Definir cookie
agent-browser cookies clear           # Limpar cookies

agent-browser storage local           # Obter todo o localStorage
agent-browser storage local <key>     # Obter chave específica
agent-browser storage local set <k> <v>  # Definir valor
agent-browser storage local clear     # Limpar tudo

agent-browser storage session         # O mesmo para sessionStorage
```

### Rede (Network)

```bash
agent-browser network route <url>              # Interceptar requisições
agent-browser network route <url> --abort      # Bloquear requisições
agent-browser network route <url> --body <json>  # Simular (mock) resposta
agent-browser network unroute [url]            # Remover rotas
agent-browser network requests                 # Visualizar requisições rastreadas
```

### Abas e Janelas

```bash
agent-browser tab                     # Listar abas
agent-browser tab new [url]           # Nova aba (opcionalmente com URL)
agent-browser tab <n>                 # Mudar para aba n
agent-browser tab close [n]           # Fechar aba
agent-browser window new              # Nova janela
```

### Sessões

Execute várias instâncias isoladas do navegador:

```bash
# Diferentes sessões
agent-browser --session agente1 open site-a.com
agent-browser --session agente2 open site-b.com

# Ou via variável de ambiente
AGENT_BROWSER_SESSION=agente1 agent-browser click "#btn"
```

Cada sessão tem seu próprio:
- Instância do navegador
- Cookies e armazenamento
- Histórico de navegação
- Estado de autenticação

## Uso com Agentes de IA

### Apenas peça ao agente

A abordagem mais simples -- apenas diga ao seu agente para usá-lo:

```
Use o agent-browser para testar o fluxo de login. Rode agent-browser --help para ver os comandos disponíveis.
```

O output do `--help` é abrangente e a maioria dos agentes consegue entender a partir daí.

### Fluxo Ideal para IA

```bash
# 1. Navegar e obter snapshot
agent-browser open exemplo.com
agent-browser snapshot -i --json   # IA processa a árvore e as refs

# 2. IA identifica as refs de destino no snapshot
# 3. Executar ações usando as refs
agent-browser click @e2
agent-browser fill @e3 "texto de entrada"

# 4. Novo snapshot se a página mudar
agent-browser snapshot -i --json
```

## Arquitetura

O agent-browser utiliza uma arquitetura cliente-daemon:

1. **CLI em Rust** (binário nativo rápido) - Processa comandos, comunica-se com o daemon.
2. **Daemon em Node.js** - Gerencia a instância do navegador Playwright.
3. **Fallback** - Se o binário nativo não estiver disponível, usa Node.js diretamente.

O daemon inicia automaticamente no primeiro comando e persiste entre os comandos para operações subsequentes rápidas.

## Licença

Apache-2.0
