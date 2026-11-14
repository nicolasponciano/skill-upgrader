# skill-upgrader

Skill global para OpenCode que audita skills registradas, verifica origem e versão e atualiza pacotes somente quando a atualização é comprovada e segura.

## Instalação

Clone o repositório e entre na pasta:

```sh
git clone https://github.com/nicolasponciano/skill-upgrader.git
cd skill-upgrader
```

Copie `SKILL.md` para a pasta global de skills:

- Windows PowerShell: `$HOME/.config/opencode/skills/skill-upgrader/SKILL.md`
- macOS/Linux: `~/.config/opencode/skills/skill-upgrader/SKILL.md`

PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME/.config/opencode/skills/skill-upgrader" | Out-Null
Copy-Item ./SKILL.md "$HOME/.config/opencode/skills/skill-upgrader/SKILL.md" -Force
```

macOS/Linux:

```sh
mkdir -p ~/.config/opencode/skills/skill-upgrader
cp SKILL.md ~/.config/opencode/skills/skill-upgrader/SKILL.md
```

## Uso

Invoque com `@skill-upgrader` ou peça para auditar/atualizar skills. Ao carregar, ela usa o catálogo e caminhos ativos do OpenCode, agrupa skills por pacote/repositório e não confunde cópias antigas de cache com instalações ativas.

## Segurança

- Compara releases estáveis, revisões Git e, quando necessário, conteúdo de pastas com normalização de texto.
- Não conclui que há atualização só por hashes diferentes ou commits móveis mais recentes.
- Não sobrescreve alterações locais nem arquivos de skills de plugins. Plugins podem ser atualizados apenas pelo comando oficial do gerenciador.
- Exige backup e rollback verificável; interrompe quando a origem, segurança ou reversão forem inconclusivas.
- Não instala dependências, não executa scripts remotos arbitrários e não altera MCPs ou configurações não relacionadas.

## Status do relatório

`Atualizado`, `Atualização disponível e comprovada`, `Conteúdo divergente sem versão comparável`, `Sem repositório verificável`, `Erro de consulta`, `Ignorada por alteração local`, `Inconclusiva` e `Falha com rollback`.

## Licença

MIT. Consulte [LICENSE](LICENSE).
