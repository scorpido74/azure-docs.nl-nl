---
title: Problemen met gegevensversleuteling oplossen - Azure Database voor PostgreSQL - Single Server
description: Meer informatie over het oplossen van problemen met de gegevensversleuteling in uw Azure Database voor PostgreSQL - Single Server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2902ff17ac14a48f1a11259339c2ab1bc4595980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299257"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Problemen met gegevensversleuteling in Azure Database voor PostgreSQL - Enkele server oplossen

Met dit artikel u veelvoorkomende problemen identificeren en oplossen die zich kunnen voordoen bij de implementatie van Azure Database met één server voor PostgreSQL wanneer u bent geconfigureerd met gegevensversleuteling met behulp van een door de klant beheerde sleutel.

## <a name="introduction"></a>Inleiding

Wanneer u gegevensversleuteling configureert om een door de klant beheerde sleutel in Azure Key Vault te gebruiken, heeft de server continue toegang tot de sleutel nodig. Als de server geen toegang meer heeft tot de door de klant beheerde sleutel in Azure Key Vault, worden alle verbindingen verbroken, wordt het juiste foutbericht teruggegeven en wordt de status ervan gewijzigd ***in Ontoegankelijk*** in de Azure-portal.

Als u geen ontoegankelijke Azure-database meer nodig hebt voor PostgreSQL-server, u deze verwijderen om te voorkomen dat u kosten maakt. Er zijn geen andere acties op de server toegestaan totdat de toegang tot de sleutelkluis is hersteld en de server beschikbaar is. Het is ook niet mogelijk om de `Yes`optie voor gegevensversleuteling te wijzigen van (door de klant beheerd) naar `No` (servicebeheerd) op een ontoegankelijke server wanneer deze is versleuteld met een door de klant beheerde sleutel. U moet de sleutel handmatig opnieuw valideren voordat de server weer toegankelijk is. Deze actie is nodig om de gegevens te beschermen tegen ongeautoriseerde toegang, terwijl machtigingen voor de door de klant beheerde sleutel worden ingetrokken.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Veelvoorkomende fouten waardoor server niet toegankelijk wordt

De volgende verkeerde configuraties veroorzaken de meeste problemen met gegevensversleuteling die Azure Key Vault-sleutels gebruiken:

- De sleutelkluis is niet beschikbaar of bestaat niet:
  - De sleutelkluis is per ongeluk verwijderd.
  - Een intermitterende netwerkfout zorgt ervoor dat de sleutelkluis niet beschikbaar is.

- U hebt geen machtigingen om toegang te krijgen tot de sleutelkluis of de sleutel bestaat niet:
  - De sleutel is verlopen of per ongeluk verwijderd of uitgeschakeld.
- De beheerde identiteit van de Azure Database voor PostgreSQL-instantie is per ongeluk verwijderd.
  - De beheerde identiteit van de Instantie Azure Database voor PostgreSQL heeft onvoldoende sleutelmachtigingen. De machtigingen bevatten bijvoorbeeld geen Get, Wrap en Uitpakken.
  - De beheerde identiteitsmachtigingen voor de Azure Database voor PostgreSQL-instantie zijn ingetrokken of verwijderd.

## <a name="identify-and-resolve-common-errors"></a>Veelvoorkomende fouten identificeren en oplossen

### <a name="errors-on-the-key-vault"></a>Fouten in de sleutelkluis

#### <a name="disabled-key-vault"></a>Uitgeschakelde sleutelkluis

- `AzureKeyVaultKeyDisabledMessage`
- **Uitleg:** de bewerking kan niet worden voltooid op de server omdat de Azure Key Vault-sleutel is uitgeschakeld.

#### <a name="missing-key-vault-permissions"></a>Machtigingen voor ontbrekende sleutelkluizen

- `AzureKeyVaultMissingPermissionsMessage`
- **Uitleg:** de server beschikt niet over de vereiste machtigingen Voor het verzamelen, verpakken en uitpakken van Azure Key Vault. Geef ontbrekende machtigingen aan de serviceprincipal met id.

### <a name="mitigation"></a>Oplossing

- Controleer of de door de klant beheerde sleutel aanwezig is in de sleutelkluis.
- Identificeer de sleutelkluis en ga vervolgens naar de sleutelkluis in de Azure-portal.
- Zorg ervoor dat de sleutel URI een sleutel identificeert die aanwezig is.

## <a name="next-steps"></a>Volgende stappen

[De Azure-portal gebruiken om gegevensversleuteling in te stellen met een door de klant beheerde sleutel in Azure Database voor PostgreSQL](howto-data-encryption-portal.md)
