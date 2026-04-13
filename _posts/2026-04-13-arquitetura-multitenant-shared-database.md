---
title: Arquitetura Multitenant — Shared Database, Isolated Schema e Shared Schema
date: 2026-04-13 00:10:00 -0300
categories: [Arquitetura]
tags: [multitenant, shared-database, saas, arquitetura-de-software, banco-de-dados]
description: Comparação entre os modelos Shared Database / Isolated Schema e Shared Database / Shared Schema.
---

Depois do Single-Tenant, dois modelos comuns em arquiteturas multitenant são:

1. **Shared Database / Isolated Schema**
2. **Shared Database / Shared Schema**

Ambos compartilham infraestrutura e banco de dados, mas diferem no nível de isolamento usado para separar os tenants.

## Shared Database / Isolated Schema

Nesse modelo, os tenants usam o mesmo banco de dados, mas cada tenant possui seu próprio schema.

A infraestrutura é compartilhada, a aplicação também pode ser única, mas o isolamento dos dados acontece no nível de schema.

Uma analogia: um prédio com vários apartamentos. Todos estão no mesmo edifício, mas cada apartamento possui separações próprias para organizar seus espaços e recursos.

### Características

- **Servidor de banco compartilhado:** reduz custo financeiro e operacional.
- **Isolamento lógico por schema:** cada tenant possui uma separação mais clara dentro do banco.
- **Aplicação compartilhada:** uma única instância da aplicação e uma única esteira de CI/CD podem atender vários tenants.
- **Equilíbrio entre custo e isolamento:** costuma ser um compromisso intermediário.

### Vantagens

- Melhor uso de recursos quando comparado ao Single-Tenant.
- Redução de custos de infraestrutura.
- Bom nível de isolamento lógico.
- Flexibilidade maior para customizações por tenant, porque cada schema pode ter separações específicas.

### Desvantagens

- Gerenciamento de schemas pode ficar complexo conforme o número de tenants cresce.
- Migrações precisam ser replicadas em múltiplos schemas.
- Ainda existe risco de noisy neighbor effect no nível do servidor.
- Sharding e escalabilidade horizontal de dados podem ser mais difíceis.

### Quando escolher

Esse modelo pode funcionar bem quando:

- o volume de tenants é moderado;
- os requisitos de isolamento de dados são moderados ou altos;
- existe uma migração gradual de Single-Tenant para Multi-Tenant;
- a empresa quer reduzir custo sem abrir mão de uma separação lógica mais clara.

## Shared Database / Shared Schema

No modelo **Shared Database / Shared Schema**, os tenants compartilham a mesma aplicação, o mesmo banco e as mesmas tabelas. O isolamento é feito por lógica de aplicação, normalmente usando uma coluna como `tenant_id`.

Nesse modelo, toda consulta precisa saber qual tenant está sendo acessado. Um erro simples em filtros pode se transformar em uma falha crítica de segurança.

Uma analogia: um grande armário compartilhado, onde cada item tem uma etiqueta indicando seu proprietário.

### Características

- **Banco de dados completamente compartilhado:** um único schema e tabelas compartilhadas.
- **Isolamento lógico por coluna:** geralmente com `tenant_id`.
- **Aplicação consciente de tenant:** toda query, permissão e operação precisa considerar o tenant atual.
- **Máxima eficiência de recursos:** mais tenants podem ser atendidos com menos infraestrutura dedicada.

### Vantagens

- Maior eficiência de custos.
- Manutenção e atualizações mais simples: um pipeline, um deployment, uma aplicação.
- Escalabilidade horizontal mais simples em muitos cenários.
- Melhor uso de pool de conexões.
- Rollout rápido de novas funcionalidades.

### Desvantagens

- Menor isolamento lógico em comparação com Single-Tenant ou Isolated Schema.
- Risco maior de vazamento de dados entre tenants se a aplicação falhar no filtro correto.
- Noisy neighbor effect pode impactar performance.
- Backup e restauração por tenant ficam mais complexos.
- Menor flexibilidade para customizações profundas de schema.

### Quando escolher

Esse modelo tende a fazer sentido quando:

- existe grande volume de tenants;
- o produto precisa priorizar baixo custo e eficiência;
- há baixa necessidade de customizações por tenant;
- o time consegue implementar bons controles de segurança, testes e observabilidade;
- o produto precisa iterar e fazer rollout rapidamente.

## Comparando os dois modelos

| Critério | Isolated Schema | Shared Schema |
|---|---|---|
| Isolamento | Maior, por schema | Menor, por coluna/lógica |
| Custo | Médio | Baixo |
| Migrações | Mais complexas em muitos schemas | Mais simples em um schema |
| Customização | Mais flexível | Mais restrita |
| Risco de vazamento | Menor, mas ainda existe | Maior se a aplicação falhar |
| Escala para muitos tenants | Boa até certo ponto | Melhor para volumes muito altos |

## Nota final

O modelo Shared Database exige disciplina de engenharia. Quanto mais compartilhado o ambiente, mais importante se tornam testes, revisão de queries, autorização, auditoria, observabilidade e mecanismos que impeçam acesso cruzado entre tenants.

A eficiência vem com responsabilidade: economizar infraestrutura não pode significar fragilizar o isolamento dos dados.
