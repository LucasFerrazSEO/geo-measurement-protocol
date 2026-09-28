[English](README.md) · **Português (Brasil)**

# geo-measurement-protocol

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](LICENSE) ![Protocol version 1.0.0](https://img.shields.io/badge/protocol-v1.0.0-blue.svg)

`geo-measurement-protocol` é um protocolo aberto e reproduzível para medir
se uma marca ou uma URL é mencionada, recomendada, citada como fonte ou
linkada por superfícies de resposta com IA: Google AI Mode, Google AI
Overviews, Gemini, Perplexity, ChatGPT e Claude. Ele define como escrever o
conjunto de prompts, como neutralizar a sessão, quantas rodadas fazer, o
que conta como citação, como tratar a recusa e como calcular cada métrica,
com fórmulas explícitas. É um protocolo em texto com modelos de registro,
não um software, e não consulta IA nenhuma por você.

## Sumário

- [Contexto](#contexto)
- [O que o protocolo cobre](#o-que-o-protocolo-cobre)
- [Arquivos](#arquivos)
- [Como usar](#como-usar)
- [Perguntas frequentes](#perguntas-frequentes)
- [Limitações](#limitações)
- [Método e origem](#método-e-origem)
- [Projetos relacionados](#projetos-relacionados)
- [Como citar](#como-citar)
- [Como contribuir](#como-contribuir)
- [Autor](#autor)
- [Licença](#licença)

## Contexto

A resposta das superfícies de IA muda entre execuções, entre contas e entre
versões de modelo. O print de uma resposta não prova nada sobre a
frequência com que uma marca é recomendada. Para comparar no tempo, ou
comparar marcas, a medição precisa ser repetível: as mesmas perguntas
fixas, feitas em sessões limpas, várias vezes por superfície, classificadas
com as mesmas regras e registradas no mesmo formato. Este protocolo põe
essas regras por escrito, para qualquer pessoa rodar a mesma medição e
chegar a um resultado comparável.

## O que o protocolo cobre

1. **Escopo, e o que ele não mede.** Não é previsão de ranking, não é prova
   de causa, não é tráfego e não é promessa de citação.
2. **Superfícies e canais.** Seis ids de superfície e como registrar o
   canal (navegador, aplicativo, API), o modo, o estado de login e a busca
   na web.
3. **Desenho do conjunto de prompts.** Intenção primeiro, sem marca no
   prompt, variação de formulação, prompts fixos e versionados para série
   temporal.
4. **Neutralização da sessão.** Sessão limpa, sem histórico nem memória,
   bloco fixo e versionado, localização e idioma declarados.
5. **Rodadas.** Três rodadas por prompt por superfície, dentro de uma
   janela curta, com a regra de estabilidade de 2 em 3.
6. **Classificação.** Quatro marcadores independentes: menção,
   recomendação, citação como fonte e link, mais a posição e o motivo
   literal.
7. **Recusa.** Recusa técnica é `n/d (recusa)` e sai do denominador; nunca
   conta como "marca ausente".
8. **Métricas.** Taxa de presença, taxa de recomendação, taxa de citação,
   taxa de link, share of voice, posição média, estabilidade, taxa de
   acionamento do AI Overviews e intervalo de Wilson, cada uma com a
   fórmula.
9. **Ameaças à validade, ética e versão.**

## Arquivos

| Arquivo | Conteúdo |
|---|---|
| [PROTOCOL.md](PROTOCOL.md) | O protocolo completo, em inglês |
| [PROTOCOL.pt-BR.md](PROTOCOL.pt-BR.md) | O protocolo completo, em português do Brasil |
| [templates/rounds-template.csv](templates/rounds-template.csv) | Log de rodadas, uma linha por marca por rodada, com linhas de exemplo fictícias |
| [templates/prompt-set-template.md](templates/prompt-set-template.md) | Modelo de conjunto de prompts versionado (inglês) |
| [templates/prompt-set-template.pt-BR.md](templates/prompt-set-template.pt-BR.md) | O mesmo modelo em português do Brasil |
| [CITATION.cff](CITATION.cff) | Metadados de citação |

Os nomes das colunas do CSV são os mesmos nos dois idiomas, então um só
arquivo serve para os dois. As seis primeiras colunas (`data`, `ia`,
`prompt`, `marca`, `mencionada`, `posicao`) mantêm os nomes de campo do
[citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger),
por isso estão em português.

## Como usar

**1. Leia o protocolo** ([PROTOCOL.pt-BR.md](PROTOCOL.pt-BR.md)) uma vez,
do início ao fim.

**2. Escreva o conjunto de prompts** com o
[modelo](templates/prompt-set-template.pt-BR.md): prompts de decisão e
prompts informacionais, sem nome de marca, cada um com id estável. Declare
o conjunto de comparação de marcas, as superfícies, a localização e o
idioma antes da primeira rodada.

**3. Rode uma onda.** Três rodadas por prompt por superfície, cada uma em uma
sessão limpa nova com o bloco de neutralização, todas dentro de uma janela
curta.

**4. Registre cada rodada** em
[templates/rounds-template.csv](templates/rounds-template.csv) e guarde o
texto bruto de cada resposta.

**5. Calcule as métricas** com as fórmulas da seção 9 do protocolo e
publique com a linha de relato da seção 12 (versão do protocolo, número de
prompts, rodadas válidas, recusas, janela, localização).

**6. Repita** em uma cadência fixa e compare ondas com pelo menos 2 a 4
semanas de distância, nunca rodadas isoladas.

Se preferir linha de comando a planilha, as mesmas rodadas podem ser
registradas com o
[citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger)
(do autor). A seção 9 do protocolo explica como os números dele se ligam às
métricas definidas aqui.

## Perguntas frequentes

**Este protocolo garante que minha marca vai ser citada por uma IA?**
Não. Ele mede o que as superfícies responderam em datas definidas, em
condições declaradas. Não controla, não prevê e não garante resposta
nenhuma.

**Por que três rodadas e não uma?**
Porque a mesma pergunta recebe respostas diferentes na mesma superfície.
Uma execução é caso isolado. Três é o menor número ímpar que permite
leitura por maioria (estável quando o resultado se repete em pelo menos 2
de 3 rodadas).

**Por que a recusa fica fora do denominador?**
A recusa não diz nada sobre a marca ter ou não ter sido nomeada. Contar a
recusa como ausência baixaria a taxa da marca por um motivo que não tem
relação com ela.

**Posso automatizar as rodadas?**
Só com APIs oficiais ou ferramentas que as plataformas permitem, dentro
dos termos delas, e sem contornar login, limite de uso ou CAPTCHA. A
resposta por API difere da interface de consumo, então fica em uma série
separada.

**Share of voice é o mesmo que taxa de presença?**
Não. Taxa de presença é a fração das rodadas válidas em que a marca
aparece. Share of voice é a fatia da marca em todas as menções de marca do
conjunto de comparação, e por isso muda quando se põe ou se tira uma
marca.

## Limitações

O protocolo depende de leitura e classificação manual cuidadosa. Não
elimina a variância dos modelos, só a torna visível. Conjuntos pequenos de
prompts dão intervalos largos. Resultado obtido em uma localização, em um
idioma, em uma configuração de conta ou em um canal não vale para outro. As
superfícies mudam de interface e de recurso, e uma versão futura do
protocolo pode precisar de regras novas para elas.

## Método e origem

O protocolo vem da rotina de medição usada internamente desde 2026 no
[lucasferrazseo.com](https://lucasferrazseo.com) para medir citação e
recomendação por IA, generalizada aqui para uso público, sem dado de
cliente, sem medição interna e sem regra específica de site.

## Projetos relacionados

- [citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger), do autor: registro de rodadas de citação por linha de comando.
- [ai-referrer-report](https://github.com/LucasFerrazSEO/ai-referrer-report), do autor: tráfego de indicação de assistentes de IA em exportações do GA4.
- [awesome-generative-engine-optimization](https://github.com/LucasFerrazSEO/awesome-generative-engine-optimization), do autor: lista curada sobre Generative Engine Optimization.
- [Método Rodada de Citação](https://lucasferrazseo.com/glossario/metodo-rodada-de-citacao/): verbete público do glossário da agência do autor.

## Como citar

Use os metadados do [CITATION.cff](CITATION.cff), ou:

> Ferraz, L. (2026). *GEO Measurement Protocol* (Versão 1.0.0). https://github.com/LucasFerrazSEO/geo-measurement-protocol

Cite sempre a versão do protocolo usada na sua medição.

## Como contribuir

Sugestões, correções e relatos de como o protocolo funcionou nas suas
medições são bem-vindos pelas [Issues do GitHub](https://github.com/LucasFerrazSEO/geo-measurement-protocol/issues).

## Autor

[Lucas Ferraz](https://lucasferraz.com) é especialista em SEO, criação de sites e Generative Engine Optimization e fundador da [Lucas Ferraz SEO](https://lucasferrazseo.com).

## Licença

Texto e modelos sob a licença Creative Commons Atribuição 4.0 Internacional (CC BY 4.0). Veja o arquivo [LICENSE](LICENSE).
