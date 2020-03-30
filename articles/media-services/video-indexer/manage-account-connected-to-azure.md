---
title: Een Video Indexer-account beheren
titleSuffix: Azure Media Services
description: Meer informatie over het beheren van een Video Indexer-account dat is verbonden met Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499667"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Een Video Indexer-account beheren dat is verbonden met Azure

In dit artikel wordt uitgelegd hoe u een Video Indexer-account beheert dat is verbonden met uw Azure-abonnement en een Azure Media Services-account.

> [!NOTE]
> U moet de eigenaar van het Video-indexer-account zijn om accountconfiguratieaanpassingen uit te voeren die in dit onderwerp worden besproken.

## <a name="prerequisites"></a>Vereisten

Verbind uw Video Indexer-account met Azure, zoals beschreven in [Verbonden met Azure.](connect-to-azure.md)

Volg [voorwaarden](connect-to-azure.md#prerequisites) en bekijk [overwegingen](connect-to-azure.md#considerations) in het artikel.

## <a name="examine-account-settings"></a>Accountinstellingen onderzoeken

In deze sectie worden de instellingen van uw Video-indexeraccount onderzocht.

Instellingen weergeven:

1. Klik op het gebruikerspictogram in de rechterbovenhoek en selecteer **Instellingen**.

    ![Instellingen in video-indexer](./media/manage-account-connected-to-azure/select-settings.png)

2. Selecteer **op** de pagina Instellingen het tabblad **Account.**

Als uw Video-indexeraccount is verbonden met Azure, ziet u de volgende dingen:

* De naam van het onderliggende Azure Media Services-account.
* Het aantal indexeringstaken dat wordt uitgevoerd en in de wachtrij staat.
* Het aantal en het type toegewezen gereserveerde eenheden.

Als uw account een aantal aanpassingen nodig heeft, ziet u relevante fouten en waarschuwingen over uw accountconfiguratie op de pagina **Instellingen.** De berichten bevatten koppelingen naar exacte plaatsen in de Azure-portal waar u wijzigingen moet aanbrengen. Zie voor meer informatie de sectie [fouten en waarschuwingen](#errors-and-warnings) die volgt.

## <a name="repair-the-connection-to-azure"></a>De verbinding met Azure herstellen

In het dialoogvenster **Verbinding bijwerken met Azure Media Services** van uw [video-indexerpagina](https://www.videoindexer.ai/) wordt u gevraagd waarden op te geven voor de volgende instellingen:

|Instelling|Beschrijving|
|---|---|
|Azure-abonnements-ID|De abonnements-ID kan worden opgehaald uit de Azure-portal. Klik op **Alle services** in het linkerdeelvenster en zoek naar "abonnementen". Selecteer **Abonnementen** en kies de gewenste id in de lijst met abonnementen.|
|Naam van azure Media Services-brongroep|De naam voor de resourcegroep waarin u het Media Services-account hebt gemaakt.|
|Toepassings-id|De Azure AD-toepassings-id (met machtigingen voor het opgegeven Media Services-account) die u hebt gemaakt voor dit Video Indexer-account. <br/><br/>Als u de app-id wilt krijgen, navigeert u naar azure-portal. Kies onder het Media Services-account uw account en ga naar **API Access.** Selecteer **Verbinding maken met de Api van Media Services met serviceprincipal** -> **Azure AD App**. Kopieer de relevante parameters.|
|Toepassingssleutel|De Azure AD-toepassingssleutel die is gekoppeld aan uw Media Services-account die u hierboven hebt opgegeven. <br/><br/>Als u de app-sleutel wilt downloaden, navigeert u naar azure-portal. Kies onder het Media Services-account uw account en ga naar **API Access.** Selecteer **Verbinding maken met De API van Media Services met serviceprincipal** -> **Beheertoepassingscertificaten** -> **& geheimen**. Kopieer de relevante parameters.|

## <a name="autoscale-reserved-units"></a>Gereserveerde eenheden automatisch schalen

Op de pagina **Instellingen** u het automatisch schalen van gereserveerde mediaeenheden (RU) instellen. Als de optie **is ingeschakeld,** u het maximum aantal RU's toewijzen en er zeker van zijn dat Video Indexer stops/start-se's automatisch stopt/start. Met deze optie betaalt u geen extra geld voor idle-tijd, maar wacht u ook niet tot het indexeren van taken een lange tijd voltooit wanneer de indexeringsbelasting hoog is.

Autoscale schaalt niet onder 1 RU of boven de standaardlimiet van het Media Services-account. Als u de limiet wilt verhogen, maakt u een serviceaanvraag. Zie [Quota en beperkingen](../../media-services/previous/media-services-quotas-and-limitations.md)voor informatie over quota en beperkingen en het openen van een ondersteuningsticket.

![Video-indexeren voor gereserveerde eenheden automatisch schalen](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Fouten en waarschuwingen

Als uw account een aantal aanpassingen nodig heeft, ziet u relevante fouten en waarschuwingen over uw accountconfiguratie op de pagina **Instellingen.** De berichten bevatten koppelingen naar exacte plaatsen in de Azure-portal waar u wijzigingen moet aanbrengen. In deze sectie vindt u meer informatie over de fout- en waarschuwingsberichten.

* EventGrid (EventGrid)

    U moet de EventGrid-resourceprovider registreren via de Azure-portal. Ga in de [Azure-portal](https://portal.azure.com/)naar **Abonnementen** > [abonnement] > **ResourceProviders** > **Microsoft.EventGrid**. Als deze niet in de **geregistreerde** staat staat is, selecteert u **Register**. Het duurt een paar minuten om te registreren.

* Streaming-eindpunt

    Controleer of het onderliggende Media Services-account het standaard **eindpunt voor streaming** in een gestarte status heeft. Anders u geen video's bekijken van dit Media Services-account of in Video Indexer.

* Media gereserveerde eenheden

    U moet mediagereserveerde eenheden toewijzen aan uw Media Service-bron om video's te indexeren. Voor optimale indexeringsprestaties is het raadzaam om ten minste 10 Gereserveerde S3-eenheden toe te wijzen. Zie de veelgestelde vragen van de [prijspagina Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor prijsinformatie.

## <a name="next-steps"></a>Volgende stappen

U programmatisch communiceren met uw proefaccount of Video Indexer-accounts die zijn verbonden met Azure door de instructies te volgen in: [API's gebruiken.](video-indexer-use-apis.md)

Gebruik dezelfde Azure AD-gebruiker die u hebt gebruikt bij een verbinding met Azure.
