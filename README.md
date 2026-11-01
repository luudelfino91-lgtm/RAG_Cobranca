# Assistente de Cobrança — Demo (Prova de Conceito)

Chat interno para operadores de cobrança tirarem dúvidas sobre **produtos** e **situações de atendimento** (prazo do acordo, parcelamento, desconto, custas processuais, quebra de acordo...) sem precisar acionar a gestão a cada pergunta.

O assistente responde **em tom de atendimento**, usando **somente** a base cadastrada, e sugere uma fala para o operador dizer ao cliente.

> **Status: demonstração.** Roda no Google Colab, com uma página de chat acessível por link temporário. As regras de acordo, prazo, parcelamento, desconto e jurídico são **fictícias**. Veja [Limitações](docs/LIMITACOES.md).

---

## Como funciona (resumo)

```
Pergunta do operador
        │
        ▼
[1] Roteadores por palavras-chave (custo zero, sem IA)
        ├─ de PRODUTO  → cartão, consignado, prejuízo...
        └─ de CENÁRIO  → prazo, parcelamento, custas, quebra de acordo...
        │
        ▼
[2] Monta um contexto enxuto
        ficha do produto + política de acordo + regra do cenário + glossário
        │
        ▼
[3] Modelo de linguagem pequeno responde só com esse contexto
        │
        ▼
Resposta curta  +  "Sugestão de fala ao cliente"
```

**Por que não é um RAG com busca vetorial?** A base é pequena e estruturada (20 produtos e 8 cenários), e cada produto é uma unidade completa de sentido. Enviar só a ficha certa gasta menos tokens e evita trazer o produto errado. Detalhes e critério para evoluir para RAG em [Decisões de projeto](docs/DECISOES_DE_PROJETO.md).

## Custo

Cerca de **700 tokens de entrada e 90 de saída por pergunta** (medido com `gpt-4o-mini`), isto é, uma fração de centavo. O teto da resposta é limitado por `MAX_TOKENS_RESPOSTA = 400` e o histórico enviado por `MAX_HISTORICO = 6`.

## Estrutura do repositório

```
.
├── README.md                          ← este arquivo
├── notebooks/
│   └── assistente_cobranca_demo.ipynb ← o notebook completo, sem saídas
├── docs/
│   ├── ARQUITETURA.md                 ← fluxo, módulos, funções e formato dos dados
│   ├── GUIA_CELULA_A_CELULA.md        ← o que fazer e o que explicar em cada célula
│   ├── DECISOES_DE_PROJETO.md         ← por que foi feito assim (e o que fica para depois)
│   ├── ROTEIRO_DEMO.md                ← perguntas sugeridas para apresentar aos gestores
│   ├── SEGURANCA_E_LGPD.md            ← chaves, dados, link público e boas práticas
│   └── LIMITACOES.md                  ← limitações conhecidas e próximos passos
├── .env.example                       ← nomes das variáveis (sem valores)
└── .gitignore
```

## Como executar (Google Colab)

1. Abra `notebooks/assistente_cobranca_demo.ipynb` no Google Colab (**Arquivo → Abrir notebook → GitHub**, ou faça upload do arquivo).
2. Configure a chave de API em **Secrets** (ícone de chave na barra lateral), com **Acesso ao notebook** ativado. Crie **um** destes segredos:
   - `ANTHROPIC_API_KEY` — usa Claude; ou
   - `GPT` (ou `OPENAI_API_KEY`) — usa GPT.

   Nunca cole a chave no código nem em células de texto. Veja [Segurança](docs/SEGURANCA_E_LGPD.md).
3. Menu **Ambiente de execução → Executar tudo**. A última célula (chat) imprime um link `https://....gradio.live`.
4. Envie o link a quem vai testar. Ele só funciona **enquanto o notebook estiver rodando**.

**Depois de reconectar o Colab**, use sempre *Executar tudo*. Rodar só a célula do chat causa `NameError`, porque as células anteriores definem os dados e as funções. Veja [Guia célula a célula](docs/GUIA_CELULA_A_CELULA.md#solucao-de-problemas).

## Provedores de IA

O notebook funciona com **Claude (Anthropic)** ou **GPT (OpenAI)**. Só a função `chamar_modelo` conhece o provedor; fichas, roteadores, cenários e testes não mudam ao trocar de um para o outro. Se existirem as duas chaves, o notebook usa Claude.

| Provedor | Segredo | Modelo usado no notebook |
|---|---|---|
| Anthropic | `ANTHROPIC_API_KEY` | `claude-haiku-5-5` (confira o nome atual na documentação) |
| OpenAI | `GPT` ou `OPENAI_API_KEY` | `gpt-4o-mini` |

## Sobre os dados

- **Fichas de produto** e a regra "pode / não pode informar" vêm de material interno da empresa ("Produtos Cobrança"). Por isso este repositório deve permanecer **privado**. O PDF original **não** está versionado aqui.
- **Política de acordos** (parcelas, entrada, desconto, prazos) e **cenários** (jurídico, custas, restrição cadastral, quebra de acordo...) são **exemplos inventados** para a demo. Em produção, a gestão e o jurídico fornecem as regras reais, por cooperativa.
- **Não há dados de clientes** em lugar nenhum, e o assistente não consulta nem exibe dados pessoais (LGPD).

## Identidade visual

A página usa uma paleta de verde e turquesa **inspirada** nas cores da instituição, sem logotipo ou marca registrada, e traz o aviso "Demo interno · dados fictícios". Não é um produto oficial.

## Próximos passos

Resumo (lista completa em [Limitações e próximos passos](docs/LIMITACOES.md)):

1. Validar as fichas com a área de cobrança.
2. Substituir a política fictícia pela real, por cooperativa.
3. Revisar com o jurídico o cenário de custas e honorários.
4. Ampliar os testes com casos reais anonimizados.
5. Publicar em ambiente próprio, com controle de acesso e registro de uso.
