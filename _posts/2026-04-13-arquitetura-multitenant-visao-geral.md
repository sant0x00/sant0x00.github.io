---
title: Arquitetura Multitenant — visão geral e critérios de escolha
date: 2026-04-13 00:30:00 -0300
categories: [Arquitetura]
tags: [multitenant, saas, arquitetura-de-software, banco-de-dados]
description: Uma visão geral sobre modelos de tenancy, isolamento de dados e critérios para escolher a abordagem certa.
---

Quando penso em **arquitetura multitenant**, a primeira pergunta não é “qual modelo é o melhor?”, mas sim: **qual modelo se encaixa melhor na estratégia de negócio e nas exigências do cliente?**

Antes de falar dos modelos, vale definir dois termos:

- **Tenant** é o cliente, organização ou grupo que usa uma aplicação. Em alguns contextos, pode ser uma empresa, filial, unidade, parceiro ou qualquer recorte que precise ter seus dados e configurações separados.
- **Multitenant** é uma arquitetura em que uma mesma solução atende vários tenants. Esses clientes podem compartilhar a aplicação, a infraestrutura ou o banco de dados, dependendo do modelo escolhido.

Ou seja, multitenancy é uma forma de estruturar sistemas para atender **vários clientes** com algum nível de compartilhamento, mantendo o isolamento necessário entre eles. Existem diferentes modelos porque negócios diferentes têm necessidades diferentes de custo, segurança, performance, compliance, personalização e operação.

Multitenancy envolve uma troca constante entre custo, isolamento, complexidade operacional, escalabilidade, segurança, performance e nível de personalização. Não existe uma resposta universal. Existe a escolha mais coerente para o contexto.

## O que é isolamento de dados?

O ponto central de qualquer arquitetura multitenant é garantir que os dados de um tenant não sejam acessíveis por outro tenant.

Esse isolamento pode acontecer de duas formas principais:

- **Isolamento físico:** recursos separados, como aplicação, banco de dados ou infraestrutura dedicada.
- **Isolamento lógico:** recursos compartilhados, mas dados separados por mecanismos como schemas, tabelas ou identificadores (`tenant_id`).

Quanto maior o isolamento, normalmente maior o custo e a complexidade operacional. Quanto maior o compartilhamento, normalmente maior a eficiência, mas também maior a responsabilidade da aplicação em garantir segurança e separação correta dos dados.

## Principais modelos de tenancy

### Single-Tenant

No modelo **Single-Tenant**, cada cliente possui uma instância dedicada da aplicação e do banco de dados. É comum em cenários on-premise tradicionais ou em clientes com exigências fortes de isolamento.

Em termos simples: cada cliente tem sua própria “casa”, com seus próprios móveis, cômodos e infraestrutura.

### Shared Database / Isolated Schema

Nesse modelo, os tenants compartilham o mesmo banco de dados, mas cada um possui seu próprio schema. A aplicação e a infraestrutura são compartilhadas, enquanto o isolamento acontece no nível dos schemas.

É um meio-termo entre isolamento e eficiência de custo.

### Shared Database / Shared Schema

Aqui, todos os tenants compartilham a mesma aplicação, o mesmo banco de dados e o mesmo schema. O isolamento é feito de forma lógica, geralmente por uma coluna como `tenant_id`.

Esse modelo tende a ser o mais eficiente em custo e escalabilidade, mas exige muito cuidado na implementação, porque toda consulta precisa respeitar corretamente o tenant atual.

## Fatores para escolher o modelo

Antes de escolher uma abordagem, eu considero perguntas como:

- Qual é a exigência de isolamento do cliente?
- Existem requisitos contratuais, regulatórios ou de compliance?
- O cliente precisa de customizações profundas?
- A infraestrutura será minha ou do cliente?
- O custo de licenças, banco de dados e manutenção é aceitável?
- O volume de tenants tende a ser baixo, moderado ou muito alto?
- Existe risco de um tenant impactar a performance de outro?
- O modelo permite evoluir o produto sem gerar uma sobrecarga operacional grande demais?

## Critérios importantes

### Custos

Infraestrutura dedicada, licenças, manutenção e times especializados podem elevar bastante o custo por tenant. Em contrapartida, modelos mais compartilhados reduzem custo, mas aumentam a responsabilidade da aplicação.

### Segurança e conformidade

Contratos governamentais, saúde, finanças e outros contextos regulados podem exigir isolamento mais rígido. Nesses casos, o custo maior pode ser justificável.

### Complexidade de implementação

Um modelo mais compartilhado pode parecer simples por usar menos infraestrutura, mas a aplicação precisa ser mais consciente de tenant, principalmente em filtros, permissões, migrações, logs, backups e auditoria.

### Escalabilidade

Modelos compartilhados tendem a escalar melhor em custo e operação. Porém, dependendo do volume de dados e carga, pode ser necessário pensar em sharding, particionamento e estratégias de isolamento de workloads.

### Personalização

Quanto mais customização por cliente, mais difícil manter um modelo totalmente compartilhado. Se cada tenant precisa de regras, integrações ou schemas muito diferentes, o isolamento por instância ou schema pode fazer mais sentido.

### Noisy Neighbor Effect

O **noisy neighbor effect** acontece quando um tenant consome muitos recursos e impacta outros tenants que compartilham a mesma infraestrutura. Esse risco cresce em modelos compartilhados e precisa ser mitigado com limites, filas, escalabilidade, observabilidade e isolamento adequado.

## Resumo mental

Uma forma simples de comparar:

| Modelo | Isolamento | Custo | Complexidade operacional | Escalabilidade |
|---|---:|---:|---:|---:|
| Single-Tenant | Alto | Alto | Alta | Menor em escala |
| Shared DB / Isolated Schema | Médio/alto | Médio | Média/alta | Boa até certo volume |
| Shared DB / Shared Schema | Menor | Baixo | Média na aplicação | Alta |

A escolha não deve ser puramente técnica. Ela precisa refletir o tipo de cliente, o risco aceitável, o modelo de negócio e a capacidade do time de operar a solução com segurança.
