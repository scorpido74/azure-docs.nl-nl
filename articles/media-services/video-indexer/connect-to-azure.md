---
title: Een Video Indexer-account maken in de Azure Portal
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u een Video Indexer-account maakt in de Azure Portal.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: dc844392d64178cb9000db15af39f923521efc19
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838384"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Een Video Indexer-account maken dat is verbonden met Azure

Wanneer u een Video Indexer-account maakt, kunt u kiezen uit een gratis proefversie (waarmee u een bepaald aantal gratis minuten indexering krijgt) of een betaalde optie (zonder quotumlimiet). Bij de gratis proefversie biedt Video Indexer websitegebruikers maximaal 600 minuten aan gratis indexering en API-gebruikers maximaal 2400 minuten gratis indexering. Met de betaalde optie maakt u een Video Indexer-account dat is verbonden met uw Azure-abonnement en een Azure Media Services-account. U betaalt zowel voor de geïndexeerde minuten als voor kosten verbonden aan het Media-account. 

In dit artikel wordt beschreven hoe u een Video Indexer-account maakt dat is gekoppeld aan een Azure-abonnement en een Azure Media Services-account. Het onderwerp bevat stappen voor het maken van verbinding met Azure met behulp van de automatische stroom (standaard). Ook wordt uitgelegd hoe u hand matig verbinding maakt met Azure (Geavanceerd).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement.

    Als u nog geen Azure-abonnement hebt, meldt u zich aan voor een [gratis proef versie van Azure](https://azure.microsoft.com/free/).

* Een Azure Active Directory-domein (AD).

    Als u geen Azure AD-domein hebt, maakt u dit domein met uw Azure-abonnement. Zie [aangepaste domein namen beheren in uw Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md) voor meer informatie.

* Een gebruiker in uw Azure AD-domein met een rol van **toepassings beheerder** . U gebruikt dit lid wanneer u uw Video Indexer-account verbindt met Azure.

    Deze gebruiker moet een Azure AD-gebruiker zijn met een werk-of school account, niet een persoonlijk account, zoals outlook.com, live.com of hotmail.com.

    ![alle AAD-gebruikers](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Aanvullende vereisten voor automatische stroom

Een gebruiker en lid van uw Azure AD-domein. U gebruikt dit lid wanneer u uw Video Indexer-account verbindt met Azure.

Deze gebruiker moet lid zijn van uw Azure-abonnement met ofwel een rol van **eigenaar** , ofwel beheerder rollen voor **Inzender** en **gebruikers toegang** . Een gebruiker kan twee maal worden toegevoegd met 2 rollen. Eenmaal met Inzender en eenmaal met beheerder voor gebruikers toegang.

![Toegangs beheer](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Aanvullende vereisten voor hand matige stroom

Registreer de EventGrid-resource provider met behulp van de Azure Portal.

Ga in [Azure Portal](https://portal.azure.com/)naar **abonnementen**-> [Subscription]->**ResourceProviders**. 

Zoek naar **micro soft. Media** en **micro soft. EventGrid**. Als dat niet het geval is, klikt u op **registreren**. Het duurt enkele minuten om u te registreren.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Verbinding maken met Azure

> [!NOTE]
> Als uw Azure-abonnement gebruikmaakt van multi-factor Authentication op basis van certificaten, is het van cruciaal belang dat u de volgende stappen uitvoert op een apparaat waarop de vereiste certificaten zijn geïnstalleerd.

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.

2. Klik op de knop **Nieuw account maken** :

    ![verbinding maken met Azure](./media/create-account/connect-to-azure.png)

3. Wanneer de lijst abonnementen wordt weer gegeven, selecteert u het abonnement dat u wilt gebruiken.

    ![Video Indexer verbinden met Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecteer een Azure-regio op de locatie die wordt ondersteund: VS-West 2, Europa-noord of Azië-oost.
5. Kies onder **Azure Media Services account**een van de volgende opties:

    * Als u een nieuw Media Services account wilt maken, selecteert u **nieuwe resource groep maken**. Geef een naam op voor de resource groep.

        In azure wordt uw nieuwe account in uw abonnement gemaakt, met inbegrip van een nieuw Azure Storage-account. Uw nieuwe Media Services-account heeft een standaard initiële configuratie met een streaming-eind punt en 10 S3 gereserveerde eenheden.
    * Als u een bestaand Media Services account wilt gebruiken, selecteert u **bestaande resource gebruiken**. Selecteer uw account in de lijst accounts.

        Uw Media Services-account moet dezelfde regio hebben als uw Video Indexer-account. 

        > [!NOTE]
        > Om de indexerings duur en lage door voer te minimaliseren, wordt het ten zeerste aanbevolen om het type en het aantal [gereserveerde eenheden](../previous/media-services-scale-media-processing-overview.md ) aan te passen aan **10 S3 gereserveerde eenheden** in uw Media Services-account. Zie [Portal gebruiken om gereserveerde eenheden te wijzigen](../previous/media-services-portal-scale-media-processing.md).

    * Als u de verbinding hand matig wilt configureren, klikt u op de koppeling **overschakelen naar hand matige configuratie** .

        Zie de sectie [verbinding maken met Azure Manual](#connect-to-azure-manually-advanced-option) (geavanceerde optie) hieronder voor gedetailleerde informatie.
6. Wanneer u klaar bent, kiest u **verbinding maken**. Deze bewerking kan enkele minuten duren. 

    Nadat u verbinding hebt gemaakt met Azure, wordt uw nieuwe Video Indexer-account weer gegeven in de lijst met accounts:

    ![nieuw account](./media/create-account/new-account.png)

7. Naar uw nieuwe account bladeren

## <a name="connect-to-azure-manually-advanced-option"></a>Hand matig verbinding maken met Azure (geavanceerde optie)

Als de verbinding met Azure is mislukt, kunt u proberen om het probleem op te lossen door hand matig verbinding te maken.

> [!NOTE]
> Het is raadzaam om de volgende drie accounts in dezelfde regio te hebben: de Video Indexer account waarmee u verbinding maakt met het Media Services account, evenals het Azure Storage-account dat is verbonden met hetzelfde Media Services-account.

### <a name="create-and-configure-a-media-services-account"></a>Een Media Services-account maken en configureren

1. Gebruik [Azure](https://portal.azure.com/) Portal om een Azure Media Services-account te maken, zoals wordt beschreven in [een account maken](../previous/media-services-portal-create-account.md).

    Wanneer u een opslag account voor uw Media Services-account maakt, selecteert u **StorageV2** voor de account soort en **geo-redundante (GRS)** voor de replicatie velden.

    ![nieuw AMS-account](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Zorg ervoor dat u de Media Services resource-en account namen noteert. U hebt deze nodig voor de stappen in de volgende sectie.

2. Pas het type en het aantal [gereserveerde eenheden](../previous/media-services-scale-media-processing-overview.md ) aan op **10 S3 gereserveerde eenheden** in het Media Services-account dat u hebt gemaakt. Zie [Portal gebruiken om gereserveerde eenheden te wijzigen](../previous/media-services-portal-scale-media-processing.md).
3. Voordat u uw Video's kunt afspelen in de webtoepassing Video Indexer, moet u het standaard **streaming-eind punt** van het nieuwe Media Services-account starten.

    Klik in het nieuwe Media Services-account op **streaming-eind punten**. Selecteer het streaming-eind punt en klik op Start.

    ![nieuw AMS-account](./media/create-account/create-ams-account2.png)

4. Er moet een AD-toepassing worden gemaakt om Video Indexer te verifiëren met Media Services-API. De volgende stappen leiden u door het Azure AD-verificatie proces dat wordt beschreven in [aan de slag met Azure AD-verificatie met behulp van de Azure Portal](../previous/media-services-portal-get-started-with-aad.md):

    1. Selecteer in het nieuwe Media Services account **API-toegang**.
    2. Selecteer de [verificatie methode](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)voor de Service-Principal.
    3. Haal de client-ID en het client geheim op, zoals wordt beschreven in de sectie [de client-id en het client geheim ophalen](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) .

        Nadat u **instellingen**->**sleutels**hebt geselecteerd, voegt u **Beschrijving**toe en drukt u op **Opslaan**. de sleutel waarde wordt gevuld.

        Als de sleutel verloopt, moet de eigenaar van het account contact opnemen met Video Indexer ondersteuning om de sleutel te vernieuwen.

        > [!NOTE]
        > Zorg ervoor dat u de sleutel waarde en de toepassings-ID noteert. U hebt deze nodig voor de stappen in de volgende sectie.

### <a name="connect-manually"></a>Hand matig verbinding maken

Selecteer in het dialoog venster **video indexer verbinding maken met een Azure-abonnement op** de pagina [video indexer](https://www.videoindexer.ai/) de **optie overschakelen naar hand matige configuratie** .

Geef in het dialoog venster de volgende informatie op:

|Instelling|Beschrijving|
|---|---|
|Regio van Video Indexer-account|De naam van de regio van het Video Indexer-account. Voor betere prestaties en lagere kosten wordt u ten zeerste aangeraden de naam op te geven van de regio waar de Azure Media Services resource en het Azure Storage account zich bevinden. |
|Azure Active Directory-Tenant (AAD)|De naam van de Azure AD-Tenant, bijvoorbeeld ' contoso.onmicrosoft.com '. De gegevens van de Tenant kunnen worden opgehaald uit de Azure Portal. Plaats de cursor op de naam van de aangemelde gebruiker in de rechter bovenhoek. Zoek de naam rechts van het **domein**.|
|Abonnements-id|Het Azure-abonnement waarmee deze verbinding moet worden gemaakt. De abonnements-ID kan worden opgehaald uit de Azure Portal. Klik op **alle services** in het linkerdeel venster en zoek naar ' Abonnementen '. Selecteer **abonnementen** en kies de gewenste id in de lijst met uw abonnementen.|
|Naam van de resource groep Azure Media Services|De naam voor de resource groep waarin u het Media Services-account hebt gemaakt.|
|Resource naam van media service|De naam van het Azure Media Services-account dat u in de vorige sectie hebt gemaakt.|
|Toepassings-id|De Azure AD-toepassings-ID (met machtigingen voor het opgegeven Media Services-account) dat u in de vorige sectie hebt gemaakt.|
|Toepassings sleutel|De Azure AD-toepassings sleutel die u in de vorige sectie hebt gemaakt. |

## <a name="considerations"></a>Overwegingen

De volgende Azure Media Services gerelateerde overwegingen zijn van toepassing:

* Als u automatisch verbinding maakt, ziet u een nieuwe resource groep, Media Services-account en een opslag account in uw Azure-abonnement.
* Als u automatisch verbinding maakt, stelt Video Indexer de **gereserveerde** media-eenheden in op 10 S3-eenheden:

    ![Gereserveerde eenheden Media Services](./media/create-account/ams-reserved-units.png)

* Als u verbinding maakt met een bestaand Media Services-account, wijzigt Video Indexer de bestaande configuratie van **gereserveerde** media-eenheden niet.

   Mogelijk moet u het type en het aantal gereserveerde media-eenheden aanpassen volgens de geplande belasting. Als uw belasting hoog is en u onvoldoende eenheden of snelheid hebt, kan de video verwerking leiden tot time-outstoringen.

* Als u verbinding maakt met een nieuw Media Services-account, wordt het standaard **streaming-eind punt** in video indexer automatisch gestart:

    ![Media Services streaming-eind punt](./media/create-account/ams-streaming-endpoint.png)

    Streaming-eind punten hebben een aanzienlijke opstart tijd. Daarom kan het enkele minuten duren vanaf het moment dat u uw account hebt verbonden met Azure, totdat uw Video's kunnen worden gestreamd en bekeken in de Video Indexer-webtoepassing.

* Als u verbinding maakt met een bestaand Media Services-account, Video Indexer de standaard configuratie voor het bijhouden van het eind punt niet wijzigen. Als er geen actief **streaming-eind punt**is, kunt u geen Video's bekijken uit dit Media Services-account of video indexer.

## <a name="next-steps"></a>Volgende stappen

U kunt programmatisch communiceren met uw proef account en/of met uw Video Indexer-accounts die zijn verbonden met Azure door de instructies te volgen in: [Api's gebruiken](video-indexer-use-apis.md).

U moet dezelfde Azure AD-gebruiker gebruiken die u hebt gebruikt om verbinding te maken met Azure.


