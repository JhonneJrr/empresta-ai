# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Stack

Um único arquivo HTML, com HTML, CSS e JavaScript juntos, sem backend e sem etapa de build. Abre com dois cliques no navegador. Bibliotecas externas só por CDN e só se forem necessárias. Dados no `localStorage`. Exigência do enunciado da Etapa 2 do trabalho.

## Users

O **dono**: uma pessoa que empresta livros, ferramentas e jogos a amigos e esquece o que emprestou, para quem e até quando. Usa o app no celular, em casa, em dois momentos: na hora de entregar o objeto ao amigo e, dias depois, quando um aviso chega ou quando quer saber o que ainda está fora. Há um dono por aparelho, sem login.

O **amigo** não usa o app: é um contato cadastrado pelo dono.

Segundo público: o **professor** que avalia o protótipo. Abre o arquivo no computador com a spec ao lado e executa os critérios de aceite, avançando a data simulada.

## Product Purpose

Registrar o que foi emprestado, para quem e até quando, e ajudar o dono a acompanhar devoluções. O dono deve saber a qualquer momento o que está fora, o que está atrasado e como cada amigo costuma devolver.

## Positioning

O app parece simples de propósito; a diferença está nas regras: prazo que vence no fim do dia, tolerância de 3 dias antes do atraso, uma única renovação, estado de conservação comparado na saída e na volta, perda, alerta ao emprestar para quem já atrasou, perdeu ou danificou. O amigo não precisa instalar nada.

## Operating Context

- Fonte única das regras: `spec.md` (revisão de 12/11/2026). O design não cria nem altera regra de negócio.
- Alcance atual: só o MVP da seção 2.3 da spec. O que está marcado `[v2]` (itens e devolução parcial, reaparecimento, sugestão automática de bloqueio, backup) entra depois, se o MVP for aprovado.
- Fluxos: cadastrar objeto e amigo; registrar, renovar e encerrar empréstimo; devolução com estado, dano, desfecho e contestação; perda; listas Ativos, Atrasados e Encerrados; avisos de prazo e de atraso; históricos de objeto e de amigo; bloqueio manual; arquivar, excluir; remover dados pessoais (LGPD).
- O protótipo precisa de um controle visível de data simulada (voltar um dia, avançar um dia, avançar sete dias, voltar para hoje) e de dados de exemplo.

## Capabilities and Constraints

- Mensagens de erro, de validação e de confirmação usam o texto exato dos critérios de aceite da spec.
- Estados do empréstimo no MVP: Ativo, Atrasado, Devolvido, Perdido; "Em tolerância" é rótulo, não estado.
- Estado de conservação: Novo, Bom, Desgastado, Danificado, Inutilizado.
- Estado e rótulos sempre escritos por extenso, nunca indicados só por cor.
- Rastreabilidade obrigatória no código: comentários `// RN` e `// HU`; comportamento que a spec não define é marcado `// LACUNA` e listado no topo do arquivo.
- Funciona em tela de computador e de celular.
- Código simples, legível para um aluno iniciante.

## Brand Commitments

- Nome: Empresta Aí.
- Idioma: português do Brasil; datas em dd/mm/aaaa.
- O dono pediu base em sistemas de design conhecidos, de interface fluida, com animações que melhorem a sensação de uso, e com as funcionalidades bem apresentadas. Rejeitou o protótipo anterior por ter "design fraco, nada convidativo e usual".

## Evidence on Hand

- `spec.md`: 77 regras de negócio, 14 histórias com cenários, tabela de estados, modelo de dados e casos de borda.
- Sem logotipo, sem fotos reais de objetos e sem identidade visual anterior. Os dados de exemplo do protótipo são fictícios e usam os nomes dos cenários da spec.

## Product Principles

1. As regras da spec mandam; o design as torna visíveis e fáceis de testar.
2. O que está fora e o que está atrasado aparecem primeiro.
3. Toda ação bloqueada por uma regra mostra o motivo, com o texto da spec.
4. O dono decide; o app avisa e sugere, mas não age sozinho.

## Accessibility & Inclusion

Contraste mínimo de 4,5:1 (WCAG 2.1 AA); texto que respeita o tamanho de fonte do sistema até 200%; área de toque mínima de 44×44 pt; rótulos lidos por leitor de tela; animações reduzidas quando o sistema pede menos movimento.
