# Fluxo-de-Caixa-
Objetivo do trabalho foi elaborar um documento com as especificações  e em cima disso realizar um protótipo. 
# Sistema de Fluxo de Caixa - Documentação Base para Protótipo

**Data:** 04 de Novembro de 2026  
**Autor:** Fernando  
**Referência Bibliográfica:** E. Bezerra, *Princípios de Análise e Projeto de Sistemas com UML*, 3ª ed.

---

## Índice

1. [Visão Geral](#visão-geral)
2. [Escopo e Premissas](#escopo-e-premissas)
3. [Perfis de Usuário e Permissões](#perfis-de-usuário-e-permissões)
4. [Arquitetura de Telas e Navegação](#arquitetura-de-telas-e-navegação)
5. [Especificação Detalhada das Telas](#especificação-detalhada-das-telas)
6. [Regras de Negócio](#regras-de-negócio)
7. [Modelo de Dados (Proposto)](#modelo-de-dados-proposto)
8. [Fluxos e Estados do Lançamento](#fluxos-e-estados-do-lançamento)
9. [Mensagens e Validações](#mensagens-e-validações)
10. [Dados de Exemplo (Seed)](#dados-de-exemplo-seed)
11. [Diretrizes de Interface (UI/UX)](#diretrizes-de-interface-uiux)
12. [Critérios de Aceite](#critérios-de-aceite)
13. [Pontos em Aberto e Premissas Assumidas](#pontos-em-aberto-e-premissas-assumidas)

---

## Visão Geral

Este documento reúne os requisitos funcionais, regras de negócio, arquitetura de telas, estados e massa de dados necessários para a construção de um protótipo navegável do **Sistema de Fluxo de Caixa**. 

A especificação baseia-se em um modelo de casos de uso composto por **3 atores**, **8 casos de uso** e **3 regras de negócio**, tendo o caso de uso `CSU02` (Registrar Saída) especificado em detalhe. Como o modelo conceitual define apenas o comportamento do sistema e não o design de interface, elementos derivados ou inferidos estão devidamente sinalizados como **(proposto)** e consolidados na seção de pontos em aberto.

### Objetivo do Protótipo
Validar com os usuários finais os fluxos de lançamento, aprovação gerencial e consulta antes de iniciar o desenvolvimento de código de produção. O sistema contempla o registro de entradas e saídas, controle de alçada para despesas acima de R$ 5.000,00, emissão de demonstrativos, projeções financeiras e conciliação bancária.

---

## Escopo e Premissas

O protótipo deve cobrir os 8 casos de uso através de telas navegáveis alimentadas por dados fictícios. Apenas o `CSU02` necessita operar de ponta a ponta, contemplando seus fluxos alternativo e de exceção.

| Incluído no Protótipo | Fora do Escopo (Simulado ou Ausente) |
| :--- | :--- |
| Fluxo de login e segregação de perfis (Operador e Gerente) | Autenticação real e controle de sessão em servidor |
| Registro de entradas e saídas com validação das regras `RN01` e `RN03` | Persistência real em banco de dados |
| Fluxo de aprovação para saídas acima de R$ 5.000,00 | Integração real via API bancária (extrato estático) |
| Demonstrativo, projeção e conciliação com dados estáticos | Motor de cálculo financeiro real para projeções |
| Manutenção do plano de contas e consulta de lançamentos | Perfis de acesso além dos três atores previstos no modelo |

### Premissas Técnicas e de Negócio (Propostas)
* **Plataforma:** Aplicação web projetada prioritariamente para visualização em desktop.
* **Localização:** Idioma Português (Brasil), moeda em Real (`R$`) e datas no formato `dd/mm/aaaa`.
* **Estrutura:** Operação em caixa único, sem suporte a arquitetura multiempresa.

---

## Perfis de Usuário e Permissões

O acesso ao sistema é restrito mediante autenticação (`CSU06`). A interface adapta-se dinamicamente ao perfil autenticado, exibindo apenas os menus correspondentes às suas responsabilidades. O perfil **Operador** não possui visibilidade sobre demonstrativos, projeções, conciliação bancária, plano de contas ou painel de aprovações.

| Ator | Tipo | Responsabilidades (Modelo) | Casos de Uso | Tela Inicial (Proposto) |
| :--- | :--- | :--- | :--- | :--- |
| **Operador Financeiro / Caixeiro** | Primário | Lançamentos correntes de entradas e saídas e fechamento de caixa. | `CSU01`, `CSU02` | Painel do caixa |
| **Gerente Financeiro / Administrador** | Primário | Aprovar alçadas, parametrizar plano de contas, emitir demonstrativos, visualizar projeções e conciliar extratos. | `CSU03`, `CSU04`, `CSU05`, `CSU07`, `CSU08` | Painel do caixa (com destaque para aprovações pendentes) |
| **Sistema Bancário / Gateway** | Secundário (Externo) | Fornecer dados de movimentação bancária via integração. | `CSU05` | Sem interface (atua como origem dos dados do extrato) |

---

## Arquitetura de Telas e Navegação

O fluxo de navegação inicia-se na autenticação (`T01`) seguida pelo painel principal (`T02`). A partir deste ponto, a navegação ramifica-se de acordo com o perfil do usuário, convergindo apenas na tela de consulta de lançamentos (`T10`).

### Fluxo de Navegação
```text
[T01 Login (CSU06)]
       │
       ▼
[T02 Painel do Caixa]
       │
       ├──► Perfil: Operador Financeiro
       │      ├──► T03 Registrar Entrada (CSU01)
       │      ├──► T04 Registrar Saída (CSU02) [Especificação Completa]
       │      ├──► T10 Lançamentos
       │      └──► T11 Fechamento de Caixa
       │
       └──► Perfil: Gerente Financeiro
              ├──► T05 Aprovações Pendentes (CSU08)
              ├──► T06 Demonstrativo de Fluxo de Caixa (CSU03)
              ├──► T07 Projeção de Fluxo de Caixa (CSU04)
              ├──► T08 Conciliação Bancária (CSU05)
              ├──► T09 Plano de Contas (CSU07)
              └──► T10 Lançamentos
```

### Matriz de Rastreabilidade
As telas `T02`, `T10` e `T11` não possuem casos de uso dedicados no modelo original, sendo propostas como interfaces de apoio operacional.

| ID | Nome da Tela | Caso de Uso Associado | Perfil de Acesso | Regras Aplicadas |
| :--- | :--- | :--- | :--- | :--- |
| **T01** | Login | `CSU06` Autenticar Usuário | Todos | N/A |
| **T02** | Painel do caixa *(proposto)* | Apoio operacional | Operador, Gerente | N/A |
| **T03** | Registrar entrada | `CSU01` Registrar Entrada | Operador | `RN02`, `RN03` |
| **T04** | Registrar saída | `CSU02` Registrar Saída | Operador | `RN01`, `RN02`, `RN03` |
| **T05** | Aprovações pendentes | `CSU08` Solicitar Aprovação | Gerente | `RN01` |
| **T06** | Demonstrativo de fluxo de caixa | `CSU03` Emitir Demonstrativo | Gerente | N/A |
| **T07** | Projeção de fluxo de caixa | `CSU04` Projetar Fluxo | Gerente | N/A |
| **T08** | Conciliação bancária | `CSU05` Conciliar Extrato | Gerente, Sistema Bancário | N/A |
| **T09** | Plano de contas | `CSU07` Manter Categorias | Gerente | `RN03` |
| **T10** | Lançamentos *(proposto)* | Apoio operacional | Operador, Gerente | `RN02` |
| **T11** | Fechamento de caixa *(proposto)* | Apoio operacional | Operador | `RN02` |

---

## Especificação Detalhada das Telas

### T01 - Login (`CSU06`)
Responsável pela autenticação prévia antes do acesso a qualquer funcionalidade.
* **Campos:**
  * `Usuário` (Texto, Obrigatório - *proposto*)
  * `Senha` (Senha, Obrigatório - *proposto*)
* **Ações e Comportamento:** O botão **Entrar** redireciona para a `T02`. Em caso de credenciais inválidas, exibe a mensagem `M07` e preserva o texto digitado no campo usuário.

### T02 - Painel do Caixa *(proposto)*
Tela inicial após o login. Apresenta três blocos principais: saldo atual do caixa, resumo dos cinco últimos lançamentos e atalhos rápidos conforme o perfil:
* **Atalhos do Operador:** Registrar entrada, Registrar saída, Fechar caixa.
* **Atalhos do Gerente:** Aprovações pendentes (exibindo contador de pendências), Demonstrativo, Projeção, Conciliação, Plano de contas.

### T03 - Registrar Entrada (`CSU01` - *proposto*)
Estrutura espelhada da tela `T04`, apresentando duas diferenças fundamentais: o seletor de categorias lista exclusivamente registros ativos do tipo *Entrada* e não há validação de limite de alçada. Aplica-se a regra `RN03`.

### T04 - Registrar Saída (`CSU02`)
Registra pagamentos, despesas ou baixas financeiras. Exige que o operador esteja autenticado.

| Campo | Tipo | Obrigatório | Observações |
| :--- | :--- | :--- | :--- |
| **Data** | Data | Sim *(proposto)* | Valor padrão: data atual *(proposto)* |
| **Valor** | Moeda (`R$`) | Sim *(proposto)* | Deve ser maior que zero; avaliado contra alçada de R$ 5.000,00 (`RN01`) |
| **Categoria** | Lista de seleção | Sim (`RN03`) | Exibe apenas categorias ativas do tipo *Saída* *(proposto)* |
| **Histórico** | Texto | Não *(proposto)* | Descrição livre da despesa |
| **Comprovante** | Anexo | Não *(proposto)* | Aceita formatos `PDF`, `JPG` ou `PNG` |

* **Ações:** **Confirmar** e **Cancelar** *(proposto)*.
* **Desfechos ao Confirmar:**
  1. **Fluxo Principal:** Grava o lançamento com status *Efetivado*, atualiza o saldo do caixa e exibe a mensagem `M03`.
  2. **Fluxo Alternativo (`FA01` - Valor > R$ 5.000,00):** Não efetiva o lançamento. Atribui o status *Pendente de Aprovação*, aciona o `CSU08`, exibe o aviso `M02` ao operador e encerra o fluxo.
  3. **Fluxo de Exceção (`FE01` - Categoria vazia):** Exibe o erro impeditivo `M01` e retorna ao formulário preservando os demais dados preenchidos.

### T05 - Aprovações Pendentes (`CSU08`)
Lista as saídas que excederam a alçada operacional e aguardam deliberação gerencial.

| Coluna | Descrição / Comportamento |
| :--- | :--- |
| **Data, Valor, Categoria, Histórico** | Dados originais registrados na `T04`. |
| **Solicitante** | Nome/login do operador que efetuou o registro *(proposto)*. |
| **Comprovante** | Link para visualização/download do arquivo anexado. |
| **Ações** | Botões **Aprovar** e **Rejeitar** (rejeição exige justificativa obrigatória - *proposto*). |

* **Regras de Decisão:** Aprovar altera o status para *Efetivado* e deduz o valor do saldo do caixa. Rejeitar altera o status para *Rejeitado* *(proposto)* e mantém o saldo inalterado.

### T06 - Demonstrativo de Fluxo de Caixa (`CSU03`)
Relatório gerencial consolidando entradas, saídas e saldo em um intervalo de tempo.
* **Filtros:** Data inicial e data final (obrigatórios - *proposto*), Categoria (opcional - *proposto*).
* **Saída:** Tabela agrupada por categoria contendo total de entradas, total de saídas e saldo líquido do período.
* **Ações:** **Emitir** (atualiza a visualização) e **Exportar em PDF** *(proposto)*.
* **Estado Vazio:** Caso não existam registros no filtro selecionado, exibe o aviso `"Nenhuma movimentação no período"` *(proposto)*.

### T07 - Projeção de Fluxo de Caixa (`CSU04`)
Apresenta estimativas de saldo futuro. Como o modelo conceitual não especifica algoritmos de cálculo, o protótipo utiliza dados simulados estáticos.
* **Parâmetros:** Seletor de horizonte temporal de 30, 60 ou 90 dias *(proposto)*.
* **Saída:** Gráfico de linha ilustrando a evolução do saldo projetado acompanhado de tabela detalhada por semana *(proposto)*.
* **Ação:** **Gerar projeção**.

### T08 - Conciliação Bancária (`CSU05`)
Confronta os lançamentos internos do sistema com o extrato importado do Sistema Bancário (representado por massa de dados fixa no protótipo).
* **Filtros:** Período de apuração *(proposto)*.
* **Saída:** Visualização comparativa em duas colunas lado a lado (Lançamentos do Sistema vs. Movimentos do Extrato). Cada linha recebe um indicador de status: *Conciliado*, *Divergente* ou *Sem correspondência* *(proposto)*.
* **Ações:** **Importar extrato**, **Confirmar conciliação** e **Marcar como divergente** *(proposto)*.

### T09 - Plano de Contas (`CSU07`)
Interface administrativa para manutenção das categorias financeiras.

| Campo | Tipo | Observações |
| :--- | :--- | :--- |
| **Nome** | Texto | Obrigatório e exclusivo/único *(proposto)* |
| **Tipo** | Seleção (*Entrada* ou *Saída*) | Define em qual formulário de lançamento a categoria será listada *(proposto)* |
| **Situação** | Seleção (*Ativa* ou *Inativa*) | Apenas categorias ativas ficam disponíveis para novos lançamentos (`RN03`) |

* **Ações:** **Nova categoria**, **Editar** e **Inativar**. A inativação realiza exclusão lógica, preservando a integridade histórica de lançamentos passados *(proposto)*.

### T10 - Lançamentos *(proposto)*
Visão analítica de todas as movimentações financeiras para auditoria e conferência.
* **Filtros:** Período, Tipo, Categoria e Status.
* **Colunas:** Data, Tipo, Categoria, Histórico, Valor e Status (*Efetivado*, *Pendente de Aprovação* ou *Rejeitado* - *proposto*).
* **Restrição (`RN02`):** Lançamentos vinculados a períodos fechados exibem as ações de edição e estorno desabilitadas, apresentando a mensagem `M06` ao passar o cursor (tooltip).

### T11 - Fechamento de Caixa *(proposto)*
Interface operacional para encerramento do turno/período pelo Operador, viabilizando a aplicação da regra `RN02`. Exibe o resumo consolidado (total de entradas, total de saídas e saldo final) e solicita confirmação explícita. Uma vez confirmado, todos os lançamentos do período tornam-se imutáveis.

---

## Regras de Negócio

| ID | Descrição da Regra | Telas Impactadas | Comportamento na Interface |
| :--- | :--- | :--- | :--- |
| **RN01** | **Limite de Alçada:** Saídas com valor superior a R$ 5.000,00 exigem aprovação gerencial. | `T04`, `T05` | Valores $> \text{R\$ } 5.000,00$ assumem status *Pendente de Aprovação* e disparam `M02`. Valores $\le \text{R\$ } 5.000,00$ (incluindo exatos R$ 5.000,00) são efetivados imediatamente. |
| **RN02** | **Imutabilidade de Caixa Fechado:** Lançamentos de períodos encerrados não permitem edição ou estorno. | `T10`, `T11` | Ações de edição e estorno permanecem desabilitadas com exibição da mensagem `M06`. Após execução da `T11`, o período torna-se somente leitura. |
| **RN03** | **Obrigatoriedade de Classificação:** Nenhuma movimentação pode ser registrada sem uma categoria ativa vinculada. | `T03`, `T04`, `T09` | Campo categoria obrigatório nos formulários; registros inativos são omitidos das listas; tentativa de envio sem seleção dispara `M01`. |

---

## Modelo de Dados (Proposto)

Estrutura lógica inferida a partir dos requisitos de tela e regras de negócio para garantir a consistência referencial do protótipo.

| Entidade | Atributos | Observações e Relacionamentos |
| :--- | :--- | :--- |
| **Usuário** | `id`, `nome`, `login`, `perfil` (*Operador* \| *Gerente*) | Base para autenticação (`CSU06`). |
| **Categoria** | `id`, `nome`, `tipo` (*Entrada* \| *Saída*), `ativa` (booleano) | Mantida na `T09`; filtrada pela `RN03`. |
| **Lançamento** | `id`, `tipo`, `data`, `valor`, `categoria_id`, `historico`, `comprovante`, `status`, `periodo_id`, `usuario_id` | Status possíveis: *Efetivado*, *Pendente de Aprovação*, *Rejeitado*. Pertence a uma Categoria e a um Período. |
| **Solicitação de Aprovação** | `id`, `lancamento_id`, `solicitante_id`, `situacao`, `aprovador_id`, `data_decisao`, `motivo` | Gerada no fluxo `FA01` e gerenciada na `T05`. |
| **Período de Caixa** | `id`, `data_inicio`, `data_fim`, `situacao` (*Aberto* \| *Fechado*) | Controla a regra `RN02`; encerrado via `T11`. |
| **Movimento do Extrato** | `id`, `data`, `descricao`, `valor`, `lancamento_conciliado_id` | Utilizado na `T08`; pode referenciar um Lançamento. |

---

## Fluxos e Estados do Lançamento

O diagrama abaixo ilustra o ciclo de vida de uma movimentação de saída desde a submissão pelo operador até o encerramento do período contábil:

```text
[Operador confirma saída (T04)]
              │
              ▼
   <Categoria preenchida?> ──(Não)──► [Exibe M01 e retorna ao formulário (FE01)]
              │
            (Sim)
              │
              ▼
  <Valor acima de R$ 5.000,00?>
       │               │
     (Não)           (Sim: RN01 e M02)
       │               │
       ▼               ▼
  [Efetivado]    [Pendente de Aprovação]
(Saldo atualizado) (Saldo não alterado)
       │               │
       │               ├──(Gerente Aprova)───► [Efetivado] (Saldo atualizado)
       │               │
       │               └──(Gerente Rejeita)──► [Rejeitado] (Saldo não alterado)
       │
       ▼ (Fechamento de Caixa - T11)
[Período Fechado]
(Somente leitura - RN02)
```

---

## Mensagens e Validações

| ID | Contexto / Gatilho | Texto da Mensagem *(Proposto)* | Severidade |
| :--- | :--- | :--- | :--- |
| **M01** | Categoria não selecionada (`FE01`, `RN03`) | Selecione uma categoria para registrar a movimentação. | Erro impeditivo |
| **M02** | Saída excede o limite de alçada (`FA01`, `RN01`) | Saída acima de R$ 5.000,00: enviada para aprovação gerencial. | Aviso |
| **M03** | Saída gravada dentro da alçada | Saída registrada com sucesso. | Sucesso |
| **M04** | Entrada gravada com sucesso | Entrada registrada com sucesso. | Sucesso |
| **M05** | Valor informado igual a zero ou nulo | Informe um valor maior que zero. | Erro impeditivo |
| **M06** | Tentativa de alterar lançamento em caixa fechado (`RN02`) | Este lançamento pertence a um caixa encerrado e não pode ser alterado. | Informação |
| **M07** | Falha na autenticação (`T01`) | Usuário ou senha incorretos. | Erro |
| **M08** | Deliberação concluída na `T05` | Saída aprovada. / Saída rejeitada. | Sucesso |

---

## Dados de Exemplo (Seed)

Massa de dados fictícia estruturada para validar todos os cenários críticos do protótipo (limite exato de alçada, quebra de alçada, período encerrado e divergência bancária).

### Usuários
| Login | Perfil |
| :--- | :--- |
| `operador@exemplo.com` | Operador |
| `gerente@exemplo.com` | Gerente |

### Categorias
| Nome | Tipo | Situação |
| :--- | :--- | :--- |
| Vendas | Entrada | Ativa |
| Serviços | Entrada | Ativa |
| Fornecedores | Saída | Ativa |
| Aluguel | Saída | Ativa |
| Folha de pagamento | Saída | Ativa |
| Material de escritório | Saída | Ativa |
| Brindes | Saída | Inativa |

### Lançamentos
*Nota: O período referente a Outubro/2026 encontra-se com o status **Fechado**.*

| ID | Data | Tipo | Categoria | Histórico | Valor (R$) | Status / Observação |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **L001** | 01/11/2026 | Entrada | Vendas | Vendas do dia | 3.450,00 | Efetivado |
| **L002** | 02/11/2026 | Saída | Material de escritório | Papel e toner | 380,00 | Efetivado |
| **L003** | 02/11/2026 | Saída | Fornecedores | Reposição de estoque | 5.000,00 | Efetivado (no limite da alçada, sem aprovação) |
| **L004** | 03/11/2026 | Saída | Aluguel | Aluguel de novembro | 5.200,00 | Pendente de Aprovação |
| **L005** | 03/11/2026 | Entrada | Serviços | Consultoria | 2.800,00 | Efetivado |
| **L006** | 28/10/2026 | Saída | Folha de pagamento | Adiantamento | 4.100,00 | Efetivado (Período fechado) |
| **L007** | 30/10/2026 | Entrada | Vendas | Vendas da semana | 6.020,00 | Efetivado (Período fechado) |

### Extrato Bancário (Simulação para `T08`)
| Data | Descrição | Valor (R$) | Resultado Esperado na Conciliação |
| :--- | :--- | :--- | :--- |
| 01/11/2026 | Crédito vendas | 3.450,00 | Conciliado com `L001` |
| 02/11/2026 | Pagamento papelaria | -380,00 | Conciliado com `L002` |
| 02/11/2026 | Pagamento fornecedor | -5.000,00 | Conciliado com `L003` |
| 03/11/2026 | Crédito consultoria | 2.750,00 | Divergente de `L005` (R$ 2.800,00) |
| 03/11/2026 | Tarifa bancária | -19,90 | Sem correspondência |

---

## Diretrizes de Interface (UI/UX)

* **Navegação:** Menu lateral dinâmico exibindo apenas as rotas autorizadas ao perfil logado. A tela `T02` (Painel) atua sempre como página inicial.
* **Formulários:** Disposição em coluna única, rótulos posicionados acima dos campos, botão de ação primária alinhado à direita e mensagens de erro exibidas tanto junto ao campo inválido quanto em banner no topo do formulário.
* **Formatação:** Valores monetários no padrão `R$ 1.234,56` (com sinal negativo `-` antecedendo valores de saída) e datas no formato `dd/mm/aaaa`.
* **Indicadores de Status:** Representados obrigatoriamente por texto explícito (*Efetivado*, *Pendente de Aprovação*, *Rejeitado*) acompanhado de cor de apoio, nunca utilizando apenas diferenciação cromática.
* **Terminologia:** Adoção estrita do vocabulário do domínio (*lançamento*, *alçada*, *categoria*, *caixa*, *período*), evitando sinônimos.
* **Prevenção de Erros:** Ações destrutivas ou irreversíveis (rejeitar saída, fechar caixa) devem exigir diálogo de confirmação explícita.
* **Acessibilidade:** Relação de contraste mínima de $4,5:1$ para textos, indicadores de foco visíveis para navegação via teclado e associação semântica de rótulos em todos os inputs.

---

## Critérios de Aceite

O protótipo será considerado homologado quando os seguintes cenários forem validados com sucesso utilizando a massa de dados de exemplo:

- [ ] **Controle de Acesso:** Nenhuma rota interna é acessível sem autenticação; perfis Operador e Gerente visualizam menus distintos.
- [ ] **Saída Abaixo da Alçada:** O Operador registra a saída `L002` (R$ 380,00), visualiza o status *Efetivado* e constata a redução imediata do saldo.
- [ ] **Saída no Limite da Alçada:** Uma saída de exatos R$ 5.000,00 é efetivada diretamente sem exigir fluxo de aprovação.
- [ ] **Saída Acima da Alçada:** Uma saída de R$ 5.200,00 recebe o status *Pendente de Aprovação*, exibe a mensagem `M02` ao operador e torna-se visível na `T05` para o Gerente.
- [ ] **Validação de Categoria:** Tentar registrar saída sem selecionar categoria dispara `M01` e preserva os dados já preenchidos nos demais campos.
- [ ] **Fluxo de Aprovação/Rejeição:** Ao aprovar `L004` na `T05`, o status muda para *Efetivado* e o saldo é atualizado; ao rejeitar, muda para *Rejeitado* e o saldo permanece intacto.
- [ ] **Filtro de Categorias Ativas:** A categoria inativa (*Brindes*) não é listada nos formulários `T03` e `T04`.
- [ ] **Bloqueio de Período Fechado:** Os lançamentos de outubro (`L006` e `L007`) possuem botões de edição e estorno desabilitados, exibindo a mensagem `M06`.
- [ ] **Conciliação Bancária:** A tela `T08` exibe corretamente três registros conciliados, um divergente e um sem correspondência.
- [ ] **Relatórios e Projeções:** As telas `T06` e `T07` carregam os dados simulados e respondem adequadamente aos filtros aplicados.

---

## Pontos em Aberto e Premissas Assumidas

Para viabilizar a construção do protótipo diante das lacunas do modelo conceitual original, foram adotadas as seguintes premissas que deverão ser ratificadas pelos stakeholders:

1. **Impacto de Pendências no Saldo:** A pós-condição do `CSU02` menciona o decremento do saldo e a marcação como efetivado ou pendente. Assumiu-se que saídas pendentes **não** sensibilizam o saldo até que sejam aprovadas.
2. **Fluxo de Rejeição:** O `CSU08` contempla apenas a solicitação de aprovação. Foi incluída a ação de rejeição com exigência de justificativa e criação do status *Rejeitado*.
3. **Estrutura das Telas Secundárias:** Apenas o `CSU02` possui especificação textual detalhada. As demais telas (`T01`, `T03`, `T05` a `T09`) foram estruturadas por analogia.
4. **Fechamento de Caixa:** Embora a regra `RN02` dependa do fechamento de caixa pelo Operador, não há caso de uso específico no modelo. Propôs-se a criação da tela `T11`.
5. **Edição e Estorno:** Mencionados na `RN02`, porém sem casos de uso associados. Assumiu-se que tais operações são permitidas apenas em períodos com status *Aberto*.
6. **Algoritmo de Projeção:** Ausente no modelo; o protótipo operará com valores estáticos pré-definidos.
7. **Parametrização de Alçada:** Assumiu-se o valor de R$ 5.000,00 como constante fixa no sistema, sem interface de configuração pelo Gerente.
8. **Tipificação de Categorias:** A segregação entre *Entrada* e *Saída* foi assumida para permitir a filtragem adequada nos formulários de lançamento.
9. **Alçada de Aprovação:** Assumiu-se que qualquer usuário com perfil *Gerente* possui permissão para aprovar ou rejeitar qualquer solicitação pendente.
10. **Arquitetura de Implantação:** Definida como aplicação web responsiva com foco primário em uso via desktop.
