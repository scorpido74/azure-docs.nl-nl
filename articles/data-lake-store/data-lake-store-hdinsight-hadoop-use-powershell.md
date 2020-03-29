---
title: 'PowerShell: Azure HDInsight-cluster met Azure Data Lake Storage Gen1 als add-on opslag | Microsoft Documenten'
description: Meer informatie over het gebruik van Azure PowerShell om een HDInsight-cluster met Azure Data Lake Storage Gen1 als extra opslag te configureren.
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4cd61619e0417ab1db8d8413872b2dff1c904fc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970128"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Azure PowerShell gebruiken om een HDInsight-cluster te maken met Azure Data Lake Storage Gen1 (als extra opslag)

> [!div class="op_single_selector"]
> * [Portal gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell gebruiken (voor standaardopslag)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell gebruiken (voor extra opslag)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resourcebeheer gebruiken](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Meer informatie over het gebruik van Azure PowerShell om een HDInsight-cluster met Azure Data Lake Storage Gen1 te configureren **als extra opslag.** Zie [Een HDInsight-cluster maken met Data Lake Storage Gen1 als standaardopslag](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)voor instructies over het maken van een HDInsight-cluster met Data Lake Storage Gen1 als standaardopslag.

> [!NOTE]
> Als u Data Lake Storage Gen1 gaat gebruiken als extra opslag voor het HDInsight-cluster, raden we u ten zeerste aan dit te doen terwijl u het cluster maakt zoals beschreven in dit artikel. Het toevoegen van Data Lake Storage Gen1 als extra opslag aan een bestaand HDInsight-cluster is een ingewikkeld proces en gevoelig voor fouten.
>

Voor ondersteunde clustertypen kan Data Lake Storage Gen1 worden gebruikt als standaardopslag- of extra opslagaccount. Wanneer Data Lake Storage Gen1 wordt gebruikt als extra opslag, is het standaardopslagaccount voor de clusters nog steeds Azure Storage Blobs (WASB) en worden de clustergerelateerde bestanden (zoals logboeken, enz.) nog steeds naar de standaardopslag geschreven, terwijl de gegevens die u wilt proces kan worden opgeslagen in een Data Lake Storage Gen1-account. Het gebruik van Data Lake Storage Gen1 als een extra opslagaccount heeft geen invloed op de prestaties of de mogelijkheid om te lezen/schrijven naar de opslag van het cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Data Lake Storage Gen1 gebruiken voor HDInsight-clusteropslag

Hier volgen enkele belangrijke overwegingen voor het gebruik van HDInsight met Data Lake Storage Gen1:

* Optie om HDInsight-clusters te maken met toegang tot Data Lake Storage Gen1, omdat extra opslag beschikbaar is voor HDInsight-versies 3.2, 3.4, 3.5 en 3.6.

Het configureren van HDInsight om te werken met Data Lake Storage Gen1 met PowerShell omvat de volgende stappen:

* Een Data Lake Storage Gen1-account maken
* Verificatie instellen voor op rollen gebaseerde toegang tot Data Lake Storage Gen1
* HDInsight-cluster maken met verificatie naar Data Lake Storage Gen1
* Een testtaak uitvoeren op het cluster

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 of hoger**. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview).
* **Windows SDK**. Deze kunt u [hier](https://dev.windows.com/en-us/downloads) downloaden. U gebruikt dit om een beveiligingscertificaat te maken.
* **Azure Active Directory Service Principal**. Stappen in deze zelfstudie geven instructies voor het maken van een serviceprincipal in Azure AD. U moet echter een Azure AD-beheerder zijn om een serviceprincipal te kunnen maken. Als u een Azure AD-beheerder bent, u deze vereiste overslaan en doorgaan met de zelfstudie.

    **Als u geen Azure AD-beheerder bent,** u niet de stappen uitvoeren die nodig zijn om een serviceprincipal te maken. In een dergelijk geval moet uw Azure AD-beheerder eerst een serviceprincipal maken voordat u een HDInsight-cluster maken met Data Lake Storage Gen1. Ook moet de serviceprincipal worden gemaakt met behulp van een certificaat, zoals beschreven bij [Een serviceprincipal maken met certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken
Volg deze stappen om een Data Lake Storage Gen1-account aan te maken.

1. Open vanaf uw bureaublad een nieuw Azure PowerShell-venster en voer het volgende fragment in. Wanneer u wordt gevraagd om in te loggen, moet u zich aanmelden als een van de beheerder/eigenaar van het abonnement:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Als u een fout `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` ontvangt die vergelijkbaar is met bij het registreren van de Data Lake Storage Gen1-bronprovider, is het mogelijk dat uw abonnement niet op de witte lijst staat voor Data Lake Storage Gen1. Zorg ervoor dat u uw Azure-abonnement inschakelt voor Data Lake Storage Gen1 door deze instructies op te [volgen.](data-lake-store-get-started-portal.md)
   >
   >
2. Een Data Lake Storage Gen1-account is gekoppeld aan een Azure Resource Group. Maak daarom eerst een Azure-resourcegroep.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    De uitvoer ziet er als volgt uit:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Maak een Data Lake Storage Gen1-account aan. De door u opgegeven accountnaam mag alleen kleine letters en cijfers bevatten.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
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

5. Upload enkele voorbeeldgegevens naar Data Lake Storage Gen1. We gebruiken dit later in dit artikel om te controleren of de gegevens toegankelijk zijn vanuit een HDInsight-cluster. Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken.

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Verificatie instellen voor op rollen gebaseerde toegang tot Data Lake Storage Gen1

Elk Azure-abonnement is gekoppeld aan een Azure Active Directory. Gebruikers en services die toegang hebben tot resources van het abonnement met behulp van de Azure-portal of Azure Resource Manager API, moeten zich eerst verifiëren met die Azure Active Directory. Toegang wordt verleend tot Azure-abonnementen en -services door ze de juiste rol toe te geven aan een Azure-bron.  Voor services identificeert een serviceprincipal de service in de Azure Active Directory (AAD). In deze sectie wordt uitgelegd hoe u een toepassingsservice, zoals HDInsight, toegang verleent tot een Azure-bron (het Data Lake Storage Gen1-account dat u eerder hebt gemaakt) door een serviceprincipal voor de toepassing te maken en rollen toe te wijzen aan die via Azure PowerShell.

Als u Active Directory-verificatie voor Data Lake Storage Gen1 wilt instellen, moet u de volgende taken uitvoeren.

* Een zelfondertekend certificaat maken
* Een toepassing maken in Azure Active Directory en een serviceprincipal

### <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Zorg ervoor dat [Windows SDK](https://dev.windows.com/en-us/downloads) is geïnstalleerd voordat u verdergaat met de stappen in deze sectie. U moet ook een map hebben gemaakt, zoals **C:\mycertdir**, waar het certificaat wordt gemaakt.

1. Navigeer vanuit het PowerShell-venster naar de locatie waar `C:\Program Files (x86)\Windows Kits\10\bin\x86` u Windows SDK hebt geïnstalleerd (meestal en gebruik het hulpprogramma [MakeCert][makecert] om een zelfondertekend certificaat en een privésleutel te maken. Gebruik de volgende opdrachten.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    U wordt gevraagd het wachtwoord van de privésleutel in te voeren. Nadat de opdracht is uitgevoerd, ziet u een **CertFile.cer** en **mykey.pvk** in de certificaatmap die u hebt opgegeven.
2. Gebruik het [Pvk2Pfx-hulpprogramma][pvk2pfx] om de .pvk- en .cer-bestanden die MakeCert heeft gemaakt om te zetten in een PFX-bestand. Voer de volgende opdracht uit.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Voer desgevraagd het wachtwoord van de privésleutel in dat u eerder hebt opgegeven. De waarde die u opgeeft voor de **parameter -po** is het wachtwoord dat is gekoppeld aan het PFX-bestand. Nadat de opdracht is voltooid, ziet u ook een CertFile.pfx in de door u opgegeven certificaatmap.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Een Azure Active Directory en een serviceprincipal maken

In deze sectie voert u de stappen uit om een serviceprincipal voor een Azure Active Directory-toepassing te maken, een rol toe te wijzen aan de serviceprincipal en te verifiëren als serviceprincipal door een certificaat te verstrekken. Voer de volgende opdrachten uit om een toepassing te maken in Azure Active Directory.

1. Plak de volgende cmdlets in het PowerShell-consolevenster. Controleer of de waarde die u opgeeft voor de eigenschap **-DisplayName** uniek is. De waarden voor **-HomePage** en **-IdentiferUris** zijn ook tijdelijke aanduidingen en worden niet geverifieerd.

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
3. Geef de serviceprincipal toegang tot de map Data Lake Storage Gen1 en het bestand dat u vanaf het HDInsight-cluster opent. Het onderstaande fragment biedt toegang tot de hoofdmap van het Data Lake Storage Gen1-account (waar u het voorbeeldgegevensbestand hebt gekopieerd) en het bestand zelf.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Maak een HDInsight Linux-cluster met Data Lake Storage Gen1 als extra opslag

In deze sectie maken we een HDInsight Hadoop Linux-cluster met Data Lake Storage Gen1 als extra opslag. Voor deze release moeten het HDInsight-cluster en het Data Lake Storage Gen1-account zich op dezelfde locatie bevinden.

1. Begin met het ophalen van de abonnementstenant-id. Dat heb je later nodig.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. Voor deze release kan Data Lake Storage Gen1 voor een Hadoop-cluster alleen worden gebruikt als extra opslag voor het cluster. De standaardopslag is nog steeds de Azure storage blobs (WASB). Daarom maken we eerst de opslagaccount en opslagcontainers die nodig zijn voor het cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Maak het HDInsight-cluster. Gebruik de volgende cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Nadat de cmdlet is voltooid, ziet u een uitvoer met de clusterdetails.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Testtaken uitvoeren op het HDInsight-cluster om het Data Lake Storage Gen1-account te gebruiken
Nadat u een HDInsight-cluster hebt geconfigureerd, u testtaken op het cluster uitvoeren om te testen of het HDInsight-cluster toegang heeft tot Data Lake Storage Gen1. Hiervoor voeren we een voorbeeldhive-taak uit die een tabel maakt met behulp van de voorbeeldgegevens die u eerder hebt geüpload naar uw Data Lake Storage Gen1-account.

In deze sectie zult u SSH in de HDInsight Linux cluster die u hebt gemaakt en voer de een voorbeeld Hive query.

* Als u een Windows-client gebruikt om SSH in het cluster te gebruiken, raadpleegt u [SSH gebruiken met het op Linux gebaseerde Hadoop op HDInsight van Windows.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)
* Als u een Linux-client gebruikt om SSH in het cluster te gebruiken, raadpleegt u [SSH gebruiken met Linux-gebaseerde Hadoop op HDInsight van Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Start de Hive CLI na verbinding met de volgende opdracht:

        hive
2. Voer met de CLI de volgende instructies in om een nieuwe tabel met de naam **voertuigen** te maken met behulp van de voorbeeldgegevens in Data Lake Storage Gen1:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Toegang tot Data Lake Storage Gen1 met HDFS-opdrachten
Zodra u het HDInsight-cluster hebt geconfigureerd om Data Lake Storage Gen1 te gebruiken, u de opdrachten voor HDFS-shell gebruiken om toegang te krijgen tot het archief.

In deze sectie ga je naar het HDInsight Linux cluster dat je hebt gemaakt en voer je de HDFS-commando's uit.

* Als u een Windows-client gebruikt om SSH in het cluster te gebruiken, raadpleegt u [SSH gebruiken met het op Linux gebaseerde Hadoop op HDInsight van Windows.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)
* Als u een Linux-client gebruikt om SSH in het cluster te gebruiken, raadpleegt u [SSH gebruiken met Linux-gebaseerde Hadoop op HDInsight van Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Gebruik, eenmaal verbonden, de volgende opdracht HDFS-bestandssysteem om de bestanden in het Data Lake Storage Gen1-account weer te geven.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Hier vindt u een overzicht van het bestand dat u eerder hebt geüpload naar Data Lake Storage Gen1.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

U de `hdfs dfs -put` opdracht ook gebruiken om bepaalde bestanden te `hdfs dfs -ls` uploaden naar Data Lake Storage Gen1 en vervolgens te controleren of de bestanden zijn geüpload.

## <a name="see-also"></a>Zie ook
* [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: Maak een HDInsight-cluster om Data Lake Storage Gen1 te gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
