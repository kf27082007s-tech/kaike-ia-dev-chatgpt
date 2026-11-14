# Detalhamento técnico — Service Desk Acadêmico

## Objetivo

Este documento resume as solicitações atendidas no Service Desk Acadêmico, os problemas encontrados, a abordagem usada para transformar os prompts em mudanças e o fluxo de versionamento no Git/GitHub.

## Bugs e melhorias implementados

As alterações do aplicativo foram desenvolvidas na branch `Preprod`:

1. **Selo de status desalinhado** — no cabeçalho dos detalhes do chamado, o selo era esticado pelo layout flexível. O CSS passou a alinhar o selo no início do cabeçalho, preservando sua altura natural.
2. **Histórico difícil de visualizar** — a tabela do Histórico foi substituída por um quadro Kanban com colunas por status, contadores e cartões com ID, título, solicitante, prioridade e data de atualização. Chamados continuam agrupados por status; colunas vazias são indicadas.
3. **Chamados resolvidos na fila ativa** — chamados com status `Resolvido` foram removidos da fila do analista, mas continuam no Histórico. A seleção inicial passou a preferir chamados não resolvidos, e a seleção é limpa quando o chamado aberto no painel é resolvido.
4. **Prioridade não refletia urgência** — a fila passou a ordenar chamados ativos de `Crítica` para `Alta`, `Média` e `Baixa`, mantendo a ordem relativa entre chamados de mesma prioridade.
5. **Validação de e-mail insuficiente** — além do campo obrigatório e do tipo `email`, foi adicionada uma regra para exigir `@` e um ponto após o `@`.
6. **Nome vazio composto por espaços** — o atributo HTML `required`, isoladamente, aceitava texto contendo apenas espaços. Foi adicionada validação para exigir pelo menos um caractere não branco.
7. **Busca incompleta** — a busca anunciava ID, título e solicitante, mas só verificava ID e título. O nome do solicitante foi incluído na pesquisa, sem diferenciação entre maiúsculas e minúsculas. Chamados resolvidos continuam fora da busca da fila ativa.

## Construção dos prompts e abordagem

As solicitações foram feitas em linguagem natural, em português, normalmente descrevendo o resultado desejado ou o sintoma observado. Quando havia um problema visual, uma captura de tela ou a indicação da área afetada ajudava a localizar o contexto. Os pedidos funcionais especificavam a regra esperada, por exemplo:

- alinhar o selo de status dentro do próprio fundo;
- retirar chamados resolvidos da fila de atendimento;
- mostrar primeiro os chamados mais críticos;
- exigir `@` e `.` no e-mail e impedir nomes preenchidos só com espaços;
- permitir busca pelo ID, título ou nome do solicitante;
- exibir o Histórico como quadro Kanban.

O fluxo de trabalho foi investigar o HTML, CSS e JavaScript existentes; identificar a causa no ponto correspondente; fazer mudanças localizadas que preservassem o restante do comportamento; e validar o resultado. Para as regras de formulário e busca, foram usados testes no navegador. A sintaxe JavaScript e os diffs também foram verificados quando aplicável. Não foi encontrado no projeto um conjunto automatizado de testes para essas funcionalidades.

## Versionamento e publicação

- O repositório usa o remoto `upstream` para o GitHub. Inicialmente `origin` e `upstream` já existiam e apontavam para o mesmo repositório; por isso, o comando para renomear `origin` falhou. O remoto duplicado `origin` foi removido, mantendo `upstream`.
- Um primeiro push foi recusado com HTTP 403 porque a credencial ativa não tinha permissão de escrita. A autenticação foi ajustada pelo Git Credential Manager para uma conta com acesso, e a branch pôde ser publicada. A identidade de autoria dos commits é uma configuração separada da autenticação de push.
- `Staging` foi criada e publicada. Em seguida, a alteração que já estava nela foi integrada por fast-forward em `main`, até o commit `a07b1a3`.
- `Preprod` foi criada a partir de `main` para receber as alterações subsequentes do Service Desk. Os commits funcionais foram publicados nessa branch.
- Depois da validação funcional, `Preprod` foi integrada em `main` por fast-forward, sem conflitos, avançando o código de produção de `a07b1a3` para `7bf7723`.
- Este relatório é incluído em `main` em um commit documental separado do merge do código. Assim, o histórico distingue a promoção do aplicativo da documentação do processo.

### Commits funcionais registrados em `Preprod`

| Commit | Descrição |
|---|---|
| `0a96067` | `bugfix: corrige alinhamento do selo de status` |
| `9a8bae1` | `feature: transforma histórico em quadro Kanban` |
| `22263f3` | `bugfix: prioriza fila e oculta resolvidos` |
| `b067daa` | `bugfix: valida formato do e-mail` |
| `3a83c2d` | `bugfix: corrige busca por solicitante` |
| `7bf7723` | `bugfix: rejeita nome vazio no chamado` |

## Estado e próximo passo

O código do aplicativo descrito neste relatório está integrado em `main` até `7bf7723`, o mesmo conteúdo funcional publicado em `Preprod`. Este arquivo `DETALHAMENTO.md` fica na raiz de `main` e é registrado em um commit documental próprio. Após o push final, `main` contém tanto o aplicativo atualizado quanto o relatório.
