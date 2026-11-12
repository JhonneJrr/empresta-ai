# Empresta Aí — Especificação de design v1

Aluno: Jhonne Jefferson | Data: 12/11/2026

Este documento descreve **como o app se parece e se move**. O que o app faz está em `spec.md` (a spec de funcionalidades). As duas se leem juntas.

## 1. Intenção

O primeiro protótipo aplicava as regras, mas tinha uma cara fraca e comum: cabeçalho verde, cartões brancos com borda cinza, abas de texto. Esta spec define um design convidativo e sereno, em que as funcionalidades aparecem com clareza: **uma carteira de passes monocromática**. Cada empréstimo é um passe; a lista mostra pouco; o resto fica na ficha.

Decisões do dono, na ordem em que foram tomadas:

1. Base de interface fluida, com animações que melhorem a sensação de uso, e bibliotecas de apoio liberadas.
2. Entre três direções mostradas em mockup (painel de partidas, carteira de passes, sinalização de terminal), escolheu a **carteira de passes**.
3. "Sem tantas cores" e, depois, "**monocromático**, design mais vazado, mais limpo, tem muita info na tela".
4. No computador, o passe escolhido abre como **ficha ao lado da lista**.
5. Formulários: no celular, **folha que sobe de baixo**; no computador, **no lugar da ficha**, com a lista sempre visível.
6. Primeiro o MVP; as funções `[v2]` entram depois, no mesmo design.

## 2. Como esta spec conversa com a `spec.md`

- **A `spec.md` manda nas regras.** Esta spec não cria, não muda e não remove regra de negócio. Se as duas discordarem, vale a `spec.md` e a divergência é corrigida aqui.
- **Os textos de regra vêm da `spec.md`.** Mensagens de erro, de validação e de confirmação, rótulos de situação e nomes de botão definidos nos critérios de aceite são usados com o texto exato. Esta spec só define onde e como aparecem.
- **Identificadores.** As regras de design são numeradas `RD01`, `RD02`… Cada uma cita a `RN` ou a `HU` que apresenta. No código, um trecho de interface leva os dois comentários: `// RD07` e `// RN23`.
- **Recorte.** Vale o MVP da seção 2.3 da `spec.md`. A seção 12 desta spec mostra como as funções `[v2]` entram depois.
- **Textos que o design acrescenta.** Tudo o que aparece na tela e não está na `spec.md` está listado na seção 11, para ser registrado como lacuna no protótipo ou levado para a `spec.md`. Nada entra escondido.

## 3. Objetivos, não objetivos e o que seria um fracasso

**Objetivos**
- A tela inicial responde em um olhar: o que está fora, com quem e quando volta.
- A situação de cada empréstimo é entendida sem cor: pela forma do passe e por uma frase.
- Cada ação bloqueada por uma regra mostra o motivo ao lado, com o texto da `spec.md`.
- O movimento ajuda a entender o que mudou (um passe que abre, uma folha que sobe) e nunca atrasa o uso.
- O professor consegue executar os critérios de aceite sem procurar: data simulada sempre à vista.

**Não objetivos**
- Tema escuro, personalização de cores e ilustrações.
- Animação decorativa, de fundo ou contínua.
- Mostrar todos os dados do empréstimo na lista.

**Seria um fracasso, mesmo funcionando**
- Voltar a parecer o primeiro protótipo: cartões genéricos, cabeçalho colorido, tela cheia de rótulos.
- Qualquer matiz (azul, vermelho, verde, âmbar) em qualquer elemento da interface. As fotos dos objetos são a única exceção (RD01).
- Situação indicada só por cor ou só por ícone.
- Texto inventado no lugar de uma mensagem que a `spec.md` define.

## 4. Restrições

- Um único arquivo `.html`, com HTML, CSS e JavaScript juntos; abre com dois cliques; sem backend e sem etapa de build (enunciado da Etapa 2).
- Bibliotecas externas só por CDN e só as da seção 9.
- Dados no `localStorage`; o app abre com dados de exemplo.
- Funciona em celular e em computador.
- Código simples, legível para um aluno iniciante: funções pequenas com nomes em português, sem framework.
- Acessibilidade da seção 10 da `spec.md`: contraste mínimo de 4,5:1, texto que respeita o tamanho de fonte do sistema até 200%, área de toque mínima de 44×44, rótulos para leitor de tela.

## 5. Referência visual aprovada

Os mockups aprovados pelo dono ficam no repositório e são o contrato visual. Quem constrói **porta o HTML e o CSS deles**, não reinterpreta.

| Arquivo | O que mostra |
|---|---|
| `mockups/carteira-mvp.html` | Tela Empréstimos no celular e no computador, com a ficha ao lado. Base do MVP |
| `mockups/carteira-v2.html` | A folha de formulário no celular e as peças das funções `[v2]` |

O que os mockups não mostram segue as regras desta spec. Onde um mockup e esta spec discordarem, vale esta spec (as diferenças conhecidas estão na seção 13).

## 6. Fundamentos

### 6.1 Cor

- **RD01** — O app usa uma tinta só. Não existe matiz em nenhum elemento, estado, ícone, contador, link ou foco. A única cor do app são as **fotos** dos objetos, exibidas com as cores reais e só dentro da ficha e do formulário (RD49); decisão do dono.

| Nome | Valor | Uso |
|---|---|---|
| `--tinta` | `#141414` | Texto principal, passe atrasado, botão principal, contornos fortes |
| `--cinza-1` | `#5F5F5B` | Texto secundário (contraste de 6,4:1 sobre branco) |
| `--cinza-2` | `#C4C4BF` | Contornos finos, picote, texto secundário sobre tinta |
| `--cinza-3` | `#ECECE8` | Linhas divisórias, fundo de passe encerrado, campo desabilitado |
| `--papel` | `#FFFFFF` | Fundo do app e dos passes |

- **RD02** — A situação nunca depende de cor: é dita pela forma do passe (RD10) e por uma frase escrita (cumpre o requisito de acessibilidade da `spec.md`, seção 10).

### 6.2 Tipografia

- **RD03** — Uma família: **Geist** (Google Fonts), com `Manrope` e a fonte do sistema como reserva. Dois pesos: 400 e 600. Algarismos tabulares nas datas e nos números.

| Papel | Tamanho | Peso | Onde |
|---|---|---|---|
| Título de tela | 32 px (celular), 30 px (computador) | 600 | "Empréstimos" |
| Nome na ficha | 44 px (computador), 30 px (celular) | 600 | Nome do objeto ou do amigo na ficha |
| Frase de situação na ficha | 28 px | 600 | "5 dias de atraso" |
| Data no passe | 30 px | 600 | "04/11" |
| Nome no passe | 18 px | 600 | Nome do objeto |
| Corpo | 16 px | 400 | Campos, textos, botões (600) |
| Apoio | 14 px | 400 | "com Carla Souza", frase do passe, ajuda |
| Rótulo de grupo | 11 px, caixa alta, espaçamento de 0,12 em | 600 | "ATRASADOS", "O QUE VOLTOU" |

- Letras apertadas nos tamanhos grandes (`letter-spacing: -0.02em`); altura de linha de 1,15 em títulos e 1,5 no corpo.
- Tamanhos em `rem`, para o texto acompanhar o tamanho de fonte do sistema.

### 6.3 Espaço, forma e sombra

- **RD04** — A escala de espaço é 4, 8, 12, 16, 24, 32, 48, 64 px. Margem lateral: 24 px no celular, 48 px no computador. O ar vem das margens e do pouco conteúdo; nenhuma tela fica "preenchida" com elementos para ocupar espaço.
- **RD05** — Cantos: 16 px nos passes, 14 px nos botões e campos, 24 px no topo da folha. Contornos de 1 px (fino), 1,5 px (botão de contorno) e 2,5 px (seleção).
- **RD06** — Sem sombra em repouso. Só o passe sob o ponteiro ganha uma sombra quase imperceptível (`0 8px 12px rgba(20,20,20,.06)`), e a folha tem a sombra que a separa da lista (`0 -12px 32px rgba(20,20,20,.14)`). Sem gradiente, sem vidro fosco, sem brilho.

### 6.4 Ícones

- **RD07** — Ícones de traço, de uma única biblioteca (Phosphor, peso regular), com 24 px e traço uniforme, em tinta ou `--cinza-1`. Aparecem só na navegação, no botão "Novo empréstimo" (sinal de mais) e em setas de ação. Nenhum ícone substitui uma palavra: todo ícone tem rótulo visível ou `aria-label`. Sem emoji.

## 7. Componentes

### 7.1 O passe

- **RD08** — Cada empréstimo é um passe: um cartão de cantos arredondados com um **picote** (linha tracejada com dois entalhes laterais) que separa a parte de cima da tira de baixo.
- **RD09** — O passe mostra só isto (apresenta a RN68):
  - em cima, à esquerda: o nome do objeto e, abaixo, "com <nome do amigo>";
  - em cima, à direita: a data prevista em algarismos grandes, ou "Sem prazo". No passe a data aparece abreviada como `dd/mm`; quando o ano da data prevista é diferente do ano de hoje, o ano aparece em 14 px logo abaixo. A data completa (dd/mm/aaaa, RN02) está sempre na ficha e no texto lido pelo leitor de tela;
  - na tira: uma única frase de situação.

  Categoria, estado de conservação, renovações, fotos e ações **não** aparecem no passe; ficam na ficha.
- **RD10** — A forma do passe diz a situação. As frases seguem o singular e o plural da RN73 ("1 dia de atraso", "fica atrasado em 1 dia", "Falta 1 dia"):

| Situação (`spec.md`) | Forma do passe | Frase na tira | Regra |
|---|---|---|---|
| Atrasado | **Invertido**: fundo tinta, texto branco | "<D> dias de atraso" | RN22, RN68 |
| Ativo, em tolerância | Branco, contorno **tracejado** em tinta | "Em tolerância — fica atrasado em N dias" | RN23, RN68 |
| Ativo, dentro do prazo | Branco, contorno fino `--cinza-2` | "Faltam <N> dias" ou "Vence hoje" (seção 11) | RN21 |
| Ativo, sem prazo | Branco, contorno fino `--cinza-2` | "Sem prazo" | RN19, RN68 |
| Devolvido | Fundo `--cinza-3`, sem contorno, texto em `--cinza-1` | "Devolvido em dd/mm/aaaa" e, se houver, " · Com dano" | RN68 |
| Perdido | Fundo `--cinza-3`, sem contorno, nome do objeto riscado | "Perdido" | RN48, RN68 |

- **RD11** — O passe é um botão: tem foco visível (contorno de 2 px em tinta, afastado 3 px), responde a Enter e a espaço, e o leitor de tela lê "<objeto>, com <amigo>, <frase de situação>, <data prevista por extenso>".
- **RD12** — O passe selecionado (computador) ganha contorno de 2,5 px em tinta, afastado 3 px.

### 7.2 A ficha

- **RD13** — A ficha é a tela do empréstimo (apresenta as RN25, RN33, RN45, RN46, RN48, RN75). Ordem fixa, de cima para baixo:
  1. Nome do objeto (grande) e "com <amigo>".
  2. A frase de situação (grande) e uma linha de apoio em `--cinza-1` (ex.: "O prazo era 04/11/2026.").
  3. A linha do tempo (RD14).
  4. A faixa da RN25, quando couber: "Atrasado há <D> dias. Marcar como perdido?".
  5. As ações: **no máximo duas à vista**, a principal em botão cheio e a segunda em contorno; as demais em "Mais ações".
  6. O motivo de uma ação proibida, quando a `spec.md` define o texto, em texto pequeno `--cinza-1` (RD16).
  7. "Ver detalhes": abre, na própria ficha, estado na saída e na volta, renovação, avisos, desfecho do dano, contestação e fotos.
- **RD14** — A linha do tempo é um fio fino com pontos: "Saída dd/mm", "Prazo dd/mm", "Tolerância até dd/mm", "Atrasado desde dd/mm". Pontos já passados ficam cheios; o atual tem um anel; os futuros ficam vazios. Empréstimo sem prazo mostra só "Saída". Empréstimo encerrado acrescenta "Devolvido dd/mm" ou "Perdido dd/mm".
- **RD15** — Ação principal por situação:

| Situação | Principal | Segunda | Em "Mais ações" |
|---|---|---|---|
| Ativo com prazo | Registrar devolução | Renovar (se ainda não renovado) | Marcar como perdido, Silenciar avisos ou Reativar avisos (RN30), Excluir empréstimo (só no dia da criação, RN75) |
| Ativo sem prazo | Registrar devolução | Definir prazo (se ainda não definido, RN20) | Marcar como perdido, Silenciar avisos, Excluir empréstimo |
| Atrasado | Registrar devolução | Marcar como perdido | Silenciar avisos |
| Devolvido com dano | Trocar desfecho | Registrar contestação | — |
| Devolvido sem dano, Perdido | — (ficha só de leitura) | — | — |

- **RD16** — Uma ação que a regra proíbe naquela situação não aparece como botão. Quando a `spec.md` define um texto para esse caso, a ficha mostra esse texto, exato e sem acréscimo, como linha de apoio abaixo das ações (ex.: em empréstimo Atrasado, "Empréstimo atrasado não pode ser renovado", HU04). Quando a `spec.md` só diz que a opção "não aparece", nada é mostrado. "Silenciar avisos" e "Reativar avisos" (RN30) se alternam nas linhas da RD15 em que aparecem, e valem também para o empréstimo Atrasado.

### 7.3 Formulários e a folha

- **RD17** — Todo formulário (novo empréstimo, renovar, definir prazo, registrar devolução, cadastrar ou editar objeto e amigo, contestação, bloqueio) usa o mesmo componente: no celular, uma **folha** que sobe de baixo, cobre a tela a partir de 60 px do topo, com alça e cantos superiores de 24 px; no computador, ocupa **o lugar da ficha**, à direita, com a lista visível.
- **RD44** — Só existe uma folha aberta por vez. No celular, quando a ficha já está aberta como folha e o dono escolhe uma ação com formulário, o conteúdo da mesma folha é trocado pelo formulário (com esmaecimento cruzado de 200 ms); ao salvar ou cancelar, a folha volta a mostrar a ficha, já atualizada. No computador vale o mesmo para a área da direita: o formulário entra no lugar da ficha e, ao salvar ou cancelar, a ficha volta. "Novo empréstimo", sem ficha de origem, fecha a folha ao cancelar e abre a ficha do empréstimo criado ao salvar.
- **RD18** — Estrutura da folha: título, linha de contexto em `--cinza-1` (ex.: "Furadeira Bosch · com Carla Souza"), os campos, e no rodapé o botão principal cheio e o link "Cancelar". Um formulário, uma ação principal.
- **RD19** — Campos: rótulo acima em 14 px, campo de 52 px de altura, contorno fino `--cinza-2`, contorno de 1,5 px em tinta no foco. Seleções de lista curta (estado de conservação, desfecho do dano) são grupos de opções em linha, não menus suspensos. Datas usam o seletor nativo do navegador, limitado às datas válidas (RN74).
- **RD49** — Fotos (RN04, RN38, RN40): no formulário, o campo de foto é uma área tracejada de 96×96 px com o texto "Adicionar foto"; depois de escolhida, mostra a miniatura colorida, com cantos de 14 px, e os links "Trocar" e "Remover". Na ficha, as fotos aparecem só em "Ver detalhes", coloridas, com largura de até 240 px e a legenda em `--cinza-1` ("Foto do objeto", "Na saída", "Na volta"). Nenhuma foto aparece no passe nem nas listas. No protótipo, a foto é guardada no `localStorage` se couber; se não couber, o registro é salvo sem ela e o app avisa (lacuna do protótipo).
- **RD20** — Erro de validação: a mensagem exata da `spec.md` aparece logo abaixo do campo, em tinta, peso 600, com o prefixo "Erro: " lido pelo leitor de tela (`role="alert"`); o campo ganha contorno de 2 px em tinta. Nada é salvo e o foco vai para o primeiro campo com erro.
- **RD21** — Fechar a folha: botão "Cancelar", tecla Esc, ou toque na área escurecida acima da folha. Se houver dado digitado, o app pede confirmação antes de descartar (texto na seção 11).

### 7.4 Diálogos de confirmação e avisos na tela

- **RD22** — Perguntas com dois botões definidas na `spec.md` (RN13, RN24, RN47, RN48, RN52, RN57, RN65) usam um diálogo centrado, de até 360 px de largura, fundo `--papel`, com o texto exato e os botões nomeados na spec; a ação que confirma fica em botão cheio e a que desiste em contorno. Onde a spec não nomeia os botões, usam-se "Confirmar" e "Cancelar" (seção 11). O foco entra no diálogo, fica preso nele e volta ao elemento de origem ao fechar.
- **RD45** — Mensagens da `spec.md` que explicam por que uma ação não pode ser feita e não pertencem a um campo (RN59, as duas da RN64, RN51) aparecem no mesmo diálogo da RD22, com o texto exato e um único botão em contorno, "Entendi" (seção 11). A confirmação de excluir um empréstimo registrado por engano (RN75) também usa o diálogo, com "Confirmar" e "Cancelar".
- **RD23** — Resultado de uma ação (ex.: empréstimo registrado) aparece numa faixa discreta no rodapé, por 4 segundos, em tinta sobre `--papel` com contorno fino, e é anunciada ao leitor de tela (`role="status"`). Não cobre a navegação.
- **RD24** — A faixa da RN31 ("Avisos desligados…") aparece no topo da tela Empréstimos, abaixo do controle de data, em texto de apoio.

### 7.5 Botões

- **RD25** — Três níveis: **cheio** (fundo tinta, texto branco, um por tela), **contorno** (1,5 px em tinta) e **texto** (sublinhado). Altura de 52 px; área de toque nunca menor que 44×44. Desabilitado: fundo `--cinza-3`, texto `--cinza-1`, sem resposta ao ponteiro.

### 7.6 Navegação e topo

- **RD26** — Quatro destinos: Empréstimos, Objetos, Amigos, Avisos. No celular, barra inferior com ícone e rótulo; no computador, coluna estreita à esquerda. O destino atual fica em tinta; os outros em `--cinza-1`. O número de avisos não lidos aparece como algarismo pequeno em tinta ao lado do ícone, sem círculo colorido.
- **RD27** — O controle de **data simulada** fica sempre visível, numa linha fina no topo: a data em tinta e os comandos "−1 dia", "+1 dia", "+7 dias" e "Hoje" como texto pequeno, sem caixas, com área de toque de 44 px. Pertence ao protótipo (enunciado da Etapa 2), não ao app final.
- **RD28** — Topo da tela Empréstimos: o título e, ao lado, o resumo em `--cinza-1` (seção 11). Abaixo, os três filtros como palavras em linha — "Atrasados <n>", "Ativos <n>", "Encerrados <n>" —, com o ativo sublinhado em tinta.
- **RD29** — As listas da RN67 aparecem assim: por padrão, **Atrasados** e **Ativos** na mesma rolagem, como dois grupos com rótulo, os atrasados primeiro; **Encerrados** numa visão própria. Tocar em um filtro mostra só aquela lista; tocar de novo no filtro que está sozinho volta à visão padrão. A ordem dentro de cada lista é a da RN67.
- **RD30** — O botão "Novo empréstimo" é o único botão cheio da tela Empréstimos: fixo acima da navegação no celular, no topo da coluna da lista no computador.

### 7.7 Estados vazios

- **RD31** — Lista vazia mostra uma frase em `--cinza-1`, centrada, e nada mais (sem ilustração). Para a lista Atrasados, o texto é o da `spec.md` ("Nenhum empréstimo atrasado", HU08). Os demais textos estão na seção 11.

## 8. Telas e histórias

| História (`spec.md`) | Onde acontece | Como aparece |
|---|---|---|
| HU01 Cadastrar objeto | Tela Objetos → folha "Novo objeto" | Lista de objetos em linhas simples (nome, situação por extenso); formulário na folha |
| HU02 Cadastrar amigo | Tela Amigos → folha "Novo amigo" | Lista de amigos em linhas simples; formulário na folha |
| HU03 Registrar empréstimo | Botão "Novo empréstimo" → folha | Três passos na mesma folha, em sequência: objeto, amigo, prazo e estado na saída. O alerta da RN24 é um diálogo (RD22) |
| HU04 Renovar | Ficha → "Renovar" → folha | Campo de dias; a nova data aparece calculada abaixo do campo |
| HU05 Registrar devolução | Ficha → "Registrar devolução" → folha | Estado na volta em grupo de opções; se houver dano, o desfecho aparece na mesma folha |
| HU06 Desfecho e contestação | Ficha de empréstimo Devolvido com dano | "Trocar desfecho" e "Registrar contestação" |
| HU07 Perda | Ficha → "Marcar como perdido" | Diálogo de confirmação; depois, a oferta de arquivar (RN52) |
| HU08 Listar empréstimos | Tela Empréstimos | Passes em grupos (RD29) |
| HU09 Avisos | Tela Avisos | Duas listas em linhas simples: "Entregues" e "Próximos", com data e texto do aviso; "Silenciar avisos" fica na ficha |
| HU10 Históricos | Ficha do objeto e ficha do amigo | Lista de passes encerrados e em aberto daquele objeto ou amigo; no amigo, o resumo da RN70 no topo |
| HU11 Bloquear amigo | Ficha do amigo → "Bloquear" | Folha com o motivo opcional; amigo bloqueado mostra a palavra "Bloqueado" na linha e na ficha |
| HU12 Arquivar e excluir | Ficha do objeto ou do amigo → "Mais ações" | Diálogos de confirmação; filtro "Arquivados" como palavra em linha nas telas Objetos e Amigos |
| HU13 Remover dados pessoais | Ficha do amigo → "Mais ações" | Diálogo com o texto da RN65 |

- **RD32** — Objetos e amigos **não** são passes: são linhas simples, com divisória fina, nome à esquerda e a situação por extenso à direita ("Disponível", "Emprestado", "Perdido", "Bloqueado"). O passe é reservado ao empréstimo.
- **RD33** — A ficha de objeto e a de amigo seguem a mesma ordem da RD13: nome grande, frase de situação, ações (no máximo duas à vista) e o histórico em passes.

| Ficha | Principal | Segunda | Em "Mais ações" |
|---|---|---|---|
| Objeto | Editar | — | Arquivar ou Desarquivar, Excluir (só sem empréstimos, RN57) |
| Amigo | Editar | Bloquear ou Desbloquear | Arquivar ou Desarquivar, Remover dados pessoais, Excluir (só sem empréstimos) |
| Amigo anonimizado | — (só leitura, RN66) | — | — |

## 8.1 O que é próprio do protótipo

- **RD46** — O controle de data simulada anda só em dias; não há hora do dia. Ao entrar num dia, considera-se que já passou das 09:00. Os cenários da `spec.md` que dependem de hora (aviso das 09:00 no momento do registro, exclusão até 23:59:59) são tratados por dia e registrados como lacuna do protótipo.
- **RD47** — O navegador não agenda notificação. A tela Avisos é a única forma de aviso do protótipo: "Entregues" lista os avisos que já teriam chegado até a data simulada, do mais recente para o mais antigo; "Próximos" lista a fila agendada, limitada a 60 (RN29). As duas listas são recalculadas quando a data anda e em toda ação que muda avisos.
- **RD48** — Para a faixa da RN31, a tela Avisos tem uma opção em linha, "Simular notificações negadas" (seção 11), que liga e desliga a faixa. É um recurso do protótipo, registrado como lacuna.

## 9. Movimento

- **RD34** — O movimento tem três funções: mostrar de onde uma coisa veio (a folha sobe, a ficha desliza), confirmar um toque (o passe sobe, o botão cede) e chamar atenção para o que mudou quando a data anda. Nada se move sozinho em laço.
- **RD35** — Só `transform` e `opacity` são animados. Curva única de desaceleração: `cubic-bezier(.16, 1, .3, 1)`. Sem quique, sem elástico.
- **RD36** — Com "reduzir movimento" ligado no sistema, todas as animações são desligadas e as mudanças acontecem de imediato.

| Momento | Movimento | Duração |
|---|---|---|
| Entrada dos passes ao abrir uma lista | Sobem 12 px com esmaecimento, em sequência, 60 ms entre um e outro (no máximo os 8 primeiros) | 400 ms cada |
| Ponteiro sobre o passe (computador) | Sobe 4 px e ganha a sombra da RD06 | 200 ms |
| Toque no passe ou no botão | Cede para 98% do tamanho | 120 ms |
| Abrir a ficha (computador) | Desliza 16 px da direita com esmaecimento | 300 ms |
| Abrir a ficha (celular) | Sobe como folha (mesmo movimento da folha) | 350 ms |
| Abrir a folha de formulário | Sobe de baixo, com mola suave e sem ultrapassar a posição final; o fundo escurece 30% | 350 ms |
| Fechar folha ou ficha | Caminho inverso, mais curto | 200 ms |
| Diálogo de confirmação | Esmaece e cresce de 96% para 100% | 200 ms |
| A data simulada muda | Os passes cuja situação mudou trocam de forma com esmaecimento cruzado; um passe que muda de grupo desliza até a nova posição | 400 ms |
| Faixa de resultado | Sobe 8 px com esmaecimento; some esmaecendo | 250 ms |

- **RD37** — A biblioteca **Motion** (seção 9.1) cuida de três coisas que o CSS puro faz mal: a mola da folha, o arrasto para fechar a folha no celular e o deslizamento dos passes que mudam de posição quando a data anda. O resto é CSS.

### 9.1 Bibliotecas

| Biblioteca | Para quê | Como entra |
|---|---|---|
| Motion (motion.dev), versão fixa | Mola da folha, arrasto, reposicionamento dos passes | `<script>` por CDN (jsDelivr) |
| Geist e Manrope | Tipografia | Google Fonts, por `<link>` |
| Phosphor Icons | Ícones da navegação e das ações | Os SVG usados são copiados para dentro do HTML (licença MIT), sem carregar a biblioteca inteira |

- **RD38** — Sem internet, o app continua utilizável: a fonte cai para a do sistema e as animações da Motion caem para as transições em CSS. Nenhuma função depende de uma biblioteca externa para funcionar.

## 10. Celular e computador

- **RD39** — Abaixo de 900 px de largura (celular): uma coluna; navegação inferior; ficha e formulários como folha; botão "Novo empréstimo" fixo acima da navegação; a lista rola por baixo dele, com espaço final para o último passe não ficar coberto.
- **RD40** — A partir de 900 px (computador): três áreas — navegação em coluna (cerca de 120 px), lista (cerca de 380 px) e ficha (o restante, com largura de leitura de até 520 px). Sem ficha escolhida, a área da direita mostra a frase "Escolha um empréstimo para ver os detalhes." em `--cinza-1`.
- **RD41** — Entre 900 e 1100 px, a navegação mostra só os ícones (com `aria-label`), para a lista e a ficha não apertarem.
- **RD42** — Nenhuma tela tem rolagem horizontal em larguras a partir de 320 px. Com a fonte do sistema em 200%, os passes crescem em altura e a data desce para baixo do nome, sem cortar texto.

## 11. Textos que o design acrescenta

Estes textos aparecem na tela e **não estão na `spec.md`**. No protótipo, cada um é registrado como lacuna (`// LACUNA`) com a decisão abaixo, ou é levado para a `spec.md` numa próxima revisão.

| Onde | Texto | Por quê |
|---|---|---|
| Passe, empréstimo dentro do prazo | "Faltam <N> dias" (N = data prevista − hoje; com a RN73, "Falta 1 dia"); no dia da data prevista, "Vence hoje" | A RN68 não define frase para o empréstimo em dia; sem ela a tira ficaria vazia |
| Passe | "com <nome do amigo>" | A RN68 pede o nome do amigo; o "com" é do design |
| Topo da tela Empréstimos | "<F> fora · <A> atrasados" (F = empréstimos em aberto; A = Atrasados; com a RN73) | Resumo de um olhar; não é regra |
| Ficha, linha de apoio | "O prazo era dd/mm/aaaa." (Atrasado); "O prazo é dd/mm/aaaa." (Ativo com prazo); "Emprestado em dd/mm/aaaa." (sem prazo) | Contexto da frase de situação |
| Ficha, linha do tempo | "Saída", "Prazo", "Tolerância até", "Atrasado desde", "Devolvido", "Perdido" | Rótulos dos pontos |
| Ficha | "Mais ações", "Ver detalhes" | Navegação |
| Passe, data prevista | Forma abreviada `dd/mm`, com o ano abaixo só quando difere do ano de hoje | A RN02 pede dd/mm/aaaa; a data completa fica na ficha |
| Diálogo de motivo (RN51, RN59, RN64) | Botão "Entendi" | A `spec.md` dá a mensagem, mas não o botão |
| Tela Avisos | "Entregues", "Próximos", "Simular notificações negadas" | Recursos do protótipo (RD47, RD48) |
| Campo de foto | "Adicionar foto", "Trocar", "Remover"; legendas "Foto do objeto", "Na saída", "Na volta" | A `spec.md` permite a foto, mas não dá os textos do campo |
| Computador, sem ficha escolhida | "Escolha um empréstimo para ver os detalhes." | Estado vazio |
| Listas vazias | "Nenhum empréstimo ativo", "Nenhum empréstimo encerrado", "Nenhum objeto cadastrado", "Nenhum amigo cadastrado", "Nenhum aviso" | A `spec.md` só define "Nenhum empréstimo atrasado" |
| Folha com dados digitados | "Descartar o que foi preenchido?" com "Descartar" e "Continuar preenchendo" | Evita perder digitação |
| Diálogos sem botões nomeados na spec | "Confirmar" e "Cancelar" | RN48, RN57, RN65 não nomeiam os botões |
| Faixa de resultado | "Feito: <o que foi feito>" (ex.: "Feito: empréstimo registrado") | A `spec.md` não define texto de sucesso |
| Controle de data | "−1 dia", "+1 dia", "+7 dias", "Hoje" | Exigência do enunciado da Etapa 2 |

## 12. Como as funções `[v2]` entram depois

O design já prevê as funções `[v2]` da `spec.md`, sem mudar nada do MVP (referência: `mockups/carteira-v2.html`).

| Função `[v2]` | Como aparece |
|---|---|
| Objeto com itens | Na folha "Novo objeto", uma lista de itens, cada um com nome e estado de conservação |
| Devolução parcial | Na folha de devolução, o grupo "O QUE VOLTOU": uma linha por item, com caixa de marcação (desenhada em tinta) e o estado de cada um; o dano aparece em palavras na linha do item |
| Passe "Devolvido parcialmente" | Passe branco de contorno fino com a **tira preenchida em tinta pela metade** e a frase "Falta 1 item: <item>" / "Faltam N itens: …" (RN68) |
| Ficha com itens | Lista de itens com marcador (círculo cheio = voltou, vazio = falta) |
| Objeto reapareceu | Segunda ação na ficha do empréstimo Perdido |
| Sugestão de bloqueio | Diálogo da RD22, com o texto da RN56 e os botões "Bloquear" e "Agora não" |
| Backup | Quinto destino da navegação, "Ajustes", com "Exportar backup" e "Importar backup" em linhas de ação |

## 13. Decisões minhas e pontos em aberto

"Minhas" aqui quer dizer: propostas pela IA durante a escrita, a partir dos mockups aprovados, e ainda não confirmadas uma a uma pelo dono.

| # | Decisão | Alternativa |
|---|---|---|
| 1 | Passes de empréstimos encerrados em fundo `--cinza-3`; o Perdido com o nome riscado (RD10) | Encerrados com a mesma forma dos ativos |
| 2 | Atrasados e Ativos na mesma rolagem por padrão; os filtros isolam uma lista (RD29) | Três abas de seleção única, como a RN67 lista |
| 3 | Objetos e amigos como linhas simples, não como passes (RD32) | Tudo como passe |
| 4 | No celular, a ficha abre como folha (RD39) | Tela inteira com botão de voltar |
| 5 | No máximo duas ações à vista na ficha; o resto em "Mais ações" (RD13, RD15) | Todas as ações visíveis |
| 6 | Ícones Phosphor copiados para dentro do HTML (RD07) | Ícones de traço próprios, como nos mockups |
| 7 | Limite de 900 px entre celular e computador (RD39) | Outro valor |
| 8 | "Vence hoje" e "Faltam N dias" como frase do empréstimo em dia (seção 11) | Deixar a tira vazia, como a RN68 permite |

Diferenças conhecidas entre os mockups e esta spec (vale a spec):
- Nos mockups, o espaço entre passes no celular é de 10 px; a spec pede 12 px no mínimo.
- Nos mockups, os filtros "Atrasados" e "Ativos" aparecem sublinhados juntos; pela RD28 e RD29, isso representa a visão padrão com os dois grupos.
- Os mockups não mostram passes encerrados, listas de objetos e amigos, tela Avisos, diálogos do MVP nem estados de erro: valem as RD10, RD20, RD22, RD31 e RD32.
- O picote quase não aparece nos passes brancos; deve ficar visível (entalhes de 8 px e linha tracejada em `--cinza-2`).
- As frases do mockup `[v2]` não foram conferidas contra a `spec.md`; valem as da `spec.md`.

## 14. Como saber que ficou certo

1. Nenhum valor de cor com matiz no CSS: só os cinco da RD01. A única cor na tela vem das fotos, e só na ficha e no formulário.
2. Em 390 px e em 1280 px de largura, a tela Empréstimos coincide com `mockups/carteira-mvp.html` em estrutura, tipografia e espaçamento.
3. Cada passe mostra no máximo quatro informações: objeto, amigo, data e uma frase.
4. Com a tela em tons de cinza e sem ícones, ainda dá para dizer a situação de cada empréstimo.
5. Toda mensagem de erro, confirmação e rótulo de situação bate, letra por letra, com a `spec.md`.
6. Todo texto da seção 11 está marcado como lacuna no protótipo.
7. Contraste de todo texto ≥ 4,5:1; todo alvo de toque ≥ 44×44; navegação completa por teclado, com foco visível.
8. Com "reduzir movimento" ligado, nada se anima.
9. Sem internet, todas as histórias continuam funcionando.
10. Nenhuma rolagem horizontal de 320 px a 1920 px.
