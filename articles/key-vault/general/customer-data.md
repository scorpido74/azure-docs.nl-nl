---
title: Functies voor klantgegevens van Azure Key Vault - Azure Key Vault | Microsoft Documenten
description: Meer informatie over klantgegevens in Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 847b6c4616cbbb7fd9c6ccad18ec426dc38a887a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430849"
---
# <a name="azure-key-vault-customer-data-features"></a>Functies voor klantgegevens van Azure Key Vault

Azure Key Vault ontvangt klantgegevens tijdens het maken of bijwerken van kluizen, sleutels, geheimen, certificaten en beheerde opslagaccounts. Deze klantgegevens zijn direct zichtbaar in de Azure-portal en via de REST API. Klantgegevens kunnen worden bewerkt of verwijderd door het object dat de gegevens bevat bij te werken of te verwijderen.

Systeemtoegangslogboeken worden gegenereerd wanneer een gebruiker of toepassing toegang heeft tot Key Vault. Gedetailleerde toegangslogboeken zijn beschikbaar voor klanten met Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Klantgegevens identificeren

De volgende informatie identificeert klantgegevens in Azure Key Vault:

- Toegangsbeleid voor Azure Key Vault bevat object--iD's die gebruikers, groepen of toepassingen vertegenwoordigen
- Certificaatonderwerpen kunnen e-mailadressen of andere gebruikers- of organisatie-id's zijn
- Certificaatcontactpersonen kunnen e-mailadressen, namen of telefoonnummers van gebruikers bevatten
- Certificaatverstrekkers kunnen e-mailadressen, namen, telefoonnummers, accountgegevens en organisatiegegevens bevatten
- Willekeurige tags kunnen worden toegepast op objecten in Azure Key Vault. Deze objecten omvatten kluizen, sleutels, geheimen, certificaten en opslagaccounts. De gebruikte tags kunnen persoonsgegevens bevatten
- Azure Key Vault-toegangslogboeken bevatten object---iD's, [UPN's](../../active-directory/hybrid/plan-connect-userprincipalname.md)en IP-adressen voor elke REST API-aanroep
- Diagnostische logboeken van Azure Key Vault kunnen object--iD's en IP-adressen bevatten voor REST API-aanroepen

## <a name="deleting-customer-data"></a>Klantgegevens verwijderen

Dezelfde REST-API's, Portal-ervaring en SDK's die worden gebruikt om kluizen, sleutels, geheimen, certificaten en beheerde opslagaccounts te maken, kunnen deze objecten ook bijwerken en verwijderen.

Met Soft delete u verwijderde gegevens 90 dagen na verwijdering herstellen. Bij het gebruik van soft delete kunnen de gegevens permanent worden verwijderd voordat de bewaartermijn van 90 dagen verstrijkt door een zuiveringsbewerking uit te voeren. Als de kluis of het abonnement is geconfigureerd om zuiveringsbewerkingen te blokkeren, is het niet mogelijk om gegevens permanent te verwijderen totdat de geplande bewaartermijn is verstreken.

## <a name="exporting-customer-data"></a>Klantgegevens exporteren

Met dezelfde REST-API's, portalervaring en SDK's die worden gebruikt om kluizen, sleutels, geheimen, certificaten en beheerde opslagaccounts te maken, u deze objecten ook bekijken en exporteren.

Azure Key Vault access logging is een optionele functie die kan worden ingeschakeld om logboeken te genereren voor elke REST API-aanroep. Deze logboeken worden overgezet naar een opslagaccount in uw abonnement waar u het bewaarbeleid toepast dat voldoet aan de vereisten van uw organisatie.

Diagnostische logboeken van Azure Key Vault die persoonlijke gegevens bevatten, kunnen worden opgehaald door een exportverzoek in te dienen in de portal Voor gebruikersprivacy. Deze aanvraag moet worden gedaan door de tenantbeheerder.

## <a name="next-steps"></a>Volgende stappen

- [Logboekregistratie van Azure Key Vault](logging.md))

- [Azure Key Vault: overzicht van voorlopig verwijderen](soft-delete-cli.md)

- [Azure Key Vault-sleutelbewerkingen](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Azure Key Vault-geheime bewerkingen](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault-certificaten en -beleid](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault-opslagaccountbewerkingen](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
