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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372192"
---
### <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. De Cognitive Services resource en de sleutel kluis moeten zich in dezelfde regio bevinden en in dezelfde Azure Active Directory (Azure AD)-Tenant, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)voor meer informatie over Azure Key Vault.

Een nieuwe Cognitive Services resource wordt altijd versleuteld met door micro soft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te scha kelen op het moment dat de resource wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die aan de Cognitive Services-resource is gekoppeld. De beheerde identiteit is alleen beschikbaar nadat de resource is gemaakt met behulp van de prijs categorie voor CMK.

Zie voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Key Vault voor Cognitive Services versleuteling:

- [Door de klant beheerde sleutels configureren met Key Vault voor Cognitive Services versleuteling van de Azure Portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Door door de klant beheerde sleutels in te scha kelen, wordt ook een door het systeem toegewezen beheerde identiteit, een functie van Azure AD, ingeschakeld. Zodra de door het systeem toegewezen beheerde identiteit is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory. Na registratie krijgt de beheerde identiteit toegang tot de Key Vault geselecteerd tijdens de installatie van de door de klant beheerde sleutel. Meer informatie over [beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)vindt u in.

> [!IMPORTANT]
> Als u door het systeem toegewezen beheerde identiteiten uitschakelt, wordt de toegang tot de sleutel kluis verwijderd en worden alle gegevens die zijn versleuteld met de klant sleutels niet meer toegankelijk. Alle functies, afhankelijk van deze gegevens, werken niet meer.

> [!IMPORTANT]
> Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. Wanneer u door de klant beheerde sleutels in de Azure Portal configureert, wordt er automatisch een beheerde identiteit toegewezen onder de voor vallen. Als u het abonnement, de resource groep of de resource van een Azure AD-Directory vervolgens naar een andere verplaatst, wordt de beheerde identiteit die aan de resource is gekoppeld, niet overgezet naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet de instellingen **voorlopig verwijderen** en **niet wissen** in de sleutel kluis inschakelen.

Alleen RSA-sleutels met een grootte van 2048 worden ondersteund met Cognitive Services versleuteling. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)voor meer informatie over sleutels.

### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels draaien

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u de Cognitive Services resource bijwerken om de nieuwe sleutel-URI te gebruiken. Voor informatie over het bijwerken van de resource voor het gebruik van een nieuwe versie van de sleutel in de Azure Portal, zie de sectie met de titel **Update de sleutel versie** in [door de klant beheerde sleutels configureren voor Cognitive Services met behulp van de Azure Portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

Als u de sleutel roteert, wordt het opnieuw versleutelen van gegevens in de bron niet geactiveerd. Er is geen verdere actie vereist van de gebruiker.

### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of Azure CLI. Zie [Azure Key Vault Power shell](https://docs.microsoft.com/powershell/module/az.keyvault//) of [Azure Key Vault cli](https://docs.microsoft.com/cli/azure/keyvault)(Engelstalig) voor meer informatie. Toegang intrekken effectief blokkeert de toegang tot alle gegevens in de Cognitive Services resource, omdat de versleutelings sleutel niet toegankelijk is voor Cognitive Services.


