---
title: Veelgestelde vragen over azure-app-configuratie
description: Veelgestelde vragen over Azure App-configuratie
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348793"
---
# <a name="azure-app-configuration-faq"></a>Veelgestelde vragen over azure-app-configuratie

In dit artikel worden veelgestelde vragen over Azure App Configuration beantwoord.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Hoe verschilt app-configuratie van Azure Key Vault?

App-configuratie helpt ontwikkelaars bij het beheren van toepassingsinstellingen en het beheren van de beschikbaarheid van functies. Het doel is om veel van de taken van het werken met complexe configuratiegegevens te vereenvoudigen.

Ondersteuning voor app-configuratie:

- Hiërarchische naamruimten
- Labeling
- Uitgebreide query's
- Batch ophalen
- Gespecialiseerde beheeractiviteiten
- Een gebruikersinterface voor functiesbeheer

App-configuratie is een aanvulling op Key Vault en de twee moeten naast elkaar worden gebruikt in de meeste implementaties van toepassingen.

## <a name="should-i-store-secrets-in-app-configuration"></a>Moet ik geheimen opslaan in app-configuratie?

Hoewel app-configuratie geharde beveiliging biedt, is Key Vault nog steeds de beste plek voor het opslaan van toepassingsgeheimen. Key Vault biedt versleuteling op hardwareniveau, gedetailleerd toegangsbeleid en beheerbewerkingen zoals certificaatrotatie.

U app-configuratiewaarden maken die verwijzen naar geheimen die zijn opgeslagen in Key Vault. Zie [Verwijzingen naar sleutelkluizen gebruiken in een ASP.NET Core-app](./use-key-vault-references-dotnet-core.md)voor meer informatie.

## <a name="does-app-configuration-encrypt-my-data"></a>Versleutelt app-configuratie mijn gegevens?

Ja. App-configuratie versleutelt alle belangrijke waarden die het bevat en versleutelt netwerkcommunicatie. Belangrijke namen en labels worden gebruikt als indexen voor het ophalen van configuratiegegevens en worden niet versleuteld.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Hoe verschilt app-configuratie van azure app-service-instellingen?

Met Azure App Service u app-instellingen definiëren voor elk app-service-exemplaar. Deze instellingen worden doorgegeven als omgevingsvariabelen aan de toepassingscode. U een instelling desgevraagd koppelen aan een specifieke implementatiesleuf. Zie [App-instellingen configureren](/azure/app-service/configure-common#configure-app-settings)voor meer informatie .

Met Azure App Configuration u daarentegen instellingen definiëren die tussen meerdere apps kunnen worden gedeeld. Dit geldt ook voor apps die worden uitgevoerd in App Service, evenals andere platforms. Uw toepassingscode heeft toegang tot deze instellingen via de configuratieproviders voor .NET en Java, via de Azure SDK of rechtstreeks via REST API's.

U ook instellingen importeren en exporteren tussen App Service en App Configuration. Met deze mogelijkheid u snel een nieuwe app-configuratiewinkel instellen op basis van bestaande App-serviceinstellingen. U de configuratie ook delen met een bestaande app die is gebaseerd op de instellingen van App-service.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Zijn er beperkingen in de grootte van sleutels en waarden die zijn opgeslagen in app-configuratie?

Er is een limiet van 10 KB voor één belangrijk item.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hoe moet ik configuraties opslaan voor meerdere omgevingen (testen, fasering, productie, enzovoort)?

U bepaalt wie toegang heeft tot app-configuratie per winkel. Gebruik een apart archief voor elke omgeving waarvoor verschillende machtigingen vereist zijn. Deze aanpak zorgt voor de beste beveiliging isolatie.

Als u geen beveiligingsisolatie tussen omgevingen nodig hebt, u labels gebruiken om onderscheid te maken tussen configuratiewaarden. [Gebruik labels om verschillende configuraties voor verschillende omgevingen in te schakelen,](./howto-labels-aspnet-core.md) biedt een compleet voorbeeld.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Wat zijn de aanbevolen manieren om app-configuratie te gebruiken?

Bekijk [best practices](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Hoeveel kost app-configuratie?

Er zijn twee prijsniveaus:

- Gratis laag
- Standaardlaag.

Als u een winkel hebt gemaakt vóór de introductie van de standaardlaag, wordt deze automatisch verplaatst naar de laag Gratis op basis van algemene beschikbaarheid. U ervoor kiezen om te upgraden naar de standaardlaag of op de gratis laag te blijven.

U een winkel niet downgraden van de standaardlaag naar de laag Gratis. U een nieuw archief maken in de laag Gratis en vervolgens configuratiegegevens importeren in dat archief.

## <a name="which-app-configuration-tier-should-i-use"></a>Welke app-configuratielaag moet ik gebruiken?

Beide app-configuratielagen bieden kernfunctionaliteit, waaronder config-instellingen, functievlaggen, Key Vault-referenties, basisbeheerbewerkingen, statistieken en logboeken.

De volgende overwegingen zijn overwegingen voor het kiezen van een laag.

- **Resources per abonnement**: een resource bestaat uit één configuratiearchief. Elk abonnement is beperkt tot één configuratiearchief in de gratis laag. Abonnementen kunnen een onbeperkt aantal configuratiewinkels in de standaardlaag hebben.
- **Opslag per resource:** in de gratis laag is elk configuratiearchief beperkt tot 10 MB opslagruimte. In de standaardlaag kan elk configuratiearchief maximaal 1 GB opslagruimte gebruiken.
- **Sleutelgeschiedenis:** App-configuratie slaat een geschiedenis op van alle wijzigingen in sleutels. In de gratis laag wordt deze geschiedenis zeven dagen bewaard. In de standaardlaag wordt deze geschiedenis gedurende 30 dagen opgeslagen.
- **Aanvragen per dag**: Gratis lagen winkels zijn beperkt tot 1.000 aanvragen per dag. Zodra een winkel 1.000 aanvragen heeft bereikt, wordt http-statuscode 429 voor alle aanvragen teruggekeerd tot utc om middernacht.

    Voor standaardlagenwinkels zijn de eerste 200.000 aanvragen per dag inbegrepen in de dagelijkse kosten. Extra aanvragen worden gefactureerd als overschrijding.

- **Servicelevel overeenkomst**: De standaardlaag heeft een SLA van 99,9% beschikbaarheid. De gratis laag heeft geen SLA.
- **Beveiligingsfuncties:** Beide lagen bevatten basisbeveiligingsfunctionaliteit, waaronder versleuteling met door Microsoft beheerde sleutels, verificatie via HMAC of Azure Active Directory, RBAC-ondersteuning en beheerde identiteit. De standaardlaag biedt geavanceerdere beveiligingsfunctionaliteit, waaronder Private Link-ondersteuning en versleuteling met door de klant beheerde sleutels.
- **Kosten:** Standaardlagenwinkels hebben een dagelijkse gebruikskosten. Er is ook een overschrijding smeerkosten voor aanvragen voorbij de dagelijkse toewijzing. Er zijn geen kosten verbonden aan het gebruik van een gratis winkel.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Kan ik een winkel upgraden van de laag Gratis naar de standaardlaag? Kan ik een winkel downgraden van de standaardlaag naar de gratis laag?

U op elk gewenst moment upgraden van de laag Gratis naar de standaardlaag.

U een winkel niet downgraden van de standaardlaag naar de laag Gratis. U een nieuw archief maken in de laag Gratis en vervolgens [configuratiegegevens importeren in dat archief.](howto-import-export-data.md)

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Zijn er beperkingen op het aantal aanvragen voor app-configuratie?

Configuratiewinkels in de gratis laag zijn beperkt tot 1.000 aanvragen per dag. Configuratieopslag in de standaardlaag kunnen tijdelijke beperking ervaren wanneer het aanvraagpercentage meer dan 20.000 aanvragen per uur bedraagt.

Wanneer een winkel zijn limiet bereikt, retourneert deze HTTP-statuscode 429 voor alle aanvragen die worden gedaan totdat de periode is verstreken. De `retry-after-ms` koptekst in het antwoord geeft een voorgestelde wachttijd (in milliseconden) voordat u het verzoek opnieuw probeert.

Als uw toepassing regelmatig HTTP-statuscode 429-reacties ondervindt, u overwegen deze opnieuw te ontwerpen om het aantal aanvragen te verminderen. Zie [Aanvragen voor app-configuratie verminderen voor](./howto-best-practices.md#reduce-requests-made-to-app-configuration) meer informatie

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Mijn aanvraag ontvangt HTTP-statuscode 429 reacties. Hoe komt dat?

Onder deze omstandigheden ontvangt u een HTTP-statuscode 429:

* Overschrijding van de dagelijkse aanvraaglimiet voor een winkel in de rij Gratis.
* Tijdelijke beperking vanwege een hoog aanvraagpercentage voor een winkel in de standaardlaag.
* Overmatig bandbreedtegebruik.
* Een sleutel proberen te maken of wijzigen wanneer de opslagofferte wordt overschreden.

Controleer de instantie van de 429 reactie om de specifieke reden waarom het verzoek is mislukt.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hoe kan ik aankondigingen ontvangen over nieuwe releases en andere informatie met betrekking tot app-configuratie?

Abonneer u op onze [GitHub aankondigingen repo](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hoe kan ik een probleem melden of een suggestie geven?

U ons rechtstreeks bereiken op [GitHub.](https://github.com/Azure/AppConfiguration/issues)

## <a name="next-steps"></a>Volgende stappen

* [Over Azure App Configuration](./overview.md)
