---
title: Cenários avançados com o Azure MFA e VPNs de terceiros
description: Guias passo a passo de configuração para integração do Azure MFA a Cisco, Citrix e Juniper.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 1f94a214-d6f6-48a8-8a12-006b5896ae45
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: 3d59be74ec73aa10bdfba5b58a1bff4630985259
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Cenários avançados com soluções da Autenticação Multifator do Azure e VPNs de terceiros
A Autenticação Multifator do Azure pode ser usado para realizar conexão perfeita com uma variedade de soluções VPN de terceiros. Este artigo se concentra no dispositivo de VPN Cisco® ASA, o dispositivo de VPN Citrix NetScaler SSL e o dispositivo de VPN Juniper Networks Secure Access/Pulse Secure Connect Secure SSL. Criamos guias de configuração para abordar esses três dispositivos comuns. O Servidor de Autenticação Multifator também pode ser integrado a maioria dos outros sistemas que usam a autenticação RADIUS, LDAP, IIS ou baseada em declarações com o AD FS. Você pode encontrar mais detalhes em [Configurações do servidor MFA](howto-mfaserver-deploy.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Dispositivo de VPN Cisco ASA e Autenticação Multifator do Azure
A Autenticação Multifator do Azure integra-se ao seu dispositivo VPN do Cisco® ASA para fornecer segurança adicional para logons de VPN do Cisco AnyConnect® e acesso ao portal.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | DESCRIÇÃO |
| --- | --- |
| [Cisco ASA com VPN Anyconnect e configuração do Azure MFA para LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrar o dispositivo VPN Cisco ASA ao Azure MFA usando LDAP |
| [Configuração do Cisco ASA com VPN Anyconnect e do Azure MFA para RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Cisco ASA ao Azure MFA usando RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>VPN do Citrix NetScaler SSL e Autenticação Multifator do Azure
A Autenticação Multifator do Azure integra-se ao seu dispositivo VPN Citrix NetScaler SSL para fornecer segurança adicional para logons de VPN do Citrix NetScaler SSL e acesso ao portal.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | DESCRIÇÃO |
| --- | --- |
| [Configuração da VPN do Citrix NetScaler SSL e do Azure MFA para LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrar a VPN Citrix NetScaler SSL ao dispositivo Azure MFA usando LDAP |
| [Configuração da VPN Citrix NetScaler SSL e do Azure MFA para RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Citrix NetScaler SSL ao Azure MFA usando RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Dispositivo de VPN Juniper/Pulse Secure SSL e Autenticação Multifator do Azure
A Autenticação Multifator do Azure integra-se ao seu dispositivo VPN Juniper/Pulse Secure SSL para fornecer segurança adicional para logons de VPN do Juniper/Pulse Secure SSL e acesso ao portal.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | DESCRIÇÃO |
| --- | --- |
| [Configuração da VPN do Juniper/Pulse Secure SSL e do Azure MFA para LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrar o dispositivo VPN Juniper/Pulse Secure SSL ao Azure MFA usando LDAP |
| [Configuração da VPN do Juniper/Pulse Secure SSL e do Azure MFA para RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Juniper/Pulse Secure SSL ao Azure MFA usando RADIUS |

## <a name="next-steps"></a>Próximas etapas

- [Aumentar sua infraestrutura de autenticação atual com a extensão NPS para a Autenticação Multifator do Azure](howto-mfa-nps-extension.md)

- [Definir as configurações da Autenticação Multifator do Azure](howto-mfa-mfasettings.md)