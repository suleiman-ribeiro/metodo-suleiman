# Método Suleiman

**Modelo de engenharia para sistemas reais construídos com agentes** — por alguém que não lê código e valida pelo que aparece na tela.

> Observe o real, encontre a diferença, corrija o mínimo, prove proporcionalmente ao risco e transforme erros valiosos em propriedades permanentes do sistema.

Ou, operacionalmente:

> IA propõe. Engenharia restringe. Sistema executa. Evidência demonstra. Humano percebe e governa o risco. Erro ensina. Código aprende.

## O que tem aqui

- [`ENGINEERING_METHOD.md`](ENGINEERING_METHOD.md) — **o método: por quê.** 44 seções: os três tipos de regra, papéis e autoridade (o humano também é sensor), engenharia proporcional ao risco (R0–R3), verdade operacional, a hierarquia da prova (P0–P5), a parede vermelha, autorrefutação, quem fez não é quem verifica, guardrails antes de inteligência, autonomia progressiva, proveniência, memória e crença, o loop central, e a tabela de convergências com a engenharia consagrada.
- [`AGENTS.md`](AGENTS.md) — **o contrato: como trabalhar.** O arquivo que um agente de engenharia lê antes de qualquer tarefa: classificação de risco por gatilhos objetivos, o que cada nível exige, evidência bruta em vez de paráfrase, o formato de entrega por risco. A seção 10 é um **molde**: preencha com os caminhos, comandos e limites do seu projeto.

## Como usar

1. Copie o `AGENTS.md` para a raiz do seu repositório e preencha a seção 10 (onde está a verdade, caminhos sensíveis, autonomia, limites dos fusíveis).
2. Deixe o `ENGINEERING_METHOD.md` ao lado. O contrato aponta para ele por gatilho objetivo: antes de uma tarefa R2 ou R3, o agente lê as seções indicadas.
3. Cobre o formato de entrega. "Deve funcionar", "provavelmente" e "parece correto" são hipóteses, nunca prova.

## Origem

O método não nasceu de pesquisa bibliográfica. Nasceu da construção de um sistema real com agentes — a [EnterDone](https://enterdone.com), um agente de trabalho para empresas, construído do jeito que ele trabalha na empresa do cliente: por agentes, dentro de regras em código, com recibo de cada passo. Cada regra surgiu de uma situação em que o agente afirmou, com convicção, que algo estava correto, e a tela mostrou o contrário. Onde o método converge com práticas consagradas (TDD, mutation testing, pirâmide de testes, circuit breaker, SRE, PROV, N-version programming), a seção 44 diz com quais; a convergência a partir de um ponto de partida independente é tratada como evidência de que as regras descrevem algo real.

Um resumo para quem não é engenheiro, com a ligação de cada regra à parede correspondente no produto, está em [enterdone.com/metodo](https://enterdone.com/metodo).

## Autor e licença

Suleiman Ribeiro — [enterdone.com](https://enterdone.com) · contato@enterdone.com

Este trabalho está licenciado sob [Creative Commons Atribuição 4.0 Internacional (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/deed.pt-br): use, adapte e redistribua, inclusive comercialmente, desde que cite a origem. Ver [`LICENSE`](LICENSE).

Versão de 28 de outubro de 2026. Issues e pull requests são bem-vindos — com evidência, como manda o método.
