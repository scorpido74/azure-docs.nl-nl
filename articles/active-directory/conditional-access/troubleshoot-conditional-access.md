---
title: Aanmeldingsproblemen oplossen met voorwaardelijke toegang - Azure Active Directory
description: In dit artikel wordt beschreven wat u moet doen als uw beleid voor voorwaardelijke toegang tot onverwachte resultaten leidt
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337439"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Aanmeldingsproblemen oplossen met voorwaardelijke toegang

De informatie in dit artikel kan worden gebruikt om onverwachte aanmeldingsresultaten met betrekking tot voorwaardelijke toegang op te lossen met behulp van foutberichten en Azure AD-aanmeldingslogboeken.

## <a name="conditional-access-sign-in-interrupt"></a>Aanmeldingsonderbreking voorwaardelijke toegang

De eerste manier is om de foutmelding te bekijken die wordt weergegeven. Voor problemen met het aanmelden bij het gebruik van een webbrowser bevat de foutpagina zelf gedetailleerde informatie. Deze informatie alleen al kan beschrijven wat het probleem is en dat kan een oplossing suggereren.

![Aanmeldingsfout - apparaat dat compatibel is](./media/troubleshoot-conditional-access/image1.png)

In de bovenstaande fout wordt in het bericht vermeld dat de toepassing alleen toegankelijk is vanaf apparaten of clienttoepassingen die voldoen aan het beleid voor het beheer van mobiele apparaten van het bedrijf. In dit geval voldoen de toepassing en het apparaat niet aan dat beleid.

## <a name="azure-ad-sign-in-events"></a>Aanmeldingsgebeurtenissen voor Azure AD

De tweede methode om gedetailleerde informatie over de aanmeldingsonderbreking te krijgen, is om de aanmeldingsgebeurtenissen van Azure AD te controleren om te zien welk beleid of beleid voor voorwaardelijke toegang is toegepast en waarom.

Meer informatie is te vinden over het probleem door te klikken op **Meer details** in de eerste foutpagina. Als u op **Meer details** klikt, worden informatie over probleemoplossing weergegeven die nuttig is bij het zoeken in de aanmeldingsgebeurtenissen van Azure AD voor de specifieke foutgebeurtenis die de gebruiker heeft gezien of bij het openen van een ondersteuningsincident met Microsoft.

![Meer details van een aangemeld van een voorwaardelijke toegang onderbroken webbrowser.](./media/troubleshoot-conditional-access/image2.png)

Om erachter te komen welk beleid of beleid voor voorwaardelijke toegang is toegepast en waarom doe je het volgende.

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of wereldwijde lezer.
1. Blader naar Azure Active > **Directory-aanmeldingen**. **Azure Active Directory**
1. Zoek de gebeurtenis voor de aanmelding om te bekijken. Filters en kolommen toevoegen of verwijderen om onnodige informatie eruit te filteren.
   1. Filters toevoegen om het bereik te verkleinen:
      1. **Correlatie-ID** wanneer u een specifieke gebeurtenis moet onderzoeken.
      1. **Voorwaardelijke toegang** tot beleidsfouten en succes. Bereik uw filter om alleen fouten weer te geven om resultaten te beperken.
      1. **Gebruikersnaam** om informatie te zien met betrekking tot specifieke gebruikers.
      1. **Datum** die is beperkt tot het betrokken tijdsbestek.

   ![Het filter Voorwaardelijke toegang selecteren in het aanmeldingslogboek](./media/troubleshoot-conditional-access/image3.png)

1. Zodra de aanmeldingsgebeurtenis die overeenkomt met de aanmeldingsfout van de gebruiker is gevonden, selecteert u het tabblad **Voorwaardelijke toegang.** Op het tabblad Voorwaardelijke toegang wordt het specifieke beleid of beleid weergegeven dat heeft geleid tot de onderbreking van de aanmelding.
   1. Informatie op het tabblad **Probleemoplossing en ondersteuning** kan een duidelijke reden geven waarom een aanmelding is mislukt, zoals een apparaat dat niet aan de nalevingsvereisten voldoet.
   1. Als u het verder wilt onderzoeken, gaat u dieper in op de configuratie van het beleid door op de **beleidsnaam**te klikken. Als u op de **beleidsnaam** klikt, wordt de gebruikersinterface van de beleidsconfiguratie weergegeven voor het geselecteerde beleid voor controle en bewerking.
   1. De **clientgebruikers-** en **apparaatgegevens** die zijn gebruikt voor de beoordeling van het beleid voorwaardelijke toegang, zijn ook beschikbaar in de tabbladen **Basisgegevens**, **Locatie,** **Apparaatgegevens,** **Verificatiegegevens**en **Aanvullende details** van de aanmeldingsgebeurtenis.

   ![Tabblad Voorwaardelijke toegang aanmelden](./media/troubleshoot-conditional-access/image5.png)

Als de informatie in de gebeurtenis niet voldoende is om de aanmeldingsresultaten te begrijpen of het beleid aan te passen om de gewenste resultaten te krijgen, kan een ondersteuningsincident worden geopend. Navigeer naar het **tabblad Probleemoplossing en ondersteuning** van die aanmeldingsgebeurtenis en selecteer **Een nieuw ondersteuningsverzoek maken**.

![Het tabblad Probleemoplossing en ondersteuning van de aanmeldingsgebeurtenis](./media/troubleshoot-conditional-access/image6.png)

Geef bij het indienen van het incident de aanvraag-id en tijd en datum van de aanmeldingsgebeurtenis op in de details van het incident. Met deze informatie kan Microsoft-ondersteuning de gebeurtenis vinden waarover u zich zorgen maakt.

### <a name="conditional-access-error-codes"></a>Foutcodes voor voorwaardelijke toegang

| Aanmeldingsfoutcode | Fouttekenreeks |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined DeviceNotDomainJoined DeviceNotDomainJoined DeviceNot |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | Geblokkeerdevoorwaardelijke toegang |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Volgende stappen

- [Aanmeldactiviteitenrapporten in Azure Active Directory Portal](../reports-monitoring/concept-sign-ins.md)
- [Probleemoplossing voorwaardelijke toegang met het gereedschap Wat als](troubleshoot-conditional-access-what-if.md)
- Aanbevolen procedures voor [voorwaardelijke toegang in Azure Active Directory](best-practices.md)
