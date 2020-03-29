---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372192"
---
### <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren. De resource Cognitive Services en de sleutelkluis moeten zich in dezelfde regio en in dezelfde Azure Active Directory-tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Een nieuwe Cognitive Services-bron wordt altijd versleuteld met door Microsoft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te schakelen op het moment dat de resource wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutelkluis moet worden ingericht met toegangsbeleid waarmee belangrijke machtigingen worden verleend aan de beheerde identiteit die is gekoppeld aan de bron Cognitive Services. De beheerde identiteit is alleen beschikbaar nadat de resource is gemaakt met de prijslaag voor CMK.

Zie voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Key Vault voor versleuteling van cognitive services:

- [Door de klant beheerde sleutels configureren met Key Vault for Cognitive Services-versleuteling vanuit de Azure-portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Als u beheerde sleutels van klanten inschakelt, wordt ook een beheerde identiteit met systeemtoegewezen, een functie van Azure AD. Zodra de beheerde identiteit met systeemtoegewezen is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory. Nadat de beheerde identiteit is geregistreerd, krijgt de beheerde identiteit toegang tot de Key Vault die is geselecteerd tijdens het instellen van door de klant beheerde sleutel. U meer informatie krijgen over [Beheerde identiteiten.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Als u beheerde identiteiten met het systeem uitschakelt, wordt de toegang tot de sleutelkluis verwijderd en zijn alle gegevens die met de klantgegevens zijn versleuteld, niet langer toegankelijk. Alle functies die afhankelijk zijn van deze gegevens werken niet meer.

> [!IMPORTANT]
> Beheerde identiteiten ondersteunen momenteel geen cross-directory scenario's. Wanneer u door de klant beheerde sleutels configureert in de Azure-portal, wordt een beheerde identiteit automatisch toegewezen onder de covers. Als u vervolgens het abonnement, de resourcegroep of de resource verplaatst van de ene Azure AD-map naar de andere, wordt de beheerde identiteit die aan de bron is gekoppeld, niet overgedragen aan de nieuwe tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **Een abonnement overbrengen tussen Azure AD-mappen** in [veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources voor](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)meer informatie.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet zowel de eigenschappen **Soft Delete** als Do **Not Purge** inschakelen op de sleutelkluis.

Alleen RSA-sleutels van grootte 2048 worden ondersteund met Cognitive Services-versleuteling. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)voor meer informatie over sleutels.

### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels roteren

U een door de klant beheerde sleutel roteren in Azure Key Vault volgens uw nalevingsbeleid. Wanneer de sleutel wordt gedraaid, moet u de bron Cognitive Services bijwerken om de nieuwe sleutel URI te gebruiken. Zie de sectie getiteld **De sleutelversie** bijwerken in [Beheerde sleutels voor cognitive services configureren met behulp van de Azure-portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)voor meer informatie over het bijwerken van de bron om een nieuwe versie van de sleutel in de Azure-portal te gebruiken.

Het roteren van de sleutel leidt niet tot herversleuteling van gegevens in de bron. Er is geen verdere actie vereist van de gebruiker.

### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u PowerShell of Azure CLI. Zie [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) of Azure Key Vault [CLI](https://docs.microsoft.com/cli/azure/keyvault)voor meer informatie. Het intrekken van toegang blokkeert effectief de toegang tot alle gegevens in de Cognitive Services-bron, omdat de versleutelingssleutel niet toegankelijk is voor Cognitive Services.


