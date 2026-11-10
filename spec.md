# Empresta Aí — Especificação v1

Aluno: Jhonne Jefferson | Data: 03/11/2026

## 1. Visão

Quem empresta livros, ferramentas e jogos costuma esquecer o que emprestou, para quem e até quando, e muitos objetos nunca voltam. O Empresta Aí é um app de celular para essa pessoa (o **dono**) registrar cada empréstimo com prazo, estado do objeto na saída e na volta, e receber avisos antes do vencimento e durante o atraso. O amigo que pega o objeto não precisa instalar nada: é um contato cadastrado pelo dono. O resultado esperado é que o dono saiba a qualquer momento o que está fora, o que está atrasado e como cada amigo costuma devolver.

## 2. Escopo

### 2.1 Dentro do escopo

1. Cadastrar objetos (nome, categoria, foto opcional, estado de conservação e, opcionalmente, itens que compõem o objeto).
2. Cadastrar amigos (contatos do dono).
3. Registrar um empréstimo com data de saída e prazo de devolução, ou sem prazo.
4. Renovar um empréstimo.
5. Registrar a devolução, completa ou parcial, com o estado em que o objeto voltou, o desfecho do dano e a contestação do amigo.
6. Registrar perda e reaparecimento.
7. Listar empréstimos ativos, atrasados e encerrados.
8. Avisar o dono sobre prazos próximos e atrasos, por notificação local.
9. Consultar o histórico de um objeto e de um amigo.
10. Bloquear amigo, arquivar e excluir cadastros, e remover dados pessoais do amigo (LGPD).
11. Exportar e importar backup em arquivo.

### 2.2 Fora do escopo

O app **não** faz:

- cobrança ou pagamento real de multas e indenizações (o desfecho do dano é só um registro de texto fixo, sem valores);
- marketplace público de objetos entre desconhecidos;
- chat entre usuários, nem envio de mensagens (SMS, WhatsApp, e-mail) ao amigo;
- rastreamento por geolocalização;
- conta, login, servidor ou sincronização entre aparelhos;
- acesso do amigo ao app;
- importação automática da agenda de contatos do celular.

## 3. Glossário

Cada termo tem este sentido único em toda a spec.

- **Dono:** a pessoa que usa o app e empresta os objetos. Há um dono por aparelho.
- **Amigo:** contato cadastrado pelo dono, a quem um objeto é emprestado. É o "tomador" do enunciado. Não usa o app.
- **Objeto:** coisa do dono que pode ser emprestada.
- **Item:** parte de um objeto que é devolvida separadamente (ex.: o objeto "PlayStation 5" com os itens "Console", "Controle 1", "Controle 2").
- **Estado de conservação:** um valor da escala ordenada Novo (1), Bom (2), Desgastado (3), Danificado (4), Inutilizado (5). Quanto maior o número, pior.
- **Empréstimo:** o registro de que um objeto saiu para um amigo numa data.
- **Data de saída:** dia em que o objeto foi entregue ao amigo.
- **Data prevista:** último dia do prazo de devolução. O prazo vence às 23:59:59 desse dia.
- **Empréstimo sem prazo:** empréstimo sem data prevista.
- **Hoje:** a data do calendário do aparelho, no fuso horário configurado nele no momento da verificação.
- **Em tolerância:** rótulo de um empréstimo Ativo ou Devolvido parcialmente entre o 1º e o 3º dia depois da data prevista. Não é um estado.
- **Ativo:** estado do empréstimo que está com o amigo e não está Atrasado nem Devolvido parcialmente: sem prazo, dentro do prazo ou em tolerância.
- **Atrasado:** estado do empréstimo que não foi devolvido por completo até 00:00 do 4º dia depois da data prevista.
- **Dias de atraso:** hoje − (data prevista + 3). No 4º dia depois da data prevista, vale 1.
- **Devolvido parcialmente:** estado do empréstimo em que parte dos itens voltou e o empréstimo ainda não está Atrasado.
- **Devolvido:** estado final do empréstimo em que todos os itens (ou o objeto sem itens) voltaram.
- **Perdido:** estado que encerra o empréstimo cujo objeto (ou cujos itens pendentes) o dono declarou que não vão voltar. Só sai dele por "Objeto reapareceu" (RN50).
- **Em aberto:** empréstimo nos estados Ativo, Atrasado ou Devolvido parcialmente.
- **Encerrado:** empréstimo nos estados Devolvido ou Perdido.
- **Situação do objeto:** Disponível, Emprestado ou Perdido, calculada pela RN08. É diferente do estado do empréstimo.
- **Disponível:** situação do objeto que está com o dono; pode ser emprestado se cumprir a RN09.
- **Emprestado:** situação do objeto que tem empréstimo em aberto.
- **Perdido (situação do objeto):** o último empréstimo do objeto está Perdido e nenhum item voltou nele (RN08).
- **Renovação:** adiamento da data prevista registrado pelo dono.
- **Dano:** quando o estado de conservação na volta tem número maior que o estado na saída.
- **Desfecho do dano:** registro do que foi combinado depois de um dano: Sem ação, Reparo combinado, Reposição combinada ou Resolvido.
- **Contestação:** texto em que o dono registra que o amigo discorda do dano.
- **Bloqueado:** amigo que o dono marcou para não receber novos empréstimos.
- **Arquivado:** objeto ou amigo que sai das listas de cadastro e da seleção de novo empréstimo, mas continua no histórico.
- **Anonimizado:** amigo cujos dados pessoais foram removidos a pedido (LGPD).
- **Aviso:** notificação local do celular gerada pelo app.

## 4. Atores e permissões

| Ator | Pode fazer | Não pode fazer |
|---|---|---|
| Dono | Tudo o que está no escopo 2.1: cadastrar, editar, arquivar e excluir objetos e amigos; registrar, renovar, devolver e marcar perda de empréstimos; registrar desfecho e contestação; bloquear amigos; remover dados pessoais; silenciar avisos; exportar e importar backup | Burlar as regras da seção 5 (ex.: renovar empréstimo Atrasado, excluir amigo com histórico) |
| Amigo | Nada no app. Fora do app, pede ao dono renovação, contestação ou remoção dos seus dados, e o dono registra | Acessar o app, ver ou alterar dados |
| Sistema (o app) | Calcular estados e rótulos pela data; enviar avisos; sugerir perda, bloqueio de amigo e arquivamento de objeto | Mudar estado de empréstimo por conta própria, exceto Ativo/Devolvido parcialmente → Atrasado pela data (RN22); bloquear amigo ou marcar perda sozinho |

## 5. Regras de negócio

### 5.1 Gerais

- **RN01** — O app tem um único dono por aparelho e não exige login.
- **RN02** — Todas as datas do app são datas de calendário (dd/mm/aaaa), sem hora. "Hoje" é a data do aparelho no fuso configurado nele no momento da verificação. Num registro, vale o "hoje" do momento em que o dono toca em salvar.
- **RN03** — Textos digitados têm os espaços do começo e do fim removidos antes da validação e da gravação.
- **RN73** — Nos textos do app, quando o número é 1 a palavra vai no singular ("1 dia", "1 item", "Falta 1 item", "1 empréstimo atrasado"); nos demais números, no plural.
- **RN74** — Mensagens de validação, quando a regra não define outra: campo obrigatório vazio → "Informe <o campo>" (ex.: "Informe a categoria"); texto acima do limite → "<O campo> pode ter no máximo <N> caracteres"; número vazio, não inteiro ou fora da faixa → "<O campo> deve ser de <A> a <B> dias"; foto inválida → "A foto deve ser JPG ou PNG de até 10 MB". Campos de escolha (categoria, estado) começam vazios. Calendários só deixam escolher datas válidas pelas regras do campo. Nada é salvo enquanto houver erro. As mensagens específicas escritas nos critérios de aceite da seção 6 fazem parte das regras citadas em cada HU e devem ser usadas com o texto exato.

### 5.2 Objetos

- **RN04** — Um objeto tem nome (obrigatório, 1 a 60 caracteres), categoria (obrigatória, da RN06), estado de conservação (obrigatório, da RN07), foto (opcional, no máximo 1, JPG ou PNG, até 10 MB, da câmera ou da galeria) e itens (opcional, RN05). Nome, categoria e foto podem ser editados a qualquer momento; mudar a categoria não altera empréstimos já registrados. O estado de conservação só pode ser editado à mão quando o objeto não tem empréstimo em aberto.
- **RN05** — Se o objeto tiver itens, são de 2 a 20, cada um com nome de 1 a 40 caracteres, e os nomes não se repetem dentro do mesmo objeto (sem diferenciar maiúsculas e minúsculas). Os itens só podem ser incluídos, renomeados ou removidos quando o objeto não tem empréstimo em aberto. Item que já participou de algum empréstimo não pode ser removido, só renomeado. Itens inativos (perdidos, RN49) não contam para o mínimo de 2 nem para o máximo de 20, aparecem na edição com o rótulo "Perdido" e podem ser reativados pelo dono quando o objeto não tem empréstimo em aberto; o nome deles continua contando na regra de nomes diferentes. O mínimo de 2 itens vale ao cadastrar o objeto e ao incluir ou remover itens; depois de uma perda (RN49), o objeto pode ficar com 1 item ativo.
- **RN06** — As categorias são fixas, e cada uma tem um prazo padrão em dias:

  | Categoria | Prazo padrão |
  |---|---|
  | Livros e revistas | 14 |
  | Jogos | 14 |
  | Filmes e música | 7 |
  | Ferramentas | 7 |
  | Eletrônicos | 7 |
  | Roupas e acessórios | 7 |
  | Esporte e lazer | 14 |
  | Casa e cozinha | 7 |
  | Outros | 14 |

- **RN07** — A escala de estado de conservação é Novo (1), Bom (2), Desgastado (3), Danificado (4), Inutilizado (5).
- **RN08** — A situação do objeto é calculada assim: **Emprestado** se tem empréstimo em aberto; **Perdido** se o último empréstimo (o criado mais recentemente) está Perdido e nenhum item voltou nele (RN49); **Disponível** nos demais casos. Arquivado é uma marca à parte (RN58). Um objeto Perdido só volta a Disponível por "Objeto reapareceu" (RN50).
- **RN09** — Um objeto só pode ser escolhido para novo empréstimo se estiver Disponível, não arquivado e com estado de conservação diferente de Inutilizado.
- **RN10** — Um objeto tem no máximo um empréstimo em aberto por vez.
- **RN11** — Nomes de objeto repetidos são permitidos.

### 5.3 Amigos

- **RN12** — Um amigo tem nome (obrigatório, 1 a 60 caracteres), telefone (opcional, só dígitos, 10 ou 11 dígitos com DDD) e observação (opcional, até 200 caracteres). Antes de validar o telefone, o app remove espaços, parênteses e hífens.
- **RN13** — Ao salvar um amigo com o mesmo nome de outro amigo não anonimizado (sem diferenciar maiúsculas, minúsculas e acentos), no cadastro ou na edição, o app mostra "Já existe um amigo chamado <nome>. Salvar mesmo assim?" com os botões "Salvar" e "Cancelar".
- **RN14** — Um amigo só pode ser escolhido para novo empréstimo se não estiver bloqueado, arquivado nem anonimizado.

### 5.4 Prazo

- **RN15** — A data de saída é hoje ou até 30 dias antes de hoje, nunca uma data futura. O valor inicial do campo é hoje.
- **RN16** — O dono informa o prazo de um destes modos: (a) em dias, de 1 a 90, e a data prevista é a data de saída + N dias; (b) escolhendo a data prevista no calendário, entre a data de saída + 1 e a data de saída + 90; (c) marcando "Sem prazo". O app grava sempre a data prevista, ou nenhuma no caso (c). Se o dono muda a data de saída depois de preencher o prazo, a data prevista é recalculada pelos dias informados (modo a) ou mantida (modo b); se a data mantida ficar fora da faixa do modo (b) ou antes de hoje, o campo é apagado e o dono escolhe outra data.
- **RN17** — O campo de prazo vem preenchido com o prazo padrão da categoria do objeto (RN06), em dias.
- **RN18** — A data prevista de um novo empréstimo tem que ser maior ou igual a hoje. Se não for, o app mostra "A data de devolução já passou. Escolha um prazo maior." e não salva.
- **RN19** — Um empréstimo sem prazo nunca fica Atrasado e não pode ser renovado.
- **RN20** — O dono pode, uma única vez, definir um prazo para um empréstimo sem prazo em aberto: de 1 a 90 dias contados a partir de hoje. Isso não conta como renovação, não gera registro de Renovacao e cancela os lembretes de 30 dias (RN28). Depois disso, o empréstimo segue todas as regras de empréstimo com prazo.
- **RN21** — O prazo vence às 23:59:59 da data prevista.

### 5.5 Atraso

- **RN22** — Um empréstimo Ativo ou Devolvido parcialmente com data prevista passa a Atrasado às 00:00 do dia data prevista + 4. A mudança vale pela data, mesmo com o app fechado, e aparece da próxima vez que o app mostrar o empréstimo. O app grava data_atrasado = data prevista + 4. Um empréstimo Atrasado nunca volta a Ativo ou Devolvido parcialmente, nem se o fuso do aparelho mudar.
- **RN23** — Do dia data prevista + 1 ao dia data prevista + 3, o empréstimo continua no seu estado e mostra o rótulo "Em tolerância — fica atrasado em N dias" (com a RN73), em que N = (data prevista + 4) − hoje.
- **RN24** — Um amigo com empréstimo Atrasado não fica impedido de receber outro empréstimo. Ao escolher esse amigo num novo empréstimo, o app mostra "<nome> tem <Q> empréstimos atrasados: <objeto> (<D> dias de atraso), <objeto> (<D> dias de atraso). Emprestar mesmo assim?" com os botões "Emprestar" e "Cancelar". D são os dias de atraso do glossário; os objetos vêm em ordem decrescente de D, separados por vírgula; vale a RN73.
- **RN25** — Em um empréstimo Atrasado com 60 ou mais dias de atraso, a tela do empréstimo mostra a faixa "Atrasado há <D> dias. Marcar como perdido?" com o botão "Marcar como perdido". O app nunca marca perda sozinho.

### 5.6 Avisos

- **RN26** — Para empréstimo com data prevista, o app envia um aviso às 09:00 do dia data prevista − 2 ("<objeto> com <amigo>: devolução em 2 dias (<data>).") e outro às 09:00 da data prevista ("<objeto> com <amigo>: devolução hoje.").
- **RN27** — Para empréstimo Atrasado, o app envia um aviso às 09:00 do 1º dia de atraso e, depois, a cada 7 dias (1º, 8º, 15º... dia de atraso): "<objeto> com <amigo>: <D> dias de atraso." Não há aviso nos dias de tolerância.
- **RN28** — Para empréstimo sem prazo em aberto, o app envia um lembrete às 09:00 a cada 30 dias depois da data de saída: "<objeto> está com <amigo> há <N> dias."
- **RN29** — Avisos com horário anterior ao momento do agendamento não são enviados. Os avisos são agendados ao criar o empréstimo e reagendados a cada renovação, definição de prazo, reativação de avisos ou importação de backup.
- **RN30** — Os avisos de um empréstimo param quando ele é encerrado, quando é excluído (RN75) ou quando o dono o silencia. O dono pode silenciar e reativar os avisos de cada empréstimo a qualquer momento; ao reativar, só os avisos futuros da mesma série são agendados (os de atraso continuam no 1º, 8º, 15º... dia). Renovar não desfaz o silenciamento.
- **RN31** — Se a permissão de notificação do celular estiver negada, a tela inicial mostra a faixa "Avisos desligados. Ative as notificações nas configurações do celular." As listas, os estados e a faixa da RN25 continuam funcionando; só as notificações deixam de chegar.

### 5.7 Renovação

- **RN32** — Só o dono registra a renovação, que pode ser a pedido do amigo.
- **RN33** — A renovação é permitida em empréstimo com data prevista nos estados Ativo (inclusive em tolerância) e Devolvido parcialmente. É proibida em Atrasado, em empréstimo encerrado e em empréstimo sem prazo.
- **RN34** — Um empréstimo pode ser renovado no máximo 2 vezes. Depois da 2ª, a opção "Renovar" não aparece.
- **RN35** — Cada renovação soma de 1 a 30 dias à data prevista anterior.
- **RN36** — A nova data prevista tem que ser maior ou igual a hoje. Se não for, o app mostra "A nova data (<data>) já passou. Renove por pelo menos <N> dias." e não salva, em que N = hoje − data prevista anterior.
- **RN37** — O limite de 90 dias da RN16 vale só para o prazo inicial; a data prevista pode passar de 90 dias por causa de renovações.

### 5.8 Devolução e dano

- **RN38** — Ao registrar um empréstimo, o dono informa o estado de conservação na saída, que não pode ser Inutilizado (a opção não aparece). O campo vem com o estado atual do objeto; se o dono mudar, o estado atual do objeto também muda. Foto de saída é opcional (mesmas regras da RN04).
- **RN39** — A devolução é registrada sempre com a data de hoje.
- **RN40** — Em toda devolução, parcial ou completa, o dono informa o estado de conservação do objeto naquele momento (obrigatório) e pode anexar uma foto (opcional). Esse estado passa a ser o estado atual do objeto.
- **RN41** — Objeto com itens: ao registrar o empréstimo, todos os itens ativos do objeto saem com ele (não há escolha de itens) e o app os guarda em ItemEmprestado. Na devolução, o dono marca quais itens pendentes voltaram (pelo menos 1). Se ainda faltar algum, a devolução é parcial. Objeto sem itens: a devolução é sempre completa.
- **RN42** — Devolução parcial em empréstimo Ativo leva a Devolvido parcialmente. Devolução parcial em empréstimo Atrasado mantém Atrasado. A devolução que faz voltar o último item pendente leva a Devolvido.
- **RN43** — Um empréstimo Devolvido parcialmente continua contando prazo, tolerância, atraso e avisos (RN21 a RN30). A tela do empréstimo lista os itens pendentes, e os avisos das RN26 e RN27 terminam com " Itens pendentes: <item>, <item>."
- **RN44** — O estado na volta é o informado na última devolução registrada no empréstimo (a de criado_em mais recente). Há dano quando o número do estado na volta é maior que o do estado na saída (RN07).
- **RN45** — O dano é avaliado só na devolução que leva o empréstimo a Devolvido (completa ou por reaparecimento). Se houver dano, o dono escolhe nessa hora um desfecho (obrigatório): Sem ação, Reparo combinado ou Reposição combinada. Depois, só em empréstimo Devolvido, pode trocá-lo por qualquer um dos quatro, inclusive Resolvido; o desfecho nunca fica vazio. Devoluções parciais e empréstimos Perdidos não têm desfecho. O app não registra valores em dinheiro.
- **RN46** — O dono pode registrar uma contestação, só em empréstimo Devolvido com dano: texto de 1 a 500 caracteres, no máximo uma por empréstimo, editável. A contestação não altera o estado na volta, o dano nem o desfecho.
- **RN47** — Quando a devolução que leva o empréstimo a Devolvido tem estado Inutilizado e o objeto não está arquivado, o app mostra "<objeto> voltou inutilizado. Arquivar?" com os botões "Arquivar" e "Agora não".

### 5.9 Perda e reaparecimento

- **RN48** — O dono pode marcar como Perdido um empréstimo em aberto, a qualquer momento. O app pede confirmação ("Marcar <objeto> como perdido? Isso encerra o empréstimo.") e grava hoje como data da perda.
- **RN49** — Se nenhum item voltou no empréstimo perdido (ou se o objeto não tem itens), a situação do objeto passa a Perdido. Se algum item voltou, os itens pendentes ficam marcados como perdidos (inativos) e o objeto volta a Disponível só com os itens ativos.
- **RN50** — Num empréstimo Perdido, o dono pode registrar "Objeto reapareceu". O app registra uma devolução de hoje (RN39 e RN40) de todos os itens que estavam pendentes, sem o dono marcá-los, reativa esses itens, aplica a RN45 se houver dano, muda o empréstimo para Devolvido e mantém a data da perda no histórico com a marca "perda revertida".
- **RN51** — "Objeto reapareceu" é bloqueado se o objeto tiver outro empréstimo em aberto, com a mensagem "Registre antes a devolução do empréstimo em aberto deste objeto." Se o objeto estiver arquivado, ele é desarquivado.
- **RN52** — Ao marcar um objeto como Perdido, o app mostra "Arquivar <objeto>?" com os botões "Arquivar" e "Agora não" (só quando a situação dele passar a Perdido).

### 5.10 Bloqueio

- **RN53** — O dono pode bloquear e desbloquear um amigo a qualquer momento, com motivo opcional de até 200 caracteres. O motivo pode ser editado enquanto o amigo está bloqueado e é apagado ao desbloquear.
- **RN54** — O amigo bloqueado não aparece na seleção de novo empréstimo. Os empréstimos em aberto dele seguem todas as regras de prazo, avisos, devolução e perda.
- **RN55** — O app sugere bloquear um amigo não bloqueado, não arquivado e não anonimizado quando, contando apenas os últimos 365 dias até hoje, ele atinge **pelo menos um** destes critérios:
  - (a) 1 empréstimo Perdido, contado pela data da perda e sem perda revertida;
  - (b) 2 empréstimos Devolvidos com dano e estado na volta Danificado ou Inutilizado, contados pela data da devolução que encerrou o empréstimo, inclusive por reaparecimento;
  - (c) 3 empréstimos que ficaram Atrasados, contados pelo dia em que ficaram Atrasados (data_atrasado), mesmo que depois tenham sido devolvidos ou perdidos.
- **RN56** — A sugestão de bloqueio aparece logo depois do registro que fez um critério ser atingido (perda ou devolução), ou na próxima abertura do app depois de um empréstimo ficar Atrasado, com o texto "<nome> teve <motivo> nos últimos 12 meses. Bloquear novos empréstimos?" e os botões "Bloquear" e "Agora não". <motivo> é "<n> perdas", "<n> devoluções com dano grave" ou "<n> atrasos" (com a RN73: "1 perda"), com os critérios atingidos juntados por " e ", na ordem (a), (b), (c). Aparece no máximo uma sugestão por amigo de cada vez. Se o dono escolher "Agora não", o app grava sugestao_bloqueio_dispensada_em, e a sugestão só volta quando um evento registrado depois desse momento contar para algum critério.

### 5.11 Arquivamento e exclusão

- **RN57** — Objeto ou amigo sem nenhum empréstimo (em qualquer estado) pode ser excluído de vez, com confirmação ("Excluir <nome>? Isso não pode ser desfeito.").
- **RN58** — Objeto ou amigo com pelo menos um empréstimo não pode ser excluído; só arquivado, e só se não tiver empréstimo em aberto. A opção "Excluir" não aparece nesse caso.
- **RN59** — Objeto ou amigo com empréstimo em aberto não pode ser arquivado; o app mostra "Encerre os empréstimos em aberto antes de arquivar."
- **RN60** — O cadastro arquivado sai das listas de objetos e amigos e da seleção de novo empréstimo, e aparece no filtro "Arquivados" e nos históricos. Pode ser desarquivado a qualquer momento, exceto amigo anonimizado.
- **RN75** — Um empréstimo registrado por engano pode ser excluído enquanto estiver Ativo, sem renovação nem devolução, até as 23:59:59 do dia em que foi criado. Depois disso, os dados do registro (objeto, amigo, data de saída, estado e foto de saída) não podem ser editados nem excluídos. Ao excluir, os avisos agendados desse empréstimo são cancelados e o estado atual do objeto volta ao que era antes do registro (estado_objeto_antes).

### 5.12 Dados pessoais (LGPD)

- **RN61** — Do amigo, o app guarda só os dados da RN12, com a finalidade de o dono identificar quem está com cada objeto e como contatá-lo. A observação mostra o texto de ajuda "Não escreva dados sensíveis (saúde, religião, documentos)."
- **RN62** — Os dados ficam só no aparelho do dono e não são enviados a nenhum servidor.
- **RN63** — O amigo pede a remoção dos seus dados ao dono, fora do app. O dono usa "Remover dados pessoais" na ficha do amigo.
- **RN64** — "Remover dados pessoais" só é permitido se o amigo não tiver empréstimo em aberto; se tiver, o app mostra "Encerre os empréstimos de <nome> antes de remover os dados." Se o amigo não tiver nenhum empréstimo, a opção exclui o cadastro (RN57).
- **RN65** — A remoção pede confirmação ("Os dados de <nome> serão apagados e não podem ser recuperados. Continuar?") e então: troca o nome por "Contato removido #N", em que N é o contador_anonimizacao + 1 (começa em 1, só avança quando a remoção é confirmada, nunca se repete dentro dos dados do app e vai junto no backup); apaga telefone, observação e motivo de bloqueio; apaga as contestações dos empréstimos desse amigo; marca o amigo como anonimizado e arquivado. Os empréstimos continuam no histórico dos objetos.
- **RN66** — Amigo anonimizado não pode ser editado, desarquivado nem desbloqueado, e não entra na verificação de nome repetido (RN13).

### 5.13 Listas e histórico

- **RN67** — A lista **Ativos** mostra os empréstimos Ativos e Devolvidos parcialmente, em ordem crescente de data prevista; os sem prazo vêm por último, por data de saída crescente. A lista **Atrasados** mostra os Atrasados, por dias de atraso decrescentes. A lista **Encerrados** mostra os Devolvidos e Perdidos, por data de encerramento decrescente. Em empate, a ordem é alfabética pelo nome do objeto.
- **RN68** — Cada linha das listas mostra: nome do objeto, nome do amigo, data prevista (ou "Sem prazo") e, quando houver, os rótulos nesta ordem, separados por " · ": "Em tolerância — fica atrasado em N dias" (texto da RN23) ou "<D> dias de atraso"; "Faltam <Q> itens"; "Perdido"; "Com dano" (este só em empréstimo Devolvido). Vale a RN73.
- **RN69** — O histórico de um objeto lista todos os seus empréstimos, do mais recente para o mais antigo, com: amigo, data de saída, data prevista e renovações, devoluções (data, itens, estado, foto), itens perdidos, estado na saída e na volta, dano, desfecho, contestação e perda, escrita "Perda em <data>" ou, se revertida, "Perda em <data> (perda revertida)".
- **RN70** — O histórico de um amigo lista os empréstimos dele no mesmo formato da RN69, mais um resumo: total de empréstimos, quantos ficaram Atrasados, quantos voltaram com dano e quantos foram Perdidos (sem perdas revertidas). O resumo conta todo o histórico, sem a janela de 12 meses da RN55, mostra também as parcelas com zero e segue o formato "<T> empréstimos · <A> atrasados · <D> com dano · <P> perdidos" (com a RN73).

### 5.14 Backup

- **RN71** — "Exportar backup" gera o arquivo `empresta-ai-backup-aaaa-mm-dd.zip` com todos os dados e fotos, salvo onde o dono escolher. Antes de exportar, o app mostra "O backup contém dados pessoais dos seus amigos. Guarde-o em local seguro." O arquivo não tem senha nem criptografia e inclui a Configuracao e o silenciamento dos avisos.
- **RN72** — "Importar backup" substitui todos os dados atuais pelos do arquivo, depois da confirmação "Os dados atuais serão substituídos. Continuar?". Se o arquivo não for um backup válido do Empresta Aí, o app mostra "Arquivo de backup inválido." e nada muda. Um backup é válido quando é um .zip gerado pela RN71, contém o arquivo `dados.json` (com o campo `versao_formato`, que nesta especificação vale 1) e a pasta `fotos/` com todas as fotos citadas nele. Se a versão do formato for mais nova que a do app, a mensagem é "Este backup é de uma versão mais nova do app. Atualize o app para importar." e nada muda. Depois de importar, os avisos são reagendados (RN29).

## 6. Histórias de usuário

Nos cenários, as datas são exemplos concretos; "hoje é 10/03/2027" fixa o dia de referência.

### HU01 — Cadastrar objeto

Como dono, quero cadastrar um objeto, para poder emprestá-lo depois.
Regras relacionadas: RN03, RN04, RN05, RN06, RN07, RN08, RN11, RN74

Critérios de aceite:
- Cenário: cadastro válido
  Dado que estou na tela de novo objeto
  Quando informo o nome "Furadeira Bosch", a categoria "Ferramentas" e o estado "Bom" e salvo
  Então o objeto aparece na lista de objetos com a situação "Disponível"
- Cenário: nome vazio
  Dado que estou na tela de novo objeto
  Quando informo só espaços no nome e tento salvar
  Então vejo a mensagem "Informe o nome do objeto" e nada é salvo
- Cenário: nome longo demais
  Dado que estou na tela de novo objeto
  Quando informo um nome com 61 caracteres e tento salvar
  Então vejo a mensagem "O nome pode ter no máximo 60 caracteres" e nada é salvo
- Cenário: objeto com itens
  Dado que estou na tela de novo objeto
  Quando informo o nome "PlayStation 5", a categoria "Jogos", o estado "Novo" e os itens "Console", "Controle 1" e "Controle 2" e salvo
  Então o objeto é salvo com 3 itens
- Cenário: apenas um item
  Dado que estou na tela de novo objeto
  Quando informo apenas o item "Console" e tento salvar
  Então vejo a mensagem "Um objeto com itens precisa de pelo menos 2 itens" e nada é salvo
- Cenário: itens repetidos
  Dado que estou na tela de novo objeto
  Quando informo os itens "Controle" e "controle" e tento salvar
  Então vejo a mensagem "Os itens precisam ter nomes diferentes" e nada é salvo
- Cenário: foto grande demais
  Dado que estou na tela de novo objeto
  Quando escolho uma foto de 12 MB
  Então vejo a mensagem "A foto deve ser JPG ou PNG de até 10 MB" e a foto não é anexada

### HU02 — Cadastrar amigo

Como dono, quero cadastrar um amigo, para registrar a quem emprestei.
Regras relacionadas: RN03, RN12, RN13, RN61, RN74

Critérios de aceite:
- Cenário: cadastro só com nome
  Dado que estou na tela de novo amigo
  Quando informo o nome "Carla Souza" e salvo
  Então "Carla Souza" aparece na lista de amigos
- Cenário: telefone inválido
  Dado que estou na tela de novo amigo
  Quando informo o nome "Carla Souza" e o telefone "8699999" e tento salvar
  Então vejo a mensagem "Telefone deve ter 10 ou 11 dígitos com DDD" e nada é salvo
- Cenário: nome repetido
  Dado que já existe o amigo "Carla Souza"
  Quando cadastro outro amigo com o nome "carla souza"
  Então vejo "Já existe um amigo chamado carla souza. Salvar mesmo assim?"
  E, ao tocar em "Salvar", os dois amigos ficam na lista
- Cenário: observação longa demais
  Dado que estou na tela de novo amigo
  Quando informo uma observação com 201 caracteres e tento salvar
  Então vejo a mensagem "A observação pode ter no máximo 200 caracteres" e nada é salvo

### HU03 — Registrar empréstimo

Como dono, quero registrar que emprestei um objeto, para saber com quem ele está e até quando.
Regras relacionadas: RN08, RN09, RN10, RN14, RN15, RN16, RN17, RN18, RN19, RN24, RN38, RN73, RN74

Critérios de aceite:
- Cenário: prazo padrão da categoria
  Dado que hoje é 10/03/2027 e a "Furadeira Bosch" (Ferramentas, Bom) está Disponível
  Quando registro o empréstimo para "Carla Souza" sem alterar o prazo
  Então o empréstimo fica Ativo, com data de saída 10/03/2027, data prevista 17/03/2027 e estado na saída "Bom"
  E a furadeira passa à situação "Emprestado"
- Cenário: prazo por data
  Dado que hoje é 10/03/2027
  Quando escolho no calendário a data prevista 01/04/2027
  Então o empréstimo é salvo com data prevista 01/04/2027
- Cenário: prazo acima do máximo
  Dado que estou registrando um empréstimo com saída em 10/03/2027
  Quando informo 91 dias
  Então vejo a mensagem "O prazo deve ser de 1 a 90 dias" e nada é salvo
- Cenário: sem prazo
  Dado que estou registrando um empréstimo
  Quando marco "Sem prazo" e salvo
  Então o empréstimo fica Ativo e aparece na lista Ativos com "Sem prazo"
- Cenário: saída retroativa com prazo já vencido
  Dado que hoje é 10/03/2027
  Quando informo a data de saída 01/03/2027 e o prazo de 7 dias (data prevista 08/03/2027)
  Então vejo "A data de devolução já passou. Escolha um prazo maior." e nada é salvo
- Cenário: saída no futuro
  Dado que hoje é 10/03/2027
  Quando abro o calendário da data de saída
  Então 11/03/2027 e as datas seguintes não podem ser escolhidas
- Cenário: objeto já emprestado
  Dado que a "Furadeira Bosch" tem um empréstimo Ativo
  Quando abro a seleção de objetos de um novo empréstimo
  Então a "Furadeira Bosch" não aparece na seleção
- Cenário: amigo com atraso
  Dado que "Carla Souza" tem o empréstimo da "Serra Tico-Tico" Atrasado há 5 dias
  Quando escolho "Carla Souza" num novo empréstimo
  Então vejo "Carla Souza tem 1 empréstimo atrasado: Serra Tico-Tico (5 dias de atraso). Emprestar mesmo assim?"
  E, ao tocar em "Emprestar", sigo para salvar o empréstimo; ao tocar em "Cancelar", volto à seleção de amigo
- Cenário: estado de saída diferente do cadastro
  Dado que a "Furadeira Bosch" está cadastrada como "Bom"
  Quando registro o empréstimo com estado na saída "Desgastado"
  Então o estado atual da furadeira passa a "Desgastado"
- Cenário: objeto inutilizado
  Dado que o "Liquidificador" está com estado "Inutilizado"
  Quando abro a seleção de objetos de um novo empréstimo
  Então o "Liquidificador" não aparece na seleção

### HU04 — Renovar empréstimo

Como dono, quero adiar a data prevista quando o amigo pede mais tempo, para que os avisos passem a usar a nova data.
Regras relacionadas: RN19, RN20, RN26, RN29, RN32, RN33, RN34, RN35, RN36, RN37, RN74

Critérios de aceite:
- Cenário: renovação a partir do prazo anterior
  Dado que hoje é 10/03/2027 e um empréstimo Ativo tem data prevista 15/03/2027 e 0 renovações
  Quando renovo por 10 dias
  Então a data prevista passa a 25/03/2027 e o contador mostra "1 de 2 renovações"
- Cenário: renovação em tolerância que ainda cai no passado
  Dado que hoje é 10/03/2027 e um empréstimo Ativo tem data prevista 08/03/2027 (em tolerância)
  Quando renovo por 1 dia
  Então vejo "A nova data (09/03/2027) já passou. Renove por pelo menos 2 dias." e nada é salvo
- Cenário: limite de renovações
  Dado que um empréstimo Ativo já foi renovado 2 vezes
  Quando abro a tela desse empréstimo
  Então a opção "Renovar" não aparece
- Cenário: empréstimo atrasado
  Dado que um empréstimo está Atrasado
  Quando abro a tela desse empréstimo
  Então a opção "Renovar" não aparece e vejo o texto "Empréstimo atrasado não pode ser renovado"
- Cenário: mais de 30 dias
  Dado que estou renovando um empréstimo Ativo
  Quando informo 31 dias
  Então vejo "A renovação deve ser de 1 a 30 dias" e nada é salvo
- Cenário: definir prazo num empréstimo sem prazo
  Dado que hoje é 10/03/2027 e há um empréstimo sem prazo Ativo
  Quando escolho "Definir prazo" e informo 14 dias
  Então a data prevista passa a 24/03/2027, o contador mostra "0 de 2 renovações" e a opção "Definir prazo" deixa de aparecer
- Cenário: avisos depois da renovação
  Dado que hoje é 10/03/2027 e o empréstimo Ativo da "Furadeira Bosch" com "Carla Souza" tem data prevista 15/03/2027
  Quando renovo por 10 dias
  Então os avisos de 13/03/2027 e 15/03/2027 são cancelados
  E os novos avisos ficam agendados para as 09:00 de 23/03/2027 e de 25/03/2027

### HU05 — Registrar devolução

Como dono, quero registrar a volta do objeto e o estado em que ele voltou, para comparar com a saída.
Regras relacionadas: RN08, RN22, RN39, RN40, RN41, RN42, RN43, RN44, RN45, RN47, RN68, RN73

Critérios de aceite:
- Cenário: devolução sem dano
  Dado que hoje é 10/03/2027 e a "Furadeira Bosch" saiu com estado "Bom" num empréstimo Ativo
  Quando registro a devolução com estado "Bom"
  Então o empréstimo fica Devolvido com data de encerramento 10/03/2027, sem dano, e a furadeira volta a "Disponível"
- Cenário: devolução com dano
  Dado que a "Furadeira Bosch" saiu com estado "Bom"
  Quando registro a devolução com estado "Danificado" e o desfecho "Reparo combinado"
  Então o empréstimo fica Devolvido, marcado "Com dano", com desfecho "Reparo combinado"
  E o estado atual da furadeira passa a "Danificado"
- Cenário: dano sem desfecho
  Dado que estou registrando uma devolução com estado pior que o da saída
  Quando tento concluir sem escolher o desfecho
  Então vejo "Escolha o desfecho do dano" e nada é salvo
- Cenário: devolução parcial
  Dado que o "PlayStation 5" (itens Console, Controle 1, Controle 2) está num empréstimo Ativo
  Quando registro a devolução marcando só "Console" e "Controle 1", com estado "Bom"
  Então o empréstimo fica Devolvido parcialmente e mostra "Falta 1 item: Controle 2"
- Cenário: devolução do item que faltava
  Dado que o empréstimo do "PlayStation 5" está Devolvido parcialmente faltando "Controle 2"
  Quando registro a devolução de "Controle 2" com estado "Bom"
  Então o empréstimo fica Devolvido
- Cenário: devolução parcial de empréstimo atrasado
  Dado que o empréstimo do "PlayStation 5" está Atrasado com os 3 itens pendentes
  Quando registro a devolução só do "Console"
  Então o empréstimo continua Atrasado e mostra "Faltam 2 itens: Controle 1, Controle 2"
- Cenário: parcial que fica atrasado
  Dado que o empréstimo do "PlayStation 5" está Devolvido parcialmente com data prevista 05/03/2027
  Quando chega 00:00 de 09/03/2027
  Então o empréstimo passa a Atrasado
- Cenário: devolução sem marcar item
  Dado que estou registrando a devolução de um objeto com itens
  Quando tento concluir sem marcar nenhum item
  Então vejo "Marque pelo menos um item devolvido" e nada é salvo
- Cenário: volta inutilizado
  Dado que estou registrando a devolução da "Furadeira Bosch"
  Quando informo o estado "Inutilizado" e o desfecho "Reposição combinada"
  Então vejo "Furadeira Bosch voltou inutilizado. Arquivar?"

### HU06 — Acompanhar desfecho e contestação do dano

Como dono, quero registrar o que combinei com o amigo e se ele discorda, para ter tudo no histórico.
Regras relacionadas: RN45, RN46, RN74

Critérios de aceite:
- Cenário: resolver o dano
  Dado um empréstimo Devolvido com dano e desfecho "Reparo combinado"
  Quando troco o desfecho para "Resolvido"
  Então o histórico mostra o desfecho "Resolvido"
- Cenário: registrar contestação
  Dado um empréstimo Devolvido com dano
  Quando registro a contestação "Carla diz que a broca já estava torta"
  Então a contestação aparece no histórico e o estado na volta continua "Danificado"
- Cenário: contestação sem dano
  Dado um empréstimo Devolvido sem dano
  Quando abro a tela desse empréstimo
  Então a opção "Registrar contestação" não aparece
- Cenário: contestação longa demais
  Dado um empréstimo Devolvido com dano
  Quando informo uma contestação com 501 caracteres
  Então vejo "A contestação pode ter no máximo 500 caracteres" e nada é salvo

### HU07 — Registrar perda e reaparecimento

Como dono, quero marcar um objeto como perdido e, se ele aparecer, registrar a volta.
Regras relacionadas: RN08, RN25, RN45, RN48, RN49, RN50, RN51, RN52, RN69

Critérios de aceite:
- Cenário: perda sem itens devolvidos
  Dado que hoje é 10/03/2027 e a "Serra Tico-Tico" está num empréstimo Atrasado
  Quando escolho "Marcar como perdido" e confirmo
  Então o empréstimo fica Perdido com data da perda 10/03/2027, a serra fica na situação "Perdido"
  E vejo "Arquivar Serra Tico-Tico?"
- Cenário: perda com itens devolvidos
  Dado que o empréstimo do "PlayStation 5" está Devolvido parcialmente faltando "Controle 2"
  Quando marco como perdido e confirmo
  Então o empréstimo fica Perdido, o item "Controle 2" fica inativo e o "PlayStation 5" volta a "Disponível" com os itens "Console" e "Controle 1"
- Cenário: sugestão aos 60 dias
  Dado que um empréstimo está Atrasado há 60 dias
  Quando abro a tela desse empréstimo
  Então vejo "Atrasado há 60 dias. Marcar como perdido?"
  E o empréstimo continua Atrasado até eu confirmar
- Cenário: objeto reapareceu
  Dado que hoje é 20/03/2027 e a "Serra Tico-Tico", que saiu com estado "Bom", está na situação "Perdido" (perda em 10/03/2027) e arquivada
  Quando escolho "Objeto reapareceu", informo o estado "Desgastado" e o desfecho "Sem ação"
  Então o empréstimo fica Devolvido, marcado "Com dano", com encerramento em 20/03/2027, e o histórico mostra "Perda em 10/03/2027 (perda revertida)"
  E a serra é desarquivada e fica "Disponível"
- Cenário: reapareceu com outro empréstimo em aberto
  Dado que o "PlayStation 5" tem um empréstimo antigo Perdido (Controle 2) e um empréstimo Ativo novo
  Quando escolho "Objeto reapareceu" no empréstimo antigo
  Então vejo "Registre antes a devolução do empréstimo em aberto deste objeto." e nada muda

### HU08 — Listar empréstimos

Como dono, quero ver os empréstimos separados em ativos, atrasados e encerrados, para saber o que cobrar.
Regras relacionadas: RN22, RN23, RN67, RN68, RN73

Critérios de aceite:
- Cenário: ordem dos ativos
  Dado empréstimos Ativos com datas previstas 20/03/2027, 12/03/2027 e um sem prazo
  Quando abro a lista Ativos
  Então vejo, nesta ordem: o de 12/03/2027, o de 20/03/2027 e o "Sem prazo"
- Cenário: rótulo de tolerância
  Dado que hoje é 10/03/2027 e um empréstimo Ativo tem data prevista 08/03/2027
  Quando abro a lista Ativos
  Então a linha mostra "Em tolerância — fica atrasado em 2 dias"
- Cenário: passagem para atrasado
  Dado que um empréstimo Ativo tem data prevista 08/03/2027
  Quando chega 00:00 de 12/03/2027
  Então ele sai da lista Ativos e aparece na lista Atrasados com "1 dia de atraso"
- Cenário: lista vazia
  Dado que não há empréstimos Atrasados
  Quando abro a lista Atrasados
  Então vejo "Nenhum empréstimo atrasado"
- Cenário: ordem dos encerrados
  Dado a "Furadeira Bosch" devolvida em 05/03/2027, a "Serra Tico-Tico" marcada como perdida em 08/03/2027 e o "Martelo" devolvido em 08/03/2027
  Quando abro a lista Encerrados
  Então vejo, nesta ordem: "Martelo", "Serra Tico-Tico" com o rótulo "Perdido" e "Furadeira Bosch"

### HU09 — Receber avisos

Como dono, quero ser avisado antes do prazo e durante o atraso, para não esquecer de cobrar.
Regras relacionadas: RN26, RN27, RN28, RN29, RN30, RN31, RN43, RN73

Critérios de aceite:
- Cenário: avisos antes do prazo
  Dado um empréstimo da "Furadeira Bosch" com "Carla Souza", data prevista 17/03/2027
  Quando chegam as 09:00 de 15/03/2027 e de 17/03/2027
  Então recebo "Furadeira Bosch com Carla Souza: devolução em 2 dias (17/03/2027)." e "Furadeira Bosch com Carla Souza: devolução hoje."
- Cenário: avisos de atraso
  Dado o mesmo empréstimo, não devolvido
  Quando chegam as 09:00 de 21/03/2027 e de 28/03/2027
  Então recebo "Furadeira Bosch com Carla Souza: 1 dia de atraso." e "Furadeira Bosch com Carla Souza: 8 dias de atraso."
  E não recebo aviso nos dias entre eles
- Cenário: aviso que já passou
  Dado que agora são 10:00 de 10/03/2027
  Quando registro um empréstimo com data prevista 12/03/2027
  Então o aviso de 10/03/2027 às 09:00 não é enviado e o aviso de 12/03/2027 às 09:00 é agendado
- Cenário: silenciar
  Dado um empréstimo Atrasado
  Quando silencio os avisos dele
  Então não recebo mais avisos desse empréstimo, e ele continua na lista Atrasados
- Cenário: devolução para os avisos
  Dado um empréstimo Atrasado
  Quando registro a devolução completa
  Então nenhum aviso desse empréstimo é enviado depois
- Cenário: lembrete sem prazo
  Dado um empréstimo sem prazo do "Livro Dom Casmurro" com "Pedro Lima", saída em 01/03/2027
  Quando chegam as 09:00 de 31/03/2027
  Então recebo "Livro Dom Casmurro está com Pedro Lima há 30 dias."
- Cenário: notificações negadas
  Dado que a permissão de notificação está negada
  Quando abro o app
  Então vejo a faixa "Avisos desligados. Ative as notificações nas configurações do celular."
- Cenário: reativar avisos
  Dado o empréstimo da "Furadeira Bosch" com "Carla Souza", data prevista 17/03/2027, silenciado em 21/03/2027 depois do aviso das 09:00
  Quando reativo os avisos em 25/03/2027
  Então recebo às 09:00 de 28/03/2027 "Furadeira Bosch com Carla Souza: 8 dias de atraso."
  E nenhum aviso dos dias em que estava silenciado é enviado
- Cenário: aviso com itens pendentes
  Dado o empréstimo do "PlayStation 5" com "Carla Souza", Devolvido parcialmente faltando "Controle 2", com data prevista 17/03/2027
  Quando chegam as 09:00 de 17/03/2027
  Então recebo "PlayStation 5 com Carla Souza: devolução hoje. Itens pendentes: Controle 2."

### HU10 — Consultar histórico

Como dono, quero ver o histórico de um objeto e de um amigo, para saber como foram os empréstimos.
Regras relacionadas: RN65, RN69, RN70, RN73

Critérios de aceite:
- Cenário: histórico do objeto
  Dado que a "Furadeira Bosch" foi emprestada a "Carla Souza" (devolvida com dano) e depois a "Pedro Lima" (Ativo)
  Quando abro o histórico da furadeira
  Então vejo primeiro o empréstimo de "Pedro Lima" e depois o de "Carla Souza", com estado na saída, estado na volta e desfecho
- Cenário: resumo do amigo
  Dado que "Carla Souza" tem 4 empréstimos: 1 que ficou Atrasado, 1 que voltou com dano e 1 Perdido
  Quando abro o histórico de "Carla Souza"
  Então o resumo mostra "4 empréstimos · 1 atrasado · 1 com dano · 1 perdido"
- Cenário: amigo anonimizado no histórico do objeto
  Dado que o amigo que pegou a furadeira foi anonimizado como "Contato removido #1"
  Quando abro o histórico da furadeira
  Então o empréstimo aparece com o amigo "Contato removido #1"

### HU11 — Bloquear amigo

Como dono, quero bloquear um amigo que não cuida das coisas, para não emprestar de novo sem perceber.
Regras relacionadas: RN14, RN26, RN53, RN54, RN55, RN56, RN73

Critérios de aceite:
- Cenário: bloqueio manual
  Dado o amigo "Pedro Lima" sem bloqueio
  Quando bloqueio com o motivo "Perdeu meu livro"
  Então "Pedro Lima" não aparece na seleção de novo empréstimo
- Cenário: empréstimos em aberto continuam
  Dado que "Pedro Lima" está bloqueado e tem um empréstimo Ativo
  Quando chega a data prevista
  Então o aviso de prazo da RN26 é enviado
- Cenário: sugestão por perda
  Dado que "Pedro Lima" não está bloqueado
  Quando marco um empréstimo dele como Perdido
  Então vejo "Pedro Lima teve 1 perda nos últimos 12 meses. Bloquear novos empréstimos?"
- Cenário: sugestão por atrasos
  Dado que "Carla Souza" teve 2 empréstimos que ficaram Atrasados em 2027
  Quando um 3º empréstimo dela fica Atrasado e eu abro o app
  Então vejo "Carla Souza teve 3 atrasos nos últimos 12 meses. Bloquear novos empréstimos?"
- Cenário: sugestão ignorada
  Dado que toquei "Agora não" na sugestão sobre "Carla Souza"
  Quando abro o app de novo sem nenhum novo evento dela
  Então a sugestão não aparece
- Cenário: evento antigo não conta
  Dado que hoje é 10/03/2027 e a única perda de "Pedro Lima" foi em 01/03/2026
  Quando registro uma devolução sem dano de "Pedro Lima"
  Então não vejo sugestão de bloqueio
- Cenário: desbloquear
  Dado que "Pedro Lima" está bloqueado
  Quando desbloqueio
  Então ele volta a aparecer na seleção de novo empréstimo

### HU12 — Arquivar e excluir

Como dono, quero tirar da lista o que não uso mais sem perder o histórico.
Regras relacionadas: RN08, RN57, RN58, RN59, RN60, RN75

Critérios de aceite:
- Cenário: excluir sem histórico
  Dado o objeto "Martelo" sem nenhum empréstimo
  Quando escolho "Excluir" e confirmo
  Então o "Martelo" deixa de existir no app
- Cenário: arquivar com histórico
  Dado a "Furadeira Bosch" com 2 empréstimos Devolvidos
  Quando abro as opções dela
  Então vejo "Arquivar" e não vejo "Excluir"
  E, ao arquivar, ela some da lista de objetos e aparece no filtro "Arquivados"
- Cenário: arquivar com empréstimo em aberto
  Dado que "Carla Souza" tem um empréstimo Ativo
  Quando escolho "Arquivar"
  Então vejo "Encerre os empréstimos em aberto antes de arquivar." e nada muda
- Cenário: desarquivar
  Dado que a "Furadeira Bosch" está arquivada
  Quando escolho "Desarquivar"
  Então ela volta à lista de objetos e à seleção de novo empréstimo
- Cenário: excluir empréstimo registrado por engano
  Dado que hoje é 10/03/2027 e registrei hoje um empréstimo Ativo da "Furadeira Bosch" para o amigo errado
  Quando escolho "Excluir empréstimo" e confirmo
  Então o empréstimo deixa de existir e a furadeira volta a "Disponível"
- Cenário: prazo para excluir empréstimo passou
  Dado que o empréstimo foi registrado em 09/03/2027 e hoje é 10/03/2027
  Quando abro a tela desse empréstimo
  Então a opção "Excluir empréstimo" não aparece

### HU13 — Remover dados pessoais do amigo

Como dono, quero apagar os dados de um amigo que pediu, para cumprir a LGPD sem estragar o histórico dos objetos.
Regras relacionadas: RN57, RN61, RN63, RN64, RN65, RN66, RN69

Critérios de aceite:
- Cenário: remoção com histórico
  Dado que "Pedro Lima" (telefone 86999998888) tem 2 empréstimos Devolvidos, um deles com contestação
  Quando escolho "Remover dados pessoais" e confirmo
  Então o amigo passa a se chamar "Contato removido #1", sem telefone, observação nem contestação, e fica arquivado
  E o histórico dos 2 objetos mostra "Contato removido #1"
- Cenário: remoção com empréstimo em aberto
  Dado que "Pedro Lima" tem um empréstimo Atrasado
  Quando escolho "Remover dados pessoais"
  Então vejo "Encerre os empréstimos de Pedro Lima antes de remover os dados." e nada muda
- Cenário: amigo sem empréstimos
  Dado que "Ana Reis" não tem nenhum empréstimo
  Quando escolho "Remover dados pessoais" e confirmo
  Então "Ana Reis" é excluída do app
- Cenário: segundo anonimizado
  Dado que já existe "Contato removido #1"
  Quando removo os dados de outro amigo com histórico
  Então ele passa a se chamar "Contato removido #2"
- Cenário: anonimizado não edita
  Dado o amigo "Contato removido #1"
  Quando abro a ficha dele
  Então não vejo as opções "Editar", "Desarquivar" nem "Desbloquear"

### HU14 — Fazer e restaurar backup

Como dono, quero salvar meus dados num arquivo, para não perder tudo se trocar ou perder o celular.
Regras relacionadas: RN29, RN71, RN72

Critérios de aceite:
- Cenário: exportar
  Dado que hoje é 10/03/2027
  Quando escolho "Exportar backup" e confirmo o aviso sobre dados pessoais
  Então é gerado o arquivo "empresta-ai-backup-2027-03-10.zip" no local que eu escolher
- Cenário: importar
  Dado um arquivo de backup válido
  Quando escolho "Importar backup" e confirmo
  Então os dados do app passam a ser os do arquivo e os avisos futuros são agendados
- Cenário: arquivo inválido
  Dado um arquivo "fotos.zip" que não é backup do Empresta Aí
  Quando tento importá-lo
  Então vejo "Arquivo de backup inválido." e os dados atuais continuam iguais

## 7. Estados do empréstimo

Estados: **Ativo**, **Devolvido parcialmente**, **Atrasado** (em aberto); **Devolvido**, **Perdido** (encerrados). "Em tolerância" é um rótulo de Ativo e de Devolvido parcialmente, não um estado (RN23).

Estado inicial: todo empréstimo nasce **Ativo**; a RN18 impede que nasça com data prevista já passada.

| Estado atual | Evento | Próximo estado | Regra |
|---|---|---|---|
| Ativo | Devolução completa | Devolvido | RN41, RN42 |
| Ativo | Devolução parcial | Devolvido parcialmente | RN41, RN42 |
| Ativo | Renovação (com data prevista, menos de 2 renovações) | Ativo | RN33, RN34 |
| Ativo | Definir prazo (sem prazo) | Ativo | RN20 |
| Ativo | 00:00 do dia data prevista + 4 | Atrasado | RN22 |
| Ativo | Marcar como perdido | Perdido | RN48 |
| Devolvido parcialmente | Devolução dos itens que faltavam | Devolvido | RN42 |
| Devolvido parcialmente | Nova devolução parcial | Devolvido parcialmente | RN42 |
| Devolvido parcialmente | Renovação (com data prevista, menos de 2 renovações) | Devolvido parcialmente | RN33, RN34 |
| Devolvido parcialmente | Definir prazo (sem prazo) | Devolvido parcialmente | RN20 |
| Devolvido parcialmente | 00:00 do dia data prevista + 4 | Atrasado | RN22, RN43 |
| Devolvido parcialmente | Marcar como perdido | Perdido | RN48, RN49 |
| Atrasado | Devolução completa | Devolvido | RN42 |
| Atrasado | Devolução parcial | Atrasado | RN42 |
| Atrasado | Marcar como perdido | Perdido | RN48 |
| Perdido | Objeto reapareceu | Devolvido | RN50, RN51 |
| Ativo | Excluir registro por engano (no dia da criação, sem renovação nem devolução) | (o empréstimo deixa de existir) | RN75 |

Transições proibidas (o app não oferece a ação):

| Estado atual | Evento | Regra |
|---|---|---|
| Atrasado | Renovação | RN33 |
| Ativo ou Devolvido parcialmente sem prazo | 00:00 de qualquer dia (nunca fica Atrasado) | RN19 |
| Ativo ou Devolvido parcialmente sem prazo | Renovação | RN19 |
| Qualquer estado com 2 renovações | Renovação | RN34 |
| Devolvido | Qualquer evento de estado (só desfecho e contestação podem mudar) | RN45, RN46 |
| Perdido | Devolução, renovação, nova perda | RN50 |
| Atrasado | Volta a Ativo | RN22, RN33 |

## 8. Modelo de dados

Tipos: `uuid`, `texto(n)` (até n caracteres), `data` (dd/mm/aaaa, sem hora), `data-hora`, `inteiro`, `booleano`, `enum`. Todos os dados ficam no aparelho (RN62).

### Objeto
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| id | uuid | sim | |
| nome | texto(60), mínimo 1 | sim | RN04 |
| categoria | enum (RN06) | sim | RN06 |
| foto | arquivo JPG/PNG até 10 MB | não | RN04 |
| estado_atual | enum Novo, Bom, Desgastado, Danificado, Inutilizado | sim | RN07, RN38, RN40 |
| arquivado | booleano, padrão falso | sim | RN58, RN60 |
| criado_em | data-hora | sim | |

A situação (Disponível, Emprestado, Perdido) é calculada pela RN08 e não é gravada.

### Item
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| id | uuid | sim | |
| objeto_id | uuid → Objeto | sim | |
| nome | texto(40), mínimo 1, único no objeto | sim | RN05 |
| ativo | booleano, padrão verdadeiro (falso = perdido) | sim | RN49, RN50 |

Relação: Objeto 1 — 0..N Item. O objeto não tem itens ou tem de 2 a 20 itens ativos ao ser cadastrado e ao incluir ou remover itens; depois de uma perda, pode ter 1 item ativo (RN05).

### Amigo
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| id | uuid | sim | |
| nome | texto(60), mínimo 1 | sim | RN12, RN65 |
| telefone | texto, 10 ou 11 dígitos | não | RN12 |
| observacao | texto(200) | não | RN12 |
| bloqueado | booleano, padrão falso | sim | RN53 |
| motivo_bloqueio | texto(200) | não | RN53 |
| arquivado | booleano, padrão falso | sim | RN58, RN60 |
| anonimizado | booleano, padrão falso | sim | RN65, RN66 |
| sugestao_bloqueio_dispensada_em | data-hora | não | RN56 |
| criado_em | data-hora | sim | |

### Emprestimo
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| id | uuid | sim | |
| objeto_id | uuid → Objeto | sim | RN10 |
| amigo_id | uuid → Amigo | sim | |
| data_saida | data | sim | RN15 |
| data_prevista | data | não (vazio = sem prazo) | RN16, RN19 |
| prazo_definido_depois | booleano, padrão falso | sim | RN20 |
| estado | enum Ativo, Devolvido parcialmente, Atrasado, Devolvido, Perdido | sim | seção 7 |
| estado_saida | enum da RN07 | sim | RN38 |
| foto_saida | arquivo | não | RN38 |
| renovacoes | inteiro 0 a 2 | sim | RN34 |
| avisos_silenciados | booleano, padrão falso | sim | RN30 |
| data_atrasado | data (data prevista + 4, quando ficou Atrasado) | não | RN22, RN55 |
| data_encerramento | data: a da devolução que encerrou; em Perdido, a data da perda; depois de reaparecimento, a data do reaparecimento | só se encerrado | RN67 |
| desfecho_dano | enum Sem ação, Reparo combinado, Reposição combinada, Resolvido | só se houver dano | RN45 |
| contestacao | texto(500) | não | RN46 |
| data_perda | data | só se já foi Perdido | RN48 |
| perda_revertida | booleano, padrão falso | sim | RN50 |
| criado_em | data-hora | sim | RN75 |
| estado_objeto_antes | enum da RN07 (estado atual do objeto antes do registro) | sim | RN75 |

O estado na volta e o dano são calculados pela última Devolucao (RN44) e não são gravados.

### Renovacao
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| id | uuid | sim | |
| emprestimo_id | uuid → Emprestimo | sim | |
| data | data | sim | |
| dias | inteiro 1 a 30 | sim | RN35 |
| data_prevista_anterior | data | sim | RN35 |
| data_prevista_nova | data | sim | RN36 |

### Devolucao
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| id | uuid | sim | |
| emprestimo_id | uuid → Emprestimo | sim | |
| data | data (sempre hoje no registro) | sim | RN39 |
| criado_em | data-hora (ordena devoluções do mesmo dia) | sim | RN44 |
| estado | enum da RN07 | sim | RN40 |
| foto | arquivo | não | RN40 |
| origem | enum Normal, Reaparecimento | sim | RN50 |

### ItemEmprestado
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| emprestimo_id | uuid → Emprestimo | sim | RN41 |
| item_id | uuid → Item | sim | RN41 |

### ItemDevolvido
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| devolucao_id | uuid → Devolucao | sim | RN41 |
| item_id | uuid → Item | sim | RN41 |

Um item é pendente num empréstimo se está em ItemEmprestado desse empréstimo e não aparece em nenhum ItemDevolvido dele.

### Configuracao (registro único)
| Atributo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| contador_anonimizacao | inteiro, começa em 0 | sim | RN65 |

Relações: Objeto 1—N Emprestimo; Amigo 1—N Emprestimo; Emprestimo 1—N Renovacao (máx. 2); Emprestimo 1—N ItemEmprestado; Emprestimo 1—N Devolucao; Devolucao 1—N ItemDevolvido.

## 9. Casos de borda e erros

| Situação | Comportamento esperado | Regra |
|---|---|---|
| Prazo de 0 dias ou negativo | "O prazo deve ser de 1 a 90 dias"; não salva | RN16 |
| Prazo de 90 dias | Aceito | RN16 |
| Data prevista no calendário igual à data de saída | Data não selecionável | RN16 |
| Saída 31 dias atrás | Data não selecionável no calendário | RN15, RN74 |
| Saída retroativa com data prevista já passada | "A data de devolução já passou. Escolha um prazo maior."; não salva | RN18 |
| Saída retroativa com data prevista hoje | Aceito; aviso "devolução hoje" só se ainda não passou das 09:00 | RN18, RN29 |
| Fuso do aparelho muda (viagem) | Datas não mudam; "hoje" passa a ser o do novo fuso | RN02 |
| Data prevista 31/01, prazo de 30 dias somado | Calendário real: 02/03 (ano não bissexto) | RN35 |
| Dia data prevista + 3 às 23:59 | Ainda em tolerância, não Atrasado | RN22, RN23 |
| Empréstimo sem prazo com 400 dias | Continua Ativo; lembrete a cada 30 dias | RN19, RN28 |
| Renovar em tolerância com dias insuficientes | Mensagem com o mínimo de dias; não salva | RN36 |
| 3ª renovação | Opção não aparece | RN34 |
| Renovar empréstimo Atrasado | Opção não aparece; texto explicativo | RN33 |
| Renovar sem prazo | Opção não aparece; aparece "Definir prazo" | RN19, RN20 |
| Dois empréstimos do mesmo objeto | Objeto fora da seleção enquanto há empréstimo em aberto | RN10 |
| Objeto Perdido escolhido para empréstimo | Não aparece na seleção | RN09 |
| Objeto Inutilizado escolhido para empréstimo | Não aparece na seleção | RN09 |
| Amigo bloqueado, arquivado ou anonimizado | Não aparece na seleção | RN14 |
| Amigo com atraso recebe novo empréstimo | Alerta com confirmação; não impede | RN24 |
| Volta melhor que a saída (saiu Desgastado, voltou Bom) | Sem dano; estado atual vira Bom | RN40, RN44 |
| Volta igual à saída | Sem dano | RN44 |
| Devolução parcial marcando 0 itens | "Marque pelo menos um item devolvido"; não salva | RN41 |
| Editar itens com empréstimo em aberto | Opção indisponível | RN05 |
| Perda parcial deixa o objeto com 1 item ativo | Permitido; o objeto segue com 1 item | RN05, RN49 |
| Excluir empréstimo por engano depois de mudar o estado na saída | O estado atual do objeto volta ao anterior; avisos cancelados | RN75 |
| Importar backup de versão mais nova do app | Mensagem da RN72; nada muda | RN72 |
| Contestação em empréstimo sem dano | Opção não aparece | RN46 |
| Marcar perda em empréstimo Devolvido | Opção não aparece | RN48 |
| Objeto reapareceu com outro empréstimo em aberto | Mensagem; nada muda | RN51 |
| Objeto reapareceu estando arquivado | Desarquiva | RN51 |
| Perda revertida conta para bloqueio? | Não | RN55 |
| Sugestão de bloqueio para amigo já bloqueado | Não aparece | RN55 |
| Excluir objeto ou amigo com histórico | Opção "Excluir" não aparece; só "Arquivar" | RN58 |
| Arquivar com empréstimo em aberto | Mensagem; nada muda | RN59 |
| Remover dados com empréstimo em aberto | Mensagem; nada muda | RN64 |
| Nome repetido de amigo | Pergunta de confirmação; permite salvar | RN13 |
| Nome repetido de objeto | Permite sem aviso | RN11 |
| Notificações negadas | Faixa na tela inicial; listas funcionam | RN31 |
| App fechado por dias | Na abertura, cada empréstimo mostra o estado calculado pela RN22 para hoje; as notificações já agendadas são entregues pelo celular mesmo com o app fechado | RN22, RN29 |
| Importar arquivo que não é backup | "Arquivo de backup inválido."; nada muda | RN72 |
| Foto acima de 10 MB ou formato diferente de JPG/PNG | "A foto deve ser JPG ou PNG de até 10 MB"; não anexa | RN04 |

## 10. Requisitos não funcionais

- **Plataforma:** app de celular para Android 10 ou superior e iOS 16 ou superior, em português do Brasil, com datas em dd/mm/aaaa.
- **Funcionamento offline:** todas as funções funcionam sem internet. Os avisos são notificações locais agendadas no aparelho.
- **Desempenho:** com 1.000 empréstimos e 300 objetos cadastrados, cada lista abre em até 1 segundo num aparelho com 3 GB de RAM.
- **Acessibilidade:** todos os botões e campos com rótulo lido por leitor de tela (TalkBack/VoiceOver); contraste mínimo 4,5:1 (WCAG 2.1 AA); o texto respeita o tamanho de fonte do sistema até 200% sem cortar conteúdo; estado e rótulos sempre escritos por extenso, nunca indicados só por cor; área de toque mínima de 44×44 pt.
- **Privacidade (LGPD):** dados só no aparelho (RN62); coleta mínima do amigo (RN61); remoção por anonimização (RN63 a RN66); aviso ao exportar backup (RN71). O app não envia dados de uso nem de análise a terceiros. Observação: o tratamento feito pelo dono, pessoa física, para fins particulares e não econômicos está fora da aplicação da LGPD (art. 4º, I); mesmo assim, o app segue os princípios de finalidade, necessidade e eliminação a pedido.
- **Confiabilidade:** toda gravação é atômica (salva tudo ou nada); se o app fechar no meio de um registro, nenhum dado parcial fica gravado.

## 11. Decisões e premissas

"D" = decisão tomada por mim na entrevista. "P" = premissa: detalhe proposto pela IA durante a redação, revisado e aceito por mim. "(IA)" = aceitei a opção que a IA recomendou.

| ID | Decisão | Alternativas consideradas | Justificativa |
|---|---|---|---|
| D01 | Amigo é só contato, sem conta (IA) | Amigo também usa o app | Não depende de o amigo instalar nada; evita login, convite e permissões, e a spec fica do tamanho do problema |
| D02 | Prazo informado em dias ou data; grava-se a data (IA) | Só dias; só data | Dá liberdade ao dono e mantém uma única fonte de verdade para os cálculos |
| D03 | Prazo padrão por categoria, de 1 a 90 dias, e "sem prazo" permitido | Padrão único; sem "sem prazo" | Uma ferramenta volta mais cedo que um livro; e há empréstimos reais sem data (ex.: livro para um parente) |
| D04 | Vence às 23:59:59 no fuso do aparelho (IA) | Horário de Brasília fixo; início do dia | "Até dia 17" significa o dia 17 inteiro para qualquer pessoa; o fuso do aparelho é o que o dono vive |
| D05 | Tolerância de 3 dias antes de Atrasado | Sem tolerância; 1 dia | Evita tratar como atraso o amigo que devolve no fim de semana seguinte |
| D06 | Atraso gera aviso e alerta ao emprestar, sem bloqueio (IA) | Bloqueio automático; pontuação | O dono conhece o amigo e decide; o bloqueio fica como decisão manual (D20) |
| D07 | Avisos 2 dias antes e no dia, às 09:00 (IA) | Só no dia; 1 dia antes | Dois dias dão tempo de combinar a devolução |
| D08 | Avisos de atraso no 1º dia e depois semanais (IA) | Diários; só 1º, 7º e 30º dia | Diário vira ruído e é silenciado; semanal mantém a lembrança até resolver |
| D09 | Até 2 renovações (IA) | 3; sem limite | Barra o amigo que renova para sempre |
| D10 | Renovação de 1 a 30 dias somada ao prazo anterior (IA) | Mesmo prazo do início; contar de hoje | Renovar cedo não faz o amigo perder dias |
| D11 | Empréstimo Atrasado não pode ser renovado | Pode, contando de hoje; só na tolerância | Atrasado já passou da tolerância; renovar seria perdoar o atraso; o caminho é devolver |
| D12 | Mesma escala de estado na saída e na volta (IA) | Escala de dano só na volta | Permite comparar a saída com a volta e definir dano de forma objetiva |
| D13 | Dono define o estado e registra o desfecho do acordo (IA) | Só registro, sem desfecho | Mostra se o dano foi resolvido, sem envolver dinheiro (fora do escopo) |
| D14 | Dono registra a contestação do amigo (IA) | Sem contestação | O amigo não tem conta; registrar a versão dele deixa o histórico mais justo |
| D15 | Devolução parcial é estado próprio e segue contando prazo (IA) | Conta como devolvido, com observação | O que falta continua emprestado e precisa de cobrança |
| D16 | Dono marca perda a qualquer momento; app sugere aos 60 dias de atraso (IA) | Só após 30 dias; automático aos 90 | Às vezes o amigo avisa que perdeu no dia seguinte; o app não decide pelo dono |
| D17 | Objeto que reaparece reabre e registra devolução (IA) | Perdido é final | Mantém o histórico verdadeiro e devolve o objeto ao uso |
| D18 | Um empréstimo em aberto por objeto (IA) | Quantidade por objeto | Cada objeto é único na casa de uma pessoa; quantidade complicaria tudo |
| D19 | Conjunto é um objeto com itens (IA) | Cada parte um objeto | Empresta-se o conjunto de uma vez e confere-se peça por peça na volta |
| D20 | Bloqueio manual, com sugestão por critério em 12 meses (IA) | Só manual; automático | Responde "por qual critério" com números testáveis, sem tirar a decisão do dono |
| D21 | Excluir só sem histórico; com histórico, arquivar (IA) | Excluir apaga tudo; lixeira de 30 dias | Preserva o histórico de objetos e amigos (funcionalidade 9) |
| D22 | LGPD: dados mínimos e remoção por anonimização (IA) | Exclusão total | Cumpre o pedido de remoção sem apagar o histórico dos objetos do dono |
| D23 | App de celular, dados só no aparelho, com backup em arquivo (IA) | Nuvem com conta; web | Sem servidor nem login; avisos locais funcionam offline; o backup cobre a perda do celular |
| P01 | Categorias fixas, com os prazos da RN06 | Dono cria categorias | Lista fixa basta para o tamanho do app e deixa o prazo padrão testável |
| P02 | "Em tolerância" é rótulo, não estado | Estado próprio | Nada muda de comportamento na tolerância além do texto; menos transições |
| P03 | Devolução parcial que chega ao 4º dia vira Atrasado | Estado "parcialmente atrasado" | Atrasado já é o estado de cobrança; a lista de itens pendentes mostra o que falta |
| P04 | Saída até 30 dias no passado | Só hoje; qualquer data | Permite registrar um empréstimo esquecido sem abrir caminho para datas absurdas |
| P05 | Devolução registrada sempre com a data de hoje | Data retroativa | Evita reescrever se houve atraso ou não, e mantém os critérios de bloqueio confiáveis |
| P06 | "Definir prazo" uma vez em empréstimo sem prazo, sem contar como renovação | Não permitir | O dono pode decidir pedir de volta depois; seria estranho obrigá-lo a devolver e emprestar de novo |
| P07 | Limite de 90 dias só no prazo inicial | Limite total de 90 dias | As renovações já são limitadas a 2 × 30 dias |
| P08 | Nova data de renovação não pode ficar no passado | Aceitar | Uma renovação que já nasce vencida não faz sentido |
| P09 | Em toda devolução se informa o estado; vale o da última | Estado por item | Mantém a escala simples e a comparação com a saída objetiva |
| P10 | Perda com itens devolvidos inativa os itens que faltam e devolve o objeto ao uso | Objeto inteiro perdido | O que voltou continua do dono e pode ser emprestado |
| P11 | Remoção de dados também apaga as contestações do amigo | Manter as contestações | A contestação traz as palavras do amigo, que podem identificá-lo |
| P12 | Lembrete a cada 30 dias em empréstimo sem prazo | Nenhum aviso | Sem prazo não pode virar "esquecido para sempre" |
| P13 | Nome de amigo repetido gera confirmação; nome de objeto repetido, não | Proibir repetição | Duas "Carlas" existem; dois objetos iguais também, e o risco de confusão é menor |
| P14 | Dano e desfecho só na devolução que encerra o empréstimo | Avaliar em cada devolução parcial | Um único desfecho por empréstimo, comparando a saída com a volta final |
| P15 | Contestação e troca de desfecho só em empréstimo Devolvido | Também em empréstimo em aberto | Só existe dano definitivo quando tudo voltou |
| P16 | Estado na saída não pode ser Inutilizado | Permitir | Objeto inutilizado não se empresta (RN09) |
| P17 | Atrasado nunca volta a Ativo, nem com troca de fuso | Recalcular sempre pela data | Evita estado oscilando e atraso contado duas vezes no bloqueio |
| P18 | "Objeto reapareceu" devolve de uma vez todos os itens pendentes | Marcar item a item | O dono usa a opção quando o que faltava apareceu; mantém o registro curto |
| P19 | Item com histórico não pode ser removido, só renomeado; itens perdidos não contam no mínimo de 2 | Permitir remover | Preserva o histórico de devoluções do objeto |
| P20 | Empréstimo registrado por engano pode ser excluído só no dia da criação, sem renovação nem devolução; depois disso nada do registro é editável | Edição livre; nunca excluir | Corrige erro de digitação sem permitir reescrever o histórico |
| P21 | Backup sem senha nem criptografia | Backup com senha | Mantém a v1 simples; o aviso da RN71 orienta guardar o arquivo em local seguro |
| P22 | Calendários só mostram datas válidas; mensagens seguem o padrão da RN74 | Validar só ao salvar | Impede o erro antes de ele acontecer e padroniza os textos |
| P23 | Só o dono registra a renovação, mesmo quando o pedido vem do amigo (RN32) | Amigo pede a renovação pelo app | O amigo não tem conta (D01); o pedido chega ao dono fora do app |
| P24 | O dono pode silenciar e reativar os avisos de cada empréstimo (RN30) | Sem silenciar; silenciar o app inteiro | Quando a devolução já foi combinada, os avisos viram ruído; silenciar só um empréstimo não esconde os outros |
| P25 | O app sugere arquivar o objeto que volta inutilizado ou é perdido, sem arquivar sozinho (RN47, RN52) | Arquivar automaticamente; não sugerir | O objeto não pode mais ser emprestado, mas quem decide tirá-lo da lista é o dono |
| P26 | Sugestão de bloqueio dispensada só volta com um evento novo (RN56) | Repetir a cada abertura; nunca mais sugerir | Não incomoda o dono a cada abertura, mas volta se o amigo piorar |
| P27 | Depois de uma perda parcial, o objeto pode ficar com 1 item ativo (RN05) | Converter em objeto sem itens; impedir a perda | O item que voltou continua registrado; converter apagaria a ligação com o histórico |
| P28 | O empréstimo guarda a lista de itens que saíram (ItemEmprestado) | Deduzir pelas datas dos itens | Registra exatamente o que o amigo levou, mesmo que os itens mudem depois |
| P29 | Excluir empréstimo por engano cancela os avisos e devolve o estado anterior do objeto (RN75) | Manter o estado alterado | Um registro feito por engano não deve deixar rastro no objeto |
| P30 | Backup válido = .zip da RN71 com `dados.json` versionado e todas as fotos (RN72) | Aceitar qualquer .zip | Evita importar dados incompletos ou de uma versão que o app não entende |
| P31 | Limites e formatos propostos pela IA na redação: tamanhos de campo (RN04, RN05, RN12), foto de até 10 MB, remoção de espaços (RN03), singular e plural (RN73), ordem das listas (RN67), formato do resumo do amigo (RN70), nome e conteúdo do backup (RN71, RN72) e os números da seção 10 (Android 10 e iOS 16, 1.000 empréstimos em até 1 s com 3 GB de RAM, toque de 44×44 pt) | Deixar sem valor ("tamanho razoável", "rápido") | O enunciado pede valores testáveis no lugar de palavras vagas; os números seguem padrões comuns de apps de celular e são fáceis de verificar |
| P32 | Todos os itens ativos saem juntos no empréstimo (RN41) | Escolher quais itens saem | Um conjunto é emprestado inteiro (D19); escolher itens transformaria o conjunto em vários objetos |
| P33 | Se a data prevista escolhida no calendário deixar de valer após mudar a data de saída, o campo é apagado (RN16) | Bloquear a mudança da saída; ajustar a data sozinho | O dono escolhe de novo, sem o app inventar uma data |

## 12. Questões em aberto

Nenhuma.
