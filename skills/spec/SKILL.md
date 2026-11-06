---
name: spec
description: Monta o prompt oficial de uma tarefa a partir de uma descrição informal, pergunta o que falta e só executa após aprovação explícita
disable-model-invocation: true
---

O usuário descreveu informalmente uma tarefa ou problema: $ARGUMENTS

Siga as etapas na ordem. A ordem é o que carrega peso em três pontos: **decompor (1) antes de ler o
repositório (3)**, **escrever o resumo (8) por último**, e **não executar nada antes da aprovação (9)**.

## 0. Os comandos deste projeto — leia antes de qualquer coisa

Você não sabe o comando de build nem o de teste deste projeto, e **não deve supor**. Leia a seção **"Stack
e comandos"** do `CLAUDE.md` da raiz: é lá que eles moram, e é de lá que sai o critério de aceite da etapa
7.

**Se a seção não existir, ou não disser o comando:** diga isso em uma linha, pergunte qual é, e proponha
escrevê-la no `CLAUDE.md` nesta mesma entrega. Não chute um default da stack ("deve ser `npm test`") — um
critério de aceite que roda o comando errado passa verde sem ter provado nada.

## 1. DECOMPOR O PEDIDO — antes de abrir o repositório

**Este passo é sobre o MUNDO, não sobre o código.** Não leia o repositório ainda: se você começa pelo que
existe, o que existe define o que vai ser pedido, e o que falta fica invisível.

Escreva, para cada coisa pedida:

- **De que ela é feita** — as partes que qualquer pessoa esperaria encontrar. Não as que o repositório tem.
- **Os verbos** — tudo que alguém precisa poder FAZER com aquilo. Verbo por verbo, em lista. Um sistema em
  que se cria e não se edita, ou em que se entra e não se sai, é um sistema pela metade, e é assim que se
  descobre isso antes de construir.
- **Os estados** — por onde a coisa passa, do nascimento ao fim, e quem a move de um para outro.
- **O que acontece no caso ruim** — ninguém responde, a pessoa desiste, quem estava cuidando sai, o dado
  não chega. O caso ruim é metade do sistema e é o que nunca é pedido em voz alta.
- **Onde ela encosta no que já existe** — sem decidir nada ainda.

⚠️ **Verbo mínimo de qualquer coisa que se cria: criar, ler, corrigir, sair de cena.** Faltar "corrigir" é
o erro mais comum e o mais caro, porque a pessoa convive com o engano para sempre. Faltar "sair de cena" é
o segundo: a lista só cresce e um dia ninguém acha nada.

⚠️ **Se o pedido cabe numa palavra que nomeia um sistema inteiro** — não um campo, não uma tela —, trate-o
como grande e decomponha item a item antes de seguir. Palavra é fácil de adiar; lista de doze partes, não.
Na dúvida entre pequeno e grande, trate como grande: decompor algo pequeno custa minutos, e não decompor
algo grande custa a entrega inteira.

## 2. A DÍVIDA — o que ficou pendente das vezes anteriores

**As fontes de um projeto são arquivos, e o pedido não é arquivo.** O que foi pedido em voz alta mora na
conversa, e some a cada entrega nova. Este passo existe para ele não sumir.

- Releia o que foi pedido nas conversas anteriores sobre este assunto, não só o texto de hoje.
- Liste o que foi adiado antes, com o motivo que foi dado na época.
- **Para cada item adiado, diga se o motivo ainda vale.** Motivo que caiu (o impedimento foi consertado, a
  dependência chegou, a decisão saiu) transforma o item em pendência ativa — e ele entra nesta entrega ou
  volta a ser dito em voz alta no passo 8.

⚠️ **"Fora de escopo" é dívida com vencimento, nunca um estado permanente.** Adiar por um motivo e nunca
mais olhar é como uma coisa pedida no primeiro dia desaparece sem que ninguém decida descartá-la.

## 3. CONTEXTO: o que o repositório já tem

Agora sim leia, obedecendo a **hierarquia de fontes**: **código > `docs/` > `plans/`.**

- O **código** é o estado real do sistema. Em qualquer conflito, ele vence.
- **`docs/`** documenta o que **já está pronto** — é a segunda palavra, e onde ficam as regras de negócio.
- **O ARCH do projeto** (`<PLATAFORMA>-ARCH.md`) é o padrão de engenharia obrigatório: leia antes de propor
  qualquer código.
- **`plans/`** informa **INTENÇÃO futura, nunca estado atual**. Um plano pode ter sido executado,
  abandonado ou contradito pelo código sem que o arquivo tenha sido atualizado. Nunca trate plano como
  verdade sobre o que existe hoje; se ele contradiz o código ou o `docs/`, **valem o código e o `docs/`** —
  e reporte a divergência a quem pediu.
- Consulte **apenas o(s) plano(s) diretamente ligados à tarefa** (ache pelo índice de `plans/`), nunca a
  pasta inteira.

Identifique os arquivos do código provavelmente envolvidos (**somente leitura** nesta etapa).

**Agora cruze com o passo 1:** cada parte e cada verbo que você listou lá ou existe, ou não existe. O que
não existe é o trabalho. Esta lista é o que impede "já tem" de ser confundido com "já resolve".

### Procure antes de criar

Antes de propor qualquer coisa reutilizável, **procure o que já está lá**, pelo índice de descoberta que
este projeto declarar (uma convenção de palavras-chave, um índice de módulos, o que houver — o `CLAUDE.md`
diz). Achou: reusa ou estende. Não achou: cria.

**Se o projeto não tem índice de descoberta, diga isso** em vez de fingir que procurou — e proponha o grep
que você de fato rodou, com o termo, para quem lê saber o alcance da busca. Uma busca não feita que passa
por feita é como a segunda implementação da mesma coisa entra no repositório.

## 4. DE QUEM É ISSO — a análise que precede QUALQUER decisão de onde colocar

**Esta etapa vale para toda informação nova, venha de onde vier**: um pedido, um ingresso, uma migração,
uma integração, uma tela nova, uma refatoração. **Os exemplos abaixo são ILUSTRAÇÃO, nunca escopo** — "o
exemplo falava de documento e isto aqui é outra coisa" não dispensa a análise. Se a tarefa introduz
informação, a análise acontece.

Antes de propor pasta, módulo, tabela ou coluna, responda POR ESCRITO, para **cada** informação nova:

- **Quem essa informação DESCREVE?** Ela pertence a quem ela descreve, jamais a quem a consome. Quem
  consome apenas CONSULTA.
- **EXIGIR NÃO É POSSUIR.** "Tal processo exige tal dado" é regra **do processo** e mora com ele; o dado
  continua sendo de quem ele descreve. Exigência e dado no mesmo lugar = dado com dono errado.
- **Obrigatoriedade é do CONSUMIDOR, não do dado.** O mesmo fato é exigido num lugar e ignorável em outro.
  "Obrigatório" gravado junto do dado não serve ao segundo consumidor.
- **TESTE DO SEGUNDO CONSUMIDOR — é ele que decide.** "Se amanhã existir outro módulo, outra tela, outro
  evento ou outro cliente que precise **desta mesma informação**, ela estaria em DOIS lugares?" Se sim, o
  dono proposto está errado. Não invente o segundo consumidor: use um que quem pediu já tenha citado, que
  exista no roadmap, ou que seja consequência óbvia do negócio.
- **QUEM PEDIU NÃO É QUEM TEM.** O primeiro consumidor a precisar de algo revela uma NECESSIDADE, nunca a
  posse. Construir onde o pedido chegou é o erro que este passo existe para pegar.

⚠️ **Se a análise disser que o dono é um lugar e a tarefa mandar construir em outro — ou mandar duplicar —
PARE e diga**, com o dono nomeado e o teste do segundo consumidor respondido. Quem decide é quem pediu, mas
decide sabendo. É o fluxo "pedido que fere regra escrita" do `CLAUDE.md`.

## 5. QUEM AGE — as pessoas, não as tabelas

O passo 4 é inteiro sobre onde o dado mora. Sistema pensado só por esse eixo nasce com o armazenamento
certo e sem ninguém dentro. Responda também:

- **Quem FAZ?** Toda coisa que alguém executa tem um responsável, e ele aparece com **nome** onde a coisa
  aparece. Identificador na tela é o mesmo que ninguém.
- **Como se define quem vai fazer?** Atribuir é ação própria — nunca efeito colateral de outra ação.
- **Como se tira de alguém?** Devolver para a fila é tão necessário quanto atribuir.
- **Quem COBRA?** Existe quem acompanha o trabalho dos outros. Diga o que ele vê que os demais não veem, e
  o que ele usa para cobrar — normalmente "o que está parado, e com quem".
- **Quem vê o de todo mundo × quem vê só o seu?** Se a resposta for "todo mundo vê tudo", diga isso como
  decisão, não como omissão.

## 6. O CAMINHO DA PESSOA — do primeiro clique até o fim

Descreva, em passos numerados, o que a pessoa faz de ponta a ponta. Um caminho por tipo de pessoa (quem
pede, quem atende, quem cobra, quem só olha).

**Aponte explicitamente todo passo que termina no vazio** — onde a resposta leva a um botão que não existe,
a uma tela que não foi pedida, a uma informação que ninguém devolve.

⚠️ **Este passo pega a falha que nenhum teste pega: a peça que responde uma pergunta cuja próxima ação não
existe.** Ela compila, passa, é documentada com capricho — e é inútil. O sinal é você escrever, na
justificativa de uma peça, o nome de algo que não foi construído.

## 7. PROMPT OFICIAL

Monte o prompt estruturado contendo:

- **Objetivo** (uma frase).
- **As partes e os verbos** do passo 1, marcados: existe / não existe / existe pela metade.
- **Dono de cada informação nova**: quem ela descreve, quem apenas consulta, e o teste do segundo consumidor
  respondido. Sem isso o prompt manda construir no lugar de quem PEDIU, e não no lugar de quem TEM.
- **Quem age** (passo 5) e **o caminho da pessoa** (passo 6).
- **Contexto técnico** — o que o ARCH e o `docs/` mandam, citado pelo arquivo.
- **Reuso — o que já existe**: o resultado da busca do passo 3, com o termo buscado, o que reusar ou
  estender, e o que de fato não existe. Sem isso o prompt manda criar o que já está lá.
- **Arquivos prováveis.**
- **Critério de aceite VERIFICÁVEL**, e ele tem DUAS metades, ambas obrigatórias:
  - **o que a máquina confere** — o **build e o teste deste projeto**, com o comando exato lido no passo 0;
  - **o que QUEM PEDIU vê na tela.** Pelo menos um critério que essa pessoa possa conferir sozinha, abrindo
    o sistema, escrito como uma ação e um resultado esperado. Sem esta metade o aceite é auto-avaliado:
    suíte verde convive perfeitamente com o sistema fazendo a coisa errada na cara do usuário.
- **Fora de escopo** — e este campo tem regra, porque é o mais perigoso do documento:
  - **Nada que foi pedido em voz alta entra aqui em silêncio.** Se vai ficar de fora, isso se diz na
    conversa (passo 8), não se escreve numa seção que ele vai assinar sem ler.
  - Todo item tem **motivo** e **gatilho de volta**: o que precisa acontecer para ele deixar de estar
    adiado. Item sem gatilho não é adiamento, é descarte — e descarte é decisão de quem pediu, não sua.
  - Item cujo gatilho já aconteceu **não pode estar aqui** (o passo 2 é quem confere isso).
- **Fechamento obrigatório**: o prompt termina mandando **documentar o que ficou pronto** e **atualizar os
  índices** na mesma entrega — é o princípio "terminou = implementar + testar + documentar". Se o projeto
  tem índice de descoberta, registrar o que foi criado nele faz parte do fechamento.

### ⚠️ Todo exemplo é neutro

Antes de escrever qualquer exemplo neste documento, leia
`${CLAUDE_PLUGIN_ROOT}/references/neutral-examples.md` e **copie a regra para dentro do documento
entregue**. Exemplo não morre no documento: ele vira placeholder, chave de i18n, massa de teste e seed, e
termina na tela de um cliente.

## 8. LACUNAS e O RESUMO

Se faltar informação que impede precisão, use `AskUserQuestion`. Pergunte SÓ o essencial — nunca o que dá
para deduzir do `docs/` ou do código.

**O resumo vai no FIM do documento, e é a última coisa que você escreve.** A ordem importa: resumo escrito
antes deforma a spec para caber nele. Resumo tirado das partes prontas descreve o que realmente está lá.

**A amarração, que é o que impede o resumo de mentir:**

- **Uma linha por parte da spec, na mesma ordem, com o mesmo nome da parte.** Um para um.
- **Parte sem linha = spec incompleta. Linha sem parte = mentira.** Dá para conferir de olho em segundos, e
  é para isso que serve a correspondência.
- Tarefa pequena: resumo de uma linha. Tarefa grande: uma linha por parte — não force tudo numa linha só.

**A linguagem, e esta parte é requisito, não estilo:**

- Escreva para alguém que **não conhece o sistema e não programa**. Informal, mas não desleixado.
- **Zero jargão**: sem nome de tabela, de rota, de classe, de arquivo, de padrão de arquitetura.
- Cada linha diz **o que a coisa faz e para quem**. Se a frase só faz sentido para quem escreveu o código,
  ela está errada — reescreva.

O resumo existe porque quem aprova precisa saber o conteúdo inteiro **sem ler o documento inteiro**.
Descobrir no meio da leitura que o rumo está errado, mandar refazer e recomeçar é o custo que ele evita.

## 9. APROVAÇÃO — dizendo em voz alta o que está sendo tirado

Apresente o prompt final e **PARE**. Não use `Edit`, `Write` nem comandos que alterem estado antes de uma
aprovação explícita ("aprovado", "pode executar" ou similar). Se pedirem ajuste, refaça e apresente de novo.

⚠️ **Junto com o prompt, diga na conversa — em texto, fora do documento — TUDO que você está deixando de
fora e que foi pedido em voz alta.** Uma linha por item: o que é, por que não entra agora, e o que faz ele
voltar.

O motivo é medido: **quem aprova costuma aprovar confiando, sem ler.** Uma aprovação assim não transfere
informação, só responsabilidade — e o que VOCÊ cortou volta carimbado como decisão de QUEM APROVOU. Dizer
em voz alta é o que devolve a decisão para quem ela é. Coisa pedida em voz alta se responde em voz alta.

## 10. EXECUÇÃO

Após aprovação, execute o prompt, rode o critério de aceite — **as duas metades** — e corrija falhas até
passar. A metade que quem pediu vê na tela é a que decide se está pronto; suíte verde sozinha nunca foi
prova de que o sistema faz o que ele pediu.
