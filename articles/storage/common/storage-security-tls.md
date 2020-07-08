---
title: Secure TLS inschakelen met .NET
titleSuffix: Azure Storage
description: Meer informatie over het inschakelen van TLS 1,2 met behulp van de .NET-client bibliotheek voor Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75371799"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Beveiligde TLS voor Azure Storage-client inschakelen

Transport Layer Security (TLS) en Secure Sockets Layer (SSL) zijn cryptografische protocollen die communicatie beveiliging bieden via een computer netwerk. SSL 1,0, 2,0 en 3,0 moeten kwetsbaar zijn. Ze zijn verboden door RFC. TLS 1,0 wordt onveilig voor het gebruik van Inveilige blok codering (DES CBC en RC2 CBC) en stream cipher (RC4). De PCI-Raad heeft ook de migratie naar hogere TLS-versies voorgesteld. Zie [Transport Layer Security (TLS) (Engelstalig)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)voor meer informatie.

Azure Storage is SSL 3,0 gestopt sinds 2015 en TLS 1,2 gebruikt voor open bare HTTPs-eind punten, maar TLS 1,0 en TLS 1,1 worden nog steeds ondersteund voor compatibiliteit met eerdere versies.

Om te zorgen voor een veilige en compatibele verbinding met Azure Storage, moet u TLS 1,2 of een nieuwere versie inschakelen aan de client zijde voordat u aanvragen verzendt om Azure Storage service te kunnen gebruiken.

## <a name="enable-tls-12-in-net-client"></a>TLS 1.2 activeren in .NET-client

Het besturingssysteem en de versie van .NET Framework moeten beide TLS 1.2 ondersteunen om de client met TLS 1.2 te laten werken. Meer informatie vindt u in [ondersteuning voor TLS 1,2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

In het volgende voorbeeld ziet u hoe u TLS 1.2 in uw .NET-client inschakelt.

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

In het volgende voor beeld ziet u hoe u TLS 1,2 in uw Power shell-client inschakelt.

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

## <a name="verify-tls-12-connection"></a>TLS 1,2-verbinding controleren

U kunt Fiddler gebruiken om te controleren of TLS 1,2 werkelijk wordt gebruikt. Open Fiddler om het vastleggen van client netwerk verkeer te starten en voer hierboven het voor beeld uit. Vervolgens kunt u de TLS-versie vinden in de verbinding die door het voor beeld wordt gemaakt.

De volgende scherm afbeelding is een voor beeld van de verificatie.

![scherm opname van het controleren van de TLS-versie in Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Zie tevens

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [PCI-naleving van TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [TLS inschakelen in Java-client](https://www.java.com/en/configure_crypto.html)
