# Limitações e próximos passos

## Limitações conhecidas

**Conteúdo**
- Política de acordos, prazos, parcelamento, desconto, restrição cadastral e jurídico/custas são **fictícios**. O que for real precisa vir da gestão e do jurídico, por cooperativa.
- O material-fonte tem **inconsistências** que o assistente repete: "SACD" aparece como "Constante *Decrescente*" (o nome usual é SAC); o slide de Financiamento de Bens e Serviços não informa a regra de atualização; Honras de Avais e Prejuízo não têm descrição, só a regra de informação.

**Comportamento do modelo**
- Em algumas perguntas sobre **custas processuais**, o modelo responde "Não encontrei isso na documentação" em vez de encaminhar ao jurídico. É ajuste de prompt (regra 6 do `SYSTEM`) ainda não aplicado.
- Em perguntas fora da base, a "sugestão de fala" às vezes diz "entre em contato com a gestão", dirigida ao cliente, o que fica estranho. Também é ajuste de prompt.
- A verificação automática da bateria de testes confere palavras esperadas e proibidas; **leia as respostas**.

**Roteamento**
- Os roteadores por palavras-chave podem errar com sinônimos. Há plano B com o modelo para produto, mas **não** para cenário.
- "Qual a diferença entre Price e SACD?" aciona só o Crédito Pessoal, embora o termo exista também em Crédito com Garantia de Imóvel.

**Infraestrutura da demo**
- O link só funciona **enquanto o Colab estiver rodando**; o Colab desconecta quando fica ocioso. Veja [Solução de problemas](GUIA_CELULA_A_CELULA.md#solucao-de-problemas).
- Sem autenticação, sem registro de uso, sem controle de quem acessa o link.
- O assistente **não consulta dados de clientes** (de propósito) e **não calcula** saldos nem custas.

## Próximos passos

1. Validar as fichas com a área de cobrança e corrigir o texto-fonte.
2. Substituir a **política fictícia** pela real, por cooperativa (planilha ou sistema).
3. Revisar com o jurídico o cenário de custas e honorários, e ajustar a regra 6 do prompt.
4. Corrigir a "sugestão de fala" nas respostas fora da base.
5. Ampliar os testes com **casos reais anonimizados**.
6. Se a base crescer (várias cooperativas, normativos, contratos), adicionar **RAG** por cima desta estrutura.
7. Publicar em ambiente próprio, com **controle de acesso e registro de uso**.
8. Avaliar uma versão em site/Hugging Face Space com link estável, em vez do link temporário do Colab.
