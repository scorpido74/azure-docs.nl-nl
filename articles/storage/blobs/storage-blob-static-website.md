---
title: Een statische website hosten in Azure Storage
description: Azure Storage statische websitehosting, die een kosteneffectieve, schaalbare oplossing biedt voor het hosten van moderne webtoepassingen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370488"
---
# <a name="static-website-hosting-in-azure-storage"></a>Een statische website hosten in Azure Storage

U statische inhoud (HTML, CSS, JavaScript en afbeeldingsbestanden) rechtstreeks vanuit een opslagcontainer met de naam *$web*. Als u uw inhoud host in Azure Storage, u serverloze architecturen gebruiken die [Azure-functies](/azure/azure-functions/functions-overview) en andere PaaS-services (Platform as a Service) bevatten.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Als uw site afhankelijk is van servercode, gebruikt u in plaats daarvan [Azure App Service.](/azure/app-service/overview)

## <a name="setting-up-a-static-website"></a>Een statische website instellen

Statische websitehosting is een functie die u moet inschakelen op het opslagaccount.

Als u statische websitehosting wilt inschakelen, selecteert u de naam van uw standaardbestand en geeft u optioneel een pad naar een aangepaste 404-pagina. Als er een blobopslagcontainer met de naam **$web** nog niet bestaat in het account, wordt er een voor u gemaakt. Voeg de bestanden van uw site toe aan deze container.

Zie [Een statische website hosten in Azure Storage](storage-blob-static-website-how-to.md)voor stapsgewijze richtlijnen.

![Metrische gegevens metriek voor statische websites azure storage](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Bestanden in de **$web** container zijn hoofdlettergevoelig, worden via anonieme toegangsverzoeken bediend en zijn alleen beschikbaar via leesbewerkingen.

## <a name="uploading-content"></a>Inhoud uploaden

U een van deze tools gebruiken om inhoud te uploaden naar de **container $web:**

> [!div class="checklist"]
> * [Azure-CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell-module](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy (AzCopy)](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure-pijplijnen](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-extensie](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Inhoud weergeven

Gebruikers kunnen site-inhoud vanuit een browser bekijken met behulp van de openbare URL van de website. U de URL vinden met behulp van de Azure-portal, Azure CLI of PowerShell. Gebruik deze tabel als leidraad.

|Hulpprogramma| Richtlijnen |
|----|----|
|**Azure-portal** | [De URL van de website zoeken met behulp van de Azure-portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure-CLI** | [De URL van de website zoeken met de Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell-module** | [De URL van de website zoeken met PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

De URL van uw site bevat een regionale code. De URL `https://contosoblobaccount.z22.web.core.windows.net/` bevat bijvoorbeeld `z22`regionale code .

Hoewel die code in de URL moet blijven, is deze alleen voor intern gebruik en hoeft u die code niet op een andere manier te gebruiken.

Het indexdocument dat u opgeeft wanneer u statische websitehosting inschakelt, wordt weergegeven wanneer `https://contosoblobaccount.z22.web.core.windows.net`gebruikers de site openen en geen specifiek bestand opgeven (bijvoorbeeld: ).  

Als de server een fout van 404 retourneert en u geen foutdocument hebt opgegeven toen u de website hebt ingeschakeld, wordt een standaardpagina van 404 naar de gebruiker geretourneerd.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) wordt niet ondersteund met statische website.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impact van de instelling van het openbare toegangsniveau van de webcontainer

U het openbare toegangsniveau van de **$web** container wijzigen, maar dit heeft geen invloed op het primaire statische websiteeindpunt omdat deze bestanden worden weergegeven via anonieme toegangsverzoeken. Dat betekent openbare (alleen-lezen) toegang tot alle bestanden.

In de volgende schermafbeelding wordt de instelling voor het openbare toegangsniveau in de Azure-portal weergegeven:

![Schermafbeelding van het instellen van openbaar toegangsniveau in de portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Hoewel het primaire statische eindpunt van de website niet wordt beïnvloed, heeft een wijziging in het openbare toegangsniveau gevolgen voor het primaire eindpunt van de blobservice.

Als u bijvoorbeeld het openbare toegangsniveau van de **$web** container wijzigt van **Privé (geen anonieme toegang)** naar **Blob (alleen anonieme leestoegang voor blobs)**, verandert het niveau van openbare toegang tot het primaire statische websiteeindpunt `https://contosoblobaccount.z22.web.core.windows.net/index.html` niet.

De openbare toegang tot het primaire `https://contosoblobaccount.blob.core.windows.net/$web/index.html` eindpunt van de blobservice verandert echter van privé naar openbaar. Nu kunnen gebruikers dat bestand openen met behulp van een van deze twee eindpunten.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Een aangepast domein toewijzen aan een statische website-URL

U uw statische website beschikbaar stellen via een aangepast domein. 

Het is eenvoudiger om HTTP-toegang in te schakelen voor uw aangepaste domein, omdat Azure Storage dit afzonderlijk ondersteunt. Als u HTTPS wilt inschakelen, moet u Azure CDN gebruiken omdat Azure Storage nog geen eigen ondersteuning biedt voor HTTPS met aangepaste domeinen. zie [Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt](storage-custom-domain-name.md) voor stapsgewijze richtlijnen.

Als het opslagaccount is geconfigureerd om een veilige overdracht via HTTPS [te vereisen,](../common/storage-require-secure-transfer.md) moeten gebruikers het HTTPS-eindpunt gebruiken. 

> [!TIP]
> Overweeg uw domein te hosten op Azure. Zie [Uw domein hosten in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)voor meer informatie.

## <a name="adding-http-headers"></a>HTTP-koppen toevoegen

Er is geen manier om kopteksten te configureren als onderdeel van de statische websitefunctie. U Azure CDN echter gebruiken om kopteksten toe te voegen en kopwaarden toe te voegen (of te overschrijven). Zie [Standaardregels engine reference voor Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Zie [Azure CDN-cachinggedrag beheren met cachingregels](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)als u kopteksten wilt gebruiken om caching te beheren.

## <a name="pricing"></a>Prijzen

U statische websitehosting gratis inschakelen. U wordt alleen gefactureerd voor de blob-opslag die uw site gebruikt en bedrijfskosten. Bekijk de [pagina Azure Blob Storage voor](https://azure.microsoft.com/pricing/details/storage/blobs/)meer informatie over de prijzen voor Azure Blob Storage.

## <a name="metrics"></a>Metrische gegevens

U statistieken inschakelen op statische websitepagina's. Zodra u statistieken hebt ingeschakeld, worden verkeersstatistieken over bestanden in de **$web** container gerapporteerd in het dashboard met statistieken.

Zie Statistieken inschakelen op statische [websitepagina's](storage-blob-static-website-how-to.md#metrics)als u statistieken op uw statische websitepagina's wilt inschakelen.

## <a name="next-steps"></a>Volgende stappen

* [Een statische website hosten in Azure Storage](storage-blob-static-website-how-to.md)
* [Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Bouw je eerste serverloze web-app](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Zelfstudie: uw domein hosten in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
