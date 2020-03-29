---
title: HDInsight-clusters maken met Azure Data Lake Storage Gen1 als standaardopslag met PowerShell | Microsoft Docs'
description: Azure PowerShell gebruiken om HDInsight-clusters te maken en te gebruiken met Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c57a45145d9abc43d0ca79839ea297dfc025db9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161420"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>HDInsight-clusters maken met Azure Data Lake Storage Gen1 als standaardopslag met PowerShell

> [!div class="op_single_selector"]
> * [De Azure-portal gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell gebruiken (voor standaardopslag)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell gebruiken (voor extra opslag)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resourcebeheer gebruiken](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Meer informatie over het gebruik van Azure PowerShell om Azure HDInsight-clusters met Azure Data Lake Storage Gen1 te configureren als standaardopslag. Zie [Een HDInsight-cluster](data-lake-store-hdinsight-hadoop-use-powershell.md)maken met Data Lake Storage Gen1 als extra opslag voor instructies over het maken van een HDInsight-cluster met Data Lake Storage Gen1 als extra opslag.

Hier volgen enkele belangrijke overwegingen voor het gebruik van HDInsight met Data Lake Storage Gen1:

* De optie om HDInsight-clusters te maken met toegang tot Data Lake Storage Gen1 als standaardopslag is beschikbaar voor HDInsight-versie 3.5 en 3.6.

* De optie om HDInsight-clusters te maken met toegang tot Data Lake Storage Gen1 als standaardopslag is *niet beschikbaar* voor HDInsight Premium-clusters.

Volg de instructies in de volgende vijf secties om HDInsight te configureren om met Data Lake Storage Gen1 te werken met PowerShell.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u met deze zelfstudie begint, moet u ervoor zorgen dat u aan de volgende vereisten voldoet:

* **Een Azure-abonnement:** Ga naar [Gratis proefversie van Azure .](https://azure.microsoft.com/pricing/free-trial/)
* **Azure PowerShell 1.0 of hoger:** zie [hoe u PowerShell installeert en configureert.](/powershell/azure/overview)
* **Windows Software Development Kit (SDK)**: Ga naar [Downloads en hulpprogramma's voor Windows 10](https://dev.windows.com/downloads)om Windows SDK te installeren. De SDK wordt gebruikt om een beveiligingscertificaat te maken.
* **Hoofd van azure Active Directory-service**: in deze zelfstudie wordt beschreven hoe u een serviceprincipal maakt in Azure Active Directory (Azure AD). Als u echter een serviceprincipal wilt maken, moet u een Azure AD-beheerder zijn. Als u een beheerder bent, u deze vereiste overslaan en doorgaan met de zelfstudie.

    >[!NOTE]
    >U een serviceprincipal alleen maken als u een Azure AD-beheerder bent. Uw Azure AD-beheerder moet een serviceprincipal maken voordat u een HDInsight-cluster maken met Data Lake Storage Gen1. De serviceprincipal moet worden gemaakt met een certificaat, zoals beschreven in [Een serviceprincipal maken met certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken

Ga als volgt te werk om een Data Lake Storage Gen1-account aan te maken:

1. Open vanaf uw bureaublad een PowerShell-venster en voer de onderstaande fragmenten in. Wanneer u wordt gevraagd zich aan te melden, meldt u zich aan als een van de abonnementsbeheerders of -eigenaren. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Als u de Data Lake Storage Gen1-bronprovider `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`registreert en een fout ontvangt die vergelijkbaar is met, wordt uw abonnement mogelijk niet op de witte lijst gelijst voor Data Lake Storage Gen1. Als u uw Azure-abonnement voor Data Lake Storage Gen1 wilt inschakelen, volgt u de instructies in [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure-portal.](data-lake-store-get-started-portal.md)
    >

2. Een Data Lake Storage Gen1-account is gekoppeld aan een Azure-brongroep. Begin met het maken van een resourcegroep.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    De uitvoer ziet er als volgt uit:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Maak een Data Lake Storage Gen1-account aan. De door u opgegeven accountnaam mag alleen kleine letters en cijfers bevatten.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

4. Als u Data Lake Storage Gen1 als standaardopslag gebruikt, moet u een hoofdpad opgeven waarnaar de clusterspecifieke bestanden worden gekopieerd tijdens het maken van het cluster. Als u een hoofdpad wilt maken, dat **/clusters/hdiadlcluster** in het fragment is, gebruikt u de volgende cmdlets:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Verificatie instellen voor op rollen gebaseerde toegang tot Data Lake Storage Gen1
Elk Azure-abonnement is gekoppeld aan een Azure AD-entiteit. Gebruikers en services die toegang hebben tot abonnementsbronnen met behulp van de Azure-portal of de Azure Resource Manager API, moeten zich eerst verifiëren met Azure AD. Toegang wordt verleend tot Azure-abonnementen en -services door ze de juiste rol toe te geven aan een Azure-bron. Voor services identificeert een serviceprincipal de service in Azure AD.

In deze sectie ziet u hoe u een toepassingsservice, zoals HDInsight, toegang verleent tot een Azure-bron (het Data Lake Storage Gen1-account dat u eerder hebt gemaakt). Dit doe je door een serviceprincipal voor de toepassing te maken en er rollen aan toe te wijzen via PowerShell.

Als u Active Directory-verificatie voor Data Lake Storage Gen1 wilt instellen, voert u de taken uit in de volgende twee secties.

### <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken
Zorg ervoor dat [Windows SDK](https://dev.windows.com/en-us/downloads) is geïnstalleerd voordat u verdergaat met de stappen in deze sectie. U moet ook een map hebben gemaakt, zoals *C:\mycertdir,* waar u het certificaat maakt.

1. Ga vanuit het PowerShell-venster naar de locatie waar u Windows SDK hebt geïnstalleerd (meestal *C:\Program Files (x86)\Windows Kits\10\bin\x86)* en gebruik het hulpprogramma [MakeCert][makecert] om een zelfondertekend certificaat en een privésleutel te maken. Gebruik de volgende opdrachten:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    U wordt gevraagd het wachtwoord van de privésleutel in te voeren. Nadat de opdracht is uitgevoerd, ziet u **CertFile.cer** en **mykey.pvk** in de certificaatmap die u hebt opgegeven.
2. Gebruik het [Pvk2Pfx-hulpprogramma][pvk2pfx] om de .pvk- en .cer-bestanden die MakeCert heeft gemaakt om te zetten in een PFX-bestand. Voer de volgende opdracht uit:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Wanneer u wordt gevraagd, voert u het wachtwoord van de privésleutel in dat u eerder hebt opgegeven. De waarde die u opgeeft voor de **parameter -po** is het wachtwoord dat is gekoppeld aan het PFX-bestand. Nadat de opdracht is voltooid, ziet u ook een **CertFile.pfx** in de certificaatmap die u hebt opgegeven.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Een Azure AD en een serviceprincipal maken
In deze sectie maakt u een serviceprincipal voor een Azure AD-toepassing, wijst u een rol toe aan de serviceprincipal en verifieert u als serviceprincipal door een certificaat te verstrekken. Als u een toepassing wilt maken in Azure AD, voert u de volgende opdrachten uit:

1. Plak de volgende cmdlets in het PowerShell-consolevenster. Controleer of de waarde die u opgeeft voor de eigenschap **-DisplayName** uniek is. De waarden voor **-HomePage** en **-IdentiferUris** zijn tijdelijke aanduidingen en worden niet geverifieerd.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Maak een serviceprincipal met behulp van de toepassings-id.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Geef de hoofdgroep van de service toegang tot de hoofdmap gegevensmeeropslag gen1 en alle mappen in het hoofdpad dat u eerder hebt opgegeven. Gebruik de volgende cmdlets:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Maak een HDInsight Linux-cluster met Data Lake Storage Gen1 als standaardopslag

In deze sectie maakt u een HDInsight Hadoop Linux-cluster met Data Lake Storage Gen1 als standaardopslag. Voor deze release moeten het HDInsight-cluster en Data Lake Storage Gen1 zich op dezelfde locatie bevinden.

1. Haal de abonnementstenant-id op en sla deze op om deze later te gebruiken.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Maak het HDInsight-cluster met behulp van de volgende cmdlets:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Nadat de cmdlet is voltooid, ziet u een uitvoer met de clusterdetails.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Testtaken uitvoeren op het HDInsight-cluster om Data Lake Storage Gen1 te gebruiken
Nadat u een HDInsight-cluster hebt geconfigureerd, u er testtaken op uitvoeren om ervoor te zorgen dat het toegang heeft tot Data Lake Storage Gen1. Voer hiervoor een voorbeeldtaak van Hive uit om een tabel te maken die de voorbeeldgegevens gebruikt die al beschikbaar zijn in Data Lake Storage Gen1 bij * \<clusterroot>/voorbeeld/data/sample.log*.

In deze sectie maakt u een Secure Shell -verbinding (SSH) in het HDInsight Linux-cluster dat u hebt gemaakt en voert u een voorbeeldhive-query uit.

* Als u een Windows-client gebruikt om een SSH-verbinding in het cluster te maken, raadpleegt u [SSH gebruiken met het op Linux gebaseerde Hadoop op HDInsight van Windows.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)
* Als u een Linux-client gebruikt om een SSH-verbinding in het cluster te maken, raadpleegt u [SSH gebruiken met het op Linux gebaseerde Hadoop op HDInsight van Linux.](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Nadat u de verbinding hebt gemaakt, start u de Hive command-line interface (CLI) met de volgende opdracht:

        hive
2. Gebruik de CLI om de volgende instructies in te voeren om een nieuwe tabel met de naam **voertuigen** te maken met behulp van de voorbeeldgegevens in Data Lake Storage Gen1:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    U ziet de queryuitvoer op de SSH-console.

    >[!NOTE]
    >Het pad naar de voorbeeldgegevens in de `adl:///example/data/`voorafgaande `adl:///` opdracht TABEL MAKEN is , waar is de clusterhoofd. Naar aanleiding van het voorbeeld van de clusterroot die `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`in deze zelfstudie is opgegeven, is de opdracht . U het kortere alternatief gebruiken of het volledige pad naar de clusterhoofd geven.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Toegang tot Data Lake Storage Gen1 met HDFS-opdrachten
Nadat u het HDInsight-cluster hebt geconfigureerd om Data Lake Storage Gen1 te gebruiken, u De Shell-opdrachten (Hadoop Distributed File System) gebruiken om toegang te krijgen tot de winkel.

In deze sectie maakt u een SSH-verbinding met het HDInsight Linux-cluster dat u hebt gemaakt en voert u vervolgens de HDFS-opdrachten uit.

* Als u een Windows-client gebruikt om een SSH-verbinding in het cluster te maken, raadpleegt u [SSH gebruiken met het op Linux gebaseerde Hadoop op HDInsight van Windows.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)
* Als u een Linux-client gebruikt om een SSH-verbinding in het cluster te maken, raadpleegt u [SSH gebruiken met het op Linux gebaseerde Hadoop op HDInsight van Linux.](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Nadat u de verbinding hebt gemaakt, vermeldt u de bestanden in Data Lake Storage Gen1 met behulp van de volgende opdracht HDFS-bestandssysteem.

    hdfs dfs -ls adl:///

U de `hdfs dfs -put` opdracht ook gebruiken om bepaalde bestanden te `hdfs dfs -ls` uploaden naar Data Lake Storage Gen1 en vervolgens te controleren of de bestanden zijn geüpload.

## <a name="see-also"></a>Zie ook
* [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure-portal: een HDInsight-cluster maken om Data Lake Storage Gen1 te gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
