---
title: Beleid voor inhoudsbescherming configureren met behulp van de Azure-portal | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u de Azure-portal gebruiken om beleid voor inhoudsbescherming te configureren. Het artikel laat ook zien hoe u dynamische versleuteling voor uw assets inschakelen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978199"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Beleid voor inhoudsbescherming configureren met behulp van de Azure-portal

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.   > Er worden geen nieuwe functies of functionaliteit toegevoegd aan Media Services v2. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)
>

 Met Azure Media Services u uw media beveiligen vanaf het moment dat deze uw computer verlaat via opslag, verwerking en levering. U Media Services gebruiken om uw inhoud dynamisch versleuteld te leveren met de Advanced Encryption Standard (AES) met behulp van 128-bits coderingssleutels. U het ook gebruiken met gemeenschappelijke versleuteling (CENC) met behulp van PlayReady en/of Widevine digital rights management (DRM) en Apple FairPlay. 

Media Services biedt een service voor het leveren van DRM-licenties en AES clear keys aan geautoriseerde clients. U de Azure-portal gebruiken om één sleutel-/licentieautorisatiebeleid te maken voor alle soorten versleutelingen.

In dit artikel wordt uitgelegd hoe u een beleid voor inhoudsbescherming configureert met behulp van de portal. Het artikel laat ook zien hoe u dynamische versleuteling toepast op uw assets.

## <a name="start-to-configure-content-protection"></a>Inhoudsbeveiliging opnieuw configureren
Als u de portal wilt gebruiken om wereldwijde inhoudsbescherming te configureren met behulp van uw Media Services-account, neemt u de volgende stappen:

1. Selecteer in de [portal](https://portal.azure.com/)uw Media Services-account.

1. Selecteer **Inhoudsbescherming instellingen** > **Content protection**.

    ![Inhoudsbeveiliging](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Licentieautorisatiebeleid
Media Services ondersteunt meerdere manieren om gebruikers te authenticeren die sleutel- of licentieaanvragen indienen. U moet het autorisatiebeleid voor de inhoudssleutel configureren. Uw klant moet dan voldoen aan het beleid voordat de sleutel / licentie kan worden geleverd aan het. Het autorisatiebeleid voor inhoudssleutels kan een of meer autorisatiebeperkingen hebben: beperking voor openen of tokenbeperkingen.

U de portal gebruiken om één sleutel-/licentieautorisatiebeleid te maken voor alle soorten versleutelingen.

### <a name="open-authorization"></a>Machtiging openen
Open beperking betekent dat het systeem de sleutel levert aan iedereen die een sleutelverzoek doet. Deze beperking kan nuttig zijn voor testdoeleinden. 

### <a name="token-authorization"></a>Tokenautorisatie
Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de indelingen Simple Web Tokens (SWT) en JSON Web Token (JWT). Media Services biedt geen STS. U een aangepaste STS maken of Azure Access Control Service gebruiken om tokens uit te geven. De STS moet zijn geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel- en uitgifteclaims die u hebt opgegeven in de configuratie van tokenbeperkingen. Als het token geldig is en de claims in het token overeenkomen met de claims die zijn geconfigureerd voor de sleutel (of licentie), retourneert de mediaservice voor de sleutellevering van Media Services de gevraagde sleutel (of licentie) naar de client.

Wanneer u het beleid met een tokenbeperking configureert, moet u de primaire verificatiesleutel, de uitgever en de doelgroepparameters opgeven. De primaire verificatiesleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de beveiligde tokenservice die het token uitgeeft. De doelgroep (ook wel scope genoemd) beschrijft de intentie van het token of de bron waartoe het token toegang geeft. De service voor het leveren van de sleutel van Media Services valideert dat deze waarden in het token overeenkomen met de waarden in de sjabloon.

![Licentieautorisatiebeleid](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licentiesjabloon
De PlayReady-licentiesjabloon stelt de functionaliteit in die is ingeschakeld op uw PlayReady-licentie. Zie het overzicht van de Media [Services PlayReady-licentiesjabloon](media-services-playready-license-template-overview.md)voor meer informatie over de PlayReady-licentiesjabloon.

### <a name="nonpersistent"></a>Niet-persistent
Als u een licentie configureert als niet-persistent, wordt deze alleen in het geheugen bewaard terwijl de speler de licentie gebruikt.  

![Niet-persistente inhoudsbescherming](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Permanent
Als u een licentie als persistent configureert, wordt deze opgeslagen in permanente opslag op de client.

![Permanente inhoudsbescherming](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licentiesjabloon
De widevine-licentiesjabloon stelt de functionaliteit in die is ingeschakeld voor uw Widevine-licenties.

### <a name="basic"></a>Basic
Wanneer u **Basic**selecteert, wordt de sjabloon gemaakt met alle standaardwaarden.

### <a name="advanced"></a>Geavanceerd
Zie het overzicht van de [widevine-licentiesjabloon](media-services-widevine-license-template-overview.md)voor meer informatie over de sjabloon widevine-rechten .

![Geavanceerde inhoudsbescherming](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-configuratie
Als u FairPlay-versleuteling wilt inschakelen, selecteert u **FairPlay-configuratie**. Selecteer vervolgens het **app-certificaat** en voer de **geheime toepassingssleutel in**. Zie [Uw HLS-inhoud beveiligen met Apple FairPlay of Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md)voor meer informatie over de configuratie en vereisten van FairPlay.

![FairPlay-configuratie](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Dynamische versleuteling toepassen op uw asset
Om te profiteren van dynamische versleuteling, codeer je je bronbestand in een set adaptive-bitrate MP4-bestanden.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecteer een element dat u wilt versleutelen
Als u al uw assets wilt zien, selecteert u > **Instellingenassets**. **Settings**

![Assets, optie](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Versleutelen met AES of DRM
Wanneer u **Versleutelen** voor een asset selecteert, ziet u twee keuzes: **AES** of **DRM**. 

#### <a name="aes"></a>AES
AES clear key encryption is ingeschakeld op alle streaming protocollen: Smooth Streaming, HLS en MPEG-DASH.

![Versleutelingsconfiguratie](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>Drm
1. Nadat u **DRM**hebt geselecteerd, ziet u verschillende inhoudsbeschermingsbeleidsregels (die op dit punt moeten worden geconfigureerd) en een reeks streamingprotocollen:

    a. **PlayReady en Widevine met MPEG-DASH** versleutelen dynamisch uw MPEG-DASH stream met PlayReady en Widevine DRMs.

    b. **PlayReady en Widevine met MPEG-DASH + FairPlay met HLS** versleutelen dynamisch je MPEG-DASH stream met PlayReady en Widevine DRM's. Deze optie versleutelt ook uw HLS-streams met FairPlay.

    c. **PlayReady alleen met Smooth Streaming, HLS en MPEG-DASH** versleutelt vloeiend streamen, HLS en MPEG-DASH-streams dynamisch met PlayReady DRM.

    d. **Widevine alleen met MPEG-DASH** versleutelt dynamisch uw MPEG-DASH met Widevine DRM.
    
    e. **FairPlay alleen met HLS** versleutelt dynamisch uw HLS-stream met FairPlay.

1. Als u FairPlay-versleuteling wilt inschakelen, selecteert u op het **blade Global Settings voor inhoudsbescherming** de optie **FairPlay-configuratie**. Selecteer vervolgens het **app-certificaat**en voer de **geheime toepassingssleutel in**.

    ![Versleutelingstype](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Nadat u de versleutelingsselectie hebt gemaakt, selecteert u **Toepassen**.

>[!NOTE] 
>Als u van plan bent een met AES versleutelde HLS in Safari af te spelen, raadpleegt u het blogbericht [Versleutelde HLS in Safari.](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

