---
title: Azure Relay - Migreren naar beheerde toegangshandtekeningautorisatie
description: Beschrijft hoe u Azure Relay-toepassingen migreert van Azure Active Directory Access Control Service naar de beheerautorisatie voor gedeelde toegang.
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 59b9e734526c56016e2ddf59c2afb5b8f7b4ad09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514898"
---
# <a name="azure-relay---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure Relay - Migreren van Azure Active Directory Access Control Service naar beheer met gedeelde toegang

Azure Relay-toepassingen hadden in het verleden de keuze om twee verschillende autorisatiemodellen te gebruiken: het [SAS-tokenmodel (Shared Access Signature)](../service-bus-messaging/service-bus-sas.md) dat rechtstreeks door de Relay-service wordt geleverd en een federatief model waarbij het beheer van autorisatieregels binnen wordt beheerd door de [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS) en tokens die van ACS zijn verkregen, worden doorgegeven aan Relay voor het toestaan van toegang tot de gewenste functies.

Het ACS-autorisatiemodel is al lang vervangen door [SAS-autorisatie](../service-bus-messaging/service-bus-authentication-and-authorization.md) als voorkeursmodel en alle documentatie, richtlijnen en monsters maken vandaag de dag uitsluitend gebruik van SAS. Bovendien is het niet meer mogelijk om nieuwe relaynaamruimten te maken die zijn gekoppeld aan ACS.

SAS heeft het voordeel dat het niet onmiddellijk afhankelijk is van een andere dienst, maar rechtstreeks van een klant kan worden gebruikt zonder tussenpersonen door de klant toegang te geven tot de SAS-regelnaam en regelsleutel. SAS kan ook eenvoudig worden geïntegreerd met een aanpak waarbij een klant eerst een autorisatiecontrole moet doorstaan met een andere service en vervolgens een token krijgt uitgegeven. Deze laatste benadering is vergelijkbaar met het ACS-gebruikspatroon, maar maakt het mogelijk om toegangstokens uit te geven op basis van toepassingsspecifieke voorwaarden die moeilijk uit te drukken zijn in ACS.

Voor alle bestaande toepassingen die afhankelijk zijn van ACS, dringen we er bij klanten op aan om hun applicaties te migreren om in plaats daarvan op SAS te vertrouwen.

## <a name="migration-scenarios"></a> Migratiescenario's

ACS en Relay zijn geïntegreerd door de gedeelde kennis van een *ondertekeningssleutel.* De ondertekeningssleutel wordt gebruikt door een ACS-naamruimte om autorisatietokens te ondertekenen en wordt door Azure Relay gebruikt om te controleren of het token is uitgegeven door de gekoppelde ACS-naamruimte. De ACS-naamruimte bevat serviceidentiteiten en autorisatieregels. De autorisatieregels bepalen welke serviceidentiteit of welk token dat wordt uitgegeven door een externe identiteitsprovider welk type toegang tot een deel van de relaynaamruimtegrafiek krijgt, in de vorm van een langst-voorvoegselovereenkomst.

Een ACS-regel kan bijvoorbeeld de claim **Verzenden** `/` op het padvoorvoegsel voor een service-identiteit verlenen, wat betekent dat een token dat door ACS is uitgegeven op basis van die regel de clientrechten verleent om naar alle entiteiten in de naamruimte te verzenden. Als het padvoorvoegsel is, is `/abc`de identiteit `abc` beperkt tot verzenden naar entiteiten die onder dat voorvoegsel zijn genoemd of georganiseerd. Aangenomen wordt dat lezers van deze migratiebegeleiding al bekend zijn met deze concepten.

De migratiescenario's vallen in drie brede categorieën:

1.  **Ongewijzigde standaardinstellingen**. Sommige klanten gebruiken een [Object SharedSecretTokenProvider,](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) waarbij de automatisch gegenereerde **eigenaarservice-identiteit** en de geheime sleutel voor de ACS-naamruimte worden doorgegeven, gekoppeld aan de naamruimte Relay en voegen geen nieuwe regels toe.

2.  **Aangepaste serviceidentiteiten met eenvoudige regels.** Sommige klanten voegen nieuwe serviceidentiteiten toe en verlenen elke nieuwe serviceidentiteit **Verzenden,** **Luisteren**en **Beheren** machtigingen voor één specifieke entiteit.

3.  **Aangepaste serviceidentiteiten met complexe regels.** Zeer weinig klanten hebben complexe regelsets waarin extern uitgegeven tokens worden toegewezen aan rechten op Relay, of waar een enkele service-identiteit gedifferentieerde rechten wordt toegewezen op verschillende naamruimtepaden via meerdere regels.

Voor hulp bij het migreren van complexe regelsets u contact opnemen met [Azure-ondersteuning.](https://azure.microsoft.com/support/options/) De andere twee scenario's maken eenvoudige migratie mogelijk.

### <a name="unchanged-defaults"></a>Ongewijzigde standaardwaarden

Als uw toepassing ACS-standaardinstellingen niet heeft gewijzigd, u alle [SharedSecretTokenProvider-gebruik](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) vervangen door een [Object SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) en de vooraf geconfigureerde naamruimte **RootManageSharedAccessKey** gebruiken in plaats van het ACS-eigenaaraccount. **owner** Houd er rekening mee dat zelfs met het **ACS-eigenaaraccount** deze configuratie over het algemeen niet is aanbevolen (en nog steeds is), omdat deze account/regel volledige beheerbevoegdheid biedt over de naamruimte, inclusief toestemming om entiteiten te verwijderen.

### <a name="simple-rules"></a>Eenvoudige regels

Als de toepassing aangepaste serviceidentiteiten met eenvoudige regels gebruikt, is de migratie eenvoudig in het geval dat een ACS-service-identiteit is gemaakt om toegangscontrole op een specifiek relais te bieden. Dit scenario is vaak het geval in SaaS-achtige oplossingen waarbij elk relais wordt gebruikt als een brug naar een tenantsite of bijkantoor en de service-identiteit wordt gemaakt voor die specifieke site. In dit geval kan de desbetreffende serviceidentiteit direct op het relay worden gemigreerd naar een regel voor gedeelde toegangshandtekening. De naam van de serviceidentiteit kan de naam van de SAS-regel worden en de service-identiteitssleutel kan de SAS-regelsleutel worden. De rechten van de SAS-regel worden vervolgens geconfigureerd gelijk aan de respectievelijk toepasselijke ACS-regel voor de entiteit.

U deze nieuwe en aanvullende configuratie van SAS op zijn plaats zetten op bestaande naamruimte die wordt gefedereerd met ACS, en de migratie uit de buurt van ACS wordt vervolgens uitgevoerd met [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) in plaats van [SharedSecretTokenProvider.](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) De naamruimte hoeft niet los te worden gekoppeld van ACS.

### <a name="complex-rules"></a>Complexe regels

SAS-regels zijn niet bedoeld als accounts, maar worden ondertekend sleutels gekoppeld aan rechten. Als zodanig vereisen scenario's waarin de toepassing veel serviceidentiteiten creëert en hen toegangsrechten verleent voor verschillende entiteiten of de hele naamruimte nog steeds een tussenpersoon die door een token uitgeeft. U begeleiding krijgen voor een dergelijke tussenpersoon door [contact op te nemen met ondersteuning.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Azure Relay-verificatie:

* [Azure Relay-verificatie en -autorisatie](relay-authentication-and-authorization.md)
* [Servicebusverificatie met gedeelde toegangshandtekeningen](../service-bus-messaging/service-bus-sas.md)


