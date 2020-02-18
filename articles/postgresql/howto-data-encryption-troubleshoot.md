---
title: Gegevens versleuteling voor Azure Database for PostgreSQL-problemen oplossen met één server
description: Meer informatie over het oplossen van problemen met de gegevens versleuteling voor uw Azure Database for PostgreSQL-één server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 6d2e027c47bef3186f95c2183b316b5c15511070
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371499"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-postgresql---single-server"></a>Problemen met gegevens versleuteling oplossen met door de klant beheerde sleutels in Azure Database for PostgreSQL-één server
In dit artikel wordt beschreven hoe u veelvoorkomende problemen/fouten kunt identificeren en oplossen die zich voordoen op een Azure Database for PostgreSQL-één server die is geconfigureerd met gegevens versleuteling met door de klant beheerde sleutel.

## <a name="introduction"></a>Inleiding
Wanneer gegevens versleuteling is geconfigureerd voor het gebruik van een door de klant beheerde sleutel in Azure Key Vault, is continue toegang tot deze sleutel vereist om de server beschikbaar te houden. Als de server de toegang tot de door de klant beheerde sleutel in Azure Key Vault kwijtraakt, wordt door de server alle verbindingen met het juiste fout bericht geweigerd en wordt de status gewijzigd in niet ***toegankelijk*** in de Azure Portal.

Als een niet-toegankelijke Azure Database for PostgreSQL-één-server niet meer nodig is, kan deze onmiddellijk worden verwijderd om de kosten voor het maken te stoppen. Alle andere acties op de server zijn pas toegestaan als de toegang tot de Azure-sleutel kluis is hersteld en de server weer beschikbaar is. Het wijzigen van de optie voor gegevens versleuteling van Ja (door de klant beheerd) naar ' nee ' (door service beheerd) op een niet-toegankelijke server is ook niet mogelijk wanneer een server is versleuteld met door de klant beheerd. U moet de sleutel hand matig opnieuw valideren om de server weer beschikbaar te maken. Dit is nodig om de gegevens te beschermen tegen onbevoegde toegang terwijl machtigingen voor de door de klant beheerde sleutel zijn ingetrokken.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Veelvoorkomende fouten waardoor de server niet meer toegankelijk is

De meeste problemen die zich voordoen wanneer u gegevens versleuteling met Azure Key Vault gebruikt, worden veroorzaakt door een van de volgende onjuiste configuraties:

De sleutel kluis is niet beschikbaar of bestaat niet

* De sleutel kluis is per ongeluk verwijderd.
* Een onregelmatige netwerk fout zorgt ervoor dat de sleutel kluis niet beschikbaar is.

Geen machtigingen voor toegang tot de sleutel kluis of de sleutel bestaat niet

* De sleutel is per ongeluk verwijderd, uitgeschakeld of de sleutel is verlopen.
* De door het Azure Database for PostgreSQL-exemplaar beheerde identiteit voor de afzonderlijke server is per ongeluk verwijderd.
* Machtigingen die zijn verleend aan de Azure Database for PostgreSQL beheerde identiteit voor de sleutels zijn niet voldoende (Get, wrap en Unwrap).
* De machtigingen voor de Azure Database for PostgreSQL met één server instantie beheerde identiteit zijn ingetrokken of verwijderd.

## <a name="identify-and-resolve-common-errors"></a>Veelvoorkomende fouten identificeren en oplossen
### <a name="errors-on-the-key-vault"></a>Fouten in de sleutel kluis

#### <a name="disabled-key-vault"></a>Uitgeschakelde sleutel kluis
* AzureKeyVaultKeyDisabledMessage
* **Uitleg** : de bewerking kan niet worden voltooid op de server omdat de Azure Key Vault sleutel is uitgeschakeld.

#### <a name="missing-key-vault-permissions"></a>Ontbrekende sleutel kluis machtigingen
* AzureKeyVaultMissingPermissionsMessage
* De-server beschikt niet over de vereiste machtigingen voor Get, rewrap en uitpakken voor de Azure Key Vault machtigingen. Ken de ontbrekende machtigingen toe aan de service-principal met de ID.

### <a name="mitigation"></a>Oplossing
* Controleer of de door de klant beheerde sleutel aanwezig is in Key Vault:
* Zoek de sleutel kluis en ga vervolgens naar de sleutel kluis in de Azure Portal.
* Zorg ervoor dat de sleutel die wordt geïdentificeerd door de sleutel-URI aanwezig is.


## <a name="next-steps"></a>Volgende stappen
[Stel gegevens versleuteling in met een door de klant beheerde sleutel voor uw Azure-Data Base voor postgresql met behulp van de Azure Portal](howto-data-encryption-portal.md).