---
title: "Modelo de dados do Azure Application Insights Telemetry – telemetria de exceções | Microsoft Docs"
description: "Modelo de dados do Application Insights para telemetria de exceções"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: 3c3c3a39c7986cc771fe4baf60ad9b316888f6ed
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2017
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria de exceções: modelo de dados do Application Insights

Um [Application Insights](app-insights-overview.md), uma instância da exceção representa uma exceção tratada ou sem tratamento que ocorreu durante a execução do aplicativo monitorado.

## <a name="problem-id"></a>ID do Problema

Identificador do local em que a exceção foi lançada no código. Usada para agrupamento de exceções. Normalmente, é uma combinação do tipo de exceção e uma função da pilha de chamadas.

Comprimento máximo: 1.024 caracteres

## <a name="severity-level"></a>Nível de severidade

Nível de severidade de rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` ou `Critical`.

## <a name="exception-details"></a>Detalhes da exceção

(A ser estendido)

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Próximas etapas

- Consulte [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- Aprenda a [diagnosticar exceções em seus aplicativos Web com o Application Insights](app-insights-asp-net-exceptions.md).
- Confira as [plataformas](app-insights-platforms.md) com suporte do Application Insights.
