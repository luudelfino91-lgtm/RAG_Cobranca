# Segurança e LGPD

## Chaves de API

- As chaves ficam **somente** no gerenciador **Secrets** do Colab (`ANTHROPIC_API_KEY`, `GPT` ou `OPENAI_API_KEY`), com *Acesso ao notebook* ativado.
- **Nunca** colocar chave no código, em célula de texto, em saída de célula, em captura de tela ou neste repositório.
- O arquivo `.env.example` lista apenas os **nomes** das variáveis, sem valores. O `.gitignore` bloqueia `.env` e variações.
- Use uma chave **dedicada** à demo, com **limite de gasto** (ou crédito pré-pago) e revogue ao final.
- Se uma chave for exposta, **revogue imediatamente** no painel do provedor e crie outra.

## O que o repositório contém (e o que não contém)

| Contém | Não contém |
|---|---|
| Código do notebook, sem saídas de execução | Chaves, tokens ou senhas |
| Fichas de produto resumidas | O PDF original |
| Política e cenários **fictícios** | Dados de clientes |
| Documentação | Links `gradio.live` de execuções |

O notebook é versionado **com as saídas limpas**: assim nenhum link temporário, resposta de teste ou metadado do ambiente vai para o histórico do Git.

## Conteúdo interno da empresa

As fichas foram resumidas de material interno ("Produtos Cobrança"). Mantenha o repositório **privado** e compartilhe apenas com quem tem acesso ao material. Confirme com a empresa se o conteúdo pode ser versionado fora dos sistemas internos.

## Link público do Gradio

- É **temporário** e **público**: qualquer pessoa com o link consegue usar o chat.
- Cada pergunta usa **a sua chave** de API (custo baixo, mas é seu).
- Compartilhe só com quem vai testar e **encerre a célula** (ou desconecte o runtime) ao terminar.
- Não há autenticação nem registro de uso. Para uso real, publicar em ambiente próprio com controle de acesso.

## LGPD

- A demo **não usa dados pessoais de clientes** e o assistente é instruído a **não pedir nem exibir** esses dados.
- Se alguém perguntar por saldo ou dívida de um cliente, o assistente informa que não tem acesso a dados de clientes.
- Os textos digitados no chat são enviados ao provedor de IA escolhido. **Não digite dados pessoais reais** durante a demonstração.
- Em produção: avaliar base legal, contrato com o provedor, local de processamento, retenção e anonimização de logs.

## Antes de publicar qualquer alteração (checklist)

- [ ] Nenhuma chave ou token no código ou nas saídas (`sk-`, `AIza`, `ghp_`, `ANTHROPIC_API_KEY=...`).
- [ ] Notebook salvo **sem saídas** (*Editar → Limpar todas as saídas*).
- [ ] Nenhum link `gradio.live` no repositório.
- [ ] Nenhum dado real de cliente, e-mail ou telefone.
- [ ] O PDF original não foi adicionado.
