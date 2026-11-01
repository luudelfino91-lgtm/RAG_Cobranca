# Decisões de projeto

## 1. Por que não usar RAG com busca vetorial (nesta demo)

- A base é **pequena e estruturada**: 20 produtos e 8 cenários. Cada slide do material é uma unidade completa de sentido.
- Fatiar o PDF em pedaços (chunks) poderia **separar o produto da sua regra** (por exemplo, a regra de atualização D+0 / D-2).
- Enviar só a ficha certa reduz tokens e o risco de trazer o produto errado.
- O roteador por palavras-chave é **previsível e auditável**.

**Quando passar a usar RAG:** se a base crescer (várias cooperativas, normativos, contratos, histórico de casos) ou se as perguntas passarem a depender de textos longos. A estrutura atual (`FICHAS`, `TEMAS`, `montar_contexto`) continua útil: o RAG entra no lugar do roteador, devolvendo os mesmos blocos de contexto.

## 2. Tamanho de trecho (chunk), para uma futura evolução

Para documentos corridos, a regra prática é trechos de 300 a 1.000 caracteres (ou 200 a 500 tokens) com 10% a 20% de sobreposição, respeitando a estrutura do documento. Para material em slides, **uma ficha por slide** é melhor que cortar por tamanho.

## 3. Dados reais e fictícios separados de propósito

- **Reais:** fichas de produto, regra "pode/não pode informar", glossário.
- **Fictícios:** política de acordos e cenários de atendimento. O material não traz essas regras, e elas variam por cooperativa.

O prompt instrui o modelo a tratar o fictício como **exemplo**, e a interface exibe "Demo interno · dados fictícios". Em produção, a empresa substitui a política por dados reais sem mexer no código.

## 4. Provedor de IA intercambiável

A função `chamar_modelo` isola o provedor. Isso permite trocar Claude por GPT (ou o contrário) com uma variável de segredo, e comparar custo e qualidade sem reescrever o notebook.

## 5. Custo baixo por construção

Roteamento sem IA, contexto enxuto, histórico curto, limite de resposta e modelo pequeno. Classificador por IA apenas como plano B (≤ 20 tokens).

## 6. Segurança do conteúdo gerado

Respostas só a partir do contexto; "não encontrei, acione a gestão" quando faltar informação; sem acesso a dados de clientes; sem cálculo de custas ou saldos; sem pressão ou ameaça na sugestão de fala; sem emojis (tom profissional).

## 7. Interface

Página Gradio com identidade visual própria (paleta verde/turquesa inspirada, **sem logotipo ou marca**), sem exibir contagem de tokens, sem rótulos redundantes ("Resposta ao operador:") e com a fonte consultada em letra pequena (opcional).

## 8. O que ficou de fora de propósito

- Autenticação e registro de uso (a demo usa link temporário).
- Integração com sistemas da empresa e dados de clientes.
- Busca vetorial.
- Avaliação automática avançada (a bateria atual confere palavras esperadas e proibidas).
