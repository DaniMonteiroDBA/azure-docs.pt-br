---
title: Eventos personalizados para a Grade de Eventos do Azure com a CLI | Microsoft Docs
description: Use a Grade de Eventos do Azure e a CLI do Azure para publicar um tópico e assinar esse evento.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: be98fa88727a867e0fca0ca3587db276f8a2d7f2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-route-custom-events-with-azure-cli-and-event-grid"></a>Criar e encaminhar eventos personalizados com a CLI e a Grade de Eventos do Azure

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste artigo, você pode usar a CLI do Azure para criar um tópico personalizado, assinar o tópico e disparar o evento para exibir o resultado. Normalmente, você envia eventos para um ponto de extremidade que responde ao evento, como um webhook ou uma Função do Azure. No entanto, para simplificar este artigo, você envia os eventos para uma URL que apenas coleta as mensagens. Essa URL é criada usando a ferramenta de terceiros [Hookbin](https://hookbin.com/).

>[!NOTE]
>O **Hookbin** não se destina ao uso em alta taxa de transferência. O uso dessa ferramenta é meramente demonstrativo. Se você efetuar push de mais de um evento por vez, talvez não veja todos os eventos na ferramenta.

Ao concluir, você verá que os dados do evento foram enviados para um ponto de extremidade.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando a versão da CLI do Azure mais recente (2.0.24 ou posterior). Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

Caso não esteja usando o Cloud Shell, primeiro você deve entrar usando `az login`.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos de Grade de Eventos são recursos do Azure e devem ser colocados em um grupo de recursos do Azure. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). 

O exemplo a seguir cria um grupo de recursos chamado *gridResourceGroup* no local *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico de grade de evento fornece um ponto de extremidade definido pelo usuário no qual você posta seus eventos. O exemplo a seguir cria o tópico personalizado no seu grupo de recursos. Substitua `<topic_name>` por um nome exclusivo para o tópico. O nome do tópico deve ser exclusivo, pois é representado por uma entrada DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar o tópico, vamos criar o ponto de extremidade para a mensagem do evento. Em vez de escrever código para responder ao evento, vamos criar um ponto de extremidade que coleta as mensagens, para que você possa exibi-las. O Hookbin é uma ferramenta de terceiros que permite criar um ponto de extremidade e exibir as solicitações enviadas a ele. Vá até [Hookbin](https://hookbin.com/) e clique em **Criar Novo Ponto de Extremidade**.  Copie a URL do compartimento, pois você precisará dela para assinar o tópico personalizado.

## <a name="subscribe-to-a-topic"></a>Assinar um tópico

Assine um tópico para indicar à Grade de Eventos quais eventos você deseja acompanhar. O exemplo a seguir assina o tópico que você criou e transmite a URL do Hookbin como o ponto de extremidade para notificação de eventos. Substitua `<event_subscription_name>` por um nome exclusivo para a sua assinatura e `<endpoint_URL>` pelo valor da seção anterior. A especificação de um ponto de extremidade durante a assinatura faz com que a Grade de Eventos manipule o roteamento de eventos para esse ponto de extremidade. Em `<topic_name>`, use o valor que você criou anteriormente. 

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="send-an-event-to-your-topic"></a>Enviar um evento para o tópico

Vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade. Primeiro, vamos obter a URL e a chave para o tópico personalizado. Novamente, use o nome do tópico em `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Para simplificar este artigo, use dados de evento de exemplo para enviar ao tópico. Normalmente, um aplicativo ou serviço do Azure enviaria os dados de evento. O exemplo abaixo obtém os dados do evento:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Para ver o evento completo, use `echo "$body"`. O elemento `data` do JSON é a carga do evento. Qualquer JSON bem formado pode ficar nesse campo. Você também pode usar o campo de assunto para roteamento e filtragem avançados.

CURL é um utilitário que envia solicitações HTTP. Neste artigo, use o CURL para enviar o evento ao tópico. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Você disparou o evento, e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. Navegue até a URL do ponto de extremidade que você criou anteriormente. Ou clique em Atualizar no navegador. Você verá o evento que acabou de ser enviado. 

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Limpar recursos
Caso planeje continuar a trabalhar com esse evento, não limpe os recursos criados neste artigo. Caso contrário, use os comandos a seguir para excluir os recursos criados por você neste artigo.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como criar tópicos e assinaturas de evento, saiba mais sobre como a Grade de Eventos pode ajudá-lo:

- [Sobre a Grade de Eventos](overview.md)
- [Rotear eventos do Armazenamento de Blobs para um ponto de extremidade da Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os Aplicativos Lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md)
