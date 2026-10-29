# MÉTODO SULEIMAN

**Modelo de Engenharia para Sistemas com Agentes**

Um sistema não está correto porque o agente disse que terminou, porque o código compilou ou porque os testes ficaram verdes.

Está correto dentro do que foi demonstrado por evidência.

A autoridade vem da prova, não da confiança no agente.

---

## 0. Propósito

Este método define como humanos, agentes de IA e software trabalham juntos na construção e operação de sistemas reais.

Ele existe porque agentes são capazes, rápidos e úteis, mas também:

- trabalham com informação incompleta;
- inferem coisas plausíveis que podem estar erradas;
- perdem contexto entre sessões;
- confundem implementação com funcionamento;
- podem otimizar para concluir a tarefa em vez de provar a tarefa;
- produzem linguagem convincente mesmo quando a evidência é insuficiente;
- leem código, mas não veem o comportamento que o código produz na tela, no tempo e na percepção de quem usa.

O método não tenta eliminar essas propriedades.

Ele constrói o sistema de engenharia assumindo que elas existem.

A premissa central é:

> O agente propõe e executa.
> O sistema restringe e verifica.
> A evidência demonstra.
> O humano governa o risco e percebe o que a máquina não vê.

### Origem

Este método não nasceu de pesquisa bibliográfica. Nasceu da construção de um sistema real com agentes, por alguém que não lê código, validando pelo que aparece na interface. Cada regra surgiu de uma situação em que o agente afirmou, com convicção, que algo estava correto, e a tela mostrou o contrário.

Várias regras convergem com práticas consagradas de engenharia de software. Essa convergência, a partir de um ponto de partida independente, é tratada aqui como evidência de que as regras são verdadeiras, não como coincidência.

---

## 1. Três tipos de regra

Nem toda lição merece virar lei universal.

Por isso, toda regra do projeto pertence a uma destas três camadas.

### 1.1 Invariante

Princípio que deve permanecer verdadeiro independentemente da linguagem, framework ou projeto.

Exemplos:

- uma ação destrutiva precisa ter limites;
- ausência de dado e indisponibilidade não são a mesma coisa;
- uma afirmação operacional precisa ser verificável;
- ações de maior impacto exigem controles mais fortes.

Invariantes formam a camada curta e permanente do método.

### 1.2 Default

É a maneira preferida de implementar um invariante, mas pode ser substituída quando houver motivo técnico.

Exemplos:

- preferir identificadores estruturais a comparação de strings;
- começar operações como CLI;
- usar checkpoint em processos longos;
- preferir at-least-once quando perda for mais grave que duplicação.

Um default pode ser quebrado.

Mas deve ser quebrado conscientemente.

### 1.3 Evidência local

É algo medido em determinado sistema, momento e ambiente.

Exemplos:

- uma chamada levou 864 ms;
- determinado cache reduziu o custo em 90%;
- N=3 expôs determinado bug;
- um container possui 4 GB de limite;
- uma sincronização devolve no máximo 50 elementos.

Evidência local nunca vira lei universal automaticamente.

> Incidente gera hipótese.
> Hipótese recorrente gera princípio.
> Princípio demonstrado vira invariante.

---

## 2. Papéis e autoridade

### 2.1 O humano

O humano:

- define intenção;
- define restrições;
- define tolerância a risco;
- resolve ambiguidades de negócio;
- aprova ações cujo impacto ultrapassa a autonomia concedida ao sistema.

O humano não precisa revisar cada linha de código.

Ele precisa conseguir entender:

- o que mudou;
- qual evidência existe;
- qual risco permanece;
- como reverter.

#### O humano também é sensor

O humano não é só governante. Ele é a única parte do sistema capaz de perceber certas classes de erro.

O agente lê o código, ou seja, a intenção. O humano vê a tela, ou seja, o comportamento. Quando o agente afirma que algo está correto, frequentemente está certo dentro do que consegue ver: o código faz, no papel, o que deveria fazer. Mas ele nunca viu o frame pulando, a animação travando, o layout quase certo, a resposta que está tecnicamente correta e soa errada.

Isso não é mentira do agente. É cegueira estrutural.

Qualidades como fluidez, timing, ritmo, tom, coerência visual e adequação de uma resposta ao contexto são, hoje, verificáveis principalmente por percepção humana treinada. Para essas qualidades:

- o agente não pode declarar a qualidade provada;
- o agente deve dizer ao humano onde olhar e o que observar;
- a verificação humana é evidência P3 legítima, não opinião.

Um olho treinado em imagem, movimento ou comportamento é um instrumento de medição. O método o trata como tal.

### 2.2 O agente

O agente:

- investiga;
- propõe;
- implementa;
- testa;
- mede;
- tenta refutar o próprio trabalho;
- registra;
- identifica incerteza;
- declara o que não consegue perceber.

O agente nunca deve usar sua própria afirmação como evidência de sucesso.

"Eu implementei" é relato.

"Este teste, esta consulta e este comportamento observável mostram o efeito" é evidência.

### 2.3 O sistema

Código, banco, schemas, permissões, filas, infraestrutura, políticas e observabilidade constituem o sistema.

O sistema deve assumir responsabilidades que possam ser mecanicamente verificadas.

Sempre que possível:

> não peça ao agente que se comporte corretamente quando você pode tornar o comportamento incorreto impossível.

- Permissão é melhor que instrução.
- Schema é melhor que recomendação.
- Enum é melhor que texto livre.
- Limite é melhor que promessa.
- Idempotência é melhor que "não execute duas vezes".

---

## 3. Engenharia proporcional ao risco

Nem toda mudança merece o mesmo ritual.

Aplicar o processo máximo a qualquer alteração transforma engenharia em burocracia.

Cada tarefa recebe uma classe de risco.

### R0 — Local e trivial

Mudança:

- totalmente reversível;
- sem persistência relevante;
- sem efeito externo;
- sem impacto financeiro;
- sem segurança;
- sem produção.

Exemplos: espaçamento, texto, refatoração local, rename, melhoria visual isolada.

**Exigência:** entender o impacto; executar verificação apropriada; não introduzir regressão evidente. Não exige relatório operacional completo.

### R1 — Comportamento reversível

Mudança altera lógica real, mas possui impacto limitado e reversível.

Exemplos: nova validação, componente novo, mudança de regra interna, endpoint interno, fluxo de interface.

**Exigência:** identificar comportamento anterior; implementar; teste adequado; executar caminho relevante; tentar refutar; declarar o que não foi testado.

### R2 — Produção, dados ou integração

Mudança afeta banco, sincronização, filas, APIs externas, infraestrutura, memória, busca, modelos ou comportamento de produção.

**Exigência:** medir antes; estabelecer baseline; identificar regressões possíveis; definir rollback; testar integração; validar ambiente real ou equivalente; observar depois da mudança; registrar descoberta relevante.

### R3 — Alto impacto

Mudança destrutiva, irreversível, financeira, legal, relacionada a segurança ou permissões, que dispara comunicação externa, afeta grande quantidade de dados ou pode interromper operação relevante.

**Exigência:** tudo de R2, mais limites mecanicamente impostos; dry-run quando aplicável; checkpoint; idempotência quando aplicável; recuperação demonstrável; autorização explícita dentro da política de autonomia; evidência posterior ao efeito.

O processo cresce com o custo do erro.

### 3.1 Quem classifica o risco

A classificação de risco não pode depender só do julgamento de quem executa a tarefa. Quem quer terminar tende a classificar para baixo.

Por isso:

- cada projeto define **gatilhos objetivos** (caminhos, tipos de operação, ambientes) que impõem um nível mínimo;
- o agente pode **subir** o nível por julgamento, nunca **descer**;
- só o humano rebaixa um nível;
- em dúvida entre dois níveis, vale o maior.

O risco de uma mudança é propriedade do que ela toca, não da confiança de quem a faz.

---

## 4. Verdade operacional

### 4.1 O placar não se digita

Números sobre o sistema devem vir de uma fonte reproduzível.

Não:

> "Há cerca de 3.300 registros."

Preferir:

> 3.328 registros em `entidade_ocorrencias`, produção, medidos por `SELECT count(*)...`

Quando material, todo número carrega: objeto medido, ambiente, momento e método de medição.

### 4.2 Meça antes de explicar

A primeira obrigação de uma investigação não é produzir uma teoria. É estabelecer fatos.

- Antes de perguntar "por que o extrator está retornando vazio?", pergunte: "o extrator executou?"
- Antes de "por que a busca ficou pior?", pergunte: "a configuração nova está realmente ativa?"
- Antes de "por que produção está lenta?", pergunte: "onde o tempo está sendo gasto?"

### 4.3 Ausência e falha são estados diferentes

Nunca represente estados semanticamente diferentes pelo mesmo valor quando eles produzem decisões diferentes.

Evite `null` quando ele pode significar `não_encontrado`, `indisponível`, `não_processado`, `erro` ou `não_autorizado`.

Modelar estados explicitamente reduz decisões falsas.

### 4.4 Nome correto faz parte da medição

Quantidade sem semântica não é conhecimento.

996 registros, eventos, ocorrências, relações ou documentos são medidas diferentes.

Nome errado transforma número verdadeiro em conclusão falsa.

### 4.5 História é parte da evidência

O estado atual sozinho pode enganar.

Antes de diagnosticar algo relevante, consulte commits, decisões, incidentes anteriores, changelog, diário técnico e configuração ativa.

Um comportamento estranho pode ser exatamente uma mudança recém-introduzida.

---

## 5. Hierarquia da prova

"Passou no teste" não é uma categoria única. Existem níveis diferentes de evidência.

- **P0 — Evidência estática.** Compilação, tipos, lint, schema, análise estática. Demonstra propriedades estruturais. Não prova comportamento real.
- **P1 — Unidade.** Unit tests, property tests, invariantes, casos de limite. Demonstra comportamento de uma unidade isolada.
- **P2 — Integração.** Banco + aplicação, fila + consumidor, API + transformação, serviço + modelo. Demonstra que partes trabalham juntas.
- **P3 — Caminho real.** Navegador, API pública, CLI real, job real, fluxo end-to-end, e a verificação perceptiva humana de interface, motion e comportamento. Demonstra que o comportamento pretendido ocorre no fluxo utilizado.
- **P4 — Produção controlada.** Canary, shadow traffic, feature flag, observação controlada, rollout progressivo. Ambiente semelhante não é produção; é evidência anterior à produção.
- **P5 — Recuperação.** Restore de backup, rollback, retomada de job, replay de evento, failover, recuperação de checkpoint. Para sistemas críticos, não basta provar que funciona. É preciso provar que falha de maneira recuperável.

---

## 6. A regra da parede vermelha

Um teste só demonstra proteção contra uma regressão se for capaz de detectar a regressão.

Quando o custo justificar:

1. crie ou identifique o teste;
2. confirme que ele passa com o comportamento correto;
3. reproduza ou simule o defeito;
4. confirme que o teste falha;
5. restaure o comportamento correto.

Isso evita a falsa segurança de testes que permanecem verdes independentemente do erro.

Não é necessário fazer isso manualmente em toda alteração. É proporcional ao risco.

> Nota: esta regra converge com o ciclo *red, green, refactor* do TDD (desenvolvimento guiado por testes) e com o *mutation testing*. Chegou-se a ela de forma independente, pela semiótica do semáforo: vermelho sinaliza que algo não está bom, verde sinaliza passagem.

---

## 7. Casos, limites e generalização

Não existe um número mágico universal de elementos para provar uma estrutura.

A regra é:

> Teste no menor caso em que caminhos diferentes passam a divergir.

Depois cubra, quando relevante: zero, um, muitos, primeiro, último, limites, retomada, duplicação, concorrência, ausência, erro.

Para sistemas complexos, exemplos específicos devem ser complementados por propriedades.

---

## 8. Produção não é desenvolvimento

Dev, staging e produção são fontes de evidência diferentes.

Uma prova em desenvolvimento significa: funciona sob estas condições. Não significa automaticamente: funciona em produção.

Diferenças importantes devem ser conhecidas e minimizadas: versão, imagem, memória, CPU, modelo, flags, volumes, rede, permissões, escala, dados.

Quanto maior o risco, mais próxima do ambiente real deve ser a prova.

---

## 9. Observabilidade

Um comportamento importante precisa deixar evidência suficiente para ser compreendido depois.

Isso não significa logar tudo. Utilize a ferramenta adequada:

- logs para eventos;
- métricas para comportamento agregado;
- traces para caminhos distribuídos;
- auditoria para ações relevantes;
- eventos estruturados para transições.

O requisito não é "todo caminho gera log". O requisito é "todo caminho importante pode ser explicado".

Falhas silenciosas em caminhos críticos são defeitos.

---

## 10. Guardrails antes de inteligência

Se determinada ação não pode acontecer, não dependa apenas de prompt.

Restrinja permissões, ferramentas, parâmetros, IDs, schemas, ranges, quotas e estados permitidos.

- Em vez de "nunca exclua clientes", prefira uma ferramenta que simplesmente não possua essa capacidade.
- Em vez de "só escolha IDs válidos", forneça IDs válidos estruturalmente.
- Em vez de "não faça transferências acima de R$ X", faça a operação recusar mecanicamente valores acima do teto autorizado.

---

## 11. Autonomia progressiva

Autonomia não é binária.

Ela deve crescer de acordo com reversibilidade, impacto, observabilidade, maturidade das proteções e capacidade de recuperação.

- **A0 — Sugerir.** O agente analisa e propõe. Não altera.
- **A1 — Executar localmente.** Pode executar ações reversíveis sem efeito externo.
- **A2 — Executar dentro de limites.** Pode realizar ações pré-autorizadas com escopo definido, limites, auditoria e rollback quando necessário.
- **A3 — Operar autonomamente.** Só para ações cujo risco residual esteja suficientemente controlado por engenharia.

Ações que ultrapassam a autonomia concedida escalam para humano.

Não existe "humano sempre no loop" nem "agente sempre autônomo". Existe autoridade proporcional ao risco.

### 11.1 A política é escrita, não presumida

Cada projeto declara por escrito o que o agente pode fazer sozinho, o que pode fazer reportando e o que nunca faz sem o humano.

Lacuna na política significa **perguntar**, nunca **permitido**. Um agente diante de uma regra ausente tende a interpretá-la a favor de concluir a tarefa.

A política sobe de nível quando as proteções amadurecem, não quando o agente parece confiável.

---

## 12. Linguagem não é evidência

Um agente pode afirmar: "A migração foi executada corretamente."

Isso é uma reivindicação.

Procure o efeito que necessariamente deveria existir se ela fosse verdadeira: linha no banco, versão ativa, checksum, arquivo, resposta da API, evento, mudança de estado.

Antes de dizer "feito", observe onde o efeito deveria estar.

### 12.1 Quem fez não é quem verifica

A evidência também pode ser apenas relato.

Se o mesmo agente que fez a mudança executa a verificação e **resume** o resultado, o humano recebe a prova filtrada pela mesma fonte que o método manda desconfiar. É o contador auditando a própria contabilidade: mesmo honesto, ele tem os mesmos pontos cegos.

Proporcional ao risco:

- **R1 — evidência bruta.** A prova inclui o comando executado e a saída literal, sem paráfrase. O humano pode conferir sem confiar em quem resumiu.
- **R2 — verificação fora do agente.** Pelo menos parte da prova vem de algo que o agente não controla: testes automáticos que rodam a cada mudança, hooks, constraints do banco, ou checagem do humano pela interface.
- **R3 — verificador independente.** Um segundo agente, que não viu o trabalho sendo produzido, recebe só o objetivo e o estado final e tenta demonstrar que está errado. Ou o humano verifica diretamente o efeito.

Isso pesa mais quando o humano não lê código. Nesse caso, a evidência é a única janela para o real e precisa ser tratada como tal.

#### Independência de verdade

Um verificador só é independente na medida em que pode errar de forma diferente de quem fez.

O mesmo modelo, com outro prompt ou outra "personalidade", tende a compartilhar os mesmos pontos cegos: mesmo treino, mesmas suposições, mesmos atalhos. Cinco máscaras no mesmo rosto erram do mesmo jeito, e a concordância entre elas vale menos do que parece.

Por isso, em R3, e sempre que o custo justificar:

- o verificador deve ser, quando possível, **um modelo diferente** do que produziu o trabalho;
- o verificador recebe o objetivo e o estado final, não o raciocínio de quem fez;
- concordância entre verificadores é sinal, não prova. Prova continua sendo o efeito observado no sistema real.

Mesmo verificadores diferentes podem errar juntos. Independência reduz erros correlacionados; não os elimina. Por isso ela complementa a evidência do sistema e nunca a substitui.

### 12.2 Autorrefutação

Antes de declarar uma tarefa pronta, o agente tenta provar que está errado.

A pergunta não é "isto funciona?". É: **"se isto estivesse quebrado, onde apareceria?"** E então ir olhar lá.

Procurar o caso não testado, o estado presumido, o caminho não executado, a dependência que mudou, a tela que ninguém abriu.

A autorrefutação é a versão mais barata do verificador independente. Não substitui o verificador em R3, mas deve existir a partir de R1.

"Tentei refutar e não consegui" só vale acompanhado da lista do que foi tentado. Sem a lista, é só outra forma de afirmar sucesso.

### 12.3 Qualidade perceptiva

Há uma classe de defeitos que o agente não percebe porque não está no código; está no comportamento que o código produz:

- frames pulando, animação travando, transição fora de ritmo;
- layout que está certo no componente e errado na tela;
- latência que os números aceitam e a percepção rejeita;
- resposta de um agente do sistema que está tecnicamente correta e soa estranha, fria ou fora do contexto.

Para qualquer mudança que afete interface, motion, design, tempo percebido ou o que um agente do sistema diz ao usuário:

- o agente declara essa qualidade em **NÃO PROVEI**;
- o agente indica **onde olhar e o que observar**: a tela, a ação que dispara, o comportamento esperado;
- a tarefa só fecha quando o humano observou.

Isso não é desconfiança do agente. É reconhecer que cada parte do sistema tem sentidos diferentes.

---

## 13. Proveniência

Informação relevante deve carregar origem suficiente para que seja auditável.

Dependendo do caso: `source`, `author`, `timestamp`, `environment`, `version`, `confidence`, `verified_by`, `derived_from`.

O sistema pode usar material produzido por agentes. Mas deve saber que foi produzido por um agente.

Material gerado pode evoluir de `agent_generated` para `human_verified` ou `system_verified`.

A solução para contaminação não é esquecer tudo que a IA produziu. É preservar proveniência.

---

## 14. Memória e crença

Memória não é armazenamento indiscriminado. É um sistema de crenças com proveniência.

Toda afirmação persistente relevante deve responder, quando necessário:

- de onde veio?
- quem afirmou?
- quando?
- continua válida?
- foi verificada?
- contradiz informação posterior?

Diferencie: observação, proposta, decisão, política, fato confirmado, hipótese, conteúdo gerado.

Um briefing não vira regra empresarial automaticamente. Uma ocorrência isolada não substitui política.

Experiência nova pode propor atualização da crença; não precisa silenciosamente reescrever a história.

---

## 15. Artefato + contexto

Arquivos possuem conteúdo. Mas seu significado operacional frequentemente depende do contexto ao redor.

Preserve quando possível: artefato + conversa + autor + momento + projeto + decisão relacionada.

Uma imagem pode mostrar algo. A mensagem "essa é a versão aprovada pelo cliente" muda completamente o significado dessa imagem para a organização.

O contexto não substitui o artefato. Os dois se complementam.

---

## 16. Arquitetura sem divergência

### 16.1 Uma fonte canônica

Quando diferentes consumidores precisam da mesma informação em formatos diferentes: mantenha uma fonte canônica e derive representações.

Evite dois estados independentes representando a mesma verdade.

### 16.2 Semântica é estrutural

Quando o comportamento depende do significado de um resultado, represente esse significado estruturalmente.

Preferir `status: extraction_failed` a `message.includes("não consegui extrair")`.

Strings são apresentação. Estado é estrutura.

### 16.3 Regra comum mora no caminho comum

Se todos os membros de uma família precisam da mesma proteção, a proteção deve existir no ponto compartilhado.

Copiar a mesma linha para N implementações cria N oportunidades de esquecimento.

### 16.4 Configuração e comportamento são coisas diferentes

Diferenças de configuração pertencem a dados: limite, endpoint, tenant, modelo, identidade.

Diferenças reais de comportamento pertencem a abstrações explícitas: strategy, adapter, policy, plugin.

Evite condicionais de cliente espalhadas pelo núcleo.

---

## 17. Tempo é estado

Jobs longos e retomáveis possuem semântica temporal.

Ao transformar uma execução única em várias passadas, classifique cada valor como: valor da execução inteira, valor da passada, valor do item ou valor do momento atual.

Persistir o nível errado pode produzir erros silenciosos, especialmente em cursores, timestamps, janelas, contagens, checkpoints e paginação.

---

## 18. Processos longos devem ser interrompíveis

Quanto maior uma execução, mais importantes são o checkpoint, a retomada, a idempotência e a capacidade de parar.

Quando possível: parar não deve destruir trabalho válido já realizado.

---

## 19. Operações destrutivas precisam de fusível

Toda operação potencialmente destrutiva em escala deve considerar limites: absoluto, percentual, escopo, tempo, tenant, tipo.

Um padrão útil pode ser:

```
se quantidade > X
E percentual > Y
então recusar
```

Os números X e Y são configuração local, não lei universal.

Para operações de maior risco: dry-run, amostra, diff, autorização, rollback, audit trail.

---

## 20. Duplicação versus perda

Não existe uma política universal. Decida explicitamente.

- **Quando perda custa mais:** pode ser adequado usar at-least-once + idempotência. Duplicação é detectável e corrigível.
- **Quando duplicação custa mais:** pode ser adequado usar at-most-once ou mecanismos transacionais mais fortes. Exemplos sensíveis: pagamentos, e-mails, emissão fiscal, créditos, comandos físicos.

A política nasce do custo relativo de perda e duplicação.

---

## 21. Idempotência representa identidade da operação

Escolha a chave de idempotência conforme a semântica: event ID, request ID, external ID, operation ID, combinação de campos ou, em alguns casos, conteúdo.

Não use hash de conteúdo apenas porque é conveniente.

Pergunte: "O que torna estas duas execuções a mesma operação?"

---

## 22. Dados ruins entram em quarentena

Não dependa de inspeção humana como primeira defesa.

Use critérios determinísticos quando existirem: origem de teste, timestamp impossível, tenant inválido, entidade inexistente, tipo incompatível, checksum, schema, volume anômalo.

Depois, se necessário, revisão humana.

---

## 23. Extração não é compreensão

Texto extraído de um documento não prova que seu conteúdo foi recuperado adequadamente.

Meça sinais de qualidade: volume, páginas, elementos encontrados, estrutura, contraste com tipo esperado.

Quando a modalidade for insuficiente, use outra: parser → OCR → visão → combinação. A estratégia depende do documento.

---

## 24. Custo e desempenho são medidos, não intuídos

Não otimize por dogma.

Meça separadamente: latência do modelo, do provedor, de rede, roteamento, retrieval, tools, serialização, fila, banco, UI.

Trocar modelo sem localizar o gargalo é adivinhação.

---

## 25. Cache é comportamento de sistema

Quando houver cache de prompt ou equivalente, trate-o como parte da arquitetura de custo.

Meça: hit rate, estabilidade do prefixo, partes variáveis, custo com hit, custo sem hit.

Valores medidos em um provedor ou sistema são evidência local. Nunca os transforme em constante universal.

---

## 26. Recursos são medidos onde o processo realmente vive

Não conclua capacidade pela máquina nominal.

Observe os limites efetivamente impostos ao workload: container, VM, processo, GPU, quota, scheduler.

Inclua quando relevante: aplicação, buffers, caches, KV cache, overhead, processos auxiliares.

---

## 27. Alertas precisam ser acionáveis

Um alerta deve produzir alguma ação possível: automática, operacional, investigativa ou humana.

Se repetidamente ninguém sabe o que fazer com um alerta, ele precisa ser corrigido, agregado, reclassificado ou removido.

Ruído corrói atenção.

---

## 28. O cérebro externo

Agentes não devem depender apenas da memória da sessão.

Projetos precisam de memória externa verificável: `ARCHITECTURE.md`, `DECISIONS.md`, `INCIDENTS.md`, `RUNBOOK.md`, `CHANGELOG.md`, diário técnico, commits, schemas, testes.

A documentação importante para decisões deve permanecer atual.

Documento desatualizado que parece autoritativo pode ser pior que ausência de documento.

### 28.1 Contexto do agente é atenção limitada

Tudo que entra no contexto do agente compete por atenção. Instrução genérica dilui instrução crítica.

O contrato operacional (`AGENTS.md`) contém só o que muda o comportamento do agente. Teste para cada regra: **se eu apagar isto, o agente se comporta diferente?** Se não, a regra pertence a este documento, não ao contrato.

O contrato aponta para este documento por **gatilho objetivo** ("antes de R2/R3, leia as seções relevantes"), não por "consulte em caso de dúvida". Um agente nem sempre percebe que está em dúvida; produzir confiança plausível é justamente a fraqueza que o método descreve.

### 28.2 Regra que vive na cabeça se perde

Toda prática que o humano aplica de forma recorrente (pedir que o agente se refute, olhar uma tela específica, desconfiar de um tipo de resposta) deve ser escrita no contrato. O que não está escrito não sobrevive à troca de sessão, de agente ou de pessoa.

---

## 29. A regra da terceira recorrência

Quando a mesma classe de erro reaparece, documentação pode não ser suficiente.

Como default: erro recorrente deve virar barreira mecânica: teste, schema, type, lint, constraint, migration guard, permission, monitor, API, abstraction.

"Três vezes" é uma heurística útil, não número mágico.

Se o erro for grave, a primeira ocorrência pode justificar a parede.

### 29.1 O método se aplica a si mesmo

Este método diz que restrição mecânica é melhor que instrução. Ele próprio é, em grande parte, instrução.

Portanto, as regras mais caras de violar devem migrar do texto para a máquina:

- comandos destrutivos bloqueados por hook antes de executar;
- formato de entrega R2/R3 conferido automaticamente;
- caminhos sensíveis forçando nível de risco;
- testes rodando a cada mudança, independentemente de alguém pedir.

Quando uma regra do `AGENTS.md` for violada mais de uma vez, ela é candidata a deixar de ser texto.

---

## 30. Lições precisam generalizar

Um incidente registrado deve responder:

- O que aconteceu?
- Por que aconteceu?
- Qual sinal já existia?
- Por que não o percebemos?
- Onde mais essa classe de erro poderia acontecer?
- Qual proteção impede repetição?

Sem generalização, o diário vira coleção de histórias. Com generalização, ele vira engenharia.

---

## 31. O loop central

1. **MEDIR** — Estabeleça o estado real.
2. **CONTRASTAR** — Compare o que foi observado com o que se acreditava. O achado mais valioso frequentemente é aquilo que contradiz a hipótese inicial.
3. **EXPLICAR** — Encontre a causa, sem confundir correlação com causalidade.
4. **CORRIGIR** — Faça a menor mudança que restaure o invariante desejado.
5. **PROVAR** — Produza evidência proporcional ao risco. Inclui tentar refutar o próprio trabalho e, quando houver qualidade perceptiva, a observação humana.
6. **GENERALIZAR** — Pergunte: "Que classe de erro é esta?"
7. **EMPAREDAR** — Quando justificar, transforme a lição em proteção permanente.
8. **REGISTRAR** — Preserve a descoberta para que outro humano ou agente não tenha que reaprendê-la.

```
MEDIR
  ↓
CONTRASTAR
  ↓
EXPLICAR
  ↓
CORRIGIR
  ↓
PROVAR
  ↓
GENERALIZAR
  ↓
EMPAREDAR
  ↓
REGISTRAR
  └────────→ próximo ciclo
```

---

## 32. Conferência antes de mudanças relevantes

Para R2 e R3, antes de alterar o sistema, estabeleça:

- **O que existe** — com referência concreta ao código ou sistema.
- **O que não existe** — o que o pedido pressupõe, mas ainda não está implementado.
- **O que será alterado** — componentes, dados, fluxos e interfaces afetados.
- **O que pode regredir** — comportamentos atualmente corretos que dependem da área modificada.
- **Como voltar** — rollback, restore, flag, versão ou outro mecanismo.
- **Como saberemos que funcionou** — defina a evidência antes da implementação, incluindo o que o humano precisará observar.

---

## 33. Não adicione sem olhar o que pode desaparecer

Toda mudança relevante deve perguntar: "O que esta implementação torna desnecessário?"

Pode ser duplicação, branch antigo, workaround, estado redundante, flag, documentação, código morto, caminho paralelo.

Se cada mudança apenas adiciona coisas, complexidade cresce indefinidamente.

---

## 34. Relatório proporcional ao risco

**R0**
```
Alterado:
Verificado:
```

**R1**
```
Fiz:
Provei: [comando + saída literal]
Tentei refutar:
Não provei:
Olho humano:
```

**R2 / R3**
```
MEDI           Estado anterior, ambiente e método.
FIZ            Mudança realizada.
PROVEI         Evidências que sustentam o resultado, com saída literal.
TENTEI REFUTAR O que foi procurado para provar o erro, e o resultado.
NÃO PROVEI     Limitações explícitas da validação.
OLHO HUMANO    Onde olhar e o que observar, se houver qualidade perceptiva.
RISCO RESIDUAL O que ainda pode falhar.
REGISTREI      Decisão, incidente ou lição persistida.
```

Nunca substitua ausência de prova por "deve funcionar", "provavelmente" ou "parece correto".

Essas expressões são permitidas como hipótese. Não como descrição de algo demonstrado.

---

## 35. Quando o agente deve parar

O agente interrompe e pede decisão quando faltar autoridade, não simplesmente quando surgir dificuldade.

Pare quando:

- a operação ultrapassar sua autonomia;
- houver ambiguidade com consequências materialmente diferentes;
- faltar informação que não possa ser obtida tecnicamente;
- o risco estiver acima das proteções existentes;
- uma ação irreversível não estiver previamente autorizada;
- evidência contradizer premissa decisiva do humano.

Não pare apenas porque existem várias implementações possíveis, uma decisão técnica reversível precisa ser tomada, o problema é difícil ou não existe certeza absoluta.

Agentes devem resolver decisões técnicas reversíveis. Humanos devem governar consequências.

---

## 36. Perguntas e interrupções

Perguntas têm custo operacional. Portanto:

- não pergunte o que pode ser descoberto;
- não bloqueie por preferência irrelevante;
- não transforme elicitação em formulário;
- mostre o benefício de responder.

Mas: uma pergunta deve bloquear quando prosseguir sem resposta cria risco superior ao custo de interromper.

---

## 37. Operação antes de interface para agentes

Quando útil, ferramentas operacionais devem possuir uma interface determinística que humanos e agentes possam executar.

Frequentemente `função → CLI/API → agente → UI` é melhor que `UI → automação frágil do agente`.

Não é necessário que literalmente toda ferramenta comece por CLI. O princípio é: o agente deve operar interfaces explícitas, observáveis e reproduzíveis sempre que possível.

---

## 38. Adoção também é engenharia

Sistemas sociotécnicos falham mesmo quando o código funciona.

Não redesenhe processos humanos inteiros antes de entender como trabalham.

Primeiro observe: onde existe dor, quem sente, quando acontece, qual decisão é tomada, qual informação falta.

Depois insira o sistema no ponto de maior alavancagem.

Tecnologia deve reduzir trabalho. Não criar ritual apenas para alimentar a tecnologia.

---

## 39. UX mede espera percebida e espera real

Latência deve ser discutida contra aquilo que bloqueia.

Diferencie: tempo total, tempo até primeiro feedback, tempo até interação, tempo até conclusão.

É possível melhorar percepção sem fingir conclusão. Começar uma animação ou mostrar progresso imediatamente pode melhorar experiência. Não deve mascarar estado real.

A espera percebida é uma qualidade perceptiva (seção 12.3): números de latência não substituem alguém usando o sistema.

---

## 40. O que este método rejeita

- **Engenharia por afirmação** — "O agente falou que fez."
- **Engenharia por verde** — "Os testes passaram, então tudo funciona."
- **Engenharia por intenção** — "O código deveria impedir."
- **Engenharia por documentação** — "Está escrito no README."
- **Engenharia por memória** — "Acho que já resolvemos isso."
- **Engenharia por dogma** — "Sempre fazemos assim."
- **Engenharia por velocidade sem recuperação** — "Deploy primeiro, entendemos depois."
- **Engenharia cega** — "O código está certo, então a tela está certa."

---

## 41. O que este método procura

O objetivo não é ausência de falhas. É construir sistemas nos quais:

- erros importantes sejam difíceis de cometer;
- falhas sejam visíveis;
- estados sejam explicáveis;
- ações tenham proveniência;
- operações perigosas tenham limites;
- mudanças sejam reversíveis quando possível;
- aprendizado sobreviva à sessão;
- recorrência gere proteção;
- autonomia cresça junto com guardrails;
- evidência seja proporcional ao risco;
- cada parte (humano, agente, sistema) verifique aquilo que consegue perceber.

---

## 42. As invariantes portáteis

Se todo o restante precisar ser resumido, preserve estas:

1. Meça antes de explicar.
2. Toda afirmação operacional importante precisa de evidência reproduzível.
3. Ausência, erro e indisponibilidade são estados diferentes.
4. Teste demonstra apenas aquilo que efetivamente perguntou.
5. Quanto maior o impacto, mais forte deve ser a prova.
6. Prefira restrições mecânicas a instruções comportamentais.
7. Autonomia deve ser proporcional à reversibilidade e ao risco.
8. Informação importante carrega proveniência.
9. Operações de escala possuem limites e mecanismos de recuperação.
10. Erros recorrentes devem virar proteção permanente.
11. Evidência local não vira lei universal sem generalização.
12. O agente não é a autoridade sobre o próprio sucesso; o sistema observado é.
13. Quem fez não é quem verifica, na proporção do risco.
14. O risco é definido pelo que a mudança toca, não pela confiança de quem a faz.
15. Antes de declarar pronto, tente provar que está errado.
16. O que só o olho humano percebe, só o olho humano prova.

---

## 43. A frase central

O Método Suleiman pode ser reduzido a uma frase:

> Observe o real, encontre a diferença, corrija o mínimo, prove proporcionalmente ao risco e transforme erros valiosos em propriedades permanentes do sistema.

Ou, operacionalmente:

> IA propõe.
> Engenharia restringe.
> Sistema executa.
> Evidência demonstra.
> Humano percebe e governa o risco.
> Erro ensina.
> Código aprende.

Esse é o ciclo.

---

## 44. Convergências e referências

Este método foi construído a partir da prática, sem pesquisa prévia. Depois de escrito, várias regras se mostraram convergentes com ideias consagradas. Elas estão registradas aqui por honestidade intelectual e para quem quiser se aprofundar.

A convergência não é a origem das regras. É evidência de que elas descrevem algo real.

| Regra do método | Converge com | Referência |
|---|---|---|
| Parede vermelha (seção 6) | Ciclo *red, green, refactor* do TDD (desenvolvimento guiado por testes) | Kent Beck, *Test-Driven Development: By Example*, Addison-Wesley, 2002 |
| Parede vermelha (seção 6) | *Mutation testing*: introduzir defeitos de propósito para ver se os testes os detectam | DeMillo, Lipton e Sayward, "Hints on Test Data Selection", *IEEE Computer*, 1978 |
| Não adicione sem olhar o que pode desaparecer (seção 33) | Etapa *refactor* do TDD | Kent Beck, obra citada |
| Hierarquia da prova P0–P5 (seção 5) | Pirâmide de testes | Mike Cohn, *Succeeding with Agile*, 2009; Martin Fowler, "TestPyramid" — https://martinfowler.com/bliki/TestPyramid.html; Ham Vocke, "The Practical Test Pyramid" — https://martinfowler.com/articles/practical-test-pyramid.html |
| Fusível em operações destrutivas (seção 19) | Padrão *Circuit Breaker* (disjuntor) | Michael Nygard, *Release It!*, Pragmatic Bookshelf, 2007; Martin Fowler, "CircuitBreaker" — https://martinfowler.com/bliki/CircuitBreaker.html |
| Duplicação versus perda; idempotência (seções 20 e 21) | Semânticas de entrega *at-least-once* / *at-most-once* em sistemas distribuídos | Martin Kleppmann, *Designing Data-Intensive Applications*, O'Reilly, 2017 |
| Proveniência (seção 13) | Modelo PROV do W3C | W3C, "PROV-Overview" — https://www.w3.org/TR/prov-overview/ |
| Memória e crença com tempo (seções 14 e 17) | Modelagem bitemporal | Martin Fowler, "Bitemporal History" — https://martinfowler.com/articles/bitemporal-history.html |
| Observabilidade, alertas acionáveis, produção não é desenvolvimento (seções 8, 9, 27) | Engenharia de confiabilidade (SRE) | Beyer, Jones, Petoff e Murphy (orgs.), *Site Reliability Engineering*, Google/O'Reilly, 2016 — https://sre.google/sre-book/table-of-contents/ |
| Quem fez não é quem verifica (seção 12.1) | Revisão por pares; auditoria independente | Prática geral da ciência e da contabilidade |
| Verificador de modelo diferente (seção 12.1) | Conselho de modelos com revisão anônima | Andrej Karpathy, *llm-council*, 2025 — https://github.com/karpathy/llm-council |
| Verificador de modelo diferente (seção 12.1) | *N-version programming*: versões desenvolvidas de forma independente para reduzir falhas comuns | Algirdas Avižienis, "The N-Version Approach to Fault-Tolerant Software", *IEEE Transactions on Software Engineering*, 1985 |
| Independência reduz, mas não elimina, erros correlacionados (seção 12.1) | Evidência experimental de que versões "independentes" falham juntas mais do que o acaso prevê | Knight e Leveson, "An Experimental Evaluation of the Assumption of Independence in Multiversion Programming", *IEEE Transactions on Software Engineering*, 1986 |

O que não aparece nesta tabela é, até onde se sabe, contribuição própria do método. Em especial, a síntese como um todo: um modelo de engenharia para quem constrói sistemas reais **por meio de agentes, sem ler o código**, em que a evidência substitui a revisão de código e a percepção humana treinada é tratada como instrumento de medição.
