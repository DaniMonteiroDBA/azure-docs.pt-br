---
title: Operacionalizar o Microsoft R Server no HDInsight - Azure| Microsoft Docs
description: Saiba como operacionalizar o Microsoft R Server no Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 6de6e78d9b4ad68d268b59cff18c75fbdd7be757
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>Operar o cluster do Microsoft R Server no Azure HDInsight

Depois que você tiver usado o cluster do Microsoft R Server no HDInsight para concluir os modelagem de dados, você pode utilizar o modelo para fazer previsões. Esse artigo fornece instruções sobre como fazer essa tarefa.

## <a name="prerequisites"></a>pré-requisitos

* **Um cluser do Microsoft R Server no HDInsight**: para obter instruções, consulte [ Iniciar com o R Server no HDInsight](r-server-get-started.md).

* **Um cliente Secure Shell (SSH)**: um cliente SSH é usado para se conectar ao cluster HDInsight remotamente e executar comandos diretamente no cluster. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>Opera o Microsoft R Server com uma caixa de configuração

1. SSH no nó de borda.  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Para obter instruções sobre como usar SSH com o Azure HDInsight, consulte [usar SSH com HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Altere o diretório para a versão relevante e sudo do dll dot net: 

    - Para o Microsoft R Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Para o Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. São apresentadas as opções para escolher. Escolha a primeira opção, conforme mostrado na seguinte captura de tela, para **Configurar o Microsoft R Server para operacionalização**.

    ![operações de uma caixa](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Agora é apresentada a opção de escolher como você quer operar o Microsoft R Server. A partir das opções apresentadas, escolha a primeiro inserindo **A**.

    ![operações de uma caixa](./media/r-server-operationalize/admin-util-one-box-2.png)

5. Quando solicitado, digite e insira novamente a senha para um usuário de administrador local.

6. Você deve ver sugestões de saídas que a operação foi bem-sucedida. Você também precisará selecionar outra opção de menu. Selecione E para retornar ao menu principal.

    ![operações de uma caixa](./media/r-server-operationalize/admin-util-one-box-3.png)

7. Como opção, você pode executar verificações de diagnóstico com um teste de diagnóstico da seguinte forma:

    a. No menu principal, selecione **6** para executar testes de diagnóstico.

    ![operações de uma caixa](./media/r-server-operationalize/diagnostic-1.png)

    b. No menu de Testes de Diagnóstico, selecione **A**. Quando solicitado, insira a senha que você forneceu para o usuário de administrador local.

    ![operações de uma caixa](./media/r-server-operationalize/diagnostic-2.png)

    c. Verifique se a saída mostra que a integridade geral é uma passagem.

    ![operações de uma caixa](./media/r-server-operationalize/diagnostic-3.png)

    d. Nas opções de menu apresentadas, insira **E** para retornar ao menu principal e, em seguida, digite **8** para sair do utilitário de administração.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Atrasos longos ao consumir um serviço web no Spark

Se você tiver longos atrasos ao tentar consumir um serviço web criado com as funções mrsdeploy no contexto de computação do Spark, talvez seja necessário adicionar algumas pastas ausentes. O aplicativo Spark pertence a um usuário chamado '*rserve2*' sempre que ele é chamado de um serviço web usando as funções mrsdeploy. Para resolver o problema:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Nesse estágio, a configuração de operacionalização foi concluída. Agora, você pode usar o pacote `mrsdeploy`em seu RClient para conectar a operacionalização no nó de borda e começar a usar seus recursos como a [execução remota](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) e os [serviços web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). A depender se o cluster está configurado em uma rede virtual ou não, você pode precisar configurar o túnel de encaminhamento da porta por meio de logon de SSH. As seções a seguir explicam como configurar esse túnel.

### <a name="r-server-cluster-on-virtual-network"></a>Cluster do Microsoft R Server em rede virtual

Lembre-se de permitir o tráfego pela porta 12800 para o nó de borda. Dessa forma, você pode usar o nó de borda para se conectar ao recurso de operacionalização.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Se o `remoteLogin()` não puder se conectar ao nó de borda, mas for possível executar o SSH para o nó de borda, você precisará verificar se a regra que permite o tráfego na porta 12800 foi configurada corretamente. Caso o problema persista, você pode usar uma solução alternativa configurando o túnel de encaminhamento da porta por meio de SSH. Para obter instruções, consulte a seção a seguir:

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>Cluster do Microsoft R Server não configurado em rede virtual

Se o cluster não estiver configurado na rede virtual ou se você estiver tendo problemas com a conectividade por meio da rede virtual, use o túnel SSH de encaminhamento da porta:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Depois que a sessão SSH estiver ativa, o tráfego da porta 12800 da máquina será encaminhado para a porta 12800 do nó de borda por meio de uma sessão SSH. Use `127.0.0.1:12800` no seu método `remoteLogin()`. Isso faz logon na operacionalização do nó de borda por meio do encaminhamento de porta.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Nós de computação operacional de dimensionamento nos nós de trabalho do HDInsight

Para expandir os nós de computação, desativa os nós de trabalho e, em seguida, configure os nós de computação nos nós de trabalho desativados.

### <a name="step-1-decommission-the-worker-nodes"></a>Etapa 1: Desativar os nós de trabalho

O cluster do Microsoft R Server não é gerenciado por meio de YARN. Se os nós de trabalho não forem encerrados, o Gerenciador de Recursos YARN não funcionará como esperado porque não estará ciente dos recursos consumidos pelo servidor. Para evitar essa situação,recomendamos desativar os nós de trabalho antes de expandir os nós de computação.

Siga estas etapas para desativar os nós de trabalho:

1. Faça logon no console de Ambari do cluster e clique na guia **Hosts**.

2. Selecione nós de trabalho (para ser desativado).

3. Clique em **Ações** > **Hosts Selecionados** > **Hosts** > **Ativar o modo de manutenção**. Por exemplo, na imagem a seguir, selecionamos wn3 e wn4 para desativar.  

   ![encerrar nós de trabalho](./media/r-server-operationalize/get-started-operationalization.png)  

* Selecione **Ações** > **Hosts Selecionados** > **DataNodes** > clique em **Desativar**
* Selecione **Ações** > **Hosts Selecionados** > **NodeManagers** > clique em **Desativar**
* Selecione **Ações** > **Hosts Selecionados** > **DataNodes** > clique em **Parar**
* Selecione **Ações** > **Hosts Selecionados** > **NodeManagers** > clique em **Parar**
* Selecione **Ações** > **Hosts Selecionados** > **Hosts** > clique em **Parar Todos os Componentes**
* Desmarque os nós de trabalho e selecione os nós de cabeçalho.
* Selecione **Ações** > **Hosts selecionados** > "**Hosts** > **Reiniciar Todos os Componentes**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Etapa 2: Configurar nós de computação em cada nó de trabalho encerrado

1. SSH em cada nó de trabalho desativado.

2. Execute o utilitário de administração usando a DLL relevante para o cluster do Microsoft R Server que você tem. Para R Server 9.1, execute o seguinte:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Insira **1** para selecionar a opção **Configurar o Microsoft R Server para operacionalização**.

4. Insira **C** para selecionar a opção `C. Compute node`. Isso configura o nó de computação no nó de trabalho.

5. Saia do utilitário de administração.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Etapa 3: adicionar detalhes de nós de computação no nó da Web

Depois que todos os nós de trabalho encerrados forem configurados para executar o nó de computação, volte ao nó de borda e adicione os endereços IP dos nós de trabalho encerrados na configuração do nó de web do Microsoft R Server:

1. SSH no nó de borda.

2. Execute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

3. Procure a seção "URIs" e adicione o IP do nó de trabalho e detalhes de porta.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar o cluster do Microsoft R Server no HDInsight](r-server-hdinsight-manage.md)
* [Opções de contexto de computação para cluster do Microsoft R Server no HDInsight](r-server-compute-contexts.md)
* [Opções de Armazenamento do Microsoft Azure para cluster do Microsoft R Server no HDInsight](r-server-storage.md)
