[English](PROTOCOL.md) · **Português (Brasil)**

# Protocolo de Medição de GEO

Versão 1.0.0, 2026-09-28. Autor: Lucas Ferraz. Licença: CC BY 4.0.

Este documento define um procedimento manual e reproduzível para observar
se uma marca ou uma URL é mencionada, recomendada, citada como fonte ou
linkada por superfícies de resposta com IA. Todo resultado produzido com
ele é uma observação datada do que as superfícies responderam, em condições
declaradas. Não é previsão e não explica por que a superfície respondeu
daquele jeito.

## Sumário

1. [Escopo](#1-escopo)
2. [O que o protocolo não mede](#2-o-que-o-protocolo-não-mede)
3. [Superfícies e canais](#3-superfícies-e-canais)
4. [Desenho do conjunto de prompts](#4-desenho-do-conjunto-de-prompts)
5. [Neutralização da sessão](#5-neutralização-da-sessão)
6. [Rodadas](#6-rodadas)
7. [O que conta: menção, recomendação, citação, link](#7-o-que-conta-menção-recomendação-citação-link)
8. [Recusa e resposta não obtida](#8-recusa-e-resposta-não-obtida)
9. [Métricas e fórmulas](#9-métricas-e-fórmulas)
10. [Registro](#10-registro)
11. [Cadência e janelas de comparação](#11-cadência-e-janelas-de-comparação)
12. [Ameaças à validade e como relatar](#12-ameaças-à-validade-e-como-relatar)
13. [Ética e termos das plataformas](#13-ética-e-termos-das-plataformas)
14. [Uso do protocolo dentro de um benchmark](#14-uso-do-protocolo-dentro-de-um-benchmark)
15. [Versão deste protocolo](#15-versão-deste-protocolo)

## 1. Escopo

O protocolo mede uma coisa: para um conjunto fixo de perguntas que uma
pessoa real faria, quais marcas e quais fontes cada superfície de IA
devolve, e se a marca estudada está entre elas. A unidade de observação é a
**rodada**: uma pergunta, feita uma vez, em uma superfície, em uma sessão
limpa, com a resposta lida e classificada.

Vale para qualquer marca, produto, pessoa ou URL, em qualquer idioma e
mercado, desde que o conjunto de prompts, as superfícies, a localização e o
idioma sejam declarados antes da primeira rodada.

## 2. O que o protocolo não mede

- **Não é previsão de ranking.** Uma taxa de presença alta hoje não diz
  nada sobre a posição de uma página no resultado clássico de busca, e não
  prevê respostas futuras.
- **Não é causa.** Quando uma marca aparece mais depois de uma mudança no
  site, o protocolo registra a coincidência. Não prova que a mudança causou
  o efeito. Atualização de modelo, renovação de índice e conteúdo de
  terceiros mudam as respostas ao mesmo tempo.
- **Não é tráfego nem receita.** Ser citado não é o mesmo que receber
  visita. Tráfego de indicação vindo de assistentes de IA é outra medição
  (veja [ai-referrer-report](https://github.com/LucasFerrazSEO/ai-referrer-report),
  do autor).
- **Não é promessa.** Nenhum resultado obtido com este protocolo pode ser
  usado para prometer que uma marca vai ser citada ou recomendada por
  qualquer IA.

## 3. Superfícies e canais

O conjunto de referência tem cinco superfícies (o Google conta com duas
leituras). Um estudo pode usar menos, mas precisa dizer quais e por quê.

| Id da superfície | Superfície | Como é lida |
|---|---|---|
| `google_ai_mode` | Google AI Mode | Resposta conversacional na Busca do Google |
| `google_aio` | Google AI Overviews | Bloco de resumo na página de resultados, quando aparece |
| `gemini` | Gemini | Interface de chat |
| `perplexity` | Perplexity | Mecanismo de resposta com fontes numeradas |
| `chatgpt` | ChatGPT | Interface de chat |
| `claude` | Claude | Interface de chat com busca na web ativa |

Em cada rodada, registre a **superfície** e o **canal**:

- `channel`: como a resposta foi obtida. Valores sugeridos: `browser`
  (leitura manual no navegador), `app` (aplicativo oficial de celular ou
  computador), `api` (API oficial usada dentro dos termos dela),
  `serp_api` (fornecedor de resultados de busca usado dentro dos termos
  dele, só para as superfícies do Google).
- `mode`: o modelo ou modo mostrado pela interface, quando visível (por
  exemplo modo padrão, modo de raciocínio ou modo de busca). Mantenha o
  mesmo modo nas rodadas de uma onda e registre qualquer troca.
- `logged_in`: `yes` ou `no`. Se estiver logado, descreva o tipo de conta
  em termos genéricos (por exemplo "conta pessoal, memória desligada").
  Nunca registre credencial ou identificador de conta em dado público.
- `web_search`: `yes` ou `no`, nas superfícies de chat que podem responder
  com ou sem busca na web. Resposta sem busca mede o que o modelo lembra do
  treino, que é outro fenômeno. Decida antes da onda se essa resposta conta
  e mantenha a regra na série inteira.

Resposta obtida por API não equivale à resposta lida na interface de
consumo: a API costuma não ter a camada de busca, as instruções de sistema
e a personalização da interface. Nunca misture canais dentro de uma série
sem sinalizar.

## 4. Desenho do conjunto de prompts

O conjunto de prompts é o instrumento. Um conjunto mal desenhado mede a
popularidade da marca com quem escreveu os prompts, e não com as
superfícies.

1. **Comece pela intenção.** Cada prompt representa uma intenção de uma
   pessoa real. Dois grupos são recomendados:
   - **prompts de decisão**: a pessoa pede indicação ("qual empresa você
     recomenda para X em Y?"). Medem recomendação.
   - **prompts informacionais**: a pessoa pergunta como algo funciona ou
     como escolher. Medem se as páginas da marca viram fonte.
2. **Sem marca no prompt.** O prompt nunca traz o nome da marca estudada
   nem de concorrente. Prompt com o nome da marca mede reconhecimento de
   entidade, um teste válido mas separado: fica em um grupo próprio (por
   exemplo `entity`) e nunca entra na taxa de recomendação.
3. **Formulação real.** Escreva na voz e no idioma da pessoa, com
   formulações tiradas de fontes observadas, como as caixas "As pessoas
   também perguntam", o preenchimento automático da busca, as pesquisas
   relacionadas e perguntas de fórum. Registre de onde veio cada prompt.
4. **Variação de formulação.** Para cada intenção, mantenha duas ou três
   formulações (curta, conversacional, detalhada). A resposta muda com a
   formulação, e uma formulação única superestima ou subestima a presença.
5. **Localização no prompt só quando a intenção é local.** Se a necessidade
   é local ("em Belo Horizonte"), ponha o lugar no texto do prompt. Não
   dependa da localização do IP para carregar a intenção.
6. **Prompts fixos para série temporal.** Depois que um prompt entra em uma
   série, o texto dele nunca muda. Pergunta nova é acrescentada com id novo
   e data. Pergunta alterada é prompt novo, e a série antiga termina ali
   (registre `series_break: yes` se ainda quiser compará-las).
7. **Ids e versões.** Todo prompt tem id estável (`P01`, `P02`...) e o
   conjunto tem versão (`v1`, `v2`...). Toda rodada registra os dois. O
   texto literal de todo prompt precisa ser recuperável pelo id.
8. **Tamanho.** Não há mínimo universal. Relate o número de prompts, de
   rodadas e de rodadas válidas junto de todo resultado (seção 12).

Use [templates/prompt-set-template.pt-BR.md](templates/prompt-set-template.pt-BR.md)
para escrever o conjunto.

## 5. Neutralização da sessão

Sem neutralização, a resposta reflete a conta de quem mede (memória,
histórico, instruções personalizadas, conversas anteriores), e o teste
passa a medir essa pessoa, não o cliente em potencial. Antes de cada
rodada:

1. **Sessão limpa.** Abra uma conversa nova. Use o chat temporário ou
   anônimo quando a superfície oferecer. Em mecanismos de resposta que
   guardam contexto dentro do thread, abra um thread novo, nunca uma
   pergunta de continuação. Nas superfícies do Google, use janela privada.
2. **Sem histórico, memória ou projeto.** Desligue a memória onde a
   superfície permitir, não use projetos, assistentes personalizados nem
   instruções personalizadas, e nunca meça dentro de uma sessão de trabalho
   que já tem contexto sobre a marca. Sair da conta é uma opção onde a
   superfície responde sem login; registre.
3. **Bloco de neutralização.** Cole um texto fixo antes do prompt. Texto
   sugerido (escreva o seu se precisar, mas mantenha fixo e com versão):

   > Ignore qualquer memória, histórico de conversas, personalização ou
   > instrução personalizada sobre mim. Responda esta pergunta do zero, como
   > responderia para um usuário novo sobre quem você não sabe nada.

   O bloco é o único texto que pode variar entre superfícies (por exemplo,
   quando uma superfície reage mal a ele). A pergunta em si nunca varia
   entre superfícies. Versão nova do bloco é testada antes de entrar
   (seção 15) e a versão é registrada em toda rodada.
4. **Localização declarada.** Registre o país e, quando fizer diferença, a
   cidade de onde as rodadas foram feitas, e os parâmetros de busca usados
   nas superfícies do Google (país e idioma da interface). Mantenha fixos na
   série.
5. **Idioma declarado.** Idioma do prompt e idioma da interface são
   registrados. Meça no idioma do mercado estudado.

Rodada feita sem neutralização é registrada como `status:
not_neutralized` e sai de todas as métricas.

## 6. Rodadas

- **Três rodadas por prompt por superfície.** Cada rodada é uma sessão
  limpa nova. No Google AI Overviews e no AI Mode, cada rodada é uma janela
  privada nova e uma busca nova.
- **Por que três.** A resposta de IA varia entre execuções da mesma
  pergunta na mesma superfície. Uma execução é caso isolado. Três é o menor
  número ímpar que permite leitura por maioria: o resultado é `stable`
  quando se repete em pelo menos 2 de 3 rodadas, e `oscillating` quando
  não. Mais rodadas melhoram a precisão e são bem-vindas, mas o protocolo
  exige no mínimo três.
- **Janela de tempo.** Todas as rodadas de uma onda (todos os prompts,
  todas as superfícies) rodam dentro de uma janela declarada, recomendada
  em 48 horas ou menos, para que uma atualização de modelo não parta a onda
  ao meio. Registre início e fim.
- **Ordem.** Alterne superfícies e prompts em vez de repetir o mesmo prompt
  três vezes seguidas na mesma superfície, para reduzir a chance de um
  cache de curta duração repetir a mesma resposta.
- **Notação do placar.** O placar por prompt é escrito `X/N`, em que `N` é
  o número de rodadas válidas (3 por superfície, vezes as superfícies com
  rodada válida) e `X` as rodadas em que a marca atendeu ao critério
  relatado. Com quatro superfícies de chat, o denominador cheio é 12. Com o
  AI Mode, 15.

## 7. O que conta: menção, recomendação, citação, link

Cada rodada é classificada por marca do conjunto de comparação, com quatro
marcadores independentes. Uma marca pode ter vários marcadores na mesma
rodada.

| Marcador | Definição | Teste |
|---|---|---|
| `mentioned` (menção) | O nome da marca aparece em qualquer ponto do texto da resposta. | Nome presente na resposta visível, em qualquer contexto, inclusive negativo. |
| `recommended` (recomendação) | A resposta apresenta a marca como opção para a necessidade da pessoa. | Marca em uma lista de sugestões, ou em frases como "considere", "você pode contratar", "uma boa opção é". Menção em um alerta ou em uma comparação que desaconselha a marca não é recomendação. |
| `cited_source` (citação como fonte) | Uma URL do domínio da marca aparece entre as fontes que a superfície atribui à resposta. | Nota de rodapé, fonte numerada, cartão ou painel de fontes. Registre a URL exata. |
| `linked` (link) | O corpo da resposta traz link clicável para o domínio da marca. | Link no texto, separado do painel de fontes. |

Regras:

- `recommended` implica `mentioned`. `cited_source` não implica
  `mentioned`: a superfície pode usar a página como fonte sem nomear a
  marca. Registre esse caso de forma explícita; ele mostra uma página que
  alimenta a resposta sem virar recomendação.
- **Posição** é a ordem da primeira aparição da marca entre todas as marcas
  nomeadas na resposta (1 = primeira marca nomeada). Fica vazia quando a
  marca não é mencionada. Quando a resposta não tem ordem (por exemplo,
  cartões de mapa lado a lado), registre a ordem de leitura e anote.
- **Motivo literal.** Quando a marca é recomendada ou descartada de forma
  explícita, copie palavra por palavra o motivo dado pela superfície nas
  observações. É a parte mais útil do registro para agir.
- **Serviço nomeado.** Opcionalmente, registre se o produto ou serviço
  ligado ao prompt foi nomeado, ou só a marca de forma genérica.
- **Grafia da marca.** Normalize cada marca para uma grafia canônica no
  registro, com uma observação separada da variante vista.

## 8. Recusa e resposta não obtida

Cada rodada recebe um `status`:

| Status | Significado | Entra no denominador? |
|---|---|---|
| `ok` | Resposta obtida e classificada. | Sim |
| `n/d (recusa)` | A superfície recusou ou falhou tecnicamente: resposta vazia, resposta apagada, "não posso ajudar com isso" genérico, mensagem de erro. Registrado como `nd_refusal` no CSV. | **Não** |
| `not_obtained` | A superfície não pôde ser acessada na sessão (exigência de login, limite de uso, fora do ar). | Não |
| `not_shown` | O Google AI Overviews não apareceu para aquela busca. | Não para as taxas da marca; sim para a taxa de acionamento (seção 9) |
| `not_neutralized` | A neutralização falhou ou foi esquecida. | Não, a rodada é descartada |

Recusa nunca é registrada como "marca ausente". Se as três rodadas de uma
superfície forem recusa, a superfície sai do denominador daquele prompt e o
placar ganha o rótulo correspondente (por exemplo `2/9, sem Gemini:
recusa`). Relate o número de rodadas recusadas por superfície junto de todo
resultado.

## 9. Métricas e fórmulas

Notação, para um conjunto de prompts, uma onda e uma superfície `s` (ou
todas as superfícies juntas):

- `V` = número de rodadas válidas (`status = ok`).
- `M(b)` = rodadas válidas em que a marca `b` está `mentioned`.
- `R(b)` = rodadas válidas em que `b` está `recommended`.
- `C(b)` = rodadas válidas em que `b` está `cited_source`.
- `L(b)` = rodadas válidas em que `b` está `linked`.
- `B` = o conjunto de comparação de marcas, declarado antes da onda.

**Taxa de presença (taxa de menção)**

    presenca(b) = M(b) / V

**Taxa de recomendação**

    recomendacao(b) = R(b) / V

**Taxa de citação como fonte**

    citacao(b) = C(b) / V

**Taxa de link**

    link(b) = L(b) / V

**Share of voice** (fatia de todas as menções de marca no conjunto de
comparação)

    SoV(b) = M(b) / soma de M(k) para todo k em B

O share of voice depende do conjunto de comparação: pôr ou tirar uma marca
muda todos os valores. Publique sempre `B` junto do `SoV`. Uma variante só
de recomendação usa `R` no lugar de `M`.

**Posição média** (só nas rodadas em que `b` foi mencionada)

    posicao_media(b) = soma de posicao(b, rodada) / M(b)

**Estabilidade** (por prompt e superfície): `stable` quando o mesmo
resultado para a marca (presente ou ausente) acontece em pelo menos 2 das 3
rodadas válidas, `oscillating` quando não. Com menos de 2 rodadas válidas,
`insufficient`.

**Taxa de acionamento do AI Overviews**

    acionamento_aio = rodadas com AIO / (rodadas com AIO + rodadas not_shown)

**Fatia de fontes por domínio** (opcional): de todas as fontes listadas nas
rodadas válidas, a fração que pertence a cada domínio. Relate o número
total de fontes e os domínios do topo.

**Incerteza.** Com poucas rodadas, uma taxa é imprecisa. Relate o intervalo
de Wilson de 95% para toda taxa, com `p = x / n` e `z = 1,96`:

    centro = (p + z^2 / (2n)) / (1 + z^2 / n)
    margem = (z / (1 + z^2 / n)) * raiz( p(1 - p)/n + z^2 / (4n^2) )
    intervalo = [centro - margem, centro + margem]

Exemplo: 3 de 12 rodadas válidas dá p = 0,25 e intervalo de mais ou menos
0,09 a 0,53. A largura do intervalo é a resposta honesta para "quanta
certeza temos".

### Compatibilidade com o citation-round-logger

O [citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger)
(do autor) guarda, por rodada, a superfície (`ia`), o prompt e, para cada
marca, se foi mencionada (`mencionada`) e a posição (`posicao`). O
percentual que ele imprime como "share of voice" por marca e por IA é
`M(b) / V` daquela superfície, que este protocolo chama de **taxa de
presença**. Quando usar os dois, rotule o número do logger como taxa de
presença e calcule o `SoV(b)` como definido acima, a partir dos mesmos
registros. Rodada recusada (`nd_refusal`) não deve ser lançada no logger,
porque ele não tem status de recusa e contaria a rodada como ausência.

## 10. Registro

- **Log de rodadas.** Uma linha por marca por rodada, em
  [templates/rounds-template.csv](templates/rounds-template.csv). As seis
  primeiras colunas (`data`, `ia`, `prompt`, `marca`, `mencionada`,
  `posicao`) têm o mesmo nome e o mesmo sentido dos campos do
  citation-round-logger, então cada grupo de linhas com o mesmo `round_id`
  vira um registro do logger (um par `--marca` e `--posicao` por linha com
  `mencionada = true`, ou `--ausente` quando nenhuma marca apareceu). As
  demais colunas guardam o que este protocolo acrescenta. Os nomes das
  colunas ficam iguais nos dois idiomas, para o arquivo ser um só.
- **Respostas brutas.** Salve o texto completo de toda resposta em um arquivo
  separado, nomeado pelo `round_id`, e guarde o caminho em
  `response_file`. A classificação só pode ser auditada se o texto bruto
  existir.
- **Conjunto de prompts.** Mantenha o conjunto versionado junto do log,
  escrito com o [modelo de conjunto de prompts](templates/prompt-set-template.pt-BR.md).
- **Cabeçalho da onda.** Para cada onda, registre versão do protocolo,
  versão do conjunto de prompts, versão do bloco de neutralização,
  superfícies, canais, localização, idioma, início e fim da janela, e a
  pessoa ou ferramenta que leu as respostas.
- **Anonimização.** Em dado público, as marcas de terceiros podem ser
  trocadas por códigos estáveis (`Marca 01`, `Marca 02`) quando o estudo
  não precisa dos nomes. Nunca publique identificador de conta.

## 11. Cadência e janelas de comparação

- Prompts de decisão das páginas principais: uma onda a cada 30 dias é uma
  cadência prática. Os demais prompts: na publicação, a cada atualização e
  a cada 90 dias.
- **Mudança só é declarada entre janelas**, comparando ondas com pelo menos
  2 a 4 semanas de distância, nunca a partir de uma rodada ou de uma onda
  isolada.
- Antes de mudar a página medida, rode uma onda de linha de base. Depois da
  mudança, agende a onda de acompanhamento para 2 a 4 semanas depois.
- Diferença menor que a sobreposição dos intervalos de Wilson das duas
  ondas não é relatada como mudança.

## 12. Ameaças à validade e como relatar

| Ameaça | Por que importa | Mitigação e o que relatar |
|---|---|---|
| Personalização | Memória e histórico da conta direcionam a resposta. | Seção 5 em toda rodada; relatar `logged_in`, configuração de memória e versão do bloco. |
| Variância do modelo | A mesma pergunta recebe respostas diferentes. | Mínimo de três rodadas, rótulo de estabilidade, intervalos de Wilson. |
| Mudança de versão e de modo | As superfícies atualizam modelo e recurso sem aviso. | Janela curta, registrar `mode` e datas, marcar como não comparável a onda que atravessa uma mudança visível. |
| Localização | Intenção local e resultado de busca dependem do lugar. | Declarar país, cidade e parâmetros de busca; manter fixos; pôr a intenção local no texto do prompt. |
| Amostra pequena | Poucos prompts e rodadas dão intervalos largos. | Publicar número de prompts, rodadas, rodadas válidas e recusas junto de toda taxa. |
| Viés na escolha dos prompts | Prompt escrito para favorecer a marca infla a presença. | Sem marca no prompt; prompts derivados de demanda observada; publicar o conjunto inteiro. |
| Mistura de canais | API, aplicativo e navegador diferem. | Um canal por série ou sinalização explícita. |
| Erro de leitura | A classificação manual pode errar. | Guardar as respostas brutas; segundo leitor em uma amostra de rodadas; registrar as divergências. |

Todo resultado publicado deve trazer uma linha neste formato:

    Protocolo v1.0.0 | conjunto de prompts v1 (N prompts) | superfícies: ... | canal: ... |
    localização: ... | idioma: ... | janela: AAAA-MM-DD a AAAA-MM-DD |
    rodadas válidas: V de T (recusas: r) | leitura: manual

## 13. Ética e termos das plataformas

- Meça manualmente nas interfaces normais, ou com APIs oficiais e
  ferramentas que as plataformas permitem, sempre dentro dos termos de uso
  vigentes.
- Não use scraper, robô ou automação que os termos da plataforma proíbem.
  Não contorne exigência de login, limite de uso nem CAPTCHA.
- Não crie contas falsas para multiplicar rodadas.
- Não publique dado pessoal visto nas respostas.
- Não apresente os resultados como garantia de citação, recomendação ou
  posição, nem para cliente nem para o público.

## 14. Uso do protocolo dentro de um benchmark

Um benchmark de segmento pode juntar este protocolo (parte B: quem as
superfícies de IA recomendam quando um cliente pede um fornecedor do
segmento) com uma auditoria de sinais técnicos públicos nos sites dos
mesmos negócios (parte A: por exemplo regras do robots.txt para robôs de
IA, dados estruturados, conteúdo presente no HTML inicial). O cruzamento de
A e B é relatado só como padrão descritivo ("os negócios recomendados nas
rodadas tendem a ter X"), com tamanho da amostra e data, nunca como causa.
Se a sobreposição entre as duas partes for pequena demais, apresente as
duas lado a lado, sem cruzar.

## 15. Versão deste protocolo

- O protocolo segue versionamento semântico. **Major**: mudança que quebra
  a comparação com resultados anteriores (por exemplo, regra de contagem
  nova). **Minor**: acréscimo que não muda as métricas existentes.
  **Patch**: redação e esclarecimentos.
- Toda rodada registra a versão do protocolo em `protocol_version`.
- Troca de canal, de configuração de conta ou de versão do bloco de
  neutralização dentro de uma série em andamento só é adotada depois de um
  teste paralelo: três prompts, três rodadas cada, aprovado quando pelo
  menos 2 de 3 rodadas são válidas em pelo menos 2 dos 3 prompts. Resultado
  medido antes da troca não é comparado como mesma condição.

## Relacionados

- [citation-round-logger](https://github.com/LucasFerrazSEO/citation-round-logger), do autor: registro de rodadas por linha de comando.
- [ai-referrer-report](https://github.com/LucasFerrazSEO/ai-referrer-report), do autor: tráfego de indicação de assistentes de IA a partir de exportação do GA4.
- [awesome-generative-engine-optimization](https://github.com/LucasFerrazSEO/awesome-generative-engine-optimization), do autor: lista curada sobre Generative Engine Optimization.
- [Método Rodada de Citação](https://lucasferrazseo.com/glossario/metodo-rodada-de-citacao/), verbete do glossário da agência do autor.
- "GEO: Generative Engine Optimization", KDD 2024 ([arXiv:2311.09735](https://arxiv.org/abs/2311.09735)), como contexto do campo.
