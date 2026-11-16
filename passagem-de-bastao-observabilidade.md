# Passagem de bastão — Arquitetura de Referência de Observabilidade da CAIXA

Atualizado em 16/11/2026. Este arquivo serve para que outra IA (por exemplo, o Copilot) continue o trabalho sem a conversa original. Ele não depende de nenhum link: os documentos são citados apenas pelo nome e devem ser fornecidos pelo usuário (colados ou anexados) quando for preciso alterá-los.

## 1. O que é o trabalho

O usuário trabalha com arquitetura na CAIXA e mantém a arquitetura de referência de observabilidade do banco. O objetivo é um documento de fácil entendimento para todas as equipes (desenvolvimento, infraestrutura, operação, SRE, produto, gestão) que explique como a observabilidade funciona e **o que a ferramenta deve apresentar**.

A arquitetura descrita **já está implantada e em uso**. Não trate nada como "futuro" ou "TO-BE".

## 2. Os documentos

| Nome do documento | Papel |
| --- | --- |
| Arquitetura de Referência de Observabilidade — CAIXA | **Documento principal.** Sem produtos de mercado. Recebe as melhorias primeiro. |
| Arquitetura de Observabilidade — CAIXA: versão com ferramentas | Documento complementar. Mesma base, com as ferramentas que cumprem cada função. |
| Quadro da Arquitetura de Referência de Observabilidade — CAIXA | Diagrama da arquitetura, com ferramentas. |

## 3. Regras definidas pelo usuário (não quebrar)

1. **O documento de referência não cita ferramentas ou produtos de mercado.** Nada de Zabbix, Prometheus, Grafana, Azure Data Explorer, Event Hubs, PostgreSQL, APPGTW, AzAPIM ou Azion nele.
2. **Tecnologias e padrões podem ser citados pelo nome atual** no documento de referência: OpenTelemetry, OTLP, eBPF, W3C Trace Context, RUM, STM, AIOps, CICS, IMS, DB2. O documento será atualizado ao longo do tempo.
3. **Ferramentas aparecem só na versão com ferramentas** e no quadro.
4. **Linguagem simples**, em português do Brasil, para público misto. Frases curtas. Todo termo novo entra no glossário.
5. **Termos obrigatórios:** telemetria, correlação de eventos, concentrador de eventos, sobreposição e sombreamento entre ferramentas.
6. Cada camada ou tema termina, quando faz sentido, com um parágrafo **"A ferramenta deve apresentar:"**.

## 4. Conceitos centrais (para manter a coerência)

- **Ideia central:** coletar toda a telemetria, concentrar os eventos em um só lugar, correlacioná-los e agir antes que o cliente perceba o problema.
- **Fluxo de telemetria, em seis camadas:** 1. Fontes de telemetria; 2. Coleta; 3. Pipeline de telemetria; 4. Armazenamento; 5. Análise e visualização; 6. Consumo e ação.
- **Fluxo de eventos, em cinco etapas:** geração; concentrador de eventos; correlação de eventos; supressão e priorização; ação (incidente, notificação ou autocura).
- **Concentrador de eventos:** ponto único que recebe os eventos de todas as ferramentas, padroniza o formato e elimina duplicados.
- **Correlação de eventos:** agrupa eventos relacionados usando topologia (CMDB), janela de tempo e mudanças recentes, e aponta a causa provável.
- **Sobreposição:** duas ou mais ferramentas fazendo a mesma função sobre o mesmo alvo.
- **Sombreamento:** uma ferramenta encobre a outra; a função existe em duas, só uma é usada e a outra fica na sombra, gerando custo, dado divergente ou alerta sem dono.

## 5. Estrutura atual do documento de referência

Na ordem em que aparece:

1. Para que serve este documento
2. Visão geral (diagrama com 6 camadas de telemetria e 5 etapas de eventos)
3. Decisões de arquitetura (8 decisões com o motivo)
4. Glossário rápido
5. Camadas da arquitetura (as seis camadas)
6. Padrões técnicos de telemetria
7. Fluxo de eventos, com o subitem "O que torna a correlação confiável"
8. Sobreposição e sombreamento entre ferramentas (por função, sem nomes) e regras para evitar
9. Padrão mínimo de integração (obrigatório, recomendado ou opcional por criticidade)
10. Níveis de maturidade (1 a 4)
11. Padrão de alertas e severidade (S1 a S4), com o subitem "Alertas baseados em SLO"
12. Segurança e privacidade da telemetria
13. Resiliência da plataforma
14. Armadilhas comuns
15. O que a ferramenta deve apresentar (15 capacidades, com "como conferir")
16. Princípios da arquitetura (13)
17. Responsabilidades e medição
18. Metas e retenção
19. Governança deste documento

**Versão com ferramentas:** tem a estrutura anterior ao último refinamento. Não tem Decisões de arquitetura, Padrões técnicos, Níveis de maturidade, Armadilhas comuns, "correlação confiável" nem alertas baseados em SLO. Tem, a mais, as tabelas "Qual ferramenta usar para quê" e "Pontos de sobreposição a observar", com os nomes das ferramentas. Sua lista de princípios tem 12 itens.

## 6. O que é fato e o que é suposição

### Informado pelo usuário

- Ambiente: gateway central (APPGTW) distribuindo para Grafana e para a API e a interface web do Zabbix; Zabbix como núcleo de infraestrutura, com 4 proxies; Prometheus como coletor complementar; PostgreSQL dedicado; Azure Data Explorer (ADX) para a camada de aplicação, recebendo também Event Hubs de AzAPIM, APIM, Azion e logs HTTP.
- Pilares (logs, métricas, traces), service views, CMDB, KPIs, coleta em três camadas (infraestrutura, aplicação, serviço de negócio), dashboards, alertas, incidentes, correlação e supressão de eventos.
- Princípios originais: automatizar instrumentação, instrumentar o kernel, coletar infraestrutura, experiência do usuário, otimizar coletores, visibilidade fim-a-fim até o mainframe, padrão aberto, custo de armazenamento, tracing distribuído, correlacionar telemetria, visualizações, unificar coleta e análise.
- Mainframe com CICS, IMS e DB2; importância do tracing fim-a-fim e de SLOs.
- A arquitetura alvo já está implantada.

### Suposições ainda não confirmadas

- Event Hubs como barramento de toda a telemetria.
- A regra "nenhuma ferramenta abre incidente diretamente" valer hoje sem exceções.
- A tabela de responsabilidades por equipe.
- Os pontos de sobreposição entre ferramentas (deduzidos, sem levantamento do ambiente).
- A ferramenta principal de cada função.
- Os níveis de criticidade (Crítico, Importante, Apoio) e o que é obrigatório em cada um.
- As severidades S1 a S4.
- As oito decisões de arquitetura e a meta de nível 4 de maturidade para serviços críticos.
- Profiling contínuo como quarto sinal "em adoção gradual".
- Coleta em dois níveis (agente e gateway).
- Os produtos do concentrador de eventos, da correlação e do ITSM não foram informados.

Não transforme suposição em fato. Quando o usuário confirmar algo, atualize esta lista.

## 7. Pendências abertas

- Campos "A definir" ou "A preencher": prazos de resposta por severidade; situação atual e meta de MTTD, MTTR, ruído de alertas, autocura, cobertura e sobreposições; retenção por tipo de dado; nome do responsável pelo documento.
- Três perguntas sem resposta na versão com ferramentas: Event Hubs como barramento; exceções ao concentrador; divisão de responsabilidades.
- Levar para a versão com ferramentas as seções que só existem na referência (item 5).
- O quadro ainda não mostra os temas novos (segurança, resiliência, maturidade, sobreposição).
- Validar a seção de segurança e privacidade com as áreas responsáveis da CAIXA.

## 8. Como continuar

- **Peça o texto atual.** Esta passagem não contém o conteúdo completo dos documentos. Antes de alterar, peça ao usuário para colar ou anexar o documento (ou só a seção a mudar).
- **Trabalhe por seção.** Para caber no limite da ferramenta, altere uma seção por vez e devolva a seção inteira já corrigida, pronta para colar.
- **Edite, não reescreva.** Mude só o que foi pedido e mantenha o restante igual.
- **Verifique o outro documento.** Mudança de conteúdo vale para os dois; nome de ferramenta só entra na versão com ferramentas.
- **Corrija as contagens.** O texto cita "oito decisões", "quinze capacidades", "seis camadas", "quatro níveis" e princípios numerados. Se adicionar ou remover um item, ajuste o número.
- **Atualize o glossário** a cada termo novo.
- **Estilo:** a primeira frase de cada seção já diz o ponto principal; tabelas para comparações; listas só para itens paralelos.
- **Perguntas ao usuário:** ele responde de forma curta. Pergunte só o que muda o resultado; no restante, decida e diga o que assumiu.

## 9. Próximos passos sugeridos

1. Confirmar as suposições do item 6 e ajustar os dois documentos.
2. Alinhar a versão com ferramentas ao documento de referência.
3. Preencher metas, retenção e prazos com dados reais.
4. Atualizar o quadro.
5. Adições possíveis: checklist de integração de um sistema novo, exemplo de jornada ponta a ponta do canal ao mainframe, matriz "função por ferramenta" preenchida e modelo de registro de exceção.

## 10. Histórico resumido

1. O usuário enviou o texto base e pediu uma arquitetura de referência com os termos telemetria, correlação de eventos e concentrador de eventos. Foi criado o quadro.
2. O usuário informou que a arquitetura alvo já está em uso e pediu um documento de fácil entendimento com o que a ferramenta deve apresentar.
3. Uma análise crítica apontou lacunas (segurança, padrão mínimo, criticidade, sobreposição, severidade, resiliência, metas, governança). O usuário pediu para implementar e destacar sobreposição e sombreamento.
4. O usuário pediu dois documentos: a referência, sem ferramentas, e a versão com ferramentas.
5. O usuário pediu um refinamento de arquiteto sênior na referência e liberou os nomes de tecnologias atuais.
6. Foi criada esta passagem de bastão, sem links, para uso no Copilot.
