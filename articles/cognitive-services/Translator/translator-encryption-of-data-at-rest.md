---
title: Conversie van gegevens in rust versleutelen
titleSuffix: Azure Cognitive Services
description: Vertaal versleuteling van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 82b294cbda6e9d4b4cfa5ec560d802c8e5c6c51c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590890"
---
# <a name="translator-encryption-of-data-at-rest"></a>Conversie van gegevens in rust versleutelen

Met Translator worden uw gegevens automatisch versleuteld, die u uploadt om aangepaste Vertaal modellen te bouwen, wanneer deze worden opgeslagen in de Cloud, zodat u kunt voldoen aan de beveiligings-en nalevings doelen van uw organisatie.

## <a name="about-cognitive-services-encryption"></a>Over Cognitive Services versleuteling

Gegevens worden versleuteld en ontsleuteld met behulp van [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-compatibele 256-bits AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) versleuteling. Versleuteling en ontsleuteling zijn transparant, wat betekent dat versleuteling en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om te kunnen profiteren van versleuteling.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Uw abonnement maakt standaard gebruik van door micro soft beheerde versleutelings sleutels. Als u een prijs categorie gebruikt die door de klant beheerde sleutels ondersteunt, kunt u de versleutelings instellingen voor uw resource bekijken in de sectie **versleuteling** van het [Azure Portal](https://portal.azure.com), zoals wordt weer gegeven in de volgende afbeelding.

![Versleutelings instellingen weer geven](../media/cognitive-services-encryption/encryptionblade.png)

Voor abonnementen die alleen door micro soft beheerde versleutelings sleutels ondersteunen, hebt u geen sectie **versleuteling** .

## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

Er is ook een optie voor het beheren van uw abonnement met uw eigen sleutels. Door de klant beheerde sleutels (CMK), ook wel bekend als het nemen van uw eigen sleutel (BYOK), bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn beschikbaar voor alle prijs categorieën voor de Vertaal service. Als u de mogelijkheid wilt bieden om door de klant beheerde sleutels te gebruiken, vult u het aanvraag formulier voor de door de [klant beheerde sleutel](https://aka.ms/cogsvc-cmk) te vullen en in. het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Na goed keuring voor het gebruik van CMK met de Translator-service, moet u een nieuwe Translator-resource maken. Zodra uw Translator-resource is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

Volg deze stappen om door de klant beheerde sleutels voor Translator in te scha kelen:

1. Maak uw nieuwe regionale vertaler of regionale Cognitive Services resource. Dit werkt niet met een globale resource.
2. Ingeschakelde beheerde identiteit in de Azure Portal en voeg de door de klant beheerde sleutel gegevens toe.
3. Maak een nieuwe werk ruimte in Custom Translator en koppel deze abonnements gegevens.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. De Cognitive Services resource en de sleutel kluis moeten zich in dezelfde regio bevinden en in dezelfde Azure Active Directory (Azure AD)-Tenant, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)voor meer informatie over Azure Key Vault.

Een nieuwe Cognitive Services resource wordt altijd versleuteld met door micro soft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te scha kelen op het moment dat de resource wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die aan de Cognitive Services-resource is gekoppeld. De beheerde identiteit is beschikbaar zodra de resource is gemaakt.

Zie voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Key Vault voor Cognitive Services versleuteling:

- [Door de klant beheerde sleutels configureren met Key Vault voor Cognitive Services versleuteling van de Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Door door de klant beheerde sleutels in te scha kelen, wordt ook een door het systeem toegewezen beheerde identiteit, een functie van Azure AD, ingeschakeld. Zodra de door het systeem toegewezen beheerde identiteit is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory. Na registratie krijgt de beheerde identiteit toegang tot de Key Vault geselecteerd tijdens de installatie van de door de klant beheerde sleutel. Meer informatie over [beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)vindt u in.

> [!IMPORTANT]
> Als u door het systeem toegewezen beheerde identiteiten uitschakelt, wordt de toegang tot de sleutel kluis verwijderd en worden alle gegevens die zijn versleuteld met de klant sleutels niet meer toegankelijk. Alle functies, afhankelijk van deze gegevens, werken niet meer. Alle modellen die u hebt geïmplementeerd, worden ook niet-geïmplementeerd. Alle geüploade gegevens worden verwijderd uit het aangepaste conversie programma. Als de beheerde identiteiten opnieuw worden ingeschakeld, wordt het model niet automatisch opnieuw geïmplementeerd.

> [!IMPORTANT]
> Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. Wanneer u door de klant beheerde sleutels in de Azure Portal configureert, wordt er automatisch een beheerde identiteit toegewezen onder de voor vallen. Als u het abonnement, de resource groep of de resource van een Azure AD-Directory vervolgens naar een andere verplaatst, wordt de beheerde identiteit die aan de resource is gekoppeld, niet overgezet naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet de instellingen **voorlopig verwijderen** en **niet wissen** in de sleutel kluis inschakelen.

Alleen RSA-sleutels met een grootte van 2048 worden ondersteund met Cognitive Services versleuteling. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)voor meer informatie over sleutels.

> [!NOTE]
> Als de volledige sleutel kluis wordt verwijderd, worden uw gegevens niet meer weer gegeven en worden al uw modellen niet meer geïmplementeerd. Alle geüploade gegevens worden verwijderd uit het aangepaste conversie programma. 

### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of Azure CLI. Zie [Azure Key Vault Power shell](https://docs.microsoft.com/powershell/module/az.keyvault//) of [Azure Key Vault cli](https://docs.microsoft.com/cli/azure/keyvault)(Engelstalig) voor meer informatie. Als u toegang intrekt, wordt de toegang tot alle gegevens in de Cognitive Services resource geblokkeerd en worden uw modellen niet meer geïmplementeerd, omdat de versleutelings sleutel niet toegankelijk is voor Cognitive Services. Alle geüploade gegevens worden ook uit het aangepaste conversie programma verwijderd.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
