---
title: Vertaler encryptie van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Vertaler encryptie van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372236"
---
# <a name="translator-encryption-of-data-at-rest"></a>Vertaler encryptie van gegevens in rust

Translator versleutelt automatisch uw gegevens, die u uploadt om aangepaste vertaalmodellen te bouwen, wanneer deze in de cloud blijven bestaan, zodat u uw doelstellingen op het gebied van organisatiebeveiliging en compliance bereiken.

## <a name="about-cognitive-services-encryption"></a>Versleuteling van Cognitive Services

Gegevens worden versleuteld en gedecodeerd met [FIPS 140-2-compatibele](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bits AES-versleuteling.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) Versleuteling en decryptie zijn transparant, wat betekent dat encryptie en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om te profiteren van versleuteling.

## <a name="about-encryption-key-management"></a>Over beheer van versleutelingssleutels

Standaard maakt uw abonnement gebruik van door Microsoft beheerde versleutelingssleutels. Als u een prijscategorie gebruikt die door de klant beheerde sleutels ondersteunt, u de versleutelingsinstellingen voor uw bron zien in het gedeelte **Versleuteling** van de [Azure-portal](https://portal.azure.com), zoals weergegeven in de volgende afbeelding.

![Versleutelingsinstellingen weergeven](../media/cognitive-services-encryption/encryptionblade.png)

Voor abonnementen die alleen door Microsoft beheerde versleutelingssleutels ondersteunen, hebt u geen **sectie Versleuteling.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

Er is ook een optie om uw abonnement met uw eigen sleutels te beheren. Door de klant beheerde sleutels (CMK), ook wel bekend als Bring your own key (BYOK), bieden meer flexibiliteit om toegangsbesturingselementen te maken, te roteren, uit te schakelen en in te trekken. U ook de versleutelingssleutels controleren die worden gebruikt om uw gegevens te beschermen.

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn beschikbaar voor alle prijsniveaus voor de Translator-service. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [door de klant beheerde sleutelaanvraagformulier voor vertalers](https://aka.ms/cogsvc-cmk) in en dient u dit in, het duurt ongeveer 3-5 werkdagen voordat u de status van uw aanvraag hebt gehoord. Afhankelijk van de vraag u in een wachtrij worden geplaatst en worden goedgekeurd zodra er ruimte beschikbaar komt. Zodra u is goedgekeurd voor het gebruik van CMK met de Translator-service, moet u een nieuwe Translator-bron maken. Zodra uw Translator-bron is gemaakt, u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

Volg de volgende stappen om door de klant beheerde sleutels voor Translator in te schakelen:

1. Maak uw nieuwe regionale vertalerstekst of regionale bron voor cognitieve diensten. Dit zal niet werken met een wereldwijde bron.
2. Beheerde identiteit ingeschakeld in de Azure-portal en voeg uw door de klant beheerde sleutelgegevens toe.
3. Maak een nieuwe werkruimte in Aangepaste vertaler en koppel deze abonnementsgegevens.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren. De resource Cognitive Services en de sleutelkluis moeten zich in dezelfde regio en in dezelfde Azure Active Directory-tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Een nieuwe Cognitive Services-bron wordt altijd versleuteld met door Microsoft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te schakelen op het moment dat de resource wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutelkluis moet worden ingericht met toegangsbeleid waarmee belangrijke machtigingen worden verleend aan de beheerde identiteit die is gekoppeld aan de bron Cognitive Services. De beheerde identiteit is beschikbaar zodra de bron is gemaakt.

Zie voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Key Vault voor versleuteling van cognitive services:

- [Door de klant beheerde sleutels configureren met Key Vault for Cognitive Services-versleuteling vanuit de Azure-portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Als u beheerde sleutels van klanten inschakelt, wordt ook een beheerde identiteit met systeemtoegewezen, een functie van Azure AD. Zodra de beheerde identiteit met systeemtoegewezen is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory. Nadat de beheerde identiteit is geregistreerd, krijgt de beheerde identiteit toegang tot de Key Vault die is geselecteerd tijdens het instellen van door de klant beheerde sleutel. U meer informatie krijgen over [Beheerde identiteiten.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Als u beheerde identiteiten met het systeem uitschakelt, wordt de toegang tot de sleutelkluis verwijderd en zijn alle gegevens die met de klantgegevens zijn versleuteld, niet langer toegankelijk. Alle functies die afhankelijk zijn van deze gegevens werken niet meer. Alle modellen die u hebt geïmplementeerd, worden ook niet geïmplementeerd. Alle geüploade gegevens worden verwijderd uit Aangepaste vertaler. Als de beheerde identiteiten opnieuw zijn ingeschakeld, implementeren we het model niet automatisch opnieuw voor u.

> [!IMPORTANT]
> Beheerde identiteiten ondersteunen momenteel geen cross-directory scenario's. Wanneer u door de klant beheerde sleutels configureert in de Azure-portal, wordt een beheerde identiteit automatisch toegewezen onder de covers. Als u vervolgens het abonnement, de resourcegroep of de resource verplaatst van de ene Azure AD-map naar de andere, wordt de beheerde identiteit die aan de bron is gekoppeld, niet overgedragen aan de nieuwe tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **Een abonnement overbrengen tussen Azure AD-mappen** in [veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources voor](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)meer informatie.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet zowel de eigenschappen **Soft Delete** als Do **Not Purge** inschakelen op de sleutelkluis.

Alleen RSA-sleutels van grootte 2048 worden ondersteund met Cognitive Services-versleuteling. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)voor meer informatie over sleutels.

> [!NOTE]
> Als de volledige sleutelkluis wordt verwijderd, worden uw gegevens niet meer weergegeven en worden al uw modellen niet geïmplementeerd. Alle geüploade gegevens worden verwijderd uit Aangepaste vertaler. 

### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u PowerShell of Azure CLI. Zie [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) of Azure Key Vault [CLI](https://docs.microsoft.com/cli/azure/keyvault)voor meer informatie. Het intrekken van toegang blokkeert effectief de toegang tot alle gegevens in de Cognitive Services-bron en uw modellen worden niet geïmplementeerd, omdat de versleutelingssleutel ontoegankelijk is voor cognitive services. Alle geüploade gegevens worden ook verwijderd uit Aangepaste vertaler.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
