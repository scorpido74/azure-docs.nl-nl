---
title: Statische website-hosting in Azure Storage
description: Azure Storage statische website-hosting, die een rendabele en schaal bare oplossing biedt voor het hosten van moderne webtoepassingen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 8dc5599e681d9aee84f884cd4990163a2481d386
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708159"
---
# <a name="static-website-hosting-in-azure-storage"></a>Statische website-hosting in Azure Storage

U kunt statische inhoud (HTML-, CSS-, java script-en afbeeldings bestanden) rechtstreeks vanuit een opslag container met de naam *$Web*verwerken. Door uw inhoud in Azure Storage te hosten, kunt u serverloze architecturen gebruiken die [Azure functions](/azure/azure-functions/functions-overview) en andere PaaS-Services (platform as a Service) bevatten.

> [!NOTE]
> Als uw site afhankelijk is van code aan de server zijde, gebruikt u [Azure app service](/azure/app-service/overview) in plaats daarvan.

## <a name="setting-up-a-static-website"></a>Instellen van een statische website

Het hosten van statische websites is een functie die u moet inschakelen op het opslag account.

Als u statische website-hosting wilt inschakelen, selecteert u de naam van het standaard bestand en geeft u desgewenst een pad op naar een aangepaste 404-pagina. Als er geen blob storage-container met de naam **$Web** al in het account bestaat, wordt er een voor u gemaakt. Voeg de bestanden van uw site toe aan deze container.

Zie [een statische website hosten in azure Storage](storage-blob-static-website-how-to.md)voor stapsgewijze instructies.

![Metrische gegevens voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Bestanden in de **$Web** -container zijn hoofdletter gevoelig, worden aangeboden via anonieme toegangs aanvragen en zijn alleen beschikbaar via Lees bewerkingen.

## <a name="uploading-content"></a>Inhoud uploaden

U kunt elk van deze hulpprogram ma's gebruiken om inhoud te uploaden naar de container **$Web** :

> [!div class="checklist"]
> * [Azure-CLI](storage-blob-static-website-how-to.md#cli)
> * [Module Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure-opslagverkenner](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio code-extensie](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Inhoud weer geven

Gebruikers kunnen site-inhoud vanuit een browser weer geven met behulp van de open bare URL van de website. U kunt de URL vinden met behulp van de Azure Portal, Azure CLI of Power shell. Gebruik deze tabel als richt lijn.

|Hulpprogramma| Hulp |
|----|----|
|**Azure Portal** | [De URL van de website zoeken met behulp van de Azure Portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure-CLI** | [De URL van de website zoeken met behulp van de Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Module Azure PowerShell** | [De URL van de website zoeken met behulp van Power shell](storage-blob-static-website-how-to.md#powershell-find-url) |

De URL van uw site bevat een regionale code. De URL `https://contosoblobaccount.z22.web.core.windows.net/` bijvoorbeeld regionale code `z22`bevat.

Hoewel die code in de URL moet blijven, is deze alleen voor intern gebruik en hoeft u deze code niet op een andere manier te gebruiken.

Het index document dat u opgeeft wanneer u de functie voor het hosten van statische websites inschakelt, wordt weer gegeven wanneer gebruikers de site openen en geen specifiek bestand opgeven (bijvoorbeeld: `https://contosoblobaccount.z22.web.core.windows.net`).  

Als de server een 404-fout retourneert en u geen fout document hebt opgegeven toen u de website inschakelde, wordt een standaard-404-pagina naar de gebruiker geretourneerd.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) wordt niet ondersteund met een statische website.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Gevolgen van het instellen van het open bare toegangs niveau van de webcontainer

U kunt het open bare toegangs niveau van de **$Web** -container wijzigen, maar dit heeft geen invloed op het primaire statische website-eind punt omdat deze bestanden via anonieme toegangs aanvragen worden bediend. Dit betekent dat open bare (alleen-lezen) toegang tot alle bestanden.

De volgende scherm afbeelding toont de instelling voor het niveau van open bare toegang in de Azure Portal:

![Scherm afbeelding die laat zien hoe u het niveau van open bare toegang instelt in de portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Terwijl het eind punt van de primaire statische website niet wordt beïnvloed, heeft een wijziging van het open bare toegangs niveau invloed op het eind punt van de primaire BLOB-service.

Als u bijvoorbeeld het open bare toegangs niveau van de container **$Web** van **privé (geen anonieme toegang)** wijzigt in **BLOB (anonieme lees toegang alleen voor blobs)** , wordt het niveau van de open bare toegang tot het eind punt van de primaire statische website `https://contosoblobaccount.z22.web.core.windows.net/index.html` niet gewijzigd.

De open bare toegang tot het eind punt van de primaire BLOB-service `https://contosoblobaccount.blob.core.windows.net/$web/index.html` echter gewijzigd van privé in openbaar. Gebruikers kunnen dit bestand nu openen met behulp van een van deze twee eind punten.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Ondersteuning voor Content Delivery Network (CDN) en SSL (Secure Socket Layer)

Zie [de Azure CDN gebruiken om toegang te krijgen tot blobs met aangepaste domeinen via https](storage-https-custom-domain-cdn.md)om uw statische website bestanden beschikbaar te maken voor uw aangepaste domein en HTTPS. Als onderdeel van dit proces moet u uw CDN naar het primaire *statische website* -eind punt wijzen, in plaats van het primaire eind punt van de *BLOB service* . Mogelijk moet u enkele minuten wachten voordat uw inhoud zichtbaar is, omdat de CDN-configuratie niet onmiddellijk wordt uitgevoerd.

Wanneer u uw statische website bijwerkt, moet u ervoor zorgen dat de inhoud in de cache op de CDN-rand servers wordt gewist door het CDN-eind punt te verwijderen. Zie voor meer informatie [Een Azure CDN-eindpunt leegmaken](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> HTTPS wordt standaard ondersteund door het web-eind punt van het account, zodat het webeindpunt toegankelijk is via HTTP en HTTPS. Als het opslag account echter is geconfigureerd voor het vereisen van beveiligde overdracht via HTTPS, moeten gebruikers het HTTPS-eind punt gebruiken. Zie [veilige overdracht vereisen in azure Storage](../common/storage-require-secure-transfer.md)voor meer informatie.
>
> Voor het gebruik van aangepaste domeinen via HTTPS moet Azure CDN op dit moment worden gebruikt.

## <a name="custom-domain-names"></a>Aangepaste domeinnamen

U kunt uw statische website beschikbaar maken via een aangepast domein. Zie [een aangepaste domein naam configureren voor uw Azure Storage-account](storage-custom-domain-name.md)voor meer informatie.

Zie [uw domein hosten in azure DNS](../../dns/dns-delegate-domain-azure-dns.md)voor een diep gaande bespreking van het hosten van uw domein in Azure.

## <a name="pricing"></a>Prijzen

U kunt statische website-hosting gratis inschakelen. U wordt alleen gefactureerd voor de Blob-opslag die uw site gebruikt en de operationele kosten. Bekijk de [pagina met prijzen voor azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor Azure Blob Storage.

## <a name="metrics"></a>Metrische gegevens

U kunt metrische gegevens inschakelen op de vaste website pagina's. Wanneer u metrische gegevens hebt ingeschakeld, worden verkeers statistieken voor bestanden in de **$Web** container gerapporteerd in het dash board metrische gegevens.

Zie [metrische gegevens inschakelen op de pagina's van een statische website](storage-blob-static-website-how-to.md#metrics)voor informatie over het inschakelen van metrische gegevens op uw statische website pagina's.

## <a name="next-steps"></a>Volgende stappen

* [Een statische website hosten in Azure Storage](storage-blob-static-website-how-to.md)
* [De Azure CDN gebruiken om toegang te krijgen tot blobs met aangepaste domeinen via HTTPS](storage-https-custom-domain-cdn.md)
* [Een aangepaste domein naam configureren voor uw BLOB of Web-eind punt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Uw eerste serverloze web-app bouwen](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Zelf studie: uw domein hosten in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
