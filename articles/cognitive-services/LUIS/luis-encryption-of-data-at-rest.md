---
title: Taalbegrijpende serviceversleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Taal begrijpen service encryptie van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372335"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Taalbegrijpende serviceversleuteling van gegevens in rust

De taalinformatieservice versleutelt uw gegevens automatisch wanneer deze in de cloud worden weergegeven. De taalversleuteling seint uw gegevens en helpt u om te voldoen aan uw verplichtingen op het gebied van organisatiebeveiliging en naleving.

## <a name="about-cognitive-services-encryption"></a>Versleuteling van Cognitive Services

Gegevens worden versleuteld en gedecodeerd met [FIPS 140-2-compatibele](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bits AES-versleuteling.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) Versleuteling en decryptie zijn transparant, wat betekent dat encryptie en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om te profiteren van versleuteling.

## <a name="about-encryption-key-management"></a>Over beheer van versleutelingssleutels

Standaard maakt uw abonnement gebruik van door Microsoft beheerde versleutelingssleutels. Er is ook een optie om uw abonnement met uw eigen sleutels te beheren. Door de klant beheerde sleutels (CMK) bieden meer flexibiliteit om toegangsbesturingselementen te maken, te roteren, uit te schakelen en in te trekken. U ook de versleutelingssleutels controleren die worden gebruikt om uw gegevens te beschermen.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

Er is ook een optie om uw abonnement met uw eigen sleutels te beheren. Door de klant beheerde sleutels (CMK), ook wel bekend als Bring your own key (BYOK), bieden meer flexibiliteit om toegangsbesturingselementen te maken, te roteren, uit te schakelen en in te trekken. U ook de versleutelingssleutels controleren die worden gebruikt om uw gegevens te beschermen.

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren. De resource Cognitive Services en de sleutelkluis moeten zich in dezelfde regio en in dezelfde Azure Active Directory-tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

### <a name="customer-managed-keys-for-language-understanding"></a>Door de klant beheerde sleutels voor taalbegrip

Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het door [de klant beheerde sleutelaanvraagformulier van luis service](https://aka.ms/cogsvc-cmk)in en verzendt deze dit in. Het duurt ongeveer 3-5 werkdagen om de status van uw verzoek te horen. Afhankelijk van de vraag u in een wachtrij worden geplaatst en worden goedgekeurd zodra er ruimte beschikbaar komt. Nadat u is goedgekeurd voor het gebruik van CMK met LUIS, moet u een nieuwe taalinformatiebron maken vanuit de Azure-portal en E0 selecteren als de prijscategorie. De nieuwe SKU zal hetzelfde functioneren als de F0 SKU die al beschikbaar is, behalve voor CMK. Gebruikers kunnen niet upgraden van de F0 naar de nieuwe E0 SKU.

E0-bronnen zijn alleen beschikbaar voor authoring service en dat de E0-laag in eerste instantie alleen wordt ondersteund in de regio West-VS.

![LUIS-abonnementsafbeelding](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Regionale beschikbaarheid

Door de klant beheerde sleutels zijn momenteel beschikbaar in de regio **West-VS.**

### <a name="limitations"></a>Beperkingen

Er zijn enkele beperkingen bij het gebruik van de E0-laag met bestaande/eerder gemaakte toepassingen:

* Migratie naar een E0-bron wordt geblokkeerd. Gebruikers kunnen hun apps alleen migreren naar F0-bronnen. Nadat u een bestaande resource naar F0 hebt gemigreerd, u een nieuwe resource maken in de e0-laag. Meer informatie over [migratie vindt u hier](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* Het verplaatsen van toepassingen van of naar een E0-bron wordt geblokkeerd. Een work around voor deze beperking is het exporteren van uw bestaande toepassing en importeren als een E0-bron.
* De functie Bing Spell check wordt niet ondersteund.
* Het registreren van het verkeer van eindgebruikers is uitgeschakeld als uw toepassing E0 is.
* De mogelijkheid voor het aanhouden van spraak van de Azure Bot-service wordt niet ondersteund voor toepassingen in de E0-laag. Deze functie is beschikbaar via de Azure Bot-service, die geen CMK ondersteunt.
* De mogelijkheid voor spraakpriming van de portal vereist Azure Blob Storage. Zie voor meer informatie [uw eigen opslag meenemen.](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)

### <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

Een nieuwe Cognitive Services-bron wordt altijd versleuteld met door Microsoft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te schakelen op het moment dat de resource wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutelkluis moet worden ingericht met toegangsbeleid waarmee belangrijke machtigingen worden verleend aan de beheerde identiteit die is gekoppeld aan de bron Cognitive Services. De beheerde identiteit is alleen beschikbaar nadat de resource is gemaakt met de prijslaag voor CMK.

Zie voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Key Vault voor versleuteling van cognitive services:

- [Door de klant beheerde sleutels configureren met Key Vault for Cognitive Services-versleuteling vanuit de Azure-portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Als u beheerde sleutels van klanten inschakelt, wordt ook een beheerde identiteit met systeemtoegewezen, een functie van Azure AD. Zodra de beheerde identiteit met systeemtoegewezen is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory. Nadat de beheerde identiteit is geregistreerd, krijgt de beheerde identiteit toegang tot de Key Vault die is geselecteerd tijdens het instellen van door de klant beheerde sleutel. U meer informatie krijgen over [Beheerde identiteiten.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Als u beheerde identiteiten met het systeem uitschakelt, wordt de toegang tot de sleutelkluis verwijderd en zijn alle gegevens die met de klantgegevens zijn versleuteld, niet langer toegankelijk. Alle functies die afhankelijk zijn van deze gegevens werken niet meer.

> [!IMPORTANT]
> Beheerde identiteiten ondersteunen momenteel geen cross-directory scenario's. Wanneer u door de klant beheerde sleutels configureert in de Azure-portal, wordt een beheerde identiteit automatisch toegewezen onder de covers. Als u vervolgens het abonnement, de resourcegroep of de resource verplaatst van de ene Azure AD-map naar de andere, wordt de beheerde identiteit die aan de bron is gekoppeld, niet overgedragen aan de nieuwe tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **Een abonnement overbrengen tussen Azure AD-mappen** in [veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources voor](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)meer informatie.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet zowel de eigenschappen **Soft Delete** als Do **Not Purge** inschakelen op de sleutelkluis.

Alleen RSA-sleutels van grootte 2048 worden ondersteund met Cognitive Services-versleuteling. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)voor meer informatie over sleutels.

### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels roteren

U een door de klant beheerde sleutel roteren in Azure Key Vault volgens uw nalevingsbeleid. Wanneer de sleutel wordt gedraaid, moet u de bron Cognitive Services bijwerken om de nieuwe sleutel URI te gebruiken. Zie de sectie getiteld **De sleutelversie** bijwerken in [Beheerde sleutels voor cognitive services configureren met behulp van de Azure-portal](../Encryption/cognitive-services-encryption-keys-portal.md)voor meer informatie over het bijwerken van de bron om een nieuwe versie van de sleutel in de Azure-portal te gebruiken.

Het roteren van de sleutel leidt niet tot herversleuteling van gegevens in de bron. Er is geen verdere actie vereist van de gebruiker.

### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u PowerShell of Azure CLI. Zie [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) of Azure Key Vault [CLI](https://docs.microsoft.com/cli/azure/keyvault)voor meer informatie. Het intrekken van toegang blokkeert effectief de toegang tot alle gegevens in de Cognitive Services-bron, omdat de versleutelingssleutel niet toegankelijk is voor Cognitive Services.

## <a name="next-steps"></a>Volgende stappen

* [Formulier voor het aanvragen van een door de klant beheerde sleutelformulier voor de klantenservice van LUIS-service](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
