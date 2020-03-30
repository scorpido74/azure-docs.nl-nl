---
title: Geen toegang tot Data Lake-opslagbestanden in Azure HDInsight
description: Geen toegang tot Data Lake-opslagbestanden in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/13/2019
ms.openlocfilehash: 21269f7d5a9ec832a49a613351702dd24be156af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894154"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Geen toegang tot Data Lake-opslagbestanden in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue-acl-verification-failed"></a>Probleem: ACL-verificatie is mislukt

U ontvangt een foutbericht dat vergelijkbaar is met:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Oorzaak

De gebruiker heeft mogelijk de machtigingen van serviceprincipal (SP) voor bestanden/mappen ingetrokken.

### <a name="resolution"></a>Oplossing

1. Controleer of de SP 'x' machtigingen heeft om langs het pad te lopen. Zie [Machtigingen](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html)voor meer informatie. Voorbeeld van dfs-opdracht om de toegang tot bestanden/mappen in het Data Lake-opslagaccount te controleren:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Stel vereiste machtigingen in om toegang te krijgen tot het pad op basis van de lees-/schrijfbewerking die wordt uitgevoerd. Zie hier voor machtigingen die nodig zijn voor verschillende bestandssysteembewerkingen.

---

## <a name="issue-service-principal-certificate-expiry"></a>Probleem: Verlopen serviceprincipal certificaat

U ontvangt een foutbericht dat vergelijkbaar is met:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Oorzaak

Het certificaat voor serviceprincipal-toegang is mogelijk verlopen.

1. SSH in headnode. Controleer de toegang tot een opslagaccount met de volgende opdracht DFS:

    ```
    hdfs dfs -ls /
    ```

1. Controleer of het foutbericht vergelijkbaar is met het volgende:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Haal een van de `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls`urls van .

1. Voer de volgende opdracht krul uit om OAuth-token op te halen.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. De uitvoer voor een geldige serviceprincipal moet zoiets zijn als:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Als het hoofdcertificaat voor de service is verlopen, ziet de uitvoer er ongeveer als volgt uit:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Alle andere Azure Active Directory-gerelateerde fouten/certificaatgerelateerde fouten kunnen worden herkend door de gateway-url te pingen om het OAuth-token te krijgen.

1. Als u een volgende fout krijgt wanneer u adls probeert te openen vanuit het HDI-cluster. Controleer of het certificaat is verlopen door de bovenstaande stappen te volgen.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Oplossing

Maak een nieuw certificaat of wijs bestaand certificaat toe met het volgende PowerShell-script:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Voor het toewijzen van bestaand certificaat maakt u een certificaat, hebt u het .pfx-bestand en het wachtwoord gereed. Koppel het certificaat aan de serviceprincipal waarmee het cluster is gemaakt en de AppId gereed.

Voer de opdracht PowerShell uit nadat u de parameters hebt vervangen door de werkelijke waarden.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
