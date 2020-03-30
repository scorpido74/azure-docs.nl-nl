---
title: Een verbindingstekenreeks configureren
titleSuffix: Azure Storage
description: Een verbindingstekenreeks configureren voor een Azure-opslagaccount. Een verbindingstekenreeks bevat de informatie die nodig is om de toegang tot een opslagaccount van uw toepassing tijdens runtime te autoriseren met behulp van de machtiging Gedeelde sleutel.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268351"
---
# <a name="configure-azure-storage-connection-strings"></a>Azure Storage-verbindingsreeksen configureren

Een verbindingstekenreeks bevat de autorisatiegegevens die nodig zijn voor uw toepassing om toegang te krijgen tot gegevens in een Azure Storage-account tijdens runtime met behulp van de autorisatie van gedeelde sleutels. U verbindingstekenreeksen configureren op:

* Maak verbinding met de Azure-opslagemulator.
* Toegang tot een opslagaccount in Azure.
* Toegang tot bepaalde bronnen in Azure via een gedeelde toegangshandtekening (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Een verbindingstekenreeks weergeven en kopiëren

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Een verbindingstekenreeks opslaan

Uw toepassing moet toegang krijgen tot de verbindingstekenreeks tijdens runtime om aanvragen voor Azure Storage te autoriseren. U hebt verschillende opties voor het opslaan van uw verbindingstekenreeks:

* U uw verbindingstekenreeks opslaan in een omgevingsvariabele.
* Een toepassing die wordt uitgevoerd op het bureaublad of op een apparaat, kan de verbindingstekenreeks opslaan in een **app.config-** of **web.config-bestand.** Voeg de verbindingstekenreeks toe aan de sectie **AppSettings** in deze bestanden.
* Een toepassing die wordt uitgevoerd in een Azure-cloudservice, kan de verbindingstekenreeks opslaan in het [Azure-serviceconfiguratieschema (cscfg)-bestand](https://msdn.microsoft.com/library/ee758710.aspx). Voeg de verbindingstekenreeks toe aan de sectie **ConfigurationSettings** van het serviceconfiguratiebestand.

Als u uw verbindingstekenreeks opslaat in een configuratiebestand, u de verbindingstekenreeks eenvoudig bijwerken om te schakelen tussen de opslagemulator en een Azure-opslagaccount in de cloud. U hoeft alleen de verbindingstekenreeks te bewerken om naar uw doelomgeving te wijzen.

U Microsoft [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) gebruiken om toegang te krijgen tot uw verbindingstekenreeks tijdens uitvoering, ongeacht waar uw toepassing wordt uitgevoerd.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Een verbindingstekenreeks configureren voor de opslagemulator

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Zie [De Azure-opslagemulator gebruiken voor ontwikkeling en testen voor](storage-use-emulator.md)meer informatie over de opslagemulator.

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Een verbindingstekenreeks configureren voor een Azure-opslagaccount

Als u een verbindingstekenreeks voor uw Azure-opslagaccount wilt maken, gebruikt u de volgende indeling. Geef aan of u verbinding wilt maken met het opslagaccount `myAccountName` via HTTPS (aanbevolen) of `myAccountKey` HTTP, vervang de naam van uw opslagaccount en vervang deze door uw accounttoegangssleutel:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

De verbindingstekenreeks lijkt bijvoorbeeld op:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Hoewel Azure Storage zowel HTTP als HTTPS in een verbindingstekenreeks ondersteunt, *wordt HTTPS ten zeerste aanbevolen.*

> [!TIP]
> U de verbindingstekenreeksen van uw opslagaccount vinden in de [Azure-portal.](https://portal.azure.com) Navigeer naar **INSTELLINGEN** > **Toegangssleutels** in het menublad van uw opslagaccount om verbindingstekenreeksen voor zowel primaire als secundaire toegangssleutels te bekijken.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Een verbindingstekenreeks maken met een handtekening voor gedeelde toegang

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Een verbindingstekenreeks maken voor een expliciet opslageindpunt

U expliciete serviceeindpunten opgeven in uw verbindingstekenreeks in plaats van de standaardeindpunten te gebruiken. Als u een verbindingstekenreeks wilt maken die een expliciet eindpunt opgeeft, geeft u het volledige serviceeindpunt voor elke service op, inclusief de protocolspecificatie (HTTPS (aanbevolen) of HTTP), in de volgende indeling:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Een scenario waarin u een expliciet eindpunt wilt opgeven, is wanneer u het eindpunt van de Blob-opslag hebt toegewezen aan een [aangepast domein.](../blobs/storage-custom-domain-name.md) In dat geval u het aangepaste eindpunt voor Blob-opslag opgeven in uw verbindingstekenreeks. U optioneel de standaardeindpunten voor de andere services opgeven als uw toepassing deze gebruikt.

Hier is een voorbeeld van een verbindingstekenreeks die een expliciet eindpunt voor de Blob-service opgeeft:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

In dit voorbeeld worden expliciete eindpunten voor alle services opgegeven, waaronder een aangepast domein voor de Blob-service:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

De eindpuntwaarden in een verbindingstekenreeks worden gebruikt om de aanvraag-URI's voor de opslagservices te construeren en de vorm te dicteren van uri's die naar uw code worden geretourneerd.

Als u een opslageindpunt aan een aangepast domein hebt toegewezen en dat eindpunt uit een verbindingstekenreeks hebt weglaten, u die verbindingstekenreeks niet gebruiken om toegang te krijgen tot gegevens in die service vanuit uw code.

> [!IMPORTANT]
> Serviceeindpuntwaarden in uw verbindingstekenreeksen moeten goed gevormde `https://` URI's `http://`zijn, inclusief (aanbevolen) of . Omdat Azure Storage nog geen HTTPS voor *must* aangepaste `http://` domeinen ondersteunt, moet u opgeven voor eindpuntURI die naar een aangepast domein verwijst.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Een verbindingstekenreeks maken met een eindpuntachtervoegsel

Als u een verbindingstekenreeks wilt maken voor een opslagservice in regio's of instanties met verschillende eindpuntachtervoegsels, zoals voor Azure China 21Vianet of Azure Government, gebruikt u de volgende verbindingstekenreeksindeling. Geef aan of u verbinding wilt maken met het opslagaccount `myAccountName` via HTTPS (aanbevolen) `myAccountKey` of HTTP, vervang de `mySuffix` naam van uw opslagaccount, vervang uw accounttoegangssleutel en vervang het URI-achtervoegsel:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Hier vindt u een voorbeeldverbindingstekenreeks voor opslagservices in Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Een verbindingstekenreeks ontwering

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [De Azure-opslagemulator gebruiken voor ontwikkeling en testen](storage-use-emulator.md)
* [Azure Storage-verkenners](storage-explorers.md)
* [Gedeelde toegangshandtekeningen (SAS) gebruiken](storage-sas-overview.md)
