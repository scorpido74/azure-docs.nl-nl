---
title: Toegang autoriseren met een gedeelde toegangshandtekening in Azure Event Hubs
description: In dit artikel vindt u informatie over het toestaan van toegang tot Azure Event Hubs-bronnen met behulp van SAS (Shared Access Signatures).
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992794"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Toegang tot bronnen van gebeurtenishubs toestaan met behulp van gedeelde toegangshandtekeningen
Een gedeelde toegangshandtekening (SAS) biedt u een manier om beperkte toegang te verlenen tot bronnen in de naamruimte van uw Gebeurtenishubs. SAS bewaakt de toegang tot event hubs-bronnen op basis van autorisatieregels. Deze regels zijn geconfigureerd op een naamruimte of een entiteit (gebeurtenishub of onderwerp). Dit artikel geeft een overzicht van het SAS-model en beoordeelt de best practices van SAS.

## <a name="what-are-shared-access-signatures"></a>Wat zijn handtekeningen voor gedeelde toegang?
Een gedeelde toegangshandtekening (SAS) biedt gedelegeerde toegang tot bronnen van gebeurtenishubs op basis van autorisatieregels. Een autorisatieregel heeft een naam, is gekoppeld aan specifieke rechten en bevat een paar cryptografische sleutels. U gebruikt de naam en sleutel van de regel via de Clients Event Hubs of in uw eigen code om SAS-tokens te genereren. Een client kan het token vervolgens doorgeven aan Gebeurtenishubs om de autorisatie voor de gevraagde bewerking te bewijzen.

SAS is een claimgebaseerd autorisatiemechanisme met behulp van eenvoudige tokens. Met behulp van SAS worden sleutels nooit doorgegeven aan de draad. Sleutels worden gebruikt om cryptografisch tekeninformatie die later kan worden geverifieerd door de service. SAS kan worden gebruikt vergelijkbaar met een gebruikersnaam en wachtwoord schema waar de klant in het bezit is van een autorisatie regel naam en een overeenkomende sleutel. SAS kan worden gebruikt vergelijkbaar met een federatief beveiligingsmodel, waarbij de client een tijdelijk en ondertekend toegangstoken ontvangt van een beveiligingstokenservice zonder ooit in het bezit te komen van de ondertekeningssleutel.

> [!NOTE]
> Azure Event Hubs ondersteunt het autoriseren naar Gebeurtenishubs resources met Azure Active Directory (Azure AD). Het toestaan van gebruikers of toepassingen met OAuth 2.0-token die door Azure AD is geretourneerd, biedt superieure beveiliging en gebruiksgemak ten opzichte van sas (Shared Access Signatures). Met Azure AD is het niet nodig om de tokens in uw code op te slaan en potentiële beveiligingsproblemen te riskeren.
>
> Microsoft raadt aan om Azure AD waar mogelijk te gebruiken met uw Azure Event Hubs-toepassingen. Zie [Toegang tot Azure Event Hubs-bron autoriseren met Azure Active Directory](authorize-access-azure-active-directory.md)voor meer informatie.

> [!IMPORTANT]
> SAS-tokens (Shared Access Signatures) zijn essentieel om uw resources te beschermen. Terwijl SAS granulariteit biedt, geeft het klanten toegang tot uw Event Hubs-bronnen. Ze mogen niet openbaar worden gedeeld. Wanneer u het delen, indien nodig om redenen van probleemoplossing, overweegt u een beperkte versie van logboekbestanden te gebruiken of de SAS-tokens (indien aanwezig) uit de logboekbestanden te verwijderen en ervoor te zorgen dat de schermafbeeldingen ook de SAS-informatie niet bevatten.

## <a name="shared-access-authorization-policies"></a>Beleid voor toegangsautorisatie
Elke naamruimte voor gebeurtenishubs en elke gebeurtenishubsentiteit (een gebeurtenishub-instantie of een Kafka-onderwerp) heeft een beleid voor gedeelde toegangsautorisatie dat bestaat uit regels. Het beleid op naamruimteniveau is van toepassing op alle entiteiten in de naamruimte, ongeacht hun individuele beleidsconfiguratie.
Voor elke autorisatiebeleidsregel bepaalt u drie gegevens: naam, bereik en rechten. De naam is een unieke naam in dat bereik. Het toepassingsgebied is de URI van de resource in kwestie. Voor een naamruimte van gebeurtenishubs is het bereik de volledig gekwalificeerde `https://<yournamespace>.servicebus.windows.net/`domeinnaam (FQDN), zoals .

De rechten die door de beleidsregel worden geboden, kunnen een combinatie zijn van:
- **Verzenden** : geeft het recht om berichten naar de entiteit te sturen
- **Luisteren** – Geeft het recht om te luisteren of te ontvangen aan de entiteit
- **Beheren** : geeft het recht om de topologie van de naamruimte te beheren, inclusief het maken en verwijderen van entiteiten

> [!NOTE]
> Het recht **beheren** omvat de rechten **verzenden** **en luisteren.**

Een naamruimte- of entiteitsbeleid kan maximaal 12 regels voor gedeelde toegangsautorisatie bevatten, die ruimte bieden voor de drie sets regels, die elk betrekking hebben op de basisrechten en de combinatie van Verzenden en luisteren. Deze limiet onderstreept dat het SAS-beleidsarchief niet bedoeld is als gebruikers- of serviceaccountarchief. Als uw toepassing toegang moet verlenen tot Event Hubs-bronnen op basis van gebruikers- of serviceidentiteiten, moet deze een beveiligingstokenservice implementeren die SAS-tokens uitgeeft na een verificatie- en toegangscontrole.

Aan een autorisatieregel wordt een **primaire sleutel** en een **secundaire sleutel**toegewezen. Deze sleutels zijn cryptografisch sterke sleutels. Verlies ze niet of lek ze niet. Ze zijn altijd beschikbaar in de Azure-portal. U een van de gegenereerde sleutels gebruiken en u ze op elk gewenst moment regenereren. Als u een sleutel in het beleid regenereert of wijzigt, worden alle eerder uitgegeven tokens op basis van die sleutel direct ongeldig. Doorgaande verbindingen die op basis van dergelijke tokens zijn gemaakt, blijven echter werken totdat het token verloopt.

Wanneer u een naamruimte voor gebeurtenishubs maakt, wordt automatisch een beleidsregel met de naamruimte **rootbeheerGedeeldtoegangssleutel** gemaakt. Dit beleid heeft **beheermachtigingen** voor de volledige naamruimte. Het wordt aanbevolen dat u deze regel behandelt als een beheerd rootaccount en deze niet gebruikt in uw toepassing. U aanvullende beleidsregels maken op het tabblad **Configureren** voor de naamruimte in de portal, via PowerShell of Azure CLI.

## <a name="best-practices-when-using-sas"></a>Best practices bij gebruik van SAS
Wanneer u gedeelde toegangshandtekeningen in uw toepassingen gebruikt, moet u zich bewust zijn van twee potentiële risico's:

- Als een SAS is gelekt, kan deze worden gebruikt door iedereen die deze verkrijgt, wat mogelijk uw Event Hubs-bronnen in gevaar kan brengen.
- Als een SAS die aan een clienttoepassing wordt geleverd verloopt en de toepassing geen nieuwe SAS uit uw service kan ophalen, kan de functionaliteit van de toepassing worden belemmerd.

De volgende aanbevelingen voor het gebruik van handtekeningen voor gedeelde toegang kunnen helpen deze risico's te beperken:

- **Laat clients de SAS automatisch verlengen indien nodig:** Clients moeten de SAS ruim voor het verstrijken vernieuwen, zodat ze tijd hebben voor nieuwe pogingen als de service die de SAS levert niet beschikbaar is. Als uw SAS is bedoeld om te worden gebruikt voor een klein aantal onmiddellijke, kortstondige operaties die naar verwachting worden voltooid binnen de vervaldatum, dan kan het onnodig zijn als de SAS zal naar verwachting niet worden vernieuwd. Echter, als u een klant hebt die routinematig verzoeken via SAS doet, dan komt de mogelijkheid van expiratie in het spel. De belangrijkste overweging is om de noodzaak voor de SAS van korte duur te brengen (zoals eerder vermeld) met de noodzaak om ervoor te zorgen dat de klant vroeg om verlenging vroeg genoeg (om verstoring te voorkomen als gevolg van de SAS afloopt voorafgaand aan een succesvolle verlenging).
- **Wees voorzichtig met de SAS begintijd:** Als u de begintijd voor SAS instelt op **nu,** dan als gevolg van klok scheeftrekken (verschillen in huidige tijd volgens verschillende machines), storingen kunnen worden waargenomen met tussenpozen voor de eerste paar minuten. Stel in het verleden de starttijd in op ten minste 15 minuten. Of stel het helemaal niet in, waardoor het in alle gevallen onmiddellijk geldig is. Hetzelfde geldt over het algemeen ook voor de vervaldatum. Vergeet niet dat u waarnemer tot 15 minuten van de klok scheef in beide richtingen op elk verzoek. 
- **Wees specifiek met de te openen bron:** een best practice voor beveiliging is om de gebruiker de minimaal vereiste bevoegdheden te bieden. Als een gebruiker alleen leestoegang tot één entiteit nodig heeft, geeft u hem of haar leestoegang tot die ene entiteit en hebt hij geen toegang tot alle entiteiten. Het helpt ook de schade te verminderen als een SAS wordt aangetast omdat de SAS minder stroom in de handen van een aanvaller heeft.
- **Gebruik sas niet altijd:** soms wegen de risico's die verbonden zijn aan een bepaalde bewerking ten opzichte van uw Event Hubs op tegen de voordelen van SAS. Maak voor dergelijke bewerkingen een middle-tier service die naar uw Gebeurtenishubs wordt geschreven na validatie, verificatie en controle van de bedrijfsregel.
- **Gebruik altijd HTTP's:** Gebruik altijd Https om een SAS te maken of te distribueren. Als een SAS wordt doorgegeven via HTTP en onderschept, een aanvaller het uitvoeren van een man-in-the-middle attach is in staat om de SAS te lezen en vervolgens gebruiken net zoals de beoogde gebruiker zou kunnen hebben, potentieel compromitterende gevoelige gegevens of waardoor voor gegevens corruptie door de kwaadwillende gebruiker.

## <a name="conclusion"></a>Conclusie
Handtekeningen voor het delen van toegang zijn handig voor het verstrekken van beperkte machtigingen voor eventhubsbronnen aan uw klanten. Ze zijn essentieel onderdeel van het beveiligingsmodel voor elke toepassing die Azure Event Hubs gebruikt. Als u de aanbevolen procedures in dit artikel volgt, u SAS gebruiken om meer flexibiliteit te bieden voor toegang tot uw resources, zonder dat dit ten koste gaat van de beveiliging van uw toepassing.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende gerelateerde artikelen: 

- [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met Azure Active Directory](authenticate-application.md)
- [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot gebeurtenishubsbronnen](authenticate-managed-identity.md)
- [Aanvragen voor Azure Event Hubs verifiëren met behulp van gedeelde toegangshandtekeningen](authenticate-shared-access-signature.md)
- [Toegang tot gebeurtenishubsresources autoriseren met Azure Active Directory](authorize-access-azure-active-directory.md)


