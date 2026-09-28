[English](prompt-set-template.md) · **Português (Brasil)**

# Conjunto de prompts: {segmento ou página}

Modelo de conjunto fixo de prompts para o [Protocolo de Medição de GEO](../PROTOCOL.pt-BR.md).
Troque tudo o que está entre chaves. Apague este parágrafo quando o conjunto
estiver pronto.

## Cabeçalho

| Campo | Valor |
|---|---|
| Versão do conjunto | v1 |
| Criado em | {AAAA-MM-DD} |
| Versão do protocolo | 1.0.0 |
| Marca estudada | {nome canônico da marca} |
| Conjunto de comparação (B) | {Marca 01, Marca 02, ...} (declarado antes da primeira onda) |
| Superfícies | {google_ai_mode, google_aio, gemini, perplexity, chatgpt, claude} |
| Canal | {browser / app / api / serp_api} |
| Localização | {país, cidade, parâmetros de busca} |
| Idioma | {idioma do prompt, idioma da interface} |
| Versão do bloco de neutralização | {v1} |
| Rodadas por prompt por superfície | 3 |
| Resposta sem busca na web conta? | {sim / não} |

## Bloco de neutralização (versão {v1})

> {texto fixo do bloco, colado antes de cada prompt}

## Prompts de decisão (medem recomendação)

Nenhum nome de marca em nenhum prompt. Escreva na voz de quem compra.

| Id | Intenção | Prompt (literal, nunca editado) | Origem da formulação | Entrou em |
|---|---|---|---|---|
| P01 | {intenção} | {texto do prompt} | {PAA / preenchimento automático / relacionadas / fórum / entrevista com cliente} | {AAAA-MM-DD} |
| P02 | {mesma intenção, segunda formulação} | {texto do prompt} | {origem} | {AAAA-MM-DD} |

## Prompts informacionais (medem citação como fonte)

| Id | Intenção | Prompt (literal, nunca editado) | Origem da formulação | Página que deveria ser citada | Entrou em |
|---|---|---|---|---|---|
| P10 | {intenção} | {texto do prompt} | {origem} | {URL} | {AAAA-MM-DD} |

## Prompts de entidade (opcional, relatados à parte)

Estes trazem o nome da marca e medem só o reconhecimento da entidade. Nunca
entram na taxa de recomendação.

| Id | Prompt (literal) | Entrou em |
|---|---|---|
| E01 | {Quem é {marca}?} | {AAAA-MM-DD} |

## Regras deste conjunto

- O texto de um prompt nunca muda. Pergunta nova ganha id novo e data.
- Prompt aposentado fica na tabela com `aposentado: AAAA-MM-DD`.
- Mudança que quebra a comparação cria uma versão nova do conjunto (v2) e
  é registrada no histórico abaixo.

## Histórico

| Versão | Data | Mudança | Comparação |
|---|---|---|---|
| v1 | {AAAA-MM-DD} | Primeira versão | Linha de base |
