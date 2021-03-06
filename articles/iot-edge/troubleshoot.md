---
title: Solucionar problemas do Azure IoT Edge | Microsoft Docs
description: Resolver problemas comuns e aprender habilidades para solução de problemas do Azure IoT Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: b03ece52c4ff77c9e0abbc794325cd7e9a20c915
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções para o Azure IoT Edge

Se você tiver problemas com o Azure IoT Edge no seu ambiente, use este artigo como um guia para a solução desses problemas. 

## <a name="standard-diagnostic-steps"></a>Etapas de diagnóstico padrão 

Quando você encontrar um problema, saiba mais sobre o estado do dispositivo do IoT Edge examinando os logs de contêiner e as mensagens que entram e saem do dispositivo. Use as ferramentas e os comandos desta seção para coletar informações. 

* Examine os logs dos contêineres do Docker para detectar problemas. Comece com os contêineres implantados, examine os contêineres que compõem o tempo de execução do IoT Edge: Agente do Edge e Hub do Edge. Os logs do Agente do Edge normalmente fornecem informações sobre o ciclo de vida de cada contêiner. Os logs do Hub do Edge fornecem informações sobre mensagens e roteamento. 

   ```cmd
   docker logs <container name>
   ```

* Veja as mensagens que passam pelo Hub do Edge e colete informações sobre atualizações de propriedades do dispositivo nos logs detalhados dos contêineres de tempo de execução.

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```
   
* Exiba logs detalhados de comandos iotedgectl:

   ```cmd
   iotedgectl --verbose DEBUG <command>
   ```

* Se você enfrentar problemas de conectividade, inspecione as variáveis de ambiente do dispositivo do Edge, como a cadeia de conexão do dispositivo:

   ```cmd
   docker exec edgeAgent printenv
   ```

Você também pode verificar as mensagens que estão sendo enviadas entre os dispositivos do Hub IoT e do IoT Edge. Veja essas mensagens usando a extensão [Kit de ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) do Visual Studio Code. Para obter instruções, consulte [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Ferramenta útil ao desenvolver com o Azure IoT).

Após investigar os logs e as mensagens para obter informações, você também poderá tentar reiniciar o tempo de execução do Azure IoT Edge:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>O Agente do Edge é interrompido após cerca de um minuto

O Agente do Edge a é iniciado e executado com êxito por cerca de um minuto e, em seguida, é interrompido. Os logs indicam que o Agente do Edge tenta se conectar ao Hub IoT com o AMQP e, aproximadamente 30 segundos mais tarde, tenta se conectar usando o AMQP com websocket. Quando isso falha, o Agente do Edge é encerrado. 

Logs do Agente do Edge de exemplo:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Causa raiz
Uma configuração de rede na rede do host está impedindo que o Agente do Edge alcance a rede. O agente tenta se conectar com AMQP (porta 5671) primeiro. Se isso falha, ele tenta websockets (porta 443).

O tempo de execução do IoT Edge configura uma rede para que cada um dos módulos se comunique. No Linux, esta rede é uma rede de ponte. No Windows, ele usa o NAT. Esse problema é mais comum em dispositivos Windows com contêineres do Windows que usam a rede NAT. 

### <a name="resolution"></a>Resolução
Verifique se existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Às vezes, uma configuração de VPN no host substitui a rede do IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>O Hub do Edge não pode ser iniciado

O Hub do Edge não é iniciado e imprime a seguinte mensagem nos logs: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Causa raiz
Algum outro processo no computador host tem a porta 443 associada. O Hub do Edge mapeia as portas 5671 e 443 para usar em cenários de gateway. Esse mapeamento de porta falhará se outro processo já estiver associado a essa porta. 

### <a name="resolution"></a>Resolução
Localize e interrompa o processo que está usando a porta 443. Esse processo geralmente é um servidor Web.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>O Agente do Edge não pode acessar a imagem de um módulo (403)
Falha na execução de um contêiner e os logs do Agente do Edge mostram um erro 403. 

### <a name="root-cause"></a>Causa raiz
O Agente do Edge não tem permissões para acessar a imagem de um módulo. 

### <a name="resolution"></a>Resolução
Tente executar o comando `iotedgectl login` novamente.

## <a name="iotedgectl-cant-find-docker"></a>iotedgectl não consegue localizar o Docker

Os comandos `iotedgectl setup` ou `iotedgectl start` falhar e imprimem a seguinte mensagem para os logs:
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>Causa raiz
iotedgectl não consegue localizar o Docker, que é um pré-requisito.

### <a name="resolution"></a>Resolução
Instale o Docker, verifique se ele está em execução e tente novamente.

## <a name="iotedgectl-setup-fails-with-an-invalid-hostname"></a>A configuração de iotedgectl falha com um nome de host inválido

O comando `iotedgectl setup` falha e imprime a mensagem a seguir: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Causa raiz
O tempo de execução de IoT Edge só pode oferecer suporte a nomes de host com menos de 64 caracteres. Geralmente, isso não é um problema para máquinas físicas, mas poderá ocorrer quando você configurar o tempo de execução em uma máquina virtual. Os nomes de host gerados automaticamente para máquinas virtuais do Windows hospedadas no Azure, em particular, tendem a ser longos. 

### <a name="resolution"></a>Resolução
Quando você vir esse erro, você pode resolvê-lo a configurar o nome DNS de sua máquina virtual e, em seguida, definindo o nome DNS de nome de host no comando de instalação.

1. No portal do Azure, navegue até a página de visão geral de sua máquina virtual. 
2. Selecione **configurar** em nome DNS. Caso sua máquina virtual já tenha um nome DNS configurado, você não precisará configurar um novo. 

   ![Configurar nome DNS](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para **rótulo do nome DNS** e selecione **Salvar**.
4. Copie o novo nome DNS, que deve estar no formato **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. Dentro da máquina virtual, use o comando a seguir para configurar o tempo de execução do IoT Edge com seu nome DNS:

   ```input
   iotedgectl setup --connection-string "<connection string>" --nopass --edge-hostname "<DNS name>"
   ```

## <a name="next-steps"></a>Próximas etapas
Você acha que encontrou um bug na plataforma IoT Edge? [Envie um problema](https://github.com/Azure/iot-edge/issues) para que possamos continuar a melhorar. 
