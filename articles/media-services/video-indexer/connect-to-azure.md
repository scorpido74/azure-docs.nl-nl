---
title: Een Video Indexer-account maken dat is verbonden met Azure
titleSuffix: Azure Media Services
description: Meer informatie over het maken van een Video Indexer-account dat is verbonden met Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 7ec8004fa0a54265962e79320c02287b2fcf814a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499917"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Een Video Indexer-account maken dat is verbonden met Azure

Wanneer u een Video Indexer-account maakt, u een gratis proefaccount kiezen (waarbij u een bepaald aantal gratis indexeringsminuten krijgt) of een betaalde optie (waarbij u niet beperkt bent door het quotum). Met een gratis proefperiode biedt Video Indexer tot 600 minuten gratis indexering aan websitegebruikers en tot 2400 minuten gratis indexering aan API-gebruikers. Met de betaalde optie maakt u een Video Indexer-account dat is verbonden met uw Azure-abonnement en een Azure Media Services-account. U betaalt voor geïndexeerde minuten en de mediaaccountgerelateerde kosten.

In dit artikel ziet u hoe u een Video Indexer-account maakt dat is gekoppeld aan een Azure-abonnement en een Azure Media Services-account. Het onderwerp bevat stappen voor het maken van verbinding met Azure met behulp van de automatische (standaard) stroom. Het laat ook zien hoe u handmatig verbinding maken met Azure (geavanceerd).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement.

    Als u nog geen Azure-abonnement hebt, meldt u zich aan voor [Gratis proefversie van Azure.](https://azure.microsoft.com/free/)

* Een Azure AD-domein (Azure Directory) (Azure Directory).

    Als u geen Azure AD-domein hebt, maakt u dit domein met uw Azure-abonnement. Zie [Aangepaste domeinnamen beheren in uw Azure AD voor](../../active-directory/users-groups-roles/domains-manage.md) meer informatie

* Een gebruiker in uw Azure AD-domein met een **toepassingsbeheerrol.** U gebruikt dit lid wanneer u uw Video Indexer-account verbindt met Azure.

    Deze gebruiker moet een Azure AD-gebruiker zijn met een werk- of schoolaccount. Gebruik geen persoonlijk account, zoals outlook.com, live.com of hotmail.com.

    ![alle AAD-gebruikers](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Aanvullende vereisten voor automatische stroom

* Een gebruiker en lid in uw Azure AD-domein.

    U gebruikt dit lid wanneer u uw Video Indexer-account verbindt met Azure.

    Deze gebruiker moet lid zijn van uw Azure-abonnement met een **rol eigenaar** of een functie voor de **inzender** en **gebruikerstoegangsbeheerders.** Een gebruiker kan twee keer worden toegevoegd, met twee rollen. Eén keer met inzender en één keer met gebruikerstoegangsbeheerder.

    ![toegangsbeheer](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Aanvullende vereisten voor handmatige stroom

* Registreer de EventGrid-resourceprovider via de Azure-portal.

    Ga in de [Azure-portal](https://portal.azure.com/)naar **Abonnementen**->[abonnement]->**ResourceProviders**.

    Zoeken naar **Microsoft.Media** en **Microsoft.EventGrid**. Als dit niet in de status 'Geregistreerd' staat, klikt u op **Registreren**. Het duurt een paar minuten om te registreren.

    ![EventGrid (EventGrid)](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Verbinding maken met Azure

> [!NOTE]
> Als uw Azure-abonnement op basis van certificaten multifactorauthenticatie gebruikt, is het van cruciaal belang dat u de volgende stappen uitvoert op een apparaat waarop de vereiste certificaten zijn geïnstalleerd.

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.

2. Selecteer de knop **Nieuw account maken:**

    ![Nieuw Video Indexer-account maken](./media/create-account/connect-to-azure.png)

3. Wanneer de lijst met abonnementen wordt weergegeven, selecteert u het abonnement dat u wilt gebruiken.

    ![Videoindexer verbinden met Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecteer een Azure-regio op de ondersteunde locaties: West US 2, Noord-Europa of Oost-Azië.
5. Kies onder **Azure Media Services-account**een van de volgende opties:

    * Als u een nieuw Media Services-account wilt maken, selecteert u **Nieuwe resourcegroep maken**. Geef een naam op voor uw resourcegroep.

        Azure maakt uw nieuwe account in uw abonnement, inclusief een nieuw Azure Storage-account. Uw nieuwe Media Services-account heeft een standaard initiële configuratie met een streaming-eindpunt en 10 Gereserveerde S3-eenheden.
    * Als u een bestaand Media Services-account wilt gebruiken, selecteert u **Bestaande resource gebruiken**. Selecteer uw account in de lijst met accounts.

        Uw Media Services-account moet dezelfde regio hebben als uw Video Indexer-account.

        > [!NOTE]
        > Om de indexeringsduur en lage doorvoer te minimaliseren, is het ten zeerste aan te raden om het type en het aantal [gereserveerde eenheden](../previous/media-services-scale-media-processing-overview.md ) in uw Media Services-account aan te passen aan **10 gereserveerde eenheden van S3.** Zie [Portal gebruiken om gereserveerde eenheden te wijzigen](../previous/media-services-portal-scale-media-processing.md).

    * Als u uw verbinding handmatig wilt configureren, selecteert u de **koppeling Overschakelen naar handmatige configuratie.**

        Zie de sectie [Verbinding met Azure](#connect-to-azure-manually-advanced-option) (geavanceerde optie) die volgt voor gedetailleerde informatie.
6. Als u klaar bent, kiest u **Verbinding maken**. Deze bewerking kan enkele minuten duren.

    Nadat u bent verbonden met Azure, wordt uw nieuwe Video Indexer-account weergegeven in de accountlijst:

    ![nieuwe account](./media/create-account/new-account.png)

7. Blader naar je nieuwe account.

## <a name="connect-to-azure-manually-advanced-option"></a>Handmatig verbinding maken met Azure (geavanceerde optie)

Als de verbinding met Azure is mislukt, u proberen het probleem op te lossen door handmatig verbinding te maken.

> [!NOTE]
> Het wordt ten zeerste aanbevolen om de volgende drie accounts in dezelfde regio te hebben: het Video Indexer-account waarmee u verbinding maakt met het Media Services-account en het Azure-opslagaccount dat is verbonden met hetzelfde Media Services-account.

### <a name="create-and-configure-a-media-services-account"></a>Een Media Services-account maken en configureren

1. Gebruik de [Azure-portal](https://portal.azure.com/) om een Azure Media Services-account te maken, zoals beschreven in [Een account maken.](../previous/media-services-portal-create-account.md)

    Wanneer u een opslagaccount aanmaakt voor uw Media Services-account, selecteert u **StorageV2** voor accountvriendelijk en **Geo-redundant (GRS)** voor replicatievelden.

    ![Nieuwe AMS-account](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Zorg ervoor dat u de bron- en accountnamen van Media Services opschrijft. Je hebt ze nodig voor de stappen in de volgende sectie.

2. Pas het type en het aantal [gereserveerde eenheden](../previous/media-services-scale-media-processing-overview.md ) aan **10 Gereserveerde Eenheden s3** aan in het Media Services-account dat u hebt gemaakt. Zie [Portal gebruiken om gereserveerde eenheden te wijzigen](../previous/media-services-portal-scale-media-processing.md).
3. Voordat u uw video's afspelen in de web-app Video-indexer, moet u het standaard **eindpunt voor streaming** van het nieuwe Media Services-account starten.

    Selecteer **streamingeindpunten**in het nieuwe Media Services-account . Selecteer vervolgens het streaming-eindpunt en druk op Start.

    ![Nieuwe AMS-account](./media/create-account/create-ams-account2.png)

4. Als video-indexer zich wil verifiëren met de Media Services API, moet er een AD-app worden gemaakt. De volgende stappen leiden u door het Azure AD-verificatieproces dat is beschreven in [Aan de slag met Azure AD-verificatie met behulp van de Azure-portal:](../previous/media-services-portal-get-started-with-aad.md)

    1. Selecteer **API-toegang**in het nieuwe Media Services-account .
    2. Selecteer [Serviceprincipal-verificatiemethode](../previous/media-services-portal-get-started-with-aad.md).
    3. Krijg de client-ID en client geheim

        Nadat u **Instellingentoetsen**->**Keys**hebt geselecteerd, voegt u **Beschrijving**toe, drukt u op **Opslaan**en wordt de sleutelwaarde ingevuld.

        Als de sleutel verloopt, moet de eigenaar van het account contact opnemen met de ondersteuning van video-indexerom de sleutel te verlengen.

        > [!NOTE]
        > Zorg ervoor dat u de sleutelwaarde en de toepassings-id opschrijft. Je hebt het nodig voor de stappen in de volgende sectie.

### <a name="connect-manually"></a>Handmatig verbinden

Selecteer in de koppeling **Video-indexer aansluiten op een Azure-abonnement** op uw [video-indexerpagina](https://www.videoindexer.ai/) de **koppeling Overschakelen naar handmatige configuratie.**

Geef in het dialoogvenster de volgende informatie op:

|Instelling|Beschrijving|
|---|---|
|Video Indexer-accountgebied|De naam van het accountgebied Video-indexer. Voor betere prestaties en lagere kosten is het ten zeerste aan te raden om de naam op te geven van het gebied waar de Azure Media Services-bron en het Azure Storage-account zich bevinden. |
|Azure AD-tenant|De naam van de Azure AD-tenant, bijvoorbeeld 'contoso.onmicrosoft.com'. De tenantgegevens kunnen worden opgehaald uit de Azure-portal. Plaats de cursor op de naam van de aangemelde gebruiker in de rechterbovenhoek. Zoek de naam rechts van **Domein**.|
|Abonnements-id|Het Azure-abonnement waaronder deze verbinding moet worden gemaakt. De abonnements-ID kan worden opgehaald uit de Azure-portal. Selecteer **Alle services** in het linkerdeelvenster en zoek naar 'abonnementen'. Selecteer **Abonnementen** en kies de gewenste id in de lijst met abonnementen.|
|Naam van azure Media Services-brongroep|De naam voor de resourcegroep waarin u het Media Services-account hebt gemaakt.|
|Naam mediaservicebron|De naam van het Azure Media Services-account dat u in de vorige sectie hebt gemaakt.|
|Toepassings-id|De Azure AD-toepassings-id (met machtigingen voor het opgegeven Media Services-account) die u in de vorige sectie hebt gemaakt.|
|Toepassingssleutel|De Azure AD-toepassingssleutel die u in de vorige sectie hebt gemaakt. |

## <a name="considerations"></a>Overwegingen

De volgende azure mediaservices gerelateerde overwegingen zijn van toepassing:

* Als u automatisch verbinding maakt, ziet u een nieuwe Resourcegroep, Media Services-account en een Opslagaccount in uw Azure-abonnement.
* Als u automatisch verbinding maakt, stelt Video Indexer de **gereserveerde media-eenheden in op** 10 S3-eenheden:

    ![Gereserveerde mediaservices-eenheden](./media/create-account/ams-reserved-units.png)

* Als u verbinding maakt met een bestaand Media Services-account, wijzigt Video Indexer de configuratie van de bestaande media **gereserveerde eenheden** niet.

   Mogelijk moet u het type en het aantal gereserveerde mediaapparaten aanpassen aan uw geplande belasting. Houd er rekening mee dat als uw belasting hoog is en u niet genoeg eenheden of snelheid hebt, video's verwerkt kan leiden tot time-out fouten.

* Als u verbinding maakt met een nieuw Media Services-account, start Video Indexer automatisch het standaard **eindpunt voor streaming:**

    ![Streamingeindpunt voor Media Services](./media/create-account/ams-streaming-endpoint.png)

    Streaming endpoints hebben een aanzienlijke opstarttijd. Daarom kan het enkele minuten duren vanaf het moment dat u uw account aan Azure hebt gekoppeld totdat uw video's kunnen worden gestreamd en bekeken in de web-app Video-indexer.

* Als u verbinding maakt met een bestaand Media Services-account, wijzigt Video Indexer de standaardconfiguratie van Streaming Endpoint niet. Als er geen **streamingeindpunt**wordt uitgevoerd, u geen video's bekijken van dit Media Services-account of in Video Indexer.

## <a name="next-steps"></a>Volgende stappen

U programmatisch communiceren met uw proefaccount en/of met uw Video Indexer-accounts die zijn verbonden met Azure door de instructies te volgen in: [API's gebruiken.](video-indexer-use-apis.md)

U moet dezelfde Azure AD-gebruiker gebruiken die u hebt gebruikt bij een verbinding met Azure.