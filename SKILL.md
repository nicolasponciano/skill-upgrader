---
name: skill-upgrader
description: Verifica origens e versões de skills registradas no OpenCode e atualiza pacotes quando a atualização é comprovada, segura e reversível. Use ao auditar, comparar ou atualizar skills.
---

# Skill Upgrader

Audite skills registradas e atualize somente quando a evidência e o rollback forem suficientes. A invocação explícita autoriza atualizações seguras; não autoriza mudanças fora das skills/pacotes identificados.

## Descoberta

1. Use os IDs do catálogo de skills ativo nesta sessão. Carregue cada skill pelo ID com a ferramenta `skill` para obter o caminho-base efetivamente resolvido. Não use busca recursiva em caches como catálogo de skills ativas.
2. Registre ID, caminho resolvido, origem e precedência. Para investigar colisões, verifique fontes configuradas e a resolução do ID; só conte duplicidade funcional se duas definições estiverem registradas para o mesmo ID. Pastas antigas de cache não são instalações ativas por si só.
3. Classifique como builtin, local, repositório/catalogo ou fornecida por plugin. Não altere builtin nem a própria `skill-upgrader`. Skills de plugin só podem ser atualizadas pelo gerenciador oficial do plugin, nunca por cópia ou edição manual de seus arquivos.
4. Procure origem no `SKILL.md`, lockfile/manifesto do instalador, metadados do pacote e configuração ativa. Use URLs desses registros, sem adivinhar. Agrupe skills pelo mesmo repositório ou pacote.
5. Para plugins, consulte a instalação ativa com `opencode plugin list`; use sua origem e revisão, não `package.json` ou pastas antigas de cache. `opencode api get /api/plugin` pode complementar a lista quando ela não mostrar o necessário. Não inclua MCPs, Open Mind, Buffer, Token Guard, Idioma ou configuração/hook não relacionado.

## Comparação

1. Descubra releases/tags estáveis no repositório identificado. Prefira SemVer estável e exclua prereleases. Não trate HEAD ou commit recente de uma branch como release.
2. Se o instalador registrou revisão, versão ou commit, compare-a com a referência estável. Para commits, consulte ancestralidade: mesma revisão significa atual; a revisão instalada ancestral da release prova atualização disponível; revisão à frente, divergente ou sem relação conhecida não autoriza downgrade nem sobrescrita.
3. Se não há revisão instalada, compare a pasta completa da skill com o diretório correspondente na referência remota. Confirme que os caminhos relativos e o conjunto de arquivos coincidem. Para texto, normalize CRLF/CR para LF e UTF-8/BOM de forma igual nos dois lados antes de calcular SHA-256; para arquivos binários, compare bytes. Conteúdo idêntico significa **Atualizado**. Conteúdo diferente sem versão/revisão que prove qual lado é mais recente significa **Conteúdo divergente sem versão comparável**; hash diferente, isoladamente, não prova atualização.
4. Detecte mudanças locais pelo estado Git ou pela verificação de integridade documentada pelo próprio gerenciador. Se o método de hash do lockfile for desconhecido, não o recrie por suposição: trate o estado como inconclusivo e não sobrescreva.
5. Se origem ou remoto não puderem ser consultados, informe **Sem repositório verificável** ou **Erro de consulta**, conforme o caso. Não repita uma chamada inválida; confira `--help`, a origem ativa e os parâmetros aceitos.

### Superpowers

- Use `opencode plugin list` para identificar o Superpowers ativo, seu target configurado e a revisão carregada. Ignore cópias antigas em cache que não sejam a origem ativa.
- Confira `opencode plugin check --help`. O comando documentado é `opencode plugin check [target]`; `target` é a referência de pacote configurada, não apenas o ID `superpowers`. Exemplo para a instalação cujo target é esse: `opencode plugin check 'superpowers@git+https://github.com/obra/superpowers.git'`.
- Compare a revisão ativa com a última tag estável oficial, resolvendo tags anotadas para o commit apontado. Só marque atualizado quando revisão ativa e release estável forem iguais (ou quando o check oficial disser `current` e a revisão estável tiver sido confirmada). Se versão e commit divergirem, investigue a ancestralidade; não atualize por cache antigo nem por HEAD mais novo.
- Para atualização, use apenas `opencode plugin update '<target-configurado>'`. Antes, confirme alterações locais e backup restaurável. Se não houver rollback oficial para a revisão exata, scripts de instalação não forem confiáveis ou o plugin exigir mudança de configuração/hook, não atualize e explique.

## Atualização segura

Atualize apenas itens com **Atualização disponível e comprovada**. Antes, confirme integridade local, pacote correto e backup fora da origem, com procedimento de restauração verificável. Prefira o comando oficial específico do gerenciador; nunca rode scripts remotos arbitrários, altere arquivos de plugin à mão ou atualize dependências/pacotes não relacionados. Se houver mudanças locais, incompatibilidade, migração, permissão adicional ou rollback incerto, pare sem sobrescrever.

Depois de cada atualização, confira revisão/versão instalada, caminho ativo, registro da skill, colisões e uma validação básica sem efeitos colaterais. Se falhar, restaure apenas pelo procedimento seguro já confirmado. Não considere código de saída zero como prova suficiente.

## Relatório

Use uma linha por skill ou pacote agrupado e as colunas `Skill/Pacote | Versão/revisão local | Release/revisão remota | Status | Ação`. Status:

- **Atualizado**
- **Atualização disponível e comprovada**
- **Conteúdo divergente sem versão comparável**
- **Sem repositório verificável**
- **Erro de consulta**
- **Ignorada por alteração local**
- **Inconclusiva**
- **Falha com rollback**

Informe totais de skills registradas analisadas, repositórios identificados, atualizações feitas, itens ignorados e erros. Cite evidências, backups e rollback; diferencie registro ativo de cópias em cache. Não atualize a própria `skill-upgrader`.
