---
title: Instalar o Visual Studio e conecte-se a pilha do Azure | Microsoft Docs
description: "Conheça as etapas necessárias para instalar o Visual Studio e conecte-se a pilha do Azure"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 5eae2553af1e41ace878a2f8f2c1a1656c63a7c4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Instalar o Visual Studio e conecte-se a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Usar o Visual Studio para criar e implantar o Azure Resource Manager [modelos](azure-stack-arm-templates.md) na pilha do Azure. Você pode usar as etapas descritas neste artigo para instalar o Visual Studio de [Kit de desenvolvimento de pilha do Azure](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente com Windows externo se você estiver conectado por meio de [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Essas etapas executam uma nova instalação do Visual Studio 2015 Community Edition. Leia mais sobre [coexistência](https://msdn.microsoft.com/library/ms246609.aspx) entre outras versões do Visual Studio.

## <a name="install-visual-studio"></a>Instalar Visual Studio
1. Baixe e execute o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).             
2. Procurar **Visual Studio Community 2015 com o SDK do Microsoft Azure - 2.9.6**, clique em **adicionar**, e **instalar**.

    ![Etapas de instalação de captura de tela de WebPI](./media/azure-stack-install-visual-studio/image1.png) 

3. Desinstalar o **Microsoft Azure PowerShell** que é instalado como parte do SDK do Azure.

    ![Captura de tela da interface do Adicionar/remover programas do Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png) 

4. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)

5. Reinicie o sistema operacional após a conclusão da instalação.

## <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

1. Inicie o Visual Studio.

2. Do **exibição** menu, selecione **Cloud Explorer**.

3. No painel de novo, selecione **adicionar conta** e entre com suas credenciais do Active Directory do Azure.  
    ![Captura de tela de Cloud Explorer uma vez registrados e conectados à pilha do Azure](./media/azure-stack-install-visual-studio/image6.png)

Depois de conectado, você pode [implantar modelos](azure-stack-deploy-template-visual-studio.md) ou procurar tipos de recursos disponíveis e os grupos de recursos para criar seus próprios modelos.  

## <a name="next-steps"></a>Próximas etapas

 - [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)
