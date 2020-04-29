---
title: 'Zelf studie: Azure Automation runbooks gebruiken om clusters te maken-Azure HDInsight'
description: Meer informatie over het maken en verwijderen van Azure HDInsight-clusters met scripts die worden uitgevoerd in de Cloud met behulp van Azure Automation runbooks.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75553511"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Zelf studie: Azure HDInsight-clusters maken met Azure Automation

Met Azure Automation kunt u scripts maken die worden uitgevoerd in de Cloud en Azure-resources op aanvraag of op basis van een schema beheren. In dit artikel wordt beschreven hoe u Power shell-runbooks maakt om Azure HDInsight-clusters te maken en verwijderen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Modules installeren die nodig zijn voor interactie met HDInsight.
> * Referenties maken en opslaan die nodig zijn tijdens het maken van het cluster.
> * Maak een nieuw runbook van Azure Automation om een HDInsight-cluster te maken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een bestaand [Azure Automation-account](../automation/automation-quickstart-create-account.md).
* Een bestaand [Azure Storage-account](../storage/common/storage-account-create.md)dat wordt gebruikt als cluster opslag.

## <a name="install-hdinsight-modules"></a>HDInsight-modules installeren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer uw Azure Automation accounts.
1. Selecteer **modules galerie** onder **gedeelde resources**.
1. Typ **AzureRM. profile** in het vak en druk op ENTER om te zoeken. Selecteer het beschik bare Zoek resultaat.
1. Selecteer **importeren**in het scherm **AzureRM. profile** . Schakel het selectie vakje in om Azure-modules bij te werken en selecteer vervolgens **OK**.

    ![AzureRM. profile-module importeren](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Ga terug naar de galerie modules door **modules galerie** te selecteren onder **gedeelde resources**.
1. Typ **HDInsight**. Selecteer **AzureRM. HDInsight**.

    ![Bladeren door HDInsight-modules](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. Selecteer **importeren** en **OK**in het deel venster **AzureRM. HDInsight** .

    ![AzureRM. HDInsight-module importeren](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Referenties maken

1. Onder **gedeelde bronnen**selecteert u **referenties**.
1. Selecteer **een referentie toevoegen**.
1. Voer de vereiste gegevens in het deel venster **nieuwe referentie** in. Deze referentie is het wacht woord van het cluster op te slaan, zodat u zich kunt aanmelden bij Ambari.

    | Eigenschap | Waarde |
    | --- | --- |
    | Naam | `cluster-password` |
    | Gebruikersnaam | `admin` |
    | Wachtwoord | `SECURE_PASSWORD` |
    | Wachtwoord bevestigen | `SECURE_PASSWORD` |

1. Selecteer **Maken**.
1. Herhaal hetzelfde proces voor een nieuwe referentie `ssh-password` met een gebruikers `sshuser` naam en een wacht woord van uw keuze. Selecteer **Maken**. Deze referentie is om het SSH-wacht woord voor uw cluster op te slaan.

    ![referentie maken](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Een runbook maken om een cluster te maken

1. Selecteer **Runbooks** onder **proces automatisering**.
1. Selecteer **een Runbook maken**.
1. Voer in het deel venster **een Runbook maken** een naam in voor het runbook, zoals `hdinsight-cluster-create`. Selecteer **Power shell** in de vervolg keuzelijst **type Runbook** .
1. Selecteer **Maken**.

    ![runbook maken](./media/manage-clusters-runbooks/create-runbook.png)

1. Voer de volgende code in het scherm **Power shell-Runbook bewerken** in en selecteer **publiceren**:

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

1. Selecteer **Runbooks** onder **proces automatisering**.
1. Selecteer **een Runbook maken**.
1. Voer in het deel venster **een Runbook maken** een naam in voor het runbook, zoals `hdinsight-cluster-delete`. Selecteer **Power shell** in de vervolg keuzelijst **type Runbook** .
1. Selecteer **Maken**.
1. Voer de volgende code in het scherm **Power shell-Runbook bewerken** in en selecteer **publiceren**:

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

1. Bekijk de lijst met Runbooks voor uw Automation-account door **Runbooks** te selecteren onder **proces automatisering**.
1. Selecteer `hdinsight-cluster-create`of de naam die u hebt gebruikt bij het maken van het runbook voor het maken van het cluster.
1. Selecteer **starten** om het runbook direct uit te voeren. U kunt ook runbooks plannen die periodiek worden uitgevoerd. Zie [een Runbook plannen in azure Automation](../automation/shared-resources/schedules.md)
1. Voer de vereiste para meters voor het script in en selecteer **OK**. Hiermee maakt u een nieuw HDInsight-cluster met de naam die u hebt opgegeven in de para meter **clustername** .

    ![runbook voor het maken van het cluster uitvoeren](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Een cluster verwijderen

Verwijder het cluster door het `hdinsight-cluster-delete` runbook te selecteren dat u hebt gemaakt. Selecteer **Start**, voer de para meter **clustername** en sselect **OK**in.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u het Azure Automation account dat is gemaakt om onbedoelde kosten te voor komen. Als u dit wilt doen, gaat u naar de Azure Portal, selecteert u de resource groep waarin u het Azure Automation account hebt gemaakt, selecteert u het Automation-account en selecteert u vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Apache Hadoop clusters in HDInsight beheren door gebruik te maken van Azure PowerShell](hdinsight-administer-use-powershell.md)