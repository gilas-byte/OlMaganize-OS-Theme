# OlMaganize-OS-Theme

[![Licença: GPL-3.0-or-later](https://img.shields.io/badge/license-GPL--3.0--or--later-blue)](LICENSE) ![Linguagem: Python](https://img.shields.io/badge/language-Python-blue)

Tema escuro para Arch Linux/CachyOS com KDE Plasma 6 e suporte parcial ao Windows 11.
Paleta centralizada de marrons, texto bege e acentos amarelos; o nome interno é **Gibi**.

> **Créditos:** a paleta e a sensação visual deste tema vêm do **[Primary for Obsidian](https://github.com/primary-theme/obsidian)**, de **Cecilia May**. Ele serviu de inspiração e é a fonte da paleta base. Nenhum código do Primary foi copiado, só os valores de cor.

## O que faz

- Gera cores, formas, tipografia e configurações a partir de `palette.toml`.
- No Linux, integra KDE, Klassy, Kvantum, GTK, Ghostty, ícones Papirus, Brave e Stylus.
- No Windows 11, configura fontes por usuário, modo escuro, destaque, Windows Terminal,
  YASB, Brave e Stylus; gera configurações para três mods do Windhawk.
- Aplica os alvos detectados, verifica o contraste obrigatório e mantém backups para
  restauração. A aplicação é projetada para ser idempotente.

**Limitações:** o suporte Linux é voltado a Arch/CachyOS com Plasma 6. Apps QML/Kirigami
recebem o estilo parcialmente; alguns estilos web têm cores fixas e contraste abaixo
das metas. Brave, Stylus e Windhawk exigem etapas manuais. No Windows, não há tema
`.msstyles` (W3), a fonte da interface continua Segoe UI e as partes Win32 do Explorer
mantêm o visual padrão escuro. O tema não configura o Obsidian nem o layout dos painéis.

Consulte os detalhes de [alvos](docs/GUIA-TECNICO.md#alvos),
[limitações Linux e web](docs/GUIA-TECNICO.md#limitações-conhecidas) e
[Windows](docs/GUIA-TECNICO.md#windows).

## Estrutura do repositório

- `palette.toml`: cores, tipografia, formas, nome do tema e opções dos alvos.
- `install.sh` / `uninstall.sh` e `install.ps1` / `uninstall.ps1`: aplicação e restauração.
- `tooling/`: implementação Python; `tooling/backends/` contém as integrações Windows.
- `targets/`: definições em `target.toml`, templates e geradores.
- `tests/`: testes de instalação/restauração, invariantes, checks e previews visuais.
- `vendor/catppuccin-userstyles`: submódulo usado pelo gerador Stylus.
- `docs/`, `ARCHITECTURE.md` e `PHASE-*.md`: guia técnico, checklists, arquitetura e fases.

`build/` é gerado pelo tooling e ignorado pelo Git. Como os alvos Linux podem usar
symlinks para essa pasta, mantenha o clone no local escolhido após instalar.

## Como rodar

Requisitos: **Python >= 3.11** (tooling usa somente a biblioteca padrão) e Git.
No Linux, o instalador exige `pacman`, `kreadconfig6` e `kwriteconfig6`, além do Plasma 6.
As dependências de cada alvo estão na [tabela completa](docs/GUIA-TECNICO.md#dependências).
O tema usa arquivos do usuário; a instalação de pacotes Linux ausentes pode solicitar
confirmação e privilégios. No Windows, as fontes são baixadas de releases oficiais
com SHA-256 fixado, sem exigir administrador.

### Linux — Arch/CachyOS com KDE Plasma 6

```sh
git clone --recursive https://github.com/gilas-byte/OlMaganize-OS-Theme.git
cd OlMaganize-OS-Theme
./install.sh --list
./install.sh --dry-run
./install.sh
```

Se o clone já existe sem o submódulo, execute `git submodule update --init` na raiz.
Após a primeira instalação, faça logout/login e siga os
[checklists das fases Linux](docs/GUIA-TECNICO.md#arquitetura-e-fases).
Para aplicar um alvo específico: `./install.sh --only ghostty`.

### Windows 11 — PowerShell

No clone com o submódulo inicializado e Python >= 3.11 instalado, execute na raiz:

```powershell
powershell -ExecutionPolicy Bypass -File .\install.ps1 -List
powershell -ExecutionPolicy Bypass -File .\install.ps1 -DryRun
powershell -ExecutionPolicy Bypass -File .\install.ps1
```

`-ExecutionPolicy Bypass` vale apenas para o processo iniciado. O instalador procura
Python via `py -3`, `python` e instalações locais; `GIBI_PYTHON` permite indicar o executável.
Siga os checklists [W1](docs/CHECKLIST-W1.md) e [W2](docs/CHECKLIST-W2.md), incluindo a
importação manual das configurações do Windhawk.

### Verificar e restaurar

Na raiz do clone, `python3 -m tooling check` verifica o contraste no Linux.
`tests/selftest.sh` testa renderização, dry-run, idempotência, backup e restauração
em diretórios temporários. Os demais checks e previews estão no
[guia técnico](docs/GUIA-TECNICO.md#instalação).

Para restaurar, confira primeiro `./uninstall.sh --dry-run` e execute `./uninstall.sh`.
No Windows, use `powershell -ExecutionPolicy Bypass -File .\uninstall.ps1 -DryRun`
e depois o mesmo comando sem `-DryRun`. Os backups ficam em
`~/.local/state/gibi/` no Linux e `%LOCALAPPDATA%\gibi\state\` no Windows.
As configurações do Windhawk precisam ser restauradas manualmente a partir do backup indicado.

## Créditos

> **Tema Kvantum:** baseado no **KvFlat**, de **Tsu Jan**, um dos temas que acompanham o [Kvantum](https://github.com/tsujan/Kvantum) (licença **GPL-3.0-or-later**). Do KvFlat vieram a estrutura do `.kvconfig` e o conjunto de elementos; o SVG foi redesenhado por `targets/kvantum/generate.py` para ter o efeito de tecla.
>
> **Plasma Style:** baseado no **Breeze** (tema `default` do Plasma, do **KDE Visual Design Group**, licença **LGPL**). Do Breeze vieram os nomes de arquivos, prefixos, elementos `hint-*` e margens; o desenho é refeito por `targets/plasma-style/generate.py`.
>
> **Páginas web (Stylus):** os estilos vêm do **[Catppuccin Userstyles](https://github.com/catppuccin/userstyles)** (**Catppuccin Org**, licença **MIT**, texto em `targets/stylus/LICENSE-catppuccin-userstyles`), fixados como submodule em `vendor/catppuccin-userstyles`. `targets/stylus/generate.py` troca só a paleta (e o header) de cada estilo; o CSS de cada site é do Catppuccin.
>
> **YASB (Windows):** `targets/yasb/templates/gibi.css` redefine as variáveis dos dois estilos que acompanham o **[YASB](https://github.com/amnweb/yasb)** (**amnweb**, licença **MIT**): o "i3-style" (`--bar_bg`, `--text`...) e o do assistente (`--yasb-*`). Só os nomes vêm do YASB; os valores e as regras são do Gibi.
>
> **Windhawk (Windows):** os alvos `windhawk-*` geram configurações para os mods *Windows 11 Start Menu Styler*, *Notification Center Styler* e *File Explorer Styler*, de **m417z** (autor do **[Windhawk](https://windhawk.net)**). Os nomes dos elementos XAML vêm dos temas embutidos nesses mods (`TintedGlass`, `TranslucentStartMenu`); nenhum código foi copiado, e as cores são da paleta.

## Status

Ativo, em desenvolvimento: fases Linux 1–4 e Windows W1/W2 implementadas; W3 ainda planejada.

## Licença

[GPL-3.0-or-later](LICENSE). O tema Kvantum mantém a licença **GPL-3.0-or-later**
da base KvFlat; o Plasma Style deriva do Breeze (**LGPL**). Os estilos Catppuccin
mantêm sua [licença MIT](targets/stylus/LICENSE-catppuccin-userstyles).
