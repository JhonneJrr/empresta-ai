# Registro de prompts — Empresta Aí

Ferramenta: Claude Code (Claude Opus 5.5).
Regra seguida: a IA conduz a entrevista e redige o texto; **todas as regras de negócio foram escolhidas por mim**. Cada sugestão da IA aceita está registrada em `spec.md`, seção 11 (Decisões e premissas).

---

## P01 — Entrevista de decisões
**Prompt (redação revisada do pedido feito na conversa):**
> Vou fazer o trabalho "SDD — App Empresta Aí", cujo enunciado está no PDF em Downloads. Crie o repositório em `Documents\empresta-ai` com os arquivos `spec.md` e `prompts.md`. Antes de escrever a spec, me entreviste tema por tema (prazo, atraso, renovação, devolução e dano, perda, objetos e pessoas, LGPD), cobrindo todas as perguntas do enunciado: para cada uma, apresente de 2 a 4 alternativas com prós e contras e indique a que você recomenda. As regras de negócio são decisão minha: escreva só o que eu escolher.

**Resultado:** repositório criado; as decisões estão na tabela abaixo.

## P02 — Aprofundar as últimas perguntas
**Prompt (redação revisada do pedido feito na conversa):**
> Antes de eu decidir as quatro últimas perguntas (excluir amigo ou objeto com histórico, bloqueio de amigo, dados pessoais pela LGPD e onde os dados ficam), faça uma análise mais detalhada de cada uma: explique o conflito por trás da pergunta, liste as alternativas com prós e contras, mostre como cada uma se relaciona com as decisões que já tomei e quais casos do "pior amigo" a spec precisa cobrir. Indique a sua recomendação, mas a escolha é minha.

**Para quê:** entender melhor as consequências antes de decidir exclusão, bloqueio, LGPD e onde os dados ficam.
**Resultado:** análise de cada tema, com alternativas, prós e contras e casos do "pior amigo". As decisões estão na tabela abaixo.

### Decisões da entrevista
A IA fez perguntas de múltipla escolha, com uma opção marcada como recomendada. A escolha é minha; "(sugestão da IA)" indica que aceitei a opção recomendada.

| Tema | Minha escolha |
|---|---|
| Tomador | Só contato cadastrado pelo dono, sem conta no app (sugestão da IA) |
| Forma do prazo | Dono informa dias ou data; o app grava a data de devolução (sugestão da IA) |
| Limites do prazo | Prazo padrão por categoria; 1 a 90 dias; empréstimo "sem prazo" permitido |
| Vencimento | Fim do dia (23:59:59), no fuso do aparelho do dono (sugestão da IA) |
| Tolerância | 3 dias após o vencimento antes de contar como atrasado |
| Consequência do atraso | Aviso ao dono + alerta ao emprestar de novo, sem bloquear (sugestão da IA) |
| Aviso antes do prazo | 2 dias antes e no dia, às 09:00 (sugestão da IA) |
| Aviso de atraso | 1º dia de atraso e depois a cada 7 dias, até sair de Atrasado ou ser silenciado (sugestão da IA) |
| Renovações | Até 2 por empréstimo (sugestão da IA) |
| Duração da renovação | 1 a 30 dias, somados ao prazo anterior (sugestão da IA) |
| Renovar atrasado | Não pode |
| Estado de conservação | Mesma escala na saída e na volta: Novo, Bom, Desgastado, Danificado, Inutilizado (sugestão da IA) |
| Pós-dano | Dono define o estado e registra o desfecho do acordo (sugestão da IA) |
| Contestação | Dono registra a contestação do amigo; não muda o estado (sugestão da IA) |
| Devolução parcial | Estado "Devolvido parcialmente"; prazo segue para os itens que faltam (sugestão da IA) |
| Perda | Dono marca a qualquer momento; app sugere após 60 dias de atraso (sugestão da IA) |
| Objeto reaparece | Reabre e registra a devolução (sugestão da IA) |
| Empréstimo simultâneo | Não, um empréstimo em aberto por objeto (sugestão da IA) |
| Conjunto | Um objeto com lista de itens (sugestão da IA) |
| Excluir com histórico | Exclui só sem histórico; com histórico, arquiva; com empréstimo em aberto, nada (sugestão da IA) |
| Bloqueio | Manual, mais sugestão do app por critério em 12 meses: 1 perda, 2 danos graves ou 3 atrasos (sugestão da IA) |
| LGPD | Nome obrigatório, telefone e observação opcionais; remoção por anonimização (sugestão da IA) |
| Plataforma | App de celular, dados só no aparelho, com backup em arquivo (sugestão da IA) |

## P03 — Redação da spec
**Prompt (redação revisada do pedido feito na conversa):**
> Com as decisões da entrevista, escreva o `spec.md` seguindo o template do enunciado, com as 12 seções preenchidas: regras de negócio numeradas, atômicas e testáveis; histórias de usuário com critérios Dado/Quando/Então com valores concretos, incluindo cenários de erro; tabela de estados com todas as transições; modelo de dados coerente com as regras; e casos de borda ligados às RNs. Onde as minhas decisões não cobrirem um detalhe, proponha uma premissa e marque-a na seção 11 para eu revisar. Não invente regra sem registrar.

**Para quê:** transformar as decisões em RNs, HUs, estados, modelo de dados e casos de borda.
**Resultado:** `spec.md` v1. Os detalhes que não estavam na entrevista foram propostos pela IA, revisados por mim e marcados como premissa (P) na seção 11.

## P04 — Revisão cética (prompt sugerido pelo enunciado)
**Prompt:**
> Você é um desenvolvedor que vai implementar este app usando apenas a especificação abaixo, sem poder fazer perguntas aos autores. Liste todas as situações em que você precisaria adivinhar um comportamento, citando a seção da spec. Não sugira respostas, apenas as perguntas.

**Onde:** Claude, num agente separado que não participou da redação e recebeu só o `spec.md`.
**Resultado:** 104 perguntas, separadas em três grupos:

| Destino | Qtd. | Exemplos |
|---|---|---|
| Lacuna real → corrigida na spec | 65 | Quando o dano é avaliado numa devolução parcial seguida da completa (→ RN45); se um Atrasado volta a Ativo ao mudar o fuso (→ RN22); como corrigir um empréstimo registrado por engano (→ RN75); itens perdidos e o mínimo de 2 itens (→ RN05); singular/plural e mensagens de erro sem texto definido (→ RN73, RN74) |
| A spec já respondia → descartada | 23 | Se o 8º dia de atraso cai em 28/03 (glossário + RN27); se renovar por exatamente N dias é aceito (RN36: data ≥ hoje); se perda revertida conta para bloqueio (RN55) |
| Detalhe de interface ou de implementação → fora da spec | 16 | Quantas notificações locais agendar de uma vez; ordem de leitura do leitor de tela; busca e paginação das listas; o que fazer se o armazenamento encher ao exportar |

As correções que exigiram decisão nova estão registradas como premissas P14 a P22 na seção 11 da spec.

**Prompt de correção (redação revisada do pedido feito na conversa):**
> Aplique na spec só as perguntas que são lacunas reais ou contradições. Descarte as que a spec já responde e as que são detalhe de interface ou de implementação. Registre como premissa toda decisão nova que a correção exigir.

## P05 — Conferência final com o enunciado
**Prompt (redação revisada do pedido feito na conversa):**
> Confira se a entrega cumpre tudo o que o enunciado pede: entregáveis, seções do template, escopo, cada pergunta obrigatória, regras de uso de IA e cada critério da rubrica. Liste só o que precisa ser corrigido, com a citação literal do trecho. Depois, corrija os problemas confirmados.

**Onde:** Claude, num agente separado que recebeu só o PDF do enunciado, o `spec.md` e o `prompts.md`.
**Resultado:** todos os requisitos atendidos, com 12 problemas apontados e conferidos no texto. Exemplos: regras sem registro na seção 11, objeto que fica com 1 item depois de uma perda, empréstimo excluído sem cancelar os avisos, critério de backup válido indefinido e palavras vagas ("normalmente"). Todos foram corrigidos; as decisões novas estão nas premissas P23 a P30.

## P06 — Segunda conferência com o enunciado
**Prompt:** o mesmo da P05, num agente novo que não viu a conferência anterior.

**Resultado:** todas as perguntas obrigatórias respondidas, com 10 problemas apontados e conferidos no texto. Exemplos: "Perdido" chamado de estado final apesar do reaparecimento, termos da situação do objeto sem definição no glossário, devoluções do mesmo dia sem ordem, regras sem cenário Dado/Quando/Então (reativar avisos, itens pendentes no aviso, avisos após renovação, lista Encerrados) e limites numéricos sem registro na seção 11. Todos foram corrigidos; as decisões novas estão nas premissas P31 a P33.

## P07 — Polimento final
**Prompt (redação revisada do pedido feito na conversa):**
> Faça um polimento final da spec sem criar nem mudar regras: corrija ortografia, referências cruzadas entre RN, HU, D e P, termos que divergem do glossário, mensagens com redação diferente entre seções, palavras vagas, tabelas quebradas e contas de data nos cenários. Liste à parte o que só se resolve com decisão nova.

**Onde:** Claude, num agente separado.
**Resultado:** 16 ajustes, quase todos nas listas "Regras relacionadas" das HUs, que passaram a citar todas as RNs que os cenários testam. Nenhum ponto precisou de decisão nova. Todas as contas de data conferiram.

## P08 — Revisão depois da correção do professor (12/11/2026)
**Prompt (redação revisada do pedido feito na conversa):**
> O professor corrigiu a spec e apontou quatro problemas: o dano some numa devolução parcial seguida de outra melhor; importar um backup antigo desfaz a remoção de dados da LGPD; as séries de avisos não cabem no limite de notificações do celular; e a minha voz aparece pouco (quase tudo é sugestão da IA, o escopo é grande para uma v1 e "Questões em aberto: Nenhuma" soa como confiança excessiva). Me apresente as alternativas para cada ponto e eu decido; depois aplique as minhas decisões na spec.

**Resultado:** a IA apresentou alternativas e eu decidi:

| Ponto | Minha decisão | O que a IA recomendava |
|---|---|---|
| Dano que some | Dano avaliado item a item, com estado de saída e de volta por item (D25) | Valer o pior estado entre as devoluções. Recusei: não diria qual peça foi danificada |
| Backup desfaz a LGPD | Lista de remoções fora do backup, reaplicada ao importar, e limite em outro aparelho admitido (D26) | A mesma. Aceitei |
| Avisos não cabem no celular | Manter só os 60 avisos mais próximos e recalcular ao abrir o app (RN29, P34) | Proposta da IA para atender ao que o professor pediu. Aceitei |
| Corte de MVP | Ficam para a v2: itens e devolução parcial, reaparecimento, sugestão automática de bloqueio e backup (D24) | A IA listou os candidatos; a escolha dos quatro é minha |
| Decisões da IA que mudei | D07 (aviso 1 dia antes, não 2), D08 (avisos de atraso diários na 1ª semana), D09 (1 renovação, não 2), D16 (sugerir perda aos 30 dias, não 60) | Eram as sugestões originais da IA, aceitas na entrevista |
| Questões em aberto | A seção 12 deixou de dizer "Nenhuma" e lista 7 limites e dúvidas | — |

## P09 — Análise de furos antes do protótipo (12/11/2026)
**Prompt (redação revisada do pedido feito na conversa):**
> Antes de começar o protótipo, analise os furos que ainda existem na spec revisada: contradições entre regras, casos do "pior amigo" que escapam, regras do MVP que dependem de regra marcada [v2] e lacunas que um implementador teria de adivinhar. Não reabra as minhas decisões; aponte só furo real, com o trecho citado, e diga quais valem ser corrigidos antes do protótipo.

**Onde:** Claude, em dois agentes separados com papéis opostos (um avalia o rigor, o outro ataca a spec), e uma síntese das duas leituras.
**Resultado:** os dois concordaram que o MVP ainda dependia de regras marcadas [v2] (a devolução completa só estava definida em regras da v2). O agente que atacou a spec achou três furos de produto. As decisões foram minhas:

| Furo | Minha decisão | O que a IA recomendava |
|---|---|---|
| Amigo com dano pendente pedia a remoção de dados e sumia do histórico | Remoção bloqueada enquanto houver dano em "Reparo combinado" ou "Reposição combinada" (D27) | Permitir, só com aviso ao dono. Recusei: o dono precisa saber com quem combinou o conserto |
| No MVP, quem perdeu ou danificou um objeto recebia outro empréstimo sem alerta | O alerta ao emprestar cita também perdas e danos dos últimos 12 meses (D28) | A mesma. Aceitei |
| Protótipo: MVP ou spec inteira | Só o MVP | A mesma. Aceitei |
| Demais correções (devolução completa com regra própria no MVP, marcas [v2] que faltavam, amigo excluído na lista de remoções, prioridade dos avisos de prazo, dias de atraso nunca abaixo de 1) | Aplicar todas (P35 a P37) | A mesma. Aceitei |

Os limites que não foram resolvidos entraram na seção 12 (itens 8 e 9).
