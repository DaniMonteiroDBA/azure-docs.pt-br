---
title: "Integrar soluções de segurança na Central de Segurança do Azure | Microsoft Docs"
description: "Saiba como a Central de Segurança do Azure se integra aos parceiros para aprimorar a segurança geral dos recursos do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: yurid
ms.openlocfilehash: 48648c2e84d2a2e4de01f04495fb08df603c6017
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança na Central de Segurança do Azure
Este documento ajuda você a gerenciar soluções de segurança já conectadas à Central de Segurança do Azure e a adicionar novas.

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança integradas do Azure
A Central de Segurança facilita a criação de soluções de segurança integradas no Azure. Os benefícios incluem:

- **Implantação simplificada**: a Central de Segurança oferece provisionamento simplificado das soluções integradas de parceiros. Para as soluções como antimalware e avaliação de vulnerabilidades, a Central de Segurança pode provisionar o agente necessário em suas máquinas virtuais e para dispositivos de firewall, a Central de Segurança pode cuidar de grande parte da configuração de rede necessária.
- **Detecções Integradas**: os eventos de segurança das soluções de parceiro são automaticamente coletados, agregados e exibidos como parte dos incidentes e alertas da Central de Segurança. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Unificação de gerenciamento e monitoramento de integridade**: os clientes podem usar eventos de integridade integrados para monitorar todas as soluções de parceiro em um relance. O gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.

Atualmente, as soluções de segurança integradas incluem:

- Proteção de ponto de extremidade ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, Windows Defender e System Center Endpoint Protection (SCEP))
- Firewall de aplicativo Web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [Gateway de Aplicativo do Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Firewall de próxima geração ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html))
- Avaliação de vulnerabilidades ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

A experiência de integração da proteção de ponto de extremidade pode variar de acordo com a solução. A tabela a seguir tem mais detalhes sobre a experiência de cada solução:

| Proteção do ponto de extremidade               | Plataformas                             | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft Antimalware)                  | Windows Server 2016                   | Não, Integrado no SO           | sim                       |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 | Via extensão                | sim                       |
| Trend Micro – Todas as versões         | Família Windows Server                 | Via extensão                | sim                       |
| Symantec v12.1.1100+                     | Família Windows Server                 | Não                            | sim                        |
| MacAfee                           | Família Windows Server                 | Não                            | Não                         |
| Kaspersky                         | Família Windows Server                 | Não                            | Não                         |
| Sophos                            | Família Windows Server                 | Não                            | Não                         |



## <a name="how-security-solutions-are-integrated"></a>Como as soluções de segurança são integradas
As soluções de segurança do Azure implantadas da Central de Segurança serão conectadas automaticamente. Você também pode se conectar a outras fontes de dados de segurança, incluindo:

- Azure AD Identity Protection
- Computadores que executam no local ou em outras nuvens
- Solução de segurança que dá suporte ao CEF (Formato de Evento Comum)
- Microsoft Advanced Threat Analytics

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerenciar soluções de segurança integrada do Azure e outras fontes de dados

1. Entre no [portal do Azure](https://azure.microsoft.com/features/azure-portal/).

2. No menu **Microsoft Azure**, selecione **Central de Segurança**. **Central de Segurança - Visão geral** é aberto.

  ![Visão geral da Central de Segurança](./media/security-center-partner-integration/overview.png)

3. Em **Visão Geral**, selecione **Soluções de segurança**.

Em **Soluções de segurança**, você pode exibir informações sobre a integridade da solução integrada de segurança do Azure e executar tarefas básicas de gerenciamento. Você também pode conectar a outros tipos de fontes de dados de segurança, como logs de firewall e alertas do Azure Active Directory Identity Protection no CEF (Formato de Evento Comum).

### <a name="connected-solutions"></a>Soluções conectadas

A seção **Soluções conectadas** inclui soluções de segurança atualmente conectadas à Central de Segurança e informações sobre o status de integridade de cada solução.  

![Soluções conectadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Consulte [Gerenciando soluções de parceiros conectadas](security-center-partner-solutions.md) para saber mais.

### <a name="discovered-solutions"></a>Soluções descobertas

A Central de Segurança detecta automaticamente as soluções de segurança em execução no Azure, mas não conectadas à Central de Segurança, e exibe as soluções na seção **Soluções descobertas**. Isso inclui soluções do Azure, como o [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), bem como soluções de parceiros.

> [!NOTE]
> O recurso de soluções descobertas está disponível na camada Standard da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
>
>

Selecione **CONNECT** em uma solução para integrá-la à Central de Segurança e ser notificado sobre alertas de segurança.

![Soluções descobertas](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

A Central de Segurança também detecta soluções implantadas na assinatura que podem encaminhar logs em CEF (formato de eventos comuns). Saiba como [conectar a uma solução de segurança](quick-security-solutions.md) que usa os logs em CEF à Central de Segurança.

### <a name="add-data-sources"></a>Adicionar fontes de dados

A seção **Adicionar fontes de dados** inclui outras fontes de dados disponíveis que podem ser conectadas. Para obter instruções sobre como adicionar dados de qualquer uma dessas fontes, clique em **ADICIONAR**.

![Fontes de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a integrar as soluções de parceiro à Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Conexão do Microsoft Advanced Threat Analytics à Central de Segurança do Azure](security-center-ata-integration.md)
* [Conexão do Azure Active Directory Identity Protection à Central de Segurança do Azure](security-center-aadip-integration.md)
* [Monitoramento da integridade de segurança na Central de Segurança](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Monitoramento das soluções de parceiros na Central de Segurança](security-center-partner-solutions.md). Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Obtenha respostas para perguntas frequentes sobre como usar a Central de Segurança.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
