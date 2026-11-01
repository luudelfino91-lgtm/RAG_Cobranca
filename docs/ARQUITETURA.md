# Arquitetura

## Visão geral

Tudo vive em um único notebook. O fluxo de uma pergunta:

```
pergunta ─► responder()
              ├─ rotear_temas()           → cenários (TEMAS)
              ├─ identificar_produtos()   → produtos (FICHAS)
              │     ├─ rotear_keywords()  → palavras-chave na pergunta
              │     ├─ histórico          → procura o produto nas mensagens anteriores
              │     └─ classificar_com_llm() → plano B (≤ 20 tokens), só se não achou produto nem cenário
              ├─ montar_contexto()        → ficha + regra de informação + política + cenário + glossário
              └─ chamar_modelo()          → único ponto de contato com o provedor de IA
                         │
                         ▼
              texto da resposta + metadados (produtos, cenários, origem, tokens)
```

## Dados

| Estrutura | O que guarda | Origem |
|---|---|---|
| `FICHAS` | 20 produtos: `nome`, `categoria`, `atualizacao`, `descricao`, `atencao`, `keywords` | Material "Produtos Cobrança" (real) |
| `REGRAS_INFORMAR` | O que **pode** e **não pode** ser informado em Honras de Avais e Prejuízo | Material (real) |
| `POLITICA_ACORDOS` | Por produto: `permite_acordo`, `parcelas_max`, `entrada_min_pct`, `desconto_max_pct`, `primeira_parcela_dias`, `aprovador`, `obs` | **Fictícia** |
| `TEMAS` | 8 cenários de atendimento: `nome`, `regra`, `usa_tabela`, `keywords` | **Fictícia** |
| `GLOSSARIO` | D+0, D-2, Price, SACD, avalista | Material (real) |

Para incluir um produto, basta adicionar uma ficha em `FICHAS` (e, se couber, uma entrada em `POLITICA_ACORDOS`). O restante do código não muda.

## Roteadores por palavras-chave

Funções `normalizar`, `tem_palavra`, `rotear_keywords` e `rotear_temas`:

1. A pergunta é normalizada (minúsculas, sem acento, só letras e números).
2. Cada palavra-chave é procurada como **palavra inteira** (regex com `\b`).
3. A pontuação de um item é a **soma do tamanho** das palavras-chave encontradas (palavras mais longas e específicas pesam mais).
4. Ficam os itens com pontuação ≥ 60% da melhor, no máximo 2.

Custo zero de tokens e totalmente auditável: se errar, sabemos qual palavra-chave ajustar.

## Montagem do contexto (`montar_contexto`)

Para cada produto identificado entram: ficha, regra "pode/não pode informar" (se houver) e a política de acordos formatada por `formatar_politica`. Para cada cenário, entra a regra do cenário. Se a pergunta **não cita produto** e o cenário usa a tabela (prazo, parcelamento, desconto, objeções), entra `resumo_politica()`, uma visão compacta de todos os produtos. O glossário só entra quando o termo aparece.

## Instruções do assistente (`SYSTEM`)

Dez regras. As principais:

- responder **somente** com o contexto recebido; se não houver, dizer "Não encontrei isso na documentação; acione a gestão.";
- política e cenários são **exemplos fictícios**; nunca inventar desconto, prazo, parcela, percentual ou valor;
- respeitar a regra de informação, inclusive na sugestão de fala;
- não ter acesso a dados de clientes;
- questões jurídicas: não calcular nem prometer isenção; acionar jurídico/gestão;
- resposta direta, até cerca de 150 palavras, com "Sugestão de fala ao cliente:" quando útil, sem emojis.

## Provedor de IA (`chamar_modelo`)

Recebe `(system, mensagens, max_tokens)` e devolve `(texto, tokens_entrada, tokens_saida)`. Há duas implementações (Anthropic e OpenAI) atrás da mesma assinatura. Trocar de provedor é trocar o segredo; nada mais muda.

## Página de chat (Gradio)

- `chat(mensagem, historico)` chama `responder`, acrescenta (se `MOSTRAR_FONTE = True`) a linha pequena "Base consultada · Cenário" e **captura exceções**: em caso de falha, mostra uma mensagem legível e imprime o traceback na saída da célula.
- Visual: cabeçalho, paleta verde/turquesa, aviso de dados fictícios, exemplos clicáveis e rodapé.
- `demo.launch(share=True)` gera o link público temporário. A chamada se adapta à versão do Gradio (em versões novas, `theme` e `css` vão no `launch()`).

## Controle de custo

| Controle | Valor |
|---|---|
| Roteamento | palavras-chave, 0 token |
| Contexto | só ficha(s) do produto e do cenário |
| `MAX_TOKENS_RESPOSTA` | 400 |
| `MAX_HISTORICO` | 6 mensagens |
| Classificador por IA | só se não achar produto **nem** cenário (≤ 20 tokens) |
| Média medida | ~700 tokens de entrada, ~90 de saída por pergunta |
