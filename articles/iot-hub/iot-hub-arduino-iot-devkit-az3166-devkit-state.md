---
title: Usar dispositivos gêmeos do Azure para controlar o LED de usuário de MXChip IoT DevKit | Microsoft Docs
description: Neste tutorial, saiba como monitorar os estados de DevKit e controlar o LED de usuário com dispositivos gêmeos do Hub IoT do Azure.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 38a0c4f90b99686450b164637162c117b1fb6063
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Você pode usar este exemplo para monitorar os estados de informações e sensores de MXChip IoT DevKit WiFi e controlar a cor do LED de usuário utilizando dispositivos gêmeos do Hub IoT do Azure.

## <a name="what-you-learn"></a>O que você aprenderá

- Como monitorar os estados de sensor de MXChip IoT DevKit.
- Como usar dispositivos gêmeos do Azure para controlar a cor do LED de RGB do DevKit.

## <a name="what-you-need"></a>O que você precisa

- Configure o ambiente de desenvolvimento seguindo o [Guia de Introdução](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
- Na janela do terminal GitBash (ou outra interface de linha de comando Git), digite os seguintes comandos:
    - `git clone https://github.com/DevKitExamples/DevKitState.git`
    - `cd DevKitState`
    - `code .`

## <a name="provision-azure-services"></a>Provisionar Serviços do Azure

1. Clique no menu suspenso **Tarefas** no Visual Studio Code e selecione **Executar Tarefa...** - **cloud-provision**.
2. Seu andamento é exibido na guia **TERMINAL** do painel **Bem-vindo**.
3. Quando receber a mensagem *Qual assinatura gostaria de escolher*, selecione uma assinatura.
4. Selecione ou escolha um grupo de recursos. 
 
    > [!NOTE]
    > Se você já tiver um Hub IoT gratuito, esta etapa será ignorada.

5. Quando receber a mensagem *Qual hub IoT gostaria de escolher*, selecione ou crie um Hub IoT.
6. Algo semelhante a *aplicativo de funções: nome do aplicativo de funções: xxx* é exibido. Anote o nome do aplicativo de funções; ele será usado em uma etapa posterior.
7. Aguarde até que a implantação do modelo do Azure Resource Manager termine, o que é indicado quando a mensagem *Implantação de modelo do Resource Manager: Concluída* é exibida.

## <a name="deploy-function-app"></a>Implantar aplicativo de funções

1. Clique no menu suspenso **Tarefas** no Visual Studio Code e selecione **Executar Tarefa...** - **cloud-deploy**.
2. Aguarde o processo de upload do código do aplicativo de função ser concluído; a mensagem *implantações do aplicativo de funções: Concluídas* é exibida.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Configurar a cadeia de conexão do dispositivo do Hub IoT no DevKit

1. Conecte o MXChip IoT DevKit no computador.
2. Clique no menu suspenso **Tarefas** no Visual Studio Code e selecione **Executar Tarefa...** - **config-device-connection**
3. No MXChip IoT DevKit, pressione e mantenha pressionado o botão **A**, pressione o botão **Reiniciar** e, em seguida, solte o botão **A** para fazer o DekKit entrar no modo de configuração.
4. Aguarde o processo de configuração de cadeia de conexão ser concluído.

## <a name="upload-arduino-code-to-devkit"></a>Carregar código do Arduino no DevKit

Com o MXChip IoT DevKit conectado ao computador:
1. Clique no menu suspenso **Tarefas** no Visual Studio Code e selecione **Executar Tarefa de Compilação...** O esboço do Arduino é compilado e carregado no DevKit.
2. Quando o esboço tiver sido carregado com êxito, uma mensagem *Compilar e Carregar Esboço: êxito* será exibida.

## <a name="monitor-devkit-state-in-browser"></a>Monitorar estado do DevKit no navegador

1. Em um navegador da Web, abra o arquivo `DevKitState\web\index.html`, que foi criado durante a etapa [O que você precisa](#whatyouneed).
2. A página da Web a seguir é exibida:![Encontrar o nome do aplicativo de funções.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)
1. Insira o nome do aplicativo de funções que você anotou anteriormente.
2. Clique no botão **Conectar**
3. Em alguns segundos, a página é atualizada e exibe o status de conexão Wi-Fi do DevKit e o estado de cada um dos sensores integrados.

## <a name="control-the-devkits-user-led"></a>Controlar o LED de usuário do DevKit

1. Clique no gráfico de LED de usuário na ilustração da página da Web.
2. Dentro de alguns segundos, a tela é atualizada e mostra o status da cor atual do LED do usuário.
3. Tente alterar o valor de cor do LED de RGB clicando em vários locais nos controles deslizantes de RGB.

## <a name="example-operation"></a>Operação de exemplo

![Exemplo de procedimento de teste](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como:
- Conecte um dispositivo MXChip IoT DevKit ao Azure IoT Suite.
- Use a função de dispositivos gêmeos do Azure IoT para detectar e controlar a cor do LED de RGB do DevKit.

Aqui estão sugestões para as próximas etapas:

* [Visão geral do Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar um dispositivo MXChip IoT DevKit ao aplicativo Microsoft IoT Central](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
