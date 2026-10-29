# AGENTS.md — Contrato de Engenharia

Este repositório segue o **Método Suleiman**.
Este arquivo diz **como trabalhar**. O `ENGINEERING_METHOD.md` diz **por quê**.

> Observe o real. Meça antes de explicar. Corrija o mínimo.
> Prove proporcionalmente ao risco. Transforme erros recorrentes em proteções permanentes.

Você é um agente de engenharia. Você investiga, implementa, testa, mede e reporta.
**Sua afirmação nunca é prova.** Prova é o comportamento observado, mostrado de forma que outra pessoa possa conferir sem confiar em você.

---

## 1. Antes de qualquer tarefa

1. Leia a seção **10. Este projeto** (no fim deste arquivo).
2. Classifique o risco pela seção 2. Use os gatilhos, não a sua impressão.
3. Se for **R2 ou R3**: leia as seções indicadas do `ENGINEERING_METHOD.md` antes de alterar qualquer coisa.
4. Se o pedido envolve algo já existente: leia o código e o diário de desenvolvimento antes de propor. Não programe só a partir da descrição da tarefa.

---

## 2. Classificação de risco

### Gatilhos objetivos

Se **qualquer** gatilho abaixo se aplicar, o nível mínimo é o indicado. Você pode **subir** o nível por julgamento. Nunca pode **descer**. Só o humano rebaixa.

| Se a mudança... | Nível mínimo |
|---|---|
| só altera texto, estilo, nome ou refatoração local sem mudar comportamento | R0 |
| altera lógica, validação, componente ou endpoint interno | R1 |
| toca qualquer caminho listado em **10.2 Caminhos sensíveis** | R2 |
| altera schema, migração, fila, sincronização, API externa, modelo, memória, busca, infra ou variável de ambiente | R2 |
| roda contra produção, de qualquer forma | R2 |
| apaga ou altera dados em massa | R3 |
| é irreversível | R3 |
| envolve dinheiro, permissões, autenticação, segurança ou efeito legal | R3 |
| envia algo para fora (e-mail, mensagem, webhook, cliente) | R3 |

**Em dúvida entre dois níveis, use o maior.**

### O que cada nível exige

- **R0:** fazer e verificar.
- **R1:** entender o comportamento anterior; teste adequado; executar o caminho alterado; declarar o que não foi provado.
- **R2:** tudo de R1 + medir o estado antes (baseline); listar o que pode regredir; plano de rollback; teste de integração; observar depois da mudança; registrar no diário.
- **R3:** tudo de R2 + dry-run; limites mecânicos (fusível); checkpoint; idempotência quando aplicável; recuperação demonstrada; **autorização explícita do humano antes de executar**; verificação por um agente independente que não viu o trabalho, **preferencialmente de outro modelo**, ou pelo humano diretamente.

---

## 3. Autonomia

O que você pode fazer sozinho está definido em **10.3 Autonomia**.
Se a ação não está listada como permitida, ela **não** está permitida. Lacuna na política significa "perguntar", nunca "pode".

---

## 4. Investigar antes de teorizar

Quando algo falhar, nesta ordem:

1. Confirme que o componente **executou**.
2. Confirme ambiente, versão e configuração ativa.
3. Meça a saída real.
4. Procure logs, métricas, estado persistido e mudanças recentes (commits, diário).
5. Só então formule a causa.

Resultado rápido demais, vazio demais ou perfeito demais também é sinal.
Número que contradiz sua hipótese é o achado mais importante, não ruído.

---

## 5. Evidência

### 5.1 Saída bruta, não paráfrase

Toda prova em R1, R2 e R3 deve conter:

- o **comando, consulta ou ação** exata que você executou;
- a **saída literal**, colada sem edição (se for longa: as linhas relevantes + total de linhas omitidas);
- **ambiente** (dev, staging, produção) e **momento**.

"Verifiquei que funcionou" não é prova. "Os 3.328 registros foram migrados" sem a consulta e a saída não é prova.

Se você não executou, escreva **NÃO EXECUTADO** e explique por quê.

### 5.2 Níveis de prova

P0 tipos/lint/build · P1 unidade · P2 integração · P3 caminho real · P4 produção controlada · P5 recuperação.
Prova inferior não substitui superior. Build verde não prova integração. Staging não prova produção.

### 5.3 Parede vermelha

Para correção de regressão em R2/R3: mostre o teste **falhando** com o defeito e **passando** com a correção. Um teste que fica verde nos dois casos não protege nada.

### 5.4 Autorrefutação

Antes de declarar qualquer tarefa R1, R2 ou R3 pronta, **tente provar que você está errado**.

Pergunte: se esta mudança estivesse quebrada, onde isso apareceria? Vá olhar lá. Procure o caso que você não testou, o estado que você presumiu, o caminho que você não executou.

Relate o que você tentou e o que encontrou. "Tentei refutar e não consegui" só vale com a lista do que foi tentado.

### 5.5 O que você não consegue ver

Você lê código. O humano vê comportamento. Há erros que só aparecem na tela, no tempo ou na percepção: frames pulando, animação travando, timing errado, layout "quase certo", resposta do agente do sistema que está tecnicamente correta mas soa estranha ou fora do esperado.

Para qualquer mudança que afete **interface, motion, design, tempo de resposta percebido ou o que o agente do sistema diz ao usuário**:

- você **não** pode declarar essa qualidade provada;
- escreva em NÃO PROVEI: `qualidade perceptiva — requer verificação humana`;
- diga ao humano exatamente **onde olhar e o que observar** (tela, fluxo, ação que dispara, o que seria o comportamento certo).

O humano não é só quem aprova. Ele é o sensor dessas classes de erro.

---

## 6. Regras de construção que este projeto exige

- **Estados explícitos.** Não use `null` ou vazio para significar coisas diferentes (`not_found`, `unavailable`, `failed`, `unauthorized`, `not_processed`).
- **Estado é estrutura, texto é apresentação.** Nunca decida comportamento com `message.includes(...)`.
- **Guardrail antes de instrução.** Se algo não pode acontecer, torne impossível (permissão, schema, enum, limite, API estreita). Não exponha numa tool uma capacidade que não deve ser usada.
- **Fusível em operação destrutiva.** Limite absoluto + percentual, escopo por tenant, dry-run e diff antes de executar. Os valores dos limites são do projeto (seção 10), nunca inventados.
- **Idempotência pela identidade da operação.** Pergunte o que torna duas execuções a mesma operação antes de escolher a chave.
- **Processos longos** têm checkpoint, retomada e parada segura.
- **Proveniência.** Conteúdo gerado por IA é marcado como tal até ser verificado.
- **Remova ao adicionar.** Toda mudança relevante responde: o que isto torna desnecessário?

---

## 7. Quando parar e perguntar

Pare quando:

- a ação ultrapassa a autonomia da seção 10.3;
- é R3 e não há autorização explícita nesta sessão;
- há ambiguidade com consequências materialmente diferentes;
- a evidência contradiz uma premissa decisiva do pedido;
- falta informação que não pode ser obtida tecnicamente.

**Não** pare para decisões técnicas reversíveis, nem pergunte o que pode ser descoberto no código, banco, logs ou documentação.

---

## 8. Registro e aprendizado

- Mudanças de arquitetura ou comportamento relevante atualizam a documentação **na mesma mudança**.
- Descobertas de R2/R3 vão para o diário de desenvolvimento.
- Quando um erro da mesma classe reaparecer, pergunte: por que isso ainda depende de alguém lembrar? Proponha a barreira mecânica (teste, constraint, type, schema, hook). Erro grave justifica a barreira na primeira vez.

---

## 9. Formato de entrega

**R0**
```
Alterado:
Verificado:
```

**R1**
```
Risco: R1 (gatilho: ...)
Fiz:
Provei: [comando + saída literal]
Tentei refutar: [o que procurei para provar que estava errado + resultado]
Não provei:
Olho humano: [onde olhar e o que observar — se houver qualidade perceptiva]
```

**R2 / R3**
```
Risco: R2|R3 (gatilho: ...)
MEDI: estado anterior + comando + saída literal
FIZ: mudanças realizadas
PROVEI: comando + saída literal + ambiente
TENTEI REFUTAR: o que procurei para provar que estava errado + resultado
NÃO PROVEI: limitações reais
OLHO HUMANO: onde olhar e o que observar (se houver qualidade perceptiva)
RISCO RESIDUAL: o que ainda pode falhar
ROLLBACK: como desfazer
REGISTREI: onde ficou o aprendizado
```

"Deve funcionar", "provavelmente" e "parece correto" são hipóteses. Nunca aparecem em PROVEI.

**Critério final:** antes de declarar encerrado, responda: observei o estado real? Posso mostrar o efeito? Tentei provar que estou errado? Existe algo aqui que só o olho humano consegue confirmar? Se eu estiver errado, o sistema detecta? Se falhar, dá para recuperar? Esse erro pode voltar pela mesma porta? Se alguma resposta importante for "não", não está encerrado.

---

## 10. Este projeto

> Esta seção é a única que muda de projeto para projeto. Preencha antes de usar.

### 10.1 Onde está a verdade

- Diário de desenvolvimento: `[caminho]` — **leia antes de concluir qualquer coisa sobre o sistema**
- Decisões de arquitetura: `[caminho]`
- Rodar testes: `[comando]`
- Rodar o app localmente: `[comando]`
- Banco de dev: `[como acessar]`
- Diferenças conhecidas entre dev e produção: `[lista]`

### 10.2 Caminhos sensíveis (disparam R2 automaticamente)

- `[ex.: supabase/migrations/]`
- `[ex.: src/auth/]`
- `[ex.: src/integrations/]`
- `[ex.: .env*]`

### 10.3 Autonomia

| Pode sozinho | Pode e reporta | Nunca sem o humano |
|---|---|---|
| R0 e R1 em dev | migração em dev; job com dry-run; nova dependência | qualquer ação em produção |
| rodar testes e consultas de leitura | alterar configuração de dev | apagar dados reais |
| | | enviar algo para fora |
| | | mudar permissões ou autenticação |
| | | gastar dinheiro ou créditos acima de `[valor]` |

### 10.4 Limites dos fusíveis

- Operação destrutiva recusa se afetar mais de `[N]` registros **e** mais de `[X]%` da tabela.
