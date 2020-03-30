---
title: Wat is rechtenbeheer? - Azure AD
description: Krijg een overzicht van azure Active Directory-rechtenbeheer en hoe u dit gebruiken om toegang tot groepen, toepassingen en SharePoint Online-sites voor interne en externe gebruikers te beheren.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3deb87fec8241ad6126314f3f6ce5fb9600ad1fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128549"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Wat is Azure AD-rechtenbeheer?

Azure Active Directory (Azure AD)-rechtenbeheer is een functie voor [identiteitsbeheer](identity-governance-overview.md) waarmee organisaties de levenscyclus van identiteit en toegang op schaal kunnen beheren door toegangsaanvraagwerkstromen, toegangstoewijzingen, beoordelingen en vervaldatum te automatiseren.

Werknemers in organisaties hebben toegang nodig tot verschillende groepen, toepassingen en sites om hun taak uit te voeren. Het beheren van deze toegang is een uitdaging, omdat de vereisten veranderen - nieuwe toepassingen worden toegevoegd of gebruikers hebben extra toegangsrechten nodig.  Dit scenario wordt ingewikkelder wanneer u samenwerkt met externe organisaties: u weet mogelijk niet wie in de andere organisatie toegang nodig heeft tot de resources van uw organisatie en ze weten niet welke toepassingen, groepen of sites uw organisatie gebruikt.

Azure AD-rechtenbeheer kan u helpen de toegang tot groepen, toepassingen en SharePoint Online-sites efficiënter te beheren voor interne gebruikers en ook voor gebruikers buiten uw organisatie die toegang tot deze bronnen nodig hebben.

## <a name="why-use-entitlement-management"></a>Waarom gebruik maken van beheer van rechten?

Enterprise-organisaties worden vaak geconfronteerd met uitdagingen bij het beheren van de toegang van werknemers tot bronnen zoals:

- Gebruikers weten misschien niet welke toegang ze moeten hebben, en zelfs als ze dat doen, kunnen ze moeite hebben met het vinden van de juiste personen om hun toegang goed te keuren
- Zodra gebruikers toegang tot een resource vinden en ontvangen, kunnen ze langer toegang houden dan nodig is voor zakelijke doeleinden

Deze problemen worden verergerd voor gebruikers die toegang nodig hebben van een andere organisatie, zoals externe gebruikers die afkomstig zijn van supply chain-organisaties of andere zakelijke partners. Bijvoorbeeld:

- Niemand mag niet alle specifieke personen in de mappen van andere organisaties kennen om hen uit te nodigen
- Zelfs als ze in staat waren om deze gebruikers uit te nodigen, kan niemand in die organisatie herinneren om alle toegang van de gebruiker consistent te beheren

Azure AD-rechtenbeheer kan helpen deze uitdagingen aan te pakken.  Voor meer informatie over hoe klanten Azure AD-rechtenbeheer hebben gebruikt, u de [Avanade-casestudy](https://aka.ms/AvanadeELMCase) en de [Centrica-casestudy](https://aka.ms/CentricaELMCase)lezen.  Deze video geeft een overzicht van het rechtenbeheer en de waarde ervan:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Wat kan ik doen met rechtbeheer?

Hier volgen enkele mogelijkheden voor het beheer van rechten:

- Delegeren aan niet-beheerders de mogelijkheid om toegangspakketten te maken. Deze toegangspakketten bevatten bronnen die gebruikers kunnen aanvragen en de beheerders van gedelegeerd toegangspakketten kunnen beleid definiëren met regels waarvoor gebruikers kunnen vragen, wie hun toegang moet goedkeuren en wanneer de toegang verloopt.
- Selecteer verbonden organisaties waarvan de gebruikers toegang kunnen aanvragen.  Wanneer een gebruiker die nog niet in uw directory toegang heeft en is goedgekeurd, wordt deze automatisch uitgenodigd in uw directory en toegang toegewezen.  Wanneer hun toegang verloopt, als ze geen andere toewijzingen voor toegangspakketten hebben, kan hun B2B-account in uw directory automatisch worden verwijderd.

U aan de slag met onze [zelfstudie om uw eerste toegangspakket te maken.](entitlement-management-access-package-first.md) U ook de [veelvoorkomende scenario's](entitlement-management-scenarios.md)lezen of video's bekijken, waaronder

- [Azure AD-rechtenbeheer implementeren in uw organisatie](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Uw gebruik van Azure AD-rechtenbeheer controleren en schalen](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Hoe te delegeren in het beheer van rechten](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Wat zijn toegangspakketten en welke bronnen kan ik ermee beheren?

Entitlement management introduceert in Azure AD het concept van een *toegangspakket.* Een toegangspakket is een bundel van alle bronnen met de toegang die een gebruiker nodig heeft om aan een project te werken of zijn taak uit te voeren. Toegangspakketten worden gebruikt om de toegang voor uw interne medewerkers en ook gebruikers buiten uw organisatie te regelen.

 Dit zijn de typen resources waartoe de gebruiker toegang beheren met het beheer van rechten:

- Lidmaatschap van Azure AD-beveiligingsgroepen
- Lidmaatschap van Office 365-groepen en -teams
- Toewijzing aan Azure AD-bedrijfstoepassingen, inclusief SaaS-toepassingen en aangepaste geïntegreerde toepassingen die federatie/enkel aanmelden en/of inrichten ondersteunen
- Lidmaatschap van SharePoint Online-sites

U ook de toegang tot andere bronnen beheren die afhankelijk zijn van Azure AD-beveiligingsgroepen of Office 365-groepen.  Bijvoorbeeld:

- U gebruikers licenties voor Microsoft Office 365 geven door een Azure [AD-beveiligingsgroep](../users-groups-roles/licensing-groups-assign.md) te gebruiken in een toegangspakket en groepslicenties voor die groep voor die groep te configureren
- U gebruikers toegang geven tot het beheren van Azure-resources door een Azure AD-beveiligingsgroep in een toegangspakket te gebruiken en een [Azure-roltoewijzing](../../role-based-access-control/role-assignments-portal.md) voor die groep te maken

## <a name="how-do-i-control-who-gets-access"></a>Hoe bepaal ik wie toegang krijgt?

Met een toegangspakket geeft een beheerder of gedelegeerd toegangspakketbeheerder een overzicht van de bronnen (groepen, apps en sites) en de rollen die gebruikers nodig hebben voor deze bronnen.

Toegangspakketten bevatten ook een of meer *beleidsregels.* Een beleid definieert de regels of vangrails voor toewijzing om toegang te krijgen tot pakket. Elk beleid kan worden gebruikt om ervoor te zorgen dat alleen de juiste gebruikers toegang kunnen vragen, dat er fiatteurs zijn voor hun verzoek en dat hun toegang tot deze bronnen beperkt is en vervalt als ze niet worden verlengd.

![Toegangspakket en -beleid](./media/entitlement-management-overview/elm-overview-access-package.png)

Binnen elk beleid definieert een beheerder of toegangspakketbeheerder

- Ofwel de reeds bestaande gebruikers (meestal werknemers of reeds uitgenodigde gasten), of de partnerorganisaties van externe gebruikers die in aanmerking komen om toegang te vragen
- Het goedkeuringsproces en de gebruikers die de toegang kunnen goedkeuren of weigeren
- De duur van de toegangstoewijzing van een gebruiker, eenmaal goedgekeurd, voordat de toewijzing verloopt

In het volgende diagram ziet u een voorbeeld van de verschillende elementen in het beheer van rechten. Het toont één catalogus met twee voorbeeldtoegangspakketten.

- **Toegangspakket 1** bevat één groep als resource. Toegang wordt gedefinieerd met een beleid waarmee een groep gebruikers in de map toegang kan aanvragen.
- **Toegangspakket 2** bevat een groep, een toepassing en een SharePoint Online-site als resources. Toegang wordt gedefinieerd met twee verschillende beleidsregels. Met het eerste beleid kan een groep gebruikers in de map toegang aanvragen. Met het tweede beleid kunnen gebruikers in een externe map toegang aanvragen.

![Overzicht van het beheer van rechten](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Wanneer moet ik toegangspakketten gebruiken?

Toegangspakketten vervangen geen andere mechanismen voor toegangstoewijzing.  Ze zijn het meest geschikt in situaties zoals:

- Werknemers hebben tijdsbeperkte toegang nodig voor een bepaalde taak.  U bijvoorbeeld groepslicenties en een dynamische groep gebruiken om ervoor te zorgen dat alle werknemers een Exchange Online-postvak hebben en vervolgens toegangspakketten gebruiken voor situaties waarin werknemers extra toegang nodig hebben, zoals het lezen van afdelingsbronnen uit een ander Departement.
- Toegang moet worden goedgekeurd door de manager van een werknemer of andere aangewezen personen.
- Afdelingen willen hun eigen toegangsbeleid voor hun resources beheren zonder tussenkomst van IT.  
- Twee of meer organisaties werken samen aan een project en als gevolg hiervan moeten meerdere gebruikers van één organisatie worden ingeschakeld via Azure AD B2B om toegang te krijgen tot de bronnen van een andere organisatie.

## <a name="how-do-i-delegate-access"></a>Hoe deleger ik toegang?

 Toegangspakketten worden gedefinieerd in containers die *catalogi*worden genoemd.  U één catalogus hebben voor al uw toegangspakketten, of u personen aanwijzen om hun eigen catalogi te maken en te bezitten. Een beheerder kan resources toevoegen aan elke catalogus, maar een niet-beheerder kan alleen aan een catalogus de bronnen toevoegen waarvan hij eigenaar is. Een cataloguseigenaar kan andere gebruikers toevoegen als mede-eigenaars van een catalogus of als toegangspakketbeheerders.  Deze scenario's worden verder beschreven in de [artikeldelegatie en rollen in Azure AD-rechtenbeheer](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Samenvatting van de terminologie

Om het beheer van rechten en de bijbehorende documentatie beter te begrijpen, u terugverwijzen naar de volgende lijst met termen.

| Termijn | Beschrijving |
| --- | --- |
| toegangspakket | Een bundel van resources die een team of project nodig heeft en wordt geregeld met beleid. Een toegangspakket is altijd opgenomen in een catalogus. U maakt een nieuw toegangspakket voor een scenario waarin gebruikers toegang moeten aanvragen.  |
| toegangsverzoek | Een verzoek om toegang te krijgen tot de bronnen in een toegangspakket. Een aanvraag gaat meestal door een goedkeuringswerkstroom.  Indien goedgekeurd, ontvangt de verzoekende gebruiker een toewijzing van toegangspakketten. |
| Toewijzing | Een toewijzing van een toegangspakket aan een gebruiker zorgt ervoor dat de gebruiker alle resourcerollen van dat toegangspakket heeft.  Toegangspakkettoewijzingen hebben doorgaans een tijdslimiet voordat ze verlopen. |
| Catalogus | Een container met gerelateerde resources en toegangspakketten.  Catalogi worden gebruikt voor overdracht, zodat niet-beheerders hun eigen toegangspakketten kunnen maken. Cataloguseigenaren kunnen bronnen die ze bezitten toevoegen aan een catalogus. |
| catalogusmaker | Een verzameling gebruikers die gemachtigd zijn om nieuwe catalogi te maken.  Wanneer een niet-beheerdersgebruiker die gemachtigd is om een catalogusmaker te zijn, een nieuwe catalogus maakt, wordt hij automatisch de eigenaar van die catalogus. |
| verbonden organisatie | Een externe Azure AD-map of -domein waarmee u een relatie hebt. De gebruikers van een verbonden organisatie kunnen in een beleid worden opgegeven als toegang aanvragen. |
| policy | Een set regels die de levenscyclus van de toegang definieert, zoals hoe gebruikers toegang krijgen, wie kan goedkeuren en hoe lang gebruikers toegang hebben via een toewijzing. Een beleid is gekoppeld aan een toegangspakket. Een toegangspakket kan bijvoorbeeld twee beleidsregels hebben: een voor werknemers om toegang te vragen en een tweede voor externe gebruikers om toegang te vragen. |
| resource | Een asset, zoals een Office-groep, een beveiligingsgroep, een toepassing of een SharePoint Online-site, met een rol waaraan een gebruiker machtigingen kan krijgen. |
| resourcemap | Een map met een of meer bronnen om te delen. |
| resourcerol | Een verzameling machtigingen die zijn gekoppeld aan en gedefinieerd door een resource. Een groep heeft twee rollen - lid en eigenaar. SharePoint-sites hebben doorgaans drie rollen, maar kunnen extra aangepaste rollen hebben. Toepassingen kunnen aangepaste rollen hebben. |


## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Gespecialiseerde clouds, zoals Azure Government, Azure Germany en Azure China 21Vianet, zijn momenteel niet beschikbaar voor gebruik.

### <a name="how-many-licenses-must-you-have"></a>Hoeveel licenties moet u hebben?

Controleer of uw directory minstens evenveel Azure AD Premium P2-licenties heeft als werknemers die de volgende taken uitvoeren:

- Leden die een toegangspakket **kunnen** aanvragen.
- Leden en gastgebruikers die een toegangspakket aanvragen.
- Leden en gastgebruikers die verzoeken om een toegangspakket goedkeuren.

Azure AD Premium **P2-licenties** zijn niet vereist voor de volgende taken:

- Er zijn geen licenties vereist voor gebruikers met de functie Globale beheerder die de eerste catalogi, toegangspakketten en beleidsregels hebben ingesteld en administratieve taken aan andere gebruikers delegeren.
- Er zijn geen licenties vereist voor gebruikers die zijn gedelegeerd e-beheertaken, zoals maker van de catalogus, de eigenaar van de catalogus en toegangspakketbeheer.
- Er zijn geen licenties vereist voor gasten die toegangspakketten **kunnen** aanvragen, maar **geen** toegangspakket aanvragen.

Voor elke betaalde Azure AD Premium P2-licentie die u koopt voor uw leden (werknemers), u Azure AD B2B gebruiken om maximaal 5 gastgebruikers uit te nodigen. Deze gastgebruikers kunnen ook Azure AD Premium P2-functies gebruiken. Zie [Azure AD B2B-samenwerkingslicentierichtlijnen](../b2b/licensing-guidance.md)voor meer informatie.

Zie [Licenties toewijzen of verwijderen met de Azure Active Directory-portal](../fundamentals/license-users-groups.md)voor meer informatie over licenties.

### <a name="example-license-scenarios"></a>Voorbeeldlicentiescenario's

Hier volgen enkele voorbeeldlicentiescenario's waarmee u bepalen hoeveel licenties u moet hebben.

| Scenario | Berekening | Aantal licenties |
| --- | --- | --- |
| Een globale beheerder bij Woodgrove Bank maakt initiële catalogi en delegeert administratieve taken aan 6 andere gebruikers. Een van de beleidsregels bepaalt dat **alle werknemers** (2.000 werknemers) een specifieke set toegangspakketten kunnen aanvragen. 150 medewerkers vragen de toegangspakketten aan. | 2.000 medewerkers die de toegangspakketten **kunnen** aanvragen | 2.000 |
| Een globale beheerder bij Woodgrove Bank maakt initiële catalogi en delegeert administratieve taken aan 6 andere gebruikers. Een van de beleidsregels bepaalt dat **alle werknemers** (2.000 werknemers) een specifieke set toegangspakketten kunnen aanvragen. Een ander beleid bepaalt dat sommige gebruikers van **gebruikers van partner Contoso** (gasten) dezelfde toegangspakketten kunnen aanvragen onder voorbehoud van goedkeuring. Contoso heeft 30.000 gebruikers. 150 medewerkers vragen de toegangspakketten aan en 10.500 gebruikers vragen toegang aan bij Contoso. | 2.000 werknemers + 500 gastgebruikers van Contoso die de 1:5-verhouding overschrijden (10.500 - (2.000 * 5)) | 2500 |

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Uw eerste toegangspakket maken](entitlement-management-access-package-first.md)
- [Algemene scenario's](entitlement-management-scenarios.md)
