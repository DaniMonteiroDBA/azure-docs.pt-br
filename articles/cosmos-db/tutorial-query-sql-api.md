---
title: Como consultar com SQL no Azure Cosmos DB? | Microsoft Docs
description: Saiba como consultar com SQL no Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.openlocfilehash: 74ea4753e36da7686ac88d4426391a38dac0b436
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Tutorial: Consultar o Azure Cosmos DB usando a API do SQL

A [API do SQL](documentdb-introduction.md) do Azure Cosmos DB oferece suporte à consulta de documentos usando SQL. Este artigo fornece um exemplo de documento e dois exemplos de consultas SQL e resultados.

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Consultar dados com SQL

## <a name="sample-document"></a>Exemplo de documento

As consultas de SQL neste artigo usam o seguinte exemplo de documento.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="where-can-i-run-sql-queries"></a>Onde é possível executar consultas SQL?

Você pode executar consultas usando o Data Explorer no Portal do Azure, por meio da [API REST e SDKs](sql-api-sdk-dotnet.md) e até mesmo o [Playground de consultas](https://www.documentdb.com/sql/demo), que executa consultas em um conjunto existente de dados de exemplo.

Para saber mais sobre consultas SQL, confira:
* [Consulta e sintaxe SQL](sql-api-sql-query.md)

## <a name="prerequisites"></a>pré-requisitos

Este tutorial presume que você tem uma conta e uma coleção do Azure Cosmos DB. Não tenho nenhum deles? Complete o [Guia de início rápido de cinco minutos](create-mongodb-nodejs.md) ou o [tutorial de desenvolvedor](tutorial-develop-mongodb.md) para criar uma conta e uma coleção.

## <a name="example-query-1"></a>Exemplo de consulta 1

Com base no exemplo de documento de família acima, a consulta SQL a seguir retorna os documentos cujo campo de id corresponde a `WakefieldFamily`. Por se tratar de uma instrução `SELECT *`, a saída da consulta será todo o documento JSON:

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Resultados**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>Exemplo de consulta 2

A próxima consulta retorna todos os nomes dos filhos na família cuja identificação corresponde ao `WakefieldFamily` organizado por nota.

**Consulta**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a consultar usando SQL  

Agora você pode prosseguir para o próximo tutorial e aprender a distribuir seus dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir os dados globalmente](tutorial-global-distribution-sql-api.md)

