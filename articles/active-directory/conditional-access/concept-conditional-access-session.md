---
title: Sessiebesturingselementen in beleid voor voorwaardelijke toegang - Azure Active Directory
description: Wat zijn sessiebesturingselementen in een Azure AD Conditional Access-beleid
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671878"
---
# <a name="conditional-access-session"></a>Voorwaardelijke toegang: sessie

Binnen een beleid voor voorwaardelijke toegang kan een beheerder gebruik maken van sessiebesturingselementen om beperkte ervaringen binnen specifieke cloudtoepassingen mogelijk te maken.

![Voorwaardelijke toegangsbeleid met een subsidiebeheer dat meervoudige verificatie vereist](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Beperkingen voor toepassing afgedwongen

Organisaties kunnen dit besturingselement gebruiken om azure AD te verplichten apparaatgegevens door te geven aan de geselecteerde cloud-apps. Met de apparaatgegevens kunnen de cloud-apps weten of een verbinding wordt gestart vanaf een compatibel apparaat of een apparaat dat is verbonden met een domein. Dit besturingselement ondersteunt SharePoint Online en Exchange Online alleen als geselecteerde cloud-apps. Wanneer de cloud-app is geselecteerd, gebruikt hij de apparaatgegevens om gebruikers, afhankelijk van de apparaatstatus, een beperkte of volledige ervaring te bieden.

Zie de volgende artikelen voor meer informatie over het gebruik en de configuratie van door apps afgedwongen beperkingen:

- [Beperkte toegang inschakelen met SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Beperkte toegang inschakelen met Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Beheer van voorwaardelijke toegang-toepassingen

Conditional Access App Control maakt gebruik van een reverse proxy-architectuur en is uniek geïntegreerd met Azure AD Conditional Access. Met Voorwaardelijke toegang voor Azure AD u toegangsbesturingselementen voor de apps van uw organisatie afdwingen op basis van bepaalde voorwaarden. De voorwaarden bepalen wie (gebruiker of groep gebruikers) en welke (welke cloud-apps) en waar (welke locaties en netwerken) een Conditional Access-beleid wordt toegepast. Nadat u de voorwaarden hebt bepaald, u gebruikers doorsturen naar [Microsoft Cloud App Security,](/cloud-app-security/what-is-cloud-app-security) waar u gegevens beveiligen met Conditional Access App Control door toegangs- en sessiebesturingselementen toe te passen.

Met Conditional Access App Control kunnen gebruikersapptoegang en -sessies in realtime worden gecontroleerd en beheerd op basis van toegangs- en sessiebeleid. Toegangs- en sessiebeleid worden gebruikt in de Cloud App Security-portal om filters verder te verfijnen en acties in te stellen die op een gebruiker moeten worden uitgevoerd. Met het toegangs- en sessiebeleid u het nieuwe beleid doen:

- Gegevensexfiltratie voorkomen: u het downloaden, knippen, kopiëren en afdrukken van gevoelige documenten op bijvoorbeeld onbeheerde apparaten blokkeren.
- Bescherm bij het downloaden: in plaats van het downloaden van gevoelige documenten te blokkeren, u vereisen dat documenten worden gelabeld en beschermd met Azure Information Protection. Deze actie zorgt ervoor dat het document wordt beveiligd en dat de toegang van de gebruiker wordt beperkt in een mogelijk riskante sessie.
- Upload van niet-gelabelde bestanden voorkomen: voordat een gevoelig bestand wordt geüpload, gedistribueerd en gebruikt door anderen, is het belangrijk om ervoor te zorgen dat het bestand het juiste label en de juiste bescherming heeft. U ervoor zorgen dat niet-gelabelde bestanden met gevoelige inhoud worden geblokkeerd voor het uploaden totdat de gebruiker de inhoud classificeerde.
- Gebruikerssessies controleren op naleving: riskante gebruikers worden gecontroleerd wanneer ze zich aanmelden bij apps en hun acties worden geregistreerd vanuit de sessie. U gebruikersgedrag onderzoeken en analyseren om te begrijpen waar en onder welke voorwaarden sessiebeleid in de toekomst moet worden toegepast.
- Toegang blokkeren: U de toegang voor specifieke apps en gebruikers gedetailleerd blokkeren, afhankelijk van verschillende risicofactoren. U ze bijvoorbeeld blokkeren als ze clientcertificaten gebruiken als een vorm van apparaatbeheer.
- Aangepaste activiteiten blokkeren: sommige apps hebben unieke scenario's die risico's met zich meebrengen, bijvoorbeeld het verzenden van berichten met gevoelige inhoud in apps zoals Microsoft Teams of Slack. In dit soort scenario's u berichten scannen op gevoelige inhoud en deze in realtime blokkeren.

Zie het artikel [Voorwaardelijke toegangsapp-besturingselement voor aanbevolen apps implementeren voor](/cloud-app-security/proxy-deployment-aad)meer informatie.

## <a name="sign-in-frequency-preview"></a>Aanmeldingsfrequentie (voorbeeld)

Aanmeldingsfrequentie definieert de periode voordat een gebruiker wordt gevraagd zich opnieuw aan te melden wanneer hij toegang probeert te krijgen tot een bron.

De inlogfrequentieinstelling werkt met apps die OAUTH2- of OIDC-protocollen hebben geïmplementeerd volgens de normen. De meeste Microsoft-native apps voor Windows, Mac en Mobile, waaronder de volgende webtoepassingen, voldoen aan de instelling.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- O365-beheerportal
- Exchange Online
- SharePoint en OneDrive
- Teams-webclient
- Dynamics CRM Online
- Azure Portal

Zie voor meer informatie het artikel [Verificatiesessiebeheer configureren met voorwaardelijke toegang](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Permanente browsersessie (voorbeeld)

Een permanente browsersessie stelt gebruikers in staat om aangemeld te blijven na het sluiten en heropenen van hun browservenster.

Zie voor meer informatie het artikel [Verificatiesessiebeheer configureren met voorwaardelijke toegang](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Volgende stappen

- [Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus Alleen rapporteren](concept-conditional-access-report-only.md)
