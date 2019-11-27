---
title: Kan geen toegang krijgen tot deze zakelijke toepassings fout met app proxy-app
description: Klik hier voor meer informatie over het oplossen van veelvoorkomende problemen met Azure AD-toepassingsproxy-toepassingen.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275472"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>'Geen toegang tot deze zakelijke toepassing' Fout bij het gebruik van een toepassing Application Proxy

Dit artikel helpt bij het oplossen van veelvoorkomende problemen met de fout 'deze zakelijke app kan niet worden geopend' in een toepassing met Azure AD-toepassingsproxy.

## <a name="overview"></a>Overzicht

Wanneer u deze fout ziet, moet u de statuscode vinden op de foutpagina weergegeven. Deze code wordt waarschijnlijk een van de volgende statuscodes:

- **Time-out van Gateway**: de service Application proxy kan de connector niet bereiken. Deze fout wijst meestal op een probleem met de connector-toewijzing, connector zelf, of de netwerkondersteuning regels om de connector.
- **Ongeldige gateway**: de connector kan de back-end-toepassing niet bereiken. Deze fout kan duiden op een onjuiste configuratie van de toepassing.
- **Verboden**: de gebruiker is niet gemachtigd om toegang te krijgen tot de toepassing. Deze fout kan optreden als de gebruiker is niet toegewezen aan de toepassing in Azure Active Directory of als op de back-end van de gebruiker heeft geen machtiging voor toegang tot de toepassing.

Voor de code, zoekt u naar de tekst in de linkerbenedenhoek van het foutbericht voor het veld ' Status '. Ook eventuele extra tips lezen aan de onderkant van de pagina zoeken.

![Voor beeld: time-out van Gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Zie voor meer informatie over het oplossen van de hoofdoorzaak van deze fouten en meer informatie over de voorgestelde oplossingen, de bijbehorende sectie hieronder.

## <a name="gateway-timeout-errors"></a>Time-outfouten van gateway

Er is een time-out van gateway treedt op wanneer de service probeert te bereiken van de connector en kan niet binnen de time-out voor venster. Deze fout wordt meestal veroorzaakt door een toepassing die is toegewezen aan een Connectorgroep met geen werkende connectors of bepaalde poorten die nodig zijn door de Connector niet zijn geopend.

## <a name="bad-gateway-errors"></a>Ongeldige Gateway-fouten

Een ongeldige gateway-fout geeft aan dat de connector kan niet worden bereikt van de back endtoepassing. Zorg ervoor dat u de juiste toepassing hebt gepubliceerd. Veelvoorkomende fouten die ervoor zorgen deze fout dat zijn:

- Een typefout of een fout in de interne URL
- De hoofdmap van de toepassing publiceren niet. Bijvoorbeeld: Publiceer <http://expenses/reimbursement>, maar probeer toegang te krijgen tot <http://expenses>
- Problemen met de configuratie van de Kerberos-beperkte delegatie (KCD)
- Problemen met de back endtoepassing

## <a name="forbidden-errors"></a>Niet-toegestane fouten

Als u een niet-toegestane fout ziet, heeft de gebruiker niet is toegewezen aan de toepassing. Deze fout kan zijn in Azure Active Directory of op de back endtoepassing.

Zie de [configuratie documentatie](application-proxy-add-on-premises-application.md#test-the-application)voor meer informatie over het toewijzen van gebruikers aan de toepassing in Azure.

Als u hebt geverifieerd dat de gebruiker is toegewezen aan de toepassing in Azure, controleert u de Gebruikersconfiguratie van de in de back endtoepassing. Als u Kerberos-beperkte overdracht/geïntegreerde Windows-verificatie gebruikt, raadpleegt u de pagina KCD problemen oplossen voor richtlijnen.

## <a name="check-the-applications-internal-url"></a>Controleer of de interne URL van de toepassing

Als eerste snelle stap controleert en corrigeert u de interne URL door de toepassing te openen via **zakelijke toepassingen**en vervolgens het menu **toepassings proxy** te selecteren. Controleer of de interne URL wordt gebruikt in uw on-premises netwerk om toegang te krijgen tot de toepassing.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Controleer dat de toepassing is toegewezen aan een werkende Connectorgroep

Om te controleren of de toepassing wordt toegewezen aan een werkende Connectorgroep:

1. Open de toepassing in de portal door naar **Azure Active Directory**te gaan, op **bedrijfs toepassingen**en vervolgens op **alle toepassingen** te klikken. Open de toepassing en selecteer vervolgens **toepassings proxy** in het menu links.
1. Bekijk de Connectorgroep-veld. Als er geen actieve connectors in de groep, ziet u een waarschuwing. Als er geen waarschuwingen worden weer gegeven, gaat u naar om te controleren of alle vereiste poorten zijn toegestaan.
1. Als de verkeerde Connectorgroep wordt weergegeven, gebruikt u de vervolgkeuzelijst selecteert u de juiste groep en controleer of dat u niet meer waarschuwingen ziet. Als de beoogde Connectorgroep wordt weergegeven, klikt u op de waarschuwing op de pagina te openen met de Connector-beheer.
1. Hier zijn er een aantal manieren om in te zoomen verder:

   - Een actieve Connector verplaatsen naar de groep: hebt u een actieve Connector die moet behoren tot deze groep en peeren aan de doeltoepassing back-end heeft, kunt u de Connector verplaatsen naar de toegewezen groep. Om dit te doen, klikt u op de Connector. Gebruik de vervolgkeuzelijst de juiste groep selecteren en klik op opslaan in het veld 'Connectorgroep'.
   - Een nieuwe connector voor die groep downloaden: vanaf deze pagina kunt u de koppeling ophalen om [een nieuwe connector te downloaden](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). De Connector installeren op een computer met rechtstreekse verbinding naar de back endtoepassing. Normaal gesp roken wordt de connector geïnstalleerd op dezelfde server als de toepassing. Het downloaden van de koppeling voor de Certificaatconnector gebruiken voor het downloaden van een connector op de doelcomputer. Vervolgens klikt u op de Connector en de 'Connector groeperen' vervolgkeuzelijst gebruiken om ervoor te zorgen dat hoort bij de juiste groep.
   - Een niet-actieve Connector onderzoeken: als u een connector als inactief ziet, is het kan niet worden bereikt van de service. Deze fout wordt meestal veroorzaakt door sommige vereiste poorten worden geblokkeerd. Als u dit probleem wilt oplossen, gaat u naar om te controleren of alle vereiste poorten zijn toegestaan.

Nadat u test deze stappen om te controleren of de toepassing is toegewezen aan een groep met Connectors, werkt de toepassing opnieuw uit. Als dit nog steeds niet werkt, gaat u verder met de volgende sectie.

## <a name="check-all-required-ports-are-open"></a>Controleer of alle vereiste poorten zijn geopend

Om te controleren of alle vereiste poorten zijn geopend, Zie de documentatie over het openen van poorten. Als de vereiste poorten geopend zijn, verplaatsen naar de volgende sectie.

## <a name="check-for-other-connector-errors"></a>Controleer ook op andere Connector-fouten

Als geen van de bovenstaande het probleem wordt opgelost, wordt de volgende stap is om te zoeken naar problemen of fouten met de Connector zelf. In het [document problemen oplossen](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)vindt u enkele veelvoorkomende fouten.

U kunt ook kijken rechtstreeks de logboeken van de Connector voor het identificeren van eventuele fouten. Veel van de foutberichten delen specifieke aanbevelingen voor oplossingen. Raadpleeg de [documentatie van connectors](application-proxy-connectors.md#under-the-hood)als u de logboeken wilt weer geven.

## <a name="additional-resolutions"></a>Aanvullende oplossingen

Als de bovenstaande het probleem opgelost niet hebt, zijn er enkele verschillende mogelijke oorzaken. Bepalen wat het probleem:

Als uw toepassing is geconfigureerd voor het gebruik van geïntegreerde Windows-verificatie (IWA), moet u de toepassing zonder eenmalige aanmelding testen. Als dat niet het geval is, verplaatsen naar de volgende alinea. Als u de toepassing zonder eenmalige aanmelding wilt controleren, opent u uw toepassing via **bedrijfs toepassingen** en gaat u naar het menu voor **eenmalige aanmelding** . Wijzig de vervolgkeuzelijst van 'Geïntegreerde Windows-verificatie' aan "Azure AD eenmalige aanmelding uitgeschakeld".

Nu open een browser en probeert te krijgen tot de toepassing opnieuw uit. U moet worden gevraagd om verificatie en toegang tot de toepassing. Als u geverifieerd bent, wordt het probleem is met de configuratie van de Kerberos-beperkte delegatie (KCD) waarmee de eenmalige aanmelding. Zie voor meer informatie de pagina KCD oplossen.

Als u doorgaat naar de fout te zien, gaat u naar de machine waarop de Connector is geïnstalleerd, open een browser en probeert te krijgen tot de interne URL die wordt gebruikt voor de toepassing. De Connector fungeert als een andere client vanaf dezelfde computer. Als u de toepassing niet kan bereiken, moet u onderzoeken waarom die machine kan bereiken van de toepassing of een connector gebruiken op een server die toegang heeft tot de toepassing is.

Als u de toepassing vanaf deze computer wilt zoeken naar problemen of fouten met de Connector zelf bereiken kunt. In het [document problemen oplossen](application-proxy-troubleshoot.md#connector-errors)vindt u enkele veelvoorkomende fouten. U kunt ook kijken rechtstreeks de logboeken van de Connector voor het identificeren van eventuele fouten. Veel van onze foutberichten mogelijk meer specifieke aanbevelingen voor oplossingen delen. Zie [onze connectors-documentatie](application-proxy-connectors.md#under-the-hood)voor meer informatie over het weer geven van de logboeken.

## <a name="next-steps"></a>Volgende stappen

[Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)
