---
title: Azure Key Vault soft-delete | Microsoft Documenten
description: Met Soft-delete in Azure Key Vault u verwijderde sleutelkluizen en sleutelkluisobjecten, zoals sleutels, geheimen en certificaten, herstellen.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: be4f124863da39cc9f6a61ebe054d451b438e8c3
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617754"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault: overzicht van voorlopig verwijderen

Key Vault's soft-delete-functie maakt het mogelijk om de verwijderde kluizen en kluisobjecten, bekend als soft-delete, te herstellen. Concreet gaan we in op de volgende scenario's:

- Ondersteuning voor herstelbare verwijdering van een sleutelkluis
- Ondersteuning voor herstelbare verwijdering van belangrijke vault-objecten (bijvoorbeeld. sleutels, geheimen, certificaten)

## <a name="supporting-interfaces"></a>Ondersteunende interfaces

De soft-delete-functie is in eerste instantie beschikbaar via de [INTERFACES REST,](/rest/api/keyvault/) [CLI,](soft-delete-cli.md) [PowerShell](soft-delete-powershell.md) en [.NET/C#.](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)

## <a name="scenarios"></a>Scenario's

Azure Key Vaults zijn bijgehouden resources die worden beheerd door Azure Resource Manager. Azure Resource Manager geeft ook een goed gedefinieerd gedrag op voor verwijdering, wat vereist dat een succesvolle delete-bewerking moet resulteren in het feit dat die resource niet meer toegankelijk is. De functie soft-delete is gericht op het herstel van het verwijderde object, ongeacht of de verwijdering per ongeluk of opzettelijk was.

1. In het typische scenario kan een gebruiker per ongeluk een sleutelkluis of een sleutelkluisobject hebben verwijderd. als dat sleutelkluis- of sleutelkluisobject gedurende een vooraf bepaalde periode kan worden hersteld, kan de gebruiker de verwijdering ongedaan maken en zijn gegevens herstellen.

2. In een ander scenario kan een malafide gebruiker proberen een sleutelkluis of een sleutelkluisobject, zoals een sleutel in een kluis, te verwijderen om een bedrijfsonderbreking te veroorzaken. Het scheiden van het verwijderen van de sleutelkluis of het sleutelkluisobject van de daadwerkelijke verwijdering van de onderliggende gegevens kan worden gebruikt als veiligheidsmaatregel door bijvoorbeeld machtigingen voor het verwijderen van gegevens te beperken tot een andere, vertrouwde rol. Deze aanpak vereist in feite quorum voor een operatie die anders zou kunnen leiden tot een onmiddellijk verlies van gegevens.

### <a name="soft-delete-behavior"></a>Gedrag voor soft-delete

Wanneer soft-delete is ingeschakeld, worden resources die zijn gemarkeerd als verwijderde bronnen standaard voor een bepaalde periode (standaard 90 dagen) bewaard. De service biedt verder een mechanisme voor het herstellen van het verwijderde object, in wezen ongedaan maken van de verwijdering.

Bij het maken van een nieuwe sleutelkluis is soft-delete standaard ingeschakeld. U een sleutelkluis maken zonder soft-delete via azure [CLI](soft-delete-cli.md) of [Azure PowerShell.](soft-delete-powershell.md) Zodra soft-delete is ingeschakeld op een sleutelkluis, kan deze niet meer worden uitgeschakeld

De standaardbewaarperiode is 90 dagen, maar tijdens het maken van sleutelkluizen is het mogelijk om het bewaarbeleidsinterval in te stellen op een waarde van 7 tot 90 dagen via de Azure-portal. Het beleid voor het behoud van zuiveringsbeveiliging gebruikt hetzelfde interval. Eenmaal ingesteld, kan het interval voor het bewaarbeleid niet worden gewijzigd.

U de naam van een sleutelkluis die is verwijderd niet opnieuw gebruiken totdat de bewaartermijn is verstreken.

### <a name="purge-protection"></a>Zuiveringsbeveiliging 

Zuiveringsbeveiliging is een optioneel Key Vault-gedrag en is **standaard niet ingeschakeld.** Het kan worden ingeschakeld via [CLI](soft-delete-cli.md#enabling-purge-protection) of [PowerShell](soft-delete-powershell.md#enabling-purge-protection).

Wanneer de zuiveringsbeveiliging is ingeschakeld, kan een kluis of een object in de verwijderde status pas worden verwijderd nadat de bewaartermijn is verstreken. Verwijderde kluizen en objecten met zachte verwijderde kluizen kunnen nog steeds worden hersteld, zodat het bewaarbeleid wordt gevolgd. 

De standaardbewaarperiode is 90 dagen, maar het is mogelijk om het bewaarbeleidsinterval in te stellen op een waarde van 7 tot 90 dagen via de Azure-portal. Zodra het bewaarinterval is ingesteld en opgeslagen, kan deze niet meer worden gewijzigd voor die kluis. 

### <a name="permitted-purge"></a>Toegestane zuivering

Permanent verwijderen, zuiveren, een sleutelkluis is mogelijk via een POST-bewerking op de proxybron en vereist speciale privileges. Over het algemeen kan alleen de eigenaar van het abonnement een sleutelkluis verwijderen. De POST-bewerking activeert de onmiddellijke en onherstelbare verwijdering van die kluis. 

Uitzonderingen zijn:
- Wanneer het Azure-abonnement is gemarkeerd als *ondeletbaar*. In dit geval kan alleen de service dan de daadwerkelijke verwijdering uitvoeren en dit doen als een gepland proces. 
- Wanneer `--enable-purge-protection flag` de is ingeschakeld op de kluis zelf. In dit geval wacht Key Vault 90 dagen vanaf het moment waarop het oorspronkelijke geheime object is gemarkeerd voor verwijdering om het object permanent te verwijderen.

### <a name="key-vault-recovery"></a>Sleutelkluisherstel

Bij het verwijderen van een sleutelkluis maakt de service een proxybron onder het abonnement, waarbij voldoende metagegevens worden toegevoegd voor herstel. De proxybron is een opgeslagen object dat beschikbaar is op dezelfde locatie als de verwijderde sleutelkluis. 

### <a name="key-vault-object-recovery"></a>Herstel van sleutelkluisobjecten

Bij het verwijderen van een belangrijk kluisobject, zoals een sleutel, plaatst de service het object in een verwijderde status, waardoor het niet toegankelijk is voor ophalen. Terwijl in deze status, kan de sleutel kluis object alleen worden vermeld, hersteld, of krachtig / permanent verwijderd. 

Tegelijkertijd plant Key Vault de verwijdering van de onderliggende gegevens die overeenkomen met het verwijderde sleutelkluis- of sleutelkluisobject voor uitvoering na een vooraf bepaald bewaarinterval. De DNS-record die overeenkomt met de kluis wordt ook bewaard voor de duur van het bewaarinterval.

### <a name="soft-delete-retention-period"></a>Bewaarperiode soft-delete

Verwijderde resources met zachte verwijderde bronnen worden gedurende een bepaalde periode, 90 dagen, bewaard. Tijdens het retentieinterval voor softdelete geldt het volgende:

- U alle belangrijke kluizen en sleutelkluisobjecten in de status soft-delete voor uw abonnement vermelden, evenals toegangsverwijderings- en herstelgegevens over deze kluizen.
    - Alleen gebruikers met speciale machtigingen kunnen verwijderde kluizen aanbieden. We raden onze gebruikers aan een aangepaste rol te maken met deze speciale machtigingen voor het verwerken van verwijderde kluizen.
- Een sleutelkluis met dezelfde naam kan niet op dezelfde locatie worden gemaakt. een sleutelkluisobject kan niet worden gemaakt in een bepaalde kluis als die sleutelkluis een object met dezelfde naam bevat en zich in een verwijderde status bevindt 
- Alleen een specifiek bevoorrechte gebruiker kan een sleutelkluis- of sleutelkluisobject herstellen door een herstelopdracht uit te geven op de bijbehorende proxybron.
    - De gebruiker, lid van de aangepaste rol, die het voorrecht heeft om een sleutelkluis te maken onder de resourcegroep, kan de kluis herstellen.
- Alleen een specifiek bevoorrechte gebruiker mag met geweld een sleutelkluis- of sleutelkluisobject verwijderen door een delete-opdracht uit te geven op de bijbehorende proxybron.

Tenzij een sleutelkluis of sleutelkluisobject wordt hersteld, voert de service aan het einde van het retentie-interval een zuivering uit van het object met de verwijderde sleutelkluis of sleutelkluis en de inhoud ervan. Verwijdering van resources wordt mogelijk niet opnieuw gepland.

### <a name="billing-implications"></a>Factureringsimplicaties

In het algemeen, wanneer een object (een sleutelkluis of een sleutel of een geheim) zich in verwijderde staat bevindt, zijn er slechts twee bewerkingen mogelijk: 'zuiveren' en 'herstellen'. Alle andere operaties zullen mislukken. Daarom, hoewel het object bestaat, kunnen geen bewerkingen worden uitgevoerd en dus geen gebruik zal optreden, dus geen factuur. Er zijn echter volgende uitzonderingen:

- 'purge' en 'recupereren' acties tellen mee voor normale key vault operaties en worden gefactureerd.
- Als het object een HSM-toets is, worden de kosten voor de 'HSM Protected-sleutel' per sleutelversie per maand in rekening gebracht als er in de afgelopen 30 dagen een belangrijke versie is gebruikt. Daarna, omdat het object is verwijderd staat geen bewerkingen kunnen worden uitgevoerd tegen het, dus geen kosten van toepassing.

## <a name="next-steps"></a>Volgende stappen

De volgende twee hulplijnen bieden de primaire gebruiksscenario's voor het gebruik van soft-delete.

- [De Key Vault-functie voor voorlopig verwijderen gebruiken met PowerShell](soft-delete-powershell.md) 
- [De Key Vault-functie voor voorlopig verwijderen gebruiken met CLI](soft-delete-cli.md)

