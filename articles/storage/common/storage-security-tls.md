---
title: Veilige TLS inschakelen met .NET
titleSuffix: Azure Storage
description: Meer informatie over het inschakelen van TLS 1.2 met behulp van de .NET-clientbibliotheek voor Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371799"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Beveiligde TLS voor Azure Storage-client inschakelen

Tls (Transport Layer Security) en Secure Sockets Layer (SSL) zijn cryptografische protocollen die communicatiebeveiliging bieden via een computernetwerk. SSL 1.0, 2.0 en 3.0 blijken kwetsbaar te zijn. Ze zijn verboden door RFC. TLS 1.0 wordt onzeker voor het gebruik van onveilige block cipher (DES CBC en RC2 CBC) en Stream cipher (RC4). PCI-raad stelde ook de migratie naar hogere TLS-versies voor. Voor meer informatie u [TLS (Transport Layer Security) bekijken.](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)

Azure Storage is sinds 2015 gestopt met SSL 3.0 en gebruikt TLS 1.2 op openbare HTTPs-eindpunten, maar TLS 1.0 en TLS 1.1 worden nog steeds ondersteund voor achterwaartse compatibiliteit.

Om een veilige en compatibele verbinding met Azure Storage te garanderen, moet u TLS 1.2 of nieuwere versie in clientzijde inschakelen voordat u aanvragen verzendt om azure storage-service te bedienen.

## <a name="enable-tls-12-in-net-client"></a>TLS 1.2 activeren in .NET-client

Om te onderhandelen over TLS 1.2 moeten het besturingssysteem en de .NET Framework-versie TLS 1.2 ondersteunen. Zie meer details in [Ondersteuning voor TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

In het volgende voorbeeld ziet u hoe u TLS 1.2 inschakelt in uw .NET-client.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>TLS 1.2 activeren in PowerShell-client

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

In het volgende voorbeeld ziet u hoe u TLS 1.2 inschakelt in uw PowerShell-client.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>TLS 1.2-verbinding verifiëren

U Fiddler gebruiken om te controleren of TLS 1.2 daadwerkelijk wordt gebruikt. Open Fiddler om te beginnen met het vastleggen van clientnetwerkverkeer en voer hierboven voorbeeld uit. Vervolgens u de TLS-versie vinden in de verbinding die het voorbeeld maakt.

De volgende schermafbeelding is een voorbeeld voor de verificatie.

![schermafbeelding van het verifiëren van de TLS-versie in Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Zie ook

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [PCI-compliance op TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [TLS inschakelen in Java-client](https://www.java.com/en/configure_crypto.html)
