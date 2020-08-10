---
title: Problemen met aanmelden oplossen met voorwaardelijke toegang-Azure Active Directory
description: In dit artikel wordt beschreven wat u moet doen wanneer uw beleid voor voorwaardelijke toegang resulteert in onverwachte resultaten
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6778b556795f4e079100f1a7bcbb8b9465e9e315
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032965"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Problemen met aanmelden met behulp van voorwaardelijke toegang oplossen

De informatie in dit artikel kan worden gebruikt om problemen met onverwachte aanmeldings resultaten te verhelpen die betrekking hebben op voorwaardelijke toegang met behulp van fout berichten en logboeken van Azure AD-aanmeldingen.

## <a name="conditional-access-sign-in-interrupt"></a>Aanmeldings interrupt voor voorwaardelijke toegang

De eerste manier is om het fout bericht te bekijken dat wordt weer gegeven. Voor problemen bij het aanmelden bij het gebruik van een webbrowser heeft de fout pagina zelf gedetailleerde informatie. In deze informatie wordt alleen beschreven wat het probleem is en wordt er een oplossing voorgesteld.

![Fout: compatibel apparaat vereist](./media/troubleshoot-conditional-access/image1.png)

In de bovenstaande fout geeft het bericht aan dat de toepassing alleen toegankelijk is vanaf apparaten of client toepassingen die voldoen aan het Mobile Device Management-beleid van het bedrijf. In dit geval voldoen de toepassing en het apparaat niet aan dat beleid.

## <a name="azure-ad-sign-in-events"></a>Aanmeldings gebeurtenissen voor Azure AD

De tweede methode om gedetailleerde informatie over de onderbreking van de aanmelding op te halen, is door de aanmeldings gebeurtenissen van Azure AD te controleren om te zien welk beleid of beleid voor voorwaardelijke toegang is toegepast en waarom.

Meer informatie over het probleem vindt u door in de eerste fout pagina op **meer details** te klikken. Als u op **meer details** klikt, krijgt u informatie over het oplossen van problemen die handig zijn bij het zoeken in de aanmeldings gebeurtenissen van Azure AD voor de specifieke fout gebeurtenis die de gebruiker zag of bij het openen van een ondersteunings incident bij micro soft.

![Meer informatie over een onderbroken webbrowser aanmelding met voorwaardelijke toegang.](./media/troubleshoot-conditional-access/image2.png)

Ga als volgt te werk om te ontdekken welk beleid of beleid voor voorwaardelijke toegang wordt toegepast en waarom het volgende geldt.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of globale lezer.
1. Blader naar **Azure Active Directory**  >  **aanmeldingen**.
1. Zoek de gebeurtenis voor de aanmelding om te controleren. Filters en kolommen toevoegen of verwijderen om overbodige gegevens te filteren.
   1. Filters toevoegen om het bereik te beperken:
      1. **Correlatie-id** wanneer u een specifieke gebeurtenis hebt die moet worden onderzocht.
      1. **Voorwaardelijke toegang** om beleids storingen en geslaagde pogingen te bekijken. Bereik uw filter zodanig dat er alleen fouten worden weer gegeven om de resultaten te beperken.
      1. **Gebruikers naam** om informatie weer te geven die betrekking heeft op specifieke gebruikers.
      1. De **datum** ligt binnen het bereik van het desbetreffende tijds bestek.

   ![Het filter voor voorwaardelijke toegang in het aanmeldings logboek selecteren](./media/troubleshoot-conditional-access/image3.png)

1. Als de aanmeld gebeurtenis die overeenkomt met de aanmeldings fout van de gebruiker is gevonden, selecteert u het tabblad **voorwaardelijke toegang** . Op het tabblad voorwaardelijke toegang worden het specifieke beleid of beleid weer gegeven dat heeft geresulteerd in de onderbreking van de aanmelding.
   1. Informatie op het tabblad **probleem oplossing en ondersteuning** kan een duidelijke reden geven voor het mislukken van een aanmelding, zoals een apparaat dat niet aan de nalevings vereisten voldoet.
   1. Als u verder wilt onderzoeken, zoomt u in op de configuratie van het beleid door te klikken op de naam van het **beleid**. Als u op de **beleids naam** klikt, wordt de gebruikers interface van de beleids configuratie voor het geselecteerde beleid weer gegeven voor controle en bewerking.
   1. De gegevens van de **client gebruiker** en het **apparaat** die zijn gebruikt voor de evaluatie van het voorwaardelijke toegangs beleid zijn ook beschikbaar in de **basis informatie**, **locatie**, **apparaatgegevens**, **verificatie gegevens**en **aanvullende details** tabbladen van de aanmeldings gebeurtenis.

### <a name="policy-details"></a>Details van beleid

Als u het weglatings teken aan de rechter kant van het beleid selecteert in een aanmeldings gebeurtenis, worden de beleids Details geopend. Dit geeft beheerders aanvullende informatie over waarom een beleid is toegepast of niet.

   ![Tabblad voorwaardelijke toegang voor gebeurtenis aanmelden](./media/troubleshoot-conditional-access/image5.png)

   ![Beleids Details (preview-versie)](./media/troubleshoot-conditional-access/policy-details.png)

De linkerkant bevat details die zijn verzameld bij het aanmelden en de rechter kant bevat details over de vraag of deze details voldoen aan de vereisten van het toegepaste beleid voor voorwaardelijke toegang. Beleid voor voorwaardelijke toegang is alleen van toepassing wanneer aan alle voor waarden wordt voldaan of niet is geconfigureerd.

Als de informatie in de gebeurtenis niet voldoende is om inzicht te krijgen in de aanmeldings resultaten of het beleid aan te passen om de gewenste resultaten te krijgen, kan een ondersteunings incident worden geopend. Navigeer naar het tabblad **probleem oplossing en ondersteuning** van het aanmeldings gebeurtenis en selecteer **een nieuwe ondersteunings aanvraag maken**.

![Het tabblad probleem oplossing en ondersteuning van de aanmeld gebeurtenis](./media/troubleshoot-conditional-access/image6.png)

Wanneer u het incident verzendt, geeft u de aanvraag-ID en het tijdstip en de datum van de aanmeldings gebeurtenis op in de details van de indiening van het incident. Met deze informatie kan micro soft ondersteuning het evenement vinden waarmee u zich bezig houdt.

### <a name="conditional-access-error-codes"></a>Fout codes voor voorwaardelijke toegang

| Fout code voor aanmelden | Fout reeks |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Volgende stappen

- [Aanmeldactiviteitenrapporten in de Azure Active Directory-portal](../reports-monitoring/concept-sign-ins.md)
- [Problemen met voorwaardelijke toegang oplossen met het What If-hulp programma](troubleshoot-conditional-access-what-if.md)
- Aanbevolen procedures voor [voorwaardelijke toegang in azure Active Directory](best-practices.md)
