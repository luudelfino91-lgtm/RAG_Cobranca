# Guia célula a célula

Roteiro para **explicar cada etapa aos gestores**. Os mesmos textos estão no notebook, antes de cada célula de código.

## Célula 1 — Instalação
**Fazer:** rodar e esperar terminar.
**Explicar:** instala `anthropic` e `openai` (comunicação com o modelo; usa a que tiver chave) e `gradio` (a página de chat com link).

## Célula 2 — Chave de API e teste de conexão
**Fazer:** em **Secrets**, criar `ANTHROPIC_API_KEY` ou `GPT` (ou `OPENAI_API_KEY`) com *Acesso ao notebook* ativado; rodar.
**Explicar:** a chave **nunca** fica escrita no código. `chamar_modelo` é a única parte que conhece o provedor. `MAX_TOKENS_RESPOSTA` limita o tamanho da resposta e, portanto, o custo. O teste final confirma a conexão e mostra a contagem de tokens, a unidade de cobrança.

## Célula 3 — Base de conhecimento: fichas de produto
**Fazer:** rodar.
**Explicar:** é o "cérebro" do chat. Cada produto virou uma ficha com nome, categoria, regra de atualização de valor (D+0, D-2, fechamento da fatura), descrição, pontos de atenção e palavras-chave. Fichas em vez de fatiar o PDF, porque cada slide já é uma unidade completa de sentido. Para novos produtos, basta uma nova ficha.

## Célula 4 — Regras de cobrança, política de acordos (fictícia) e glossário
**Fazer:** rodar.
**Explicar:** três blocos separados de propósito.
- `REGRAS_INFORMAR`: regra **real** do material (em Honras de Avais e Prejuízo, pode informar vencimento, dias de atraso e contrato; não pode informar valores nem produto).
- `POLITICA_ACORDOS`: estruturada por campo para responder "pode parcelar?", "qual o prazo?", "quanto de desconto?". **Tudo fictício**; em produção a empresa preenche, por cooperativa, sem mexer no código.
- `GLOSSARIO`: termos do material.

## Célula 5 — Roteador de produto
**Fazer:** rodar.
**Explicar:** antes de chamar o modelo, um código simples procura palavras-chave (ignorando acento e maiúscula) e escolhe a ficha. Não gasta token. É a versão simples e previsível do que, em um RAG, seria a busca vetorial.

## Célula 6 — Cenários de atendimento
**Fazer:** rodar.
**Explicar:** o operador nem sempre pergunta por produto; muitas vezes pergunta por situação. Há uma segunda base, `TEMAS`, com uma regra curta por cenário (prazo, parcelamento, desconto, jurídico e custas, restrição cadastral, quebra de acordo, objeção do cliente, formalização). Produto e cenário se combinam. **O material não traz nada de jurídico, prazos ou custas**: todo esse conteúdo é exemplo inventado.

## Célula 7 — Teste dos roteadores (grátis)
**Fazer:** rodar e ver a taxa de acerto.
**Explicar:** testa produto e cenário com perguntas típicas, sem gastar token. É auditável: erros viram novas palavras-chave. Resultado de referência: produtos 18/18, cenários 9/9.

## Célula 8 — Instruções do assistente e montagem do contexto
**Fazer:** rodar.
**Explicar:** a parte mais importante. `SYSTEM` define o comportamento (só a documentação, nunca inventar, respeitar "pode/não pode informar", encaminhar jurídico, dar sugestão de fala). `montar_contexto` monta o pacote enviado ao modelo em cada pergunta; só o necessário, por isso o custo é baixo.

## Célula 9 — Função `responder()`
**Fazer:** rodar.
**Explicar:** o fluxo completo em uma função: identifica cenário e produto (grátis); usa o histórico se a pergunta for continuação; só chama o modelo para classificar se nada foi achado; monta contexto; envia com histórico curto e resposta limitada; devolve resposta e contagem de tokens.

## Célula 10 — Primeiro teste
**Fazer:** rodar; trocar a pergunta para testar variações.
**Explicar:** mostra exatamente o que foi enviado ao modelo. É a transparência que o gestor precisa: a resposta vem da ficha, não da "cabeça" do modelo.

## Célula 11 — Bateria de testes de ponta a ponta
**Fazer:** rodar (poucos tokens) e conferir OK/ATENÇÃO.
**Explicar:** 16 perguntas de validação, cada uma com o que deve e o que não pode aparecer. Dois casos críticos: Prejuízo não pode citar valores em reais, e o pedido de parcelar acima do máximo não pode ser aceito. A verificação por palavra é simples; **leia também as respostas**.

## Célula 12 — Página de chat com link
**Fazer:** rodar. Gera `https://....gradio.live`.
**Explicar:** a "página customizada" da demo. O gestor abre o link, digita como operador e recebe a resposta com a sugestão de fala. Em letra pequena aparece a base consultada (desligue com `MOSTRAR_FONTE = False`).
**Limitações:** o link só funciona **enquanto o notebook estiver rodando** e expira depois. Quem abrir usa **a sua** chave de API (custo baixo, mas é seu). Compartilhe só com quem vai testar e **pare a célula ao terminar**.

## Célula 13 — Roteiro, limitações e próximos passos
Texto de apoio para a apresentação. Veja também [ROTEIRO_DEMO.md](ROTEIRO_DEMO.md) e [LIMITACOES.md](LIMITACOES.md).

---

## Solução de problemas

| Sintoma | Causa provável | O que fazer |
|---|---|---|
| Botão vermelho **Erro** no chat (ou mensagem "Não consegui responder agora") | O Colab reiniciou e só a célula do chat foi executada (`NameError`), ou o runtime está desconectado | **Reconectar** e usar **Ambiente de execução → Executar tudo** |
| `RuntimeError: Nenhuma chave encontrada` | Segredo ausente ou sem *Acesso ao notebook*, ou notebook aberto em outra conta do Google | Conferir o segredo na conta correta e rodar de novo |
| `ModuleNotFoundError: anthropic` | Célula 1 não executada | Rodar a Célula 1 |
| `TypeError ... unexpected keyword argument 'type'` | Versão do Gradio não aceita `type="messages"` | Já corrigido no notebook (o argumento foi removido) |
| Link abre, mas diz que nenhuma interface está rodando | Notebook parado, desconectado ou link expirado | Reexecutar a Célula 12 (gera link novo) |

**Dica de custo ao reconectar:** a bateria de testes (Célula 11) gasta centavos e não é necessária para o chat funcionar. Ao reconectar, só é obrigatório rodar as Células 1 a 6, 8, 9 e 12.
