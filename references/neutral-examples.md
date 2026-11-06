# O EXEMPLO VAZA PARA O PRODUTO — escreva todo exemplo como neutro

Referência compartilhada pelas skills `spec`, `sibling-spec` e `new-project`. Leia antes de escrever
qualquer exemplo num documento que outra pessoa vai executar.

---

Exemplo não é ilustração que morre no documento. Ele vira **placeholder de campo, chave de i18n, massa de
teste, seed e print de documentação** — quem executa copia o que está escrito, porque é o que está na mão.
Nasce como exemplo e termina em produção, na tela de um cliente.

## Duas proibições, e as duas já custaram retrabalho

1. **Não cite funcionalidade que nem todo cliente tem.** Exemplo que fala de uma capacidade específica vira
   um texto sobre algo que **não existe no sistema de quem está lendo** — a pessoa abre a tela e o campo
   sugere uma operação que ela não contratou. Se o exemplo precisa de contexto, use uma ação que existe em
   qualquer sistema ("não consigo salvar", "a tela não carrega", "o botão não responde").

2. **Não use dado pessoal real** — nome, telefone, e-mail, documento, endereço ou empresa. Nem de quem
   pediu, nem de cliente, nem de colega. Dado real em exemplo é dado real vazado, e ele se espalha por
   cópia para lugares que ninguém mapeia depois.

## O que usar no lugar

Nome obviamente fictício e **consistente ao longo do documento**; telefone e documento **inválidos por
construção**; empresa inventada; assunto genérico. Fictício de verdade — nome plausível de pessoa real é o
mesmo problema com outra roupa.

## E o documento tem de CARREGAR esta regra

Escreva-a no próprio documento entregue, para quem executar saber que não deve inventar exemplo
específico. Sem isso, quem recebe o documento preenche as lacunas com o que conhece — e o que ele conhece
é o cliente dele.
