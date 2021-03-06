---
title: Solução de erros comuns de implantação do Azure | Microsoft Docs
description: Descreve como resolver erros comuns ao implantar recursos no Azure usando o Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: erro de implantação, implantação do azure, implante no azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: f251fe11c43dc4b3f29c70f937f5bfcb6af6c44e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solução de erros comuns de implantação do Azure com o Azure Resource Manager

Este tópico descreve alguns erros comuns de implantação do Azure que você pode encontrar e fornece informações para resolver esses erros. Se não encontrar o código de erro para ser erro de implantação, consulte [Localizar código de erro](#find-error-code).

## <a name="error-codes"></a>Códigos do Erro

| Código do erro | Redução | Mais informações |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga as restrições de nomenclatura para as contas de armazenamento. | [Resolver o nome da conta de armazenamento](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Verifique as propriedades da conta de armazenamento disponível. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | O cluster ou a região não tem recursos disponíveis ou não aceita o tamanho solicitado de VM. Repita a solicitação mais tarde ou solicite um tamanho de VM diferente. | [Problemas de provisionamento e a alocação para Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) e [Problemas de provisionamento e a alocação para Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| AnotherOperationInProgress | Aguarde a conclusão da operação simultânea. | |
| AuthorizationFailed | Sua conta ou entidade de serviço não tem acesso suficiente para concluir a implantação. Verifique a função a que sua conta pertence e seu acesso para o escopo da implantação. | [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md) |
| BadRequest | Você enviou valores de implantação que não coincidem com o que é esperado pelo Resource Manager. Verifique a mensagem de status interna para obter ajuda com a solução de problemas. | [Referência de modelos](/azure/templates/) e [Locais com suporte](resource-manager-templates-resources.md#location) |
| Conflito | Você está solicitando uma operação que não é permitida no estado atual do recurso. Por exemplo, o redimensionamento do disco é permitido apenas ao criar uma VM ou quando a VM é desalocada. | |
| DeploymentActive | Aguarde a conclusão da implantação simultânea nesse grupo de recursos. | |
| DeploymentFailed | O erro DeploymentFailed é um erro geral que não fornece os detalhes necessários para resolver o erro. Examine os detalhes do erro em busca de um código de erro que fornece mais informações. | [Encontrar código do erro](#find-error-code) |
| DeploymentQuotaExceeded | Caso você atinja o limite de 800 implantações por grupo de recursos, exclua do histórico as implantações que não são mais necessárias. Exclua entradas do histórico com [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) para a CLI do Azure ou [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) no PowerShell. A exclusão de uma entrada do histórico de implantações não afeta os recursos de implantação. | |
| DnsRecordInUse | O nome do registro DNS deve ser exclusivo. Forneça um nome diferente ou modifique o registro existente. | |
| ImageNotFound | Verifique as configurações de imagem da VM. |  |
| InUseSubnetCannotBeDeleted | Você pode encontrar este erro quando tenta atualizar um recurso, mas a solicitação é processada excluindo e criando o recurso. Certifique-se de especificar todos os valores inalterados. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obtenha o token de acesso do locatário correto. Você só pode obter o token do locatário a que sua conta pertence. | |
| InvalidContentLink | Provavelmente você tentou vincular a um modelo aninhado que não está disponível. Verifique uma segunda vez o URI que você forneceu para o modelo aninhado. Caso o modelo exista em uma conta de armazenamento, verifique se o URI está acessível. Pode ser necessário passar um token SAS. | [Modelos vinculados](resource-group-linked-templates.md) |
| InvalidParameter | Um dos valores que você forneceu para um recurso não corresponde ao valor esperado. Esse erro pode ser decorrente de várias condições diferentes. Por exemplo, uma senha pode ser insuficiente ou um nome de blob pode estar incorreto. Verifique a mensagem de erro para determinar qual valor precisa ser corrigido. | |
| InvalidRequestContent | Os valores da implantação incluem valores que não são esperados ou valores necessários estão ausentes. Confirme se os valores para seu tipo de recurso. | [Referência de modelo](/azure/templates/) |
| InvalidRequestFormat | Habilitar o log da depuração ao executar a implantação e verifique o conteúdo da solicitação. | [Log de depuração](#enable-debug-logging) |
| InvalidResourceNamespace | Verifique o namespace do recurso especificado na propriedade **type**. | [Referência de modelo](/azure/templates/) |
| InvalidResourceReference | O recurso ainda não existe ou foi referenciado incorretamente. Verifique se você precisa adicionar uma dependência. Verifique se o uso da função **referência** inclui os parâmetros necessários para seu cenário. | [Resolver dependências](resource-manager-not-found-errors.md) |
| InvalidResourceType | Verifique o tipo de recurso do recurso especificado na propriedade **type**. | [Referência de modelo](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registre sua assinatura no provedor de recursos. | [Resolver registro](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Verifique se há erros na sintaxe do modelo. | [Resolver modelo inválido](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Remova as dependências desnecessárias. | [Resolver as dependências circulares](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Verifique se sua conta pertence ao mesmo locatário que o grupo de recursos em que você está implantando. | |
| LinkedInvalidPropertyId | A ID de um recurso não está sendo resolvida corretamente. Verifique se você forneceu todos os valores necessários para a ID do recurso, incluindo a ID de assinatura, nome do grupo de recursos, o tipo de recurso, o nome do recurso pai (se necessário) e o nome de recurso. | |
| LocationRequired | Forneça um local para o recurso. | [Definir local](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Verifique se o recurso aninhado tem o número correto de segmentos de nome e tipo. | [Resolver segmentos de recurso](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Verifique o status de registro do provedor de recursos e os locais com suporte. | [Resolver registro](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registre sua assinatura no provedor de recursos. | [Resolver registro](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Verifique o status do registro do provedor de recursos. | [Resolver registro](resource-manager-register-provider-errors.md) |
| NotFound | Você pode estar tentando implantar um recurso dependente em paralelo com o recurso pai. Verifique se você precisa adicionar uma dependência. | [Resolver dependências](resource-manager-not-found-errors.md) |
| OperationNotAllowed | A implantação está tentando uma operação que ultrapassa a cota da assinatura, do grupo de recursos ou da região. Se possível, revise sua implantação para permanecer dentro das cotas. Caso contrário, considere solicitar uma alteração de suas cotas. | [Resolver cotas](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Verifique se há um recurso pai antes de criar os recursos filho. | [Resolver recurso pai](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | O endereço IP especificado contém um intervalo de endereços exigido pelo Azure. Altere o endereço IP para evitar o intervalo reservado. | [Endereços IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | O endereço IP especificado está fora do intervalo da sub-rede. Altere o endereço IP para que ele fique dentro do intervalo de sub-rede. | [Endereços IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Algumas propriedades não podem ser alteradas em um recurso implantado. Ao atualizar um recurso, limite suas alterações às propriedades permitidas. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Sua assinatura inclui uma política de recursos que impede uma ação que você está tentando executar durante a implantação. Localize a política que bloqueia a ação. Se possível, modifique sua implantação para atender às limitações da política. | [Resolver políticas](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Forneça um nome de recurso que não inclua um nome reservado. | [Nomes de recurso reservados](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Aguarde a conclusão da exclusão. | |
| ResourceGroupNotFound | Verifique o nome do grupo de recursos de destino para a implantação. Ele já deve existir em sua assinatura. Verifique o contexto de sua assinatura. | [CLI do Azure](/cli/azure/account?#az_account_set), [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Sua implantação faz referência a um recurso que não pode ser resolvido. Verifique se o uso da função de **referência** inclui os parâmetros necessários para seu cenário. | [Resolver referências](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | A implantação está tentando criar recursos que ultrapassam a cota da assinatura, do grupo de recursos ou da região. Se possível, revise sua infraestrutura para permanecer dentro das cotas. Caso contrário, considere solicitar uma alteração de suas cotas. | [Resolver cotas](resource-manager-quota-errors.md) |
| SkuNotAvailable | Selecione a SKU (por exemplo, o tamanho da VM) que está disponível para o local que você selecionou. | [Resolver SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Forneça um nome exclusivo para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Forneça um nome exclusivo para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Verifique a assinatura, o grupo de recursos e o nome da conta de armazenamento que você está tentando usar. | |
| SubnetsNotInSameVnet | Uma máquina virtual pode ter apenas uma rede virtual. Ao implantar várias NICs, certifique-se de que elas pertencem à mesma rede virtual. | [Várias NICs](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Remova as dependências desnecessárias. | [Resolver as dependências circulares](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Reduza o número de grupos de recursos para uma única implantação. | [Implantação entre grupos de recursos](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Encontrar código do erro

Há dois tipos de erros que você pode receber:

* erros de validação
* erros de implantação

Erros de validação ocorrem em cenários que podem ser determinados antes da implantação. Eles incluem erros de sintaxe no modelo ou a tentativa de implantar recursos que excederão suas cotas de assinatura. Os erros de implantação surgem de condições que ocorrem durante o processo de implantação. Eles incluem a tentativa de acessar um recurso que está sendo implantado em paralelo.

Ambos os tipos de erro retornam um código de erro que você pode usar para solucionar os problemas de implantação. Os dois tipos de erro aparecem no [log de atividades](resource-group-audit.md). No entanto, os erros de validação não aparecem no seu histórico de implantação porque a implantação nunca foi iniciada.

### <a name="validation-errors"></a>Erros de validação

Durante a implantação por meio do portal, você vê um erro de validação depois de enviar os valores.

![mostrar erro de validação no portal](./media/resource-manager-common-deployment-errors/validation-error.png)

Selecione a mensagem para obter mais detalhes. Na imagem a seguir, você vê um erro **InvalidTemplateDeployment** e uma mensagem que indica uma implantação bloqueada por política.

![mostrar detalhes da validação](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Erros de implantação

Quando a operação for aprovada na validação, mas falhar durante a implantação, você verá o erro nas notificações. Selecione a notificação.

![erro de notificação](./media/resource-manager-common-deployment-errors/notification.png)

Você verá mais detalhes sobre a implantação. Selecione a opção para obter mais informações sobre o erro.

![falha na implantação](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Você verá a mensagem de erro e os códigos de erro. Observe que há dois códigos de erro. O primeiro código de erro (**DeploymentFailed**) é um erro geral que não fornece os detalhes necessários para resolver o erro. O segundo código de erro (**StorageAccountNotFound**) fornece os detalhes necessários. 

![detalhes do erro](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Habilitar o log de depuração

Às vezes, você precisa obter mais informações sobre a solicitação e a resposta para descobrir o que deu errado. Usando o PowerShell ou a CLI do Azure, você pode solicitar que informações adicionais sejam registradas durante a implantação.

- PowerShell

   No PowerShell, defina o parâmetro **DeploymentDebugLogLevel** como All, ResponseContent ou RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Examine o conteúdo da solicitação com o seguinte cmdlet:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Ou o conteúdo da resposta com:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Essas informações podem ajudá-lo a determinar se um valor no modelo está sendo definido incorretamente.

- CLI do Azure

   Examine as operações de implantação, com o comando a seguir:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Modelo aninhado

   Para registrar informações de depuração de um modelo aninhado, use o elemento **debugSetting**.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```

## <a name="create-a-troubleshooting-template"></a>Criar um modelo de solução de problemas

Em alguns casos, a maneira mais fácil de solucionar problemas do seu modelo é testar partes dele. Você pode criar um modelo simplificado que permite a você se concentrar na parte que acredita estar causando o erro. Por exemplo, suponha que você esteja recebendo um erro ao fazer referência a um recurso. Em vez de lidar com um modelo inteiro, crie um modelo que retorne a parte que pode estar causando o problema. Ele pode ajudá-lo a determinar se você está sendo aprovado nos parâmetros certos, usando funções de modelo corretamente e obtendo os recursos desejados.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Ou, vamos supor que você esteja enfrentando erros de implantação, os quais você acredita terem relação com as dependências definidas incorretamente. Teste seu modelo dividindo-o em modelos simplificados. Primeiro, crie um modelo que implanta um único recurso (como um SQL Server). Quando você tiver certeza de que esse recurso foi definido corretamente, adicione um recurso depende dele (como um Banco de Dados SQL). Quando esses dois recursos estiverem definidos corretamente, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implantação de teste, exclua o grupo de recursos para garantir o teste adequado das dependências.


## <a name="next-steps"></a>Próximas etapas
* Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](resource-group-audit.md).
* Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](resource-manager-deployment-operations.md).
