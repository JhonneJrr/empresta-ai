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
