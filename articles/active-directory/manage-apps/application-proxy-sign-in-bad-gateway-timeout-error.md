---
title: Heeft geen toegang tot deze fout in de bedrijfstoepassing met de app Proxy-app
description: Algemene toegangsproblemen met Azure AD-toepassingsproxytoepassingen oplossen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d61f4b4bce9b8287dc13237f071684ea5d135fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275472"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Fout "Kan geen toegang krijgen tot deze bedrijfstoepassing" fout bij het gebruik van een application proxy-toepassing

Met dit artikel u veelvoorkomende problemen oplossen voor de fout 'Deze bedrijfsapp kan niet worden geopend' op een Azure AD Application Proxy-toepassing.

## <a name="overview"></a>Overzicht

Wanneer u deze fout ziet, zoekt u de statuscode op de foutpagina. Die code is waarschijnlijk een van de volgende statuscodes:

- **Gateway time-out:** de application proxy-service kan de connector niet bereiken. Deze fout duidt meestal op een probleem met de verbindingstoewijzing, connector zelf of de netwerkregels rond de connector.
- **Slechte gateway:** de connector kan de backend-toepassing niet bereiken. Deze fout kan duiden op een verkeerde configuratie van de toepassing.
- **Verboden**: De gebruiker is niet bevoegd om toegang te krijgen tot de applicatie. Deze fout kan optreden wanneer de gebruiker niet is toegewezen aan de toepassing in Azure Active Directory, of als de gebruiker op de backend geen toestemming heeft om toegang te krijgen tot de toepassing.

Als u de code wilt vinden, bekijkt u de tekst linksonder in het foutbericht voor het veld Statuscode. Zoek ook naar eventuele extra tips aan de onderkant van de pagina.

![Voorbeeld: fout time-out gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Zie het overeenkomstige gedeelte hieronder voor meer informatie over het oplossen van de hoofdoorzaak van deze fouten en meer details over voorgestelde oplossingen.

## <a name="gateway-timeout-errors"></a>Time-outfouten voor gateway's

Een time-out van de gateway vindt plaats wanneer de service de connector probeert te bereiken en niet binnen het time-outvenster kan worden uitgevoerd. Deze fout wordt meestal veroorzaakt door een toepassing die is toegewezen aan een connectorgroep zonder werkende connectoren of sommige poorten die door de connector zijn vereist, zijn niet geopend.

## <a name="bad-gateway-errors"></a>Foute gatewayfouten

Een fout in de gateway geeft aan dat de connector de backend-toepassing niet kan bereiken. zorg ervoor dat u de juiste toepassing hebt gepubliceerd. Veelvoorkomende fouten die deze fout veroorzaken zijn:

- Een typefout of fout in de interne URL
- De hoofdmap van de toepassing niet publiceren. Bijvoorbeeld publiceren, <http://expenses/reimbursement> maar proberen toegang te krijgen tot<http://expenses>
- Problemen met de Configuratie van de Kerberos Constrained Delegation (KCD)
- Problemen met de backend-toepassing

## <a name="forbidden-errors"></a>Verboden fouten

Als u een verboden fout ziet, is de gebruiker niet toegewezen aan de toepassing. Deze fout kan zich bevinden in Azure Active Directory of in de backend-toepassing.

Zie de [configuratiedocumentatie](application-proxy-add-on-premises-application.md#test-the-application)voor meer informatie over het toewijzen van gebruikers aan de toepassing in Azure.

Als u bevestigt dat de gebruiker is toegewezen aan de toepassing in Azure, controleert u de gebruikersconfiguratie in de backend-toepassing. Zie de pagina Problemen met kcd-problemen voor richtlijnen als u Kerberos Constrained Delegation/Integrated Windows Authentication gebruikt.

## <a name="check-the-applications-internal-url"></a>De interne URL van de toepassing controleren

Als eerste snelle stap controleert en repareert u de interne URL door de toepassing te openen via **Enterprise-toepassingen**en vervolgens het menu **Toepassingsproxy te** selecteren. Controleer of de interne URL de URL is die wordt gebruikt vanuit uw on-premises netwerk om toegang te krijgen tot de toepassing.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Controleren of de toepassing is toegewezen aan een werkgroep

Ga als volgt te werk om te controleren of de toepassing is toegewezen aan een werkgroepgroep:

1. Open de toepassing in de portal door naar **Azure Active Directory**te gaan , te klikken op **Enterprise-toepassingen**en vervolgens **Alle toepassingen.** Open de toepassing en selecteer **Vervolgens Toepassingsproxy** in het linkermenu.
1. Kijk naar het veld Connectorgroep. Als er geen actieve connectors in de groep zijn, ziet u een waarschuwing. Als u geen waarschuwingen ziet, gaat u verder om te controleren of alle vereiste poorten zijn toegestaan.
1. Als de verkeerde verbindingsgroep wordt weergegeven, gebruikt u de vervolgkeuzelijst om de juiste groep te selecteren en bevestigt u dat u geen waarschuwingen meer ziet. Als de beoogde connectorgroep wordt weergegeven, klikt u op het waarschuwingsbericht om de pagina met Connectorbeheer te openen.
1. Vanaf hier zijn er een paar manieren om verder te boren:

   - Een actieve connector naar de groep verplaatsen: als u een actieve connector hebt die tot deze groep moet behoren en zichtlijn heeft naar de doelbackendtoepassing, u de connector naar de toegewezen groep verplaatsen. Klik hiervoor op de connector. Gebruik in het veld 'Verbindingsgroep' de vervolgkeuzelijst om de juiste groep te selecteren en klik op Opslaan.
   - Download een nieuwe connector voor die groep: op deze pagina u de koppeling downloaden om een nieuwe connector te [downloaden.](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download) Installeer de connector op een machine met directe zichtlijn naar de backend-toepassing. Normaal gesproken is de connector geïnstalleerd op dezelfde server als de toepassing. Gebruik de koppeling Connector downloaden om een connector op de doelmachine te downloaden. Klik vervolgens op de connector en gebruik de vervolgkeuzelijst 'Connectorgroep' om te zien of deze tot de juiste groep behoort.
   - Een inactieve connector onderzoeken: als een connector als inactief wordt weergegeven, kan deze de service niet bereiken. Deze fout is meestal te wijten aan een aantal vereiste poorten worden geblokkeerd. Als u dit probleem wilt oplossen, gaat u verder om te controleren of alle vereiste poorten zijn toegestaan.

Nadat u deze stappen hebt gebruikt om ervoor te zorgen dat de toepassing is toegewezen aan een groep met werkende connectors, test u de toepassing opnieuw. Als het nog steeds niet werkt, ga dan verder naar de volgende sectie.

## <a name="check-all-required-ports-are-open"></a>Controleer of alle vereiste poorten open zijn

Zie de documentatie over het openen van poorten om te controleren of alle vereiste poorten zijn geopend. Als alle vereiste poorten zijn geopend, gaat u naar de volgende sectie.

## <a name="check-for-other-connector-errors"></a>Controleren op andere verbindingsfouten

Als geen van de bovenstaande problemen is opgelost, is de volgende stap om te zoeken naar problemen of fouten met de connector zelf. U enkele veelvoorkomende fouten zien in het [document Problemen oplossen](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors).

U ook rechtstreeks naar de Connector-logboeken kijken om eventuele fouten te identificeren. Veel van de foutberichten delen specifieke aanbevelingen voor oplossingen. Zie de [documentatie van](application-proxy-connectors.md#under-the-hood)connectors om de logboeken te bekijken.

## <a name="additional-resolutions"></a>Aanvullende resoluties

Als het bovenstaande het probleem niet heeft opgelost, zijn er een paar verschillende mogelijke oorzaken. Ga als u op de juiste plaats om het probleem te identificeren:

Als uw toepassing is geconfigureerd om Geïntegreerde Windows-verificatie (IWA) te gebruiken, test u de toepassing zonder eenmalige aanmelding. Zo niet, ga dan naar de volgende alinea. Als u de toepassing wilt controleren zonder één aanmelding, opent u uw toepassing via **Enterprise-toepassingen** en gaat u naar het menu **Eenmalig aanmelden.** Wijzig de vervolgkeuzelijst van 'Geïntegreerde Windows-verificatie' in 'Azure AD single sign-on disabled'.

Open nu een browser en probeer opnieuw toegang te krijgen tot de toepassing. U moet worden gevraagd om verificatie en krijgen in de toepassing. Als u zich verifiëren, is het probleem met de KCD-configuratie (Kerberos Constrained Delegation) waarmee de enkele aanmelding wordt ingeschakeld. Zie de pagina Problemen met kcd's voor meer informatie.

Als u de fout blijft zien, gaat u naar de machine waar de connector is geïnstalleerd, opent u een browser en probeert u de interne URL te bereiken die voor de toepassing wordt gebruikt. De Connector werkt als een andere client van dezelfde machine. Als u de toepassing niet bereiken, onderzoekt u waarom die machine de toepassing niet kan bereiken of gebruikt u een connector op een server die toegang heeft tot de toepassing.

Als u de toepassing van die machine bereiken, om te zoeken naar problemen of fouten met de Connector zelf. U enkele veelvoorkomende fouten zien in het [document Problemen oplossen](application-proxy-troubleshoot.md#connector-errors). U ook rechtstreeks naar de Connector-logboeken kijken om eventuele fouten te identificeren. Veel van onze foutmeldingen kunnen specifiekere aanbevelingen voor oplossingen delen. Zie onze documentatie voor connectors voor meer informatie over het bekijken van de [logboeken.](application-proxy-connectors.md#under-the-hood)

## <a name="next-steps"></a>Volgende stappen

[Informatie over Azure AD-toepassingsproxyconnectors](application-proxy-connectors.md)
