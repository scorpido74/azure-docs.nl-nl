---
title: 'Zelfstudie: Azure Automation-runbooks gebruiken om clusters te maken - Azure HDInsight'
description: Meer informatie over het maken en verwijderen van Azure HDInsight-clusters met scripts die in de cloud worden uitgevoerd met Azure Automation-runbooks.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75553511"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Zelfstudie: Azure HDInsight-clusters maken met Azure Automation

Met Azure Automation u scripts maken die in de cloud worden uitgevoerd en Azure-resources on-demand of op basis van een planning beheren. In dit artikel wordt beschreven hoe u PowerShell-runbooks maakt om Azure HDInsight-clusters te maken en te verwijderen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Installeer modules die nodig zijn voor interactie met HDInsight.
> * Maak en sla referenties op die nodig zijn tijdens het maken van het cluster.
> * Maak een nieuwe Azure Automation-runbook om een HDInsight-cluster te maken.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een bestaand [Azure Automation-account](../automation/automation-quickstart-create-account.md).
* Een bestaand [Azure Storage-account](../storage/common/storage-account-create.md), dat wordt gebruikt als clusteropslag.

## <a name="install-hdinsight-modules"></a>HDInsight-modules installeren

1. Meld u aan bij de [Azure-portal.](https://portal.azure.com)
1. Selecteer uw Azure Automation-accounts.
1. Selecteer **galerie Modules** onder Gedeelde **bronnen**.
1. Typ **AzureRM.Profile** in het vak en druk op Enter om te zoeken. Selecteer het beschikbare zoekresultaat.
1. **Selecteer**importeren in het scherm **AzureRM.profile** . Schakel het selectievakje in om Azure-modules bij te werken en selecteer **OK**.

    ![AzureRM.profilemodule importeren](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Ga terug naar de modulesgalerie door **galerie Modules** te selecteren onder **Gedeelde bronnen**.
1. Typ **HDInsight**. Selecteer **AzureRM.HDInsight**.

    ![door HDInsight-modules bladeren](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. Selecteer **importeren** en **OK**in het deelvenster **AzureRM.HDInsight.**

    ![AzureRM.HDInsight-module importeren](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Referenties maken

1. Selecteer Onder **Gedeelde bronnen**de optie **Referenties**.
1. Selecteer **Een referentie toevoegen**.
1. Voer de vereiste informatie in in het deelvenster **Nieuwe referenties.** Deze referentie is om het clusterwachtwoord op te slaan, waarmee u zich aanmelden bij Ambari.

    | Eigenschap | Waarde |
    | --- | --- |
    | Name | `cluster-password` |
    | Gebruikersnaam | `admin` |
    | Wachtwoord | `SECURE_PASSWORD` |
    | Wachtwoord bevestigen | `SECURE_PASSWORD` |

1. Selecteer **Maken**.
1. Herhaal hetzelfde proces voor `ssh-password` een `sshuser` nieuwe referentie met gebruikersnaam en een wachtwoord van uw keuze. Selecteer **Maken**. Deze referentie is om het SSH-wachtwoord voor uw cluster op te slaan.

    ![referentie maken](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Een runbook maken om een cluster te maken

1. Selecteer **Runbooks** onder **Procesautomatisering**.
1. Selecteer **Een runbook maken**.
1. Voer in het deelvenster **Een runbook maken** een naam `hdinsight-cluster-create`in voor het runbook, zoals . Selecteer **Powershell** in de vervolgkeuzelijst **Runbook type.**
1. Selecteer **Maken**.

    ![runbook maken](./media/manage-clusters-runbooks/create-runbook.png)

1. Voer de volgende code in op het scherm **PowerShell-runbook bewerken** en selecteer **Publiceren:**

    ![runbook publiceren](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Een runbook maken om een cluster te verwijderen

1. Selecteer **Runbooks** onder **Procesautomatisering**.
1. Selecteer **Een runbook maken**.
1. Voer in het deelvenster **Een runbook maken** een naam `hdinsight-cluster-delete`in voor het runbook, zoals . Selecteer **Powershell** in de vervolgkeuzelijst **Runbook type.**
1. Selecteer **Maken**.
1. Voer de volgende code in op het scherm **PowerShell-runbook bewerken** en selecteer **Publiceren:**

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Runbooks uitvoeren

### <a name="create-a-cluster"></a>Een cluster maken

1. Bekijk de lijst met runbooks voor uw automatiseringsaccount door **Runbooks** te selecteren onder **Procesautomatisering**.
1. Selecteer `hdinsight-cluster-create`of de naam die u hebt gebruikt bij het maken van uw runbook voor het maken van het clustermaken.
1. Selecteer **Start** om het runbook onmiddellijk uit te voeren. U ook plannen dat loopboeken periodiek worden uitgevoerd. Zie [Een runbook plannen in Azure Automation](../automation/shared-resources/schedules.md)
1. Voer de vereiste parameters voor het script in en selecteer **OK**. Hiermee wordt een nieuw HDInsight-cluster gemaakt met de naam die u hebt opgegeven in de parameter **CLUSTERNAME.**

    ![clusterrunbook maken uitvoeren](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Een cluster verwijderen

Verwijder het cluster `hdinsight-cluster-delete` door de runbook te selecteren die u hebt gemaakt. Selecteer **Start**, voer de parameter **CLUSTERNAME** in en selecteer **OK**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, verwijdert u het Azure Automation-account dat is gemaakt om onbedoelde kosten te voorkomen. Ga hiervoor naar de Azure-portal, selecteer de brongroep waarin u het Azure Automation-account hebt gemaakt, selecteer het automatiseringsaccount en selecteer **Vervolgens Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Apache Hadoop-clusters beheren in HDInsight met Azure PowerShell](hdinsight-administer-use-powershell.md)