---
title: Migrar para as ferramentas do Azure Resource Manager do HDInsight | Microsoft Docs
description: Como migrar para as ferramentas de desenvolvimento do Azure Resource Manager para clusters HDInsight
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: ''
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: b0a73ea89bec67cbf644cce60913981a0533360a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrando para ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters HDInsight

O HDInsight está preterindo as ferramentas baseadas em ASM (Azure Service Manager) para o HDInsight. Se você usa o Azure PowerShell, a CLI do Azure ou o SDK do .NET do HDInsight para trabalhar com clusters HDInsight, nós o incentivamos a usar as versões do Azure Resource Manager do PowerShell, da CLI e do SDK do .NET no futuro. Este artigo fornece sugestões sobre como migrar para a nova abordagem baseada no Resource Manager. Sempre que aplicável, este documento destaca as diferenças entre as abordagens do ASM e do Resource Manager em relação ao HDInsight.

> [!IMPORTANT]
> O suporte para o PowerShell, a CLI e o SDK do .NET baseado em ASM será descontinuado em **1º de janeiro de 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migrando a CLI do Azure para o Azure Resource Manager

> [!IMPORTANT]
> A CLI do Azure 2.0 não oferece suporte para trabalhar com clusters HDInsight. Ainda é possível usar a CLI do Azure 1.0 com HDInsight, mas a CLI do Azure 1.0 foi preterida.

A seguir, há comandos básicos para trabalhar com o HDInsight por meio da CLI do Azure 1.0:

* `azure hdinsight cluster create` - cria um novo cluster HDInsight
* `azure hdinsight cluster delete` - exclui um cluster HDInsight existente
* `azure hdinsight cluster show` - exibe informações sobre um cluster existente
* `azure hdinsight cluster list` - lista os clusters HDInsight de sua assinatura do Azure

Use a opção `-h` para inspecionar os parâmetros e as opções disponíveis para cada comando.

### <a name="new-commands"></a>Novos comandos
Os novos comandos disponíveis no Azure Resource Manager são:

* `azure hdinsight cluster resize` - altera dinamicamente o número de nós de trabalho no cluster
* `azure hdinsight cluster enable-http-access` - habilita o acesso HTTPs ao cluster (ativado por padrão)
* `azure hdinsight cluster disable-http-access` - desabilita o acesso HTTPs ao cluster
* `azure hdinsight script-action` - fornece comandos para criar/gerenciar as Ações de Script em um cluster
* `azure hdinsight config` - fornece comandos para criar um arquivo de configuração que pode ser usado com o comando `hdinsight cluster create` para fornecer informações de configuração.

### <a name="deprecated-commands"></a>Comandos preteridos
Se você usar os comandos `azure hdinsight job` para enviar trabalhos para o cluster HDInsight, esses comandos não estarão disponíveis por meio dos comandos do Resource Manager. Se você precisar enviar trabalhos ao HDInsight por meio de scripts de forma programática, será necessário usar as APIs REST fornecidas pelo HDInsight. Para obter mais informações sobre como enviar trabalhos usando as APIs REST, confira os seguintes documentos.

* [Executar trabalhos do MapReduce com o Hadoop no HDInsight usando a cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Executar consultas do Hive com o Hadoop no HDInsight usando a cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Executar trabalhos do Pig com o Hadoop no HDInsight usando a cURL](hadoop/apache-hadoop-use-pig-curl.md)

Para obter informações sobre outras maneiras de executar o MapReduce, Hive e Pig de forma interativa, veja [Usar o MapReduce com o Hadoop no HDInsight](hadoop/hdinsight-use-mapreduce.md), [Usar o Hive com o Hadoop no HDInsight](hadoop/hdinsight-use-hive.md) e [Usar o Pig com o Hadoop no HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exemplos
**Criando um cluster**

* Comando antigo (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Novo comando – `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Excluindo um cluster**

* Comando antigo (ASM) - `azure hdinsight cluster delete myhdicluster`
* Novo comando – `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Listar clusters**

* Comando antigo (ASM) - `azure hdinsight cluster list`
* Novo comando – `azure hdinsight cluster list`

> [!NOTE]
> Para o comando “list”, a especificação do grupo de recursos usando `-g` retornará apenas os clusters no grupo de recursos especificado.
> 
> 

**Mostrar informações de cluster**

* Comando antigo (ASM) - `azure hdinsight cluster show myhdicluster`
* Novo comando – `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrando o Azure PowerShell para o Azure Resource Manager
As informações gerais sobre o Azure PowerShell no modo Azure Resource Manager podem ser encontradas em [Usando o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

Os cmdlets do Resource Manager do Azure PowerShell podem ser instalados lado a lado com os cmdlets do ASM. Os cmdlets dos dois modos podem ser diferenciados por seus nomes.  O modo Resource Manager contém *AzureRmHDInsight* nos nomes de cmdlet, em comparação com *AzureHDInsight* no modo ASM.  Por exemplo, *New-AzureRmHDInsightCluster* versus *New-AzureHDInsightCluster*. Os parâmetros e as opções podem ter nomes novos, e há muitos novos parâmetros disponíveis ao usar o Resource Manager.  Por exemplo, vários cmdlets exigem uma nova opção chamada *-ResourceGroupName*. 

Antes de usar os cmdlets do HDInsight, é necessário se conectar à sua conta do Azure e criar um novo grupo de recursos:

* Connect-AzureRmAccount ou [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Veja [Autenticando uma entidade de serviço com o Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlets renomeados
Para listar os cmdlets do ASM do HDInsight no console do Windows PowerShell:

    help *azurermhdinsight*

A tabela a seguir lista os cmdlets do ASM e seus nomes no modo Resource Manager:

| Cmdlets do ASM | Cmdlets do Resource Manager |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Novos cmdlets
A seguir há os novos cmdlets que estão disponíveis no modo Resource Manager. 

**Cmdlets relacionados à ação de script:**

* **Get-AzureRmHDInsightPersistedScriptAction**: obtém as ações de script persistentes de um cluster e as lista em ordem cronológica ou obtém detalhes de uma ação de script persistente especificada. 
* **Get-AzureRmHDInsightScriptActionHistory**: obtém o histórico de ação de script de um cluster e o lista em ordem cronológica inversa ou obtém detalhes de uma ação de script executada anteriormente. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: remove uma ação de script persistente de um cluster HDInsight.
* **Set-AzureRmHDInsightPersistedScriptAction**: define uma ação de script executada anteriormente como uma ação de script persistente.
* **Submit-AzureRmHDInsightScriptAction**: envia uma nova ação de script para um cluster Azure HDInsight. 

Para obter mais informações de uso, veja [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

**Cmdlets relacionados à identidade do cluster:**

* **Add-AzureRmHDInsightClusterIdentity**: adiciona uma identidade de cluster a um objeto de configuração de cluster para que o cluster HDInsight possa acessar Repositórios Azure Data Lake. Veja [Criar um cluster HDInsight com o Repositório Data Lake usando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemplos
**Criar cluster**

Comando antigo (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Novo comando:

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Excluir cluster**

Comando antigo (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Novo comando:

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Listar clusters**

Comando antigo (ASM):

    Get-AzureHDInsightCluster

Novo comando:

    Get-AzureRmHDInsightCluster 

**Mostrar cluster**

Comando antigo (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Novo comando:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Outras amostras
* [Criar clusters HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Enviar trabalhos Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Enviar trabalhos do Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Enviar trabalhos do Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrando para o novo SDK do .NET do HDInsight
O [SDK do .NET do HDInsight](https://msdn.microsoft.com/library/azure/mt416619.aspx) baseado em ASM (Gerenciamento de Serviços do Azure) foi preterido. Nós o incentivamos a usar o [SDK do .NET do HDInsight baseado no Resource Manager](https://msdn.microsoft.com/library/azure/mt271028.aspx) baseado no Gerenciamento de Recursos do Azure. Os seguintes pacotes HDInsight baseados em ASM estão sendo preteridos.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Esta seção fornece sugestões de mais informações sobre como executar determinadas tarefas usando o SDK baseado no Resource Manager.

| Como usar o SDK do HDInsight baseado no Resource Manager | Links |
| --- | --- |
| Criar clusters HDInsight usando o SDK do .NET |Veja [Criar clusters HDInsight usando o SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Personalizar um cluster usando a Ação de Script com o SDK do .NET |Veja [Personalizar os clusters HDInsight do Linux usando a Ação de Script](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Autenticar aplicativos de forma interativa usando o Azure Active Directory com o SDK do .NET |Veja [Executar consultas do Hive usando o SDK do .NET](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). O trecho de código neste artigo usa o método de autenticação interativa. |
| Autenticar aplicativos de forma não interativa usando o Azure Active Directory com o SDK do .NET |Veja [Criar aplicativos não interativos para o HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Enviar um trabalho do Hive usando o SDK do .NET |Veja [Enviar trabalhos do Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Enviar um trabalho do Pig usando o SDK do .NET |Veja [Enviar trabalhos do Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Enviar um trabalho do Sqoop usando o SDK do .NET |Veja [Enviar trabalhos do Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Listar clusters HDInsight usando o SDK do .NET |Veja [Listar os clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Escalar clusters HDInsight usando o SDK do .NET |Veja [Escalar clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Conceder/revogar acesso aos clusters HDInsight usando o SDK do .NET |Veja [Conceder/revogar acesso aos clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Atualizar credenciais de usuário HTTP de clusters HDInsight usando o SDK do .NET |Veja [Atualizar credenciais de usuário HTTP de clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Encontrar a conta de armazenamento padrão para clusters HDInsight usando o SDK do .NET |Veja [Encontrar a conta de armazenamento padrão para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Excluir clusters HDInsight usando o SDK do .NET |Veja [Excluir clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemplos
Estes são alguns exemplos sobre como uma operação é executada usando o SDK baseado em ASM e o trecho de código equivalente para o SDK baseado no Resource Manager.

**Criando um cliente CRUD do cluster**

* Comando antigo (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Novo comando (Autorização de entidade de serviço)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Novo comando (Autorização de usuário)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Criando um cluster**

* Comando antigo (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Novo comando
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Habilitando o acesso HTTP**

* Comando antigo (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Novo comando
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Excluindo um cluster**

* Comando antigo (ASM)
  
        client.DeleteCluster(dnsName);
* Novo comando
  
        client.Clusters.Delete(resourceGroup, dnsname);

