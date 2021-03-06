---
title: Transformar dados usando o Spark no Azure Data Factory | Microsoft Docs
description: Este tutorial fornece instruções passo a passo para transformar dados usando uma atividade Spark no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: douglasl
ms.openlocfilehash: e32fa771595fdc4bf5fe54ec14630961d467d40f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Transformar os dados na nuvem usando uma atividade Spark no Azure Data Factory
Neste tutorial, você pode usar o Portal do Azure para criar um pipeline do Azure Data Factory. Este pipeline transforma os dados usando uma atividade Spark e um serviço vinculado do Azure HDInsight sob demanda. 

Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Criar um data factory. 
> * Crie um pipeline que usa uma atividade Spark.
> * Dispare uma execução de pipeline.
> * Monitorar a execução de pipeline.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>pré-requisitos
* **Conta de Armazenamento do Azure**. Você cria um script Python e um arquivo de entrada e os carrega no Armazenamento do Azure. A saída do programa Spark é armazenada nessa conta de armazenamento. O cluster do Spark sob demanda usa a mesma conta de armazenamento que o respectivo armazenamento primário.  
* **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Carregar o script Python em sua conta de Armazenamento de Blobs
1. Crie um arquivo de Python chamado **WordCount_Spark.py** com o seguinte conteúdo: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Substitua *&lt;storageAccountName&gt;* pelo nome da sua conta de Armazenamento do Azure. Em seguida, salve o arquivo. 
3. No Armazenamento de Blobs do Azure, crie um contêiner denominado **adftutorial**, se ele não existir. 
4. Crie uma pasta chamada **spark**.
5. Crie uma subpasta chamada **script** na pasta **spark**. 
6. Carregue o arquivo **WordCount_Spark.py** na subpasta **script**. 


### <a name="upload-the-input-file"></a>Carregue o arquivo de entrada
1. Crie um arquivo chamado **minecraftstory.txt** com um pouco de texto. O programa Spark conta o número de palavras no texto. 
2. Criar uma subpasta chamada **inputfiles** na pasta **spark**. 
3. Carregue o arquivo **minecraftstory.txt** na subpasta **inputfiles**. 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. Selecione **Novo** no menu à esquerda, selecione **Dados + Análise**e, em seguida, selecione **Data Factory**. 
   
   ![Seleção de Data Factory no painel "Novo"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. No painel **Novo data factory**, insira **ADFTutorialDataFactory** no campo **Nome**. 
      
   ![Painel "Novo data factory"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você vir o erro a seguir, altere o nome do data factory. (Por exemplo, use **&lt;seunome&gt;ADFTutorialDataFactory**). Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. Para **Assinatura**, selecione a assinatura do Azure na qual você deseja criar o data factory. 
4. Em **Grupo de Recursos**, use uma das seguintes etapas:
     
   - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
   Algumas das etapas neste guia de início rápido supõem que você usa o nome **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
5. Para **Versão**, selecione **V2 (Versão prévia)**.
6. Em **Local**, selecione uma localização para o data factory. 

   Atualmente, o Data Factory V2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (como o Armazenamento do Azure e Banco de Dados SQL do Azure) e serviços de computação (como o HDInsight) usados pelo Data Factory podem estar em outras regiões.
7. Selecione **Fixar no painel**.     
8. Selecione **Criar**.
9. No painel, o bloco com o seguinte status é exibido: **Implantando o Data Factory**: 

   ![Bloco “Implantando Data Factory”](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
10. Após a criação, a página do **Data Factory** será exibida. Selecione o bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário do Data Factory em uma guia separada.

    ![Página inicial do Data Factory, com o bloco “Criar e Monitorar"](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Criar serviços vinculados
Você cria dois serviços vinculados nesta seção: 
    
- Um **serviço vinculado do Armazenamento do Azure** que vincula uma conta de Armazenamento do Azure ao data factory. Esse armazenamento é usado pelo cluster HDInsight sob demanda. Ele também contém o script Spark a ser executado. 
- Um **serviço vinculado do HDInsight sob demanda**. O Azure Data Factory cria automaticamente um cluster HDInsight e executa o programa Spark. Em seguida, ele exclui o cluster HDInsight após o cluster ficar ocioso por um tempo pré-configurado. 

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento do Azure

1. Na página **Introdução**, acesse a guia **Editar** no painel esquerdo. 

   ![Página “Introdução”](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Selecione **Conexões** na parte inferior da janela e, depois, selecione **+ Novo**. 

   ![Botões para criar uma nova conexão](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. Na janela **Novo Serviço Vinculado**, selecione **Armazenamento de Dados** > **Armazenamento de Blobs do Azure** e selecione **Continuar**. 

   ![Selecionar o bloco "Armazenamento de Blobs do Azure"](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Em **Nome da conta de Armazenamento**, selecione o nome na lista e selecione **Salvar**. 

   ![Caixa para especificar o nome da conta de Armazenamento](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Criar um serviço vinculado do HDInsight sob demanda

1. Selecione o botão **+ Novo** novamente para criar outro serviço vinculado. 
2. Na janela **Novo Serviço Vinculado**, selecione **Computação** > **Azure HDInsight** e selecione **Continuar**. 

   ![Selecionar o bloco "Azure HDInsight"](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. Na janela **Novo Serviço Vinculado**, execute as seguintes etapas: 

   a. Para **Name**, insira **AzureHDInsightLinkedService**.
   
   b. Para **Tipo**, confirme se **HDInsight sob demanda** está selecionado.
   
   c. Para **Serviço Vinculado do Armazenamento do Azure**, selecione **AzureStorage1**. Você criou esse serviço vinculado anteriormente. Se você usou um nome diferente, especifique o nome correto aqui. 
   
   d. Para o campo **Tipo de cluster**, selecione **spark**.
   
   e. Para **Id da entidade de serviço**, insira a ID da entidade de serviço que tem permissão para criar um cluster do HDInsight. 
   
      Essa entidade de serviço precisa ser um membro da função de Colaborador de assinatura ou o grupo de recursos em que o cluster é criado. Para obter mais informações, consulte [Criar uma entidade de serviço e aplicativo do Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md).
   
   f. Para **Chave da entidade de serviço**, insira a chave. 
   
   g. Para **Grupo de recursos**, selecione o mesmo grupo de recursos que você usou ao criar o data factory. O cluster Spark é criado nesse grupo de recursos. 
   
   h. Expandir o **Tipo de sistema operacional**.
   
   i. Insira um nome para o usuário do cluster. 
   
   j. Insira a senha para o usuário. 
   
   k. Selecione **Salvar**. 

   ![Configurações de serviço vinculado ao HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> O Azure HDInsight limita o número total de núcleos que você pode usar em cada região do Azure a qual ele dá suporte. Para o serviço vinculado do HDInsight sob demanda, o cluster HDInsight é criado na mesma localização do Armazenamento do Azure usada como o armazenamento primário. Verifique se você tem cotas de núcleo suficientes para que o cluster seja criado com êxito. Para obter mais informações, consulte [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione o botão **+** (adição) e, em seguida, selecione **Pipeline** no menu.

   ![Botões para criar um novo pipeline](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
2. Na caixa de ferramentas **Atividades**, expanda **HDInsight**. Arraste a atividade **Spark** da caixa de ferramentas **Atividades** para a superfície do designer do pipeline. 

   ![Arrastar a atividade Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
3. Na parte inferior da janela de propriedades da atividade **Spark**, execute as seguintes etapas: 

   a. Troque para a guia **HDI Cluster**.
   
   b. Selecione **AzureHDInsightLinkedService** (criado no procedimento anterior). 
        
   ![Especificar um serviço vinculado do HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
4. Alterne para a guia **Script/Jar** e execute estas etapas: 

   a. Para **Serviço Vinculado do Trabalho**, selecione **AzureStorage1**.
   
   b. Selecione **Procurar Armazenamento**.

   ![Especificar o script Spark na guia "Script/Jar"](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Procure na pasta **adftutorial/spark/script**, selecione **WordCount_Spark.py** e selecione **Concluir**.      

5. Para validar o pipeline, selecione o botão **Validar** na barra de ferramentas. Selecione a **>>** (seta para a direita) para fechar a janela de validação. 
    
   ![Botão "Validar"](./media/tutorial-transform-data-spark-portal/validate-button.png)
6. Selecione **Publicar Tudo**. A IU de Data Factory publica entidades (serviços vinculados e pipeline) para o serviço Azure Data Factory. 
    
   ![Botão "Publicar Tudo"](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline
Selecione **Gatilho** na barra de ferramentas e selecione **Disparar Agora**. 

![Botões "Gatilho" e "Disparar Agora"](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Alterne para a guia **Monitorar**. Verifique se o pipeline está sendo executado. Leva aproximadamente 20 minutos para criar um cluster Spark. 
   
2. Selecione **Atualizar** periodicamente para verificar o status da execução do pipeline. 

   ![Guia para monitorar as execuções do pipeline, com o botão “Atualizar”](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

3. Para ver as execuções de atividade associadas com a execução de pipeline, selecione **Exibir as Execuções de Atividade** na coluna **Ações**.

   ![Status da execução do pipeline](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   Você pode alternar novamente para o modo de execução do pipeline selecionando o link **Pipelines** na parte superior.

   ![Modo de exibição "Execuções de Atividade"](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificar a saída
Verifique se o arquivo de saída é criado na pasta spark/outputfiles/wordcount no contêiner do adftutorial. 

![Local do arquivo de saída](./media/tutorial-transform-data-spark-portal/verity-output.png)

O arquivo deve ter cada palavra do texto do arquivo de entrada e o número de vezes que a palavra apareceu no arquivo. Por exemplo:  

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, o pipeline transforma dados usando a atividade Spark e um serviço vinculado do HDInsight sob demanda. Você aprendeu como: 

> [!div class="checklist"]
> * Criar um data factory. 
> * Crie um pipeline que usa uma atividade Spark.
> * Dispare uma execução de pipeline.
> * Monitorar a execução de pipeline.

Avance para o próximo tutorial para aprender como transformar dados executando o script Hive em um cluster do Azure HDInsight que está em uma rede virtual: 

> [!div class="nextstepaction"]
> [Tutorial: transformar dados usando o Hive na Rede Virtual do Azure](tutorial-transform-data-hive-virtual-network-portal.md).





