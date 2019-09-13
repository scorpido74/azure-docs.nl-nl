---
title: Azure Key Vault beveiliging | Microsoft Docs
description: Toegangs machtigingen voor Azure Key Vault, sleutels en geheimen beheren. Behandelt het verificatie-en autorisatie model voor Key Vault en hoe u uw sleutel kluis kunt beveiligen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 728398aeec4715d15ebe44ae6d4e4bfa5f295df8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884783"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault beveiliging

U moet de versleutelings sleutels en geheimen zoals certificaten, verbindings reeksen en wacht woorden in de Cloud beveiligen, zodat u Azure Key Vault gebruikt. Omdat u gevoelige en bedrijfskritische gegevens opslaat, moet u stappen ondernemen om de beveiliging van uw kluizen en de gegevens die erin zijn opgeslagen te maximaliseren. In dit artikel worden enkele van de concepten besproken die u moet overwegen bij het ontwerpen van uw Azure Key Vault-beveiliging.

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

Wanneer u een sleutel kluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-Tenant van het abonnement. Iedereen die inhoud probeert te beheren of ophalen uit een kluis, moet worden geverifieerd door Azure AD.

- Met verificatie wordt de identiteit van de aanroeper bepaald.
- Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Autorisatie in Key Vault gebruikt een combi natie van [op rollen gebaseerd toegangs beheer](../role-based-access-control/overview.md) (RBAC) en Azure Key Vault toegangs beleid.

### <a name="access-model-overview"></a>Overzicht van toegangs modellen

De toegang tot kluizen vindt plaats via twee interfaces of-abonnementen. Deze vlakken zijn het beheer vlak en het gegevens vlak.

- Het *beheer vlak* is waar u Key Vault zelf beheert en het is de interface die wordt gebruikt om kluizen te maken en verwijderen. U kunt ook sleutel kluis eigenschappen lezen en toegangs beleid beheren.
- Met het *gegevens vlak* kunt u werken met de gegevens die zijn opgeslagen in een sleutel kluis. U kunt sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Voor toegang tot een sleutel kluis in een van beide vlieg tuigen moeten alle bellers (gebruikers of toepassingen) worden geverifieerd en geautoriseerd. Beide plannen gebruiken Azure Active Directory (Azure AD) voor verificatie. Voor autorisatie gebruikt het beheer vlak op rollen gebaseerd toegangs beheer (RBAC) en gebruikt het gegevens vlak een Key Vault toegangs beleid.

Het model van één mechanisme voor verificatie voor beide abonnementen heeft verschillende voor delen:

- Organisaties kunnen de toegang centraal beheren voor alle sleutel kluizen in hun organisatie.
- Als een gebruiker deze verlaat, gaan ze onmiddellijk toegang tot alle sleutel kluizen in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in azure AD, zoals om multi-factor Authentication in te scha kelen voor extra beveiliging

### <a name="managing-administrative-access-to-key-vault"></a>Beheer toegang tot Key Vault beheren

Wanneer u een sleutel kluis maakt in een resource groep, beheert u de toegang met behulp van Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutel kluizen in een resource groep te beheren. U kunt toegang verlenen op een specifiek Scope niveau door de juiste RBAC-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om sleutel kluizen te beheren, wijst u `key vault Contributor` een vooraf gedefinieerde rol toe aan de gebruiker op een specifiek bereik. De volgende Scope niveaus kunnen worden toegewezen aan een RBAC-rol:

- **Abonnement**: Een RBAC-rol die is toegewezen op abonnements niveau, is van toepassing op alle resource groepen en resources in dat abonnement.
- **Resourcegroep**: Een RBAC-rol die is toegewezen op het niveau van de resource groep, is van toepassing op alle resources in die resource groep.
- **Specifieke resource**: Een RBAC-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutel kluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, kunt u uw eigen rol definiëren. Zie [voor meer informatie RBAC: Ingebouwde rollen](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Als een gebruiker machtigingen `Contributor` heeft voor een sleutel kluis beheer vlak, kan de gebruiker zichzelf toegang verlenen tot het gegevens vlak door een Key Vault toegangs beleid in te stellen. U moet nauw keurig bepalen wie rollen `Contributor` toegang heeft tot uw sleutel kluizen. Zorg ervoor dat alleen geautoriseerde personen uw sleutel kluizen, sleutels, geheimen en certificaten kunnen gebruiken en beheren.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Toegang tot Key Vault gegevens beheren

Key Vault toegangs beleid worden machtigingen afzonderlijk verleend aan sleutels, geheimen of certificaat. U kunt een gebruiker alleen toegang geven tot sleutels en niet op geheimen. Toegangs machtigingen voor sleutels, geheimen en certificaten worden beheerd op het niveau van de kluis.

> [!IMPORTANT]
> Key Vault toegangs beleid biedt geen ondersteuning voor granulaire machtigingen op object niveau, zoals een specifieke sleutel, geheim of certificaat. Wanneer een gebruiker gemachtigd is om sleutels te maken en te verwijderen, kunnen ze deze bewerkingen uitvoeren op alle sleutels in die sleutel kluis.

Als u toegangs beleid wilt instellen voor een sleutel kluis, gebruikt u de [Azure Portal](https://portal.azure.com/), de [Azure cli](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de [rest api's van Key Vault beheer](https://msdn.microsoft.com/library/azure/mt620024.aspx).

U kunt de toegang tot het gegevens vlak beperken door de [service-eind punten van het virtuele netwerk voor Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)te gebruiken. U kunt [firewalls en regels voor virtuele netwerken](key-vault-network-security.md) configureren voor een extra beveiligingslaag.

## <a name="network-access"></a>Netwerktoegang

U kunt de bloot stelling van uw kluizen verminderen door op te geven welke IP-adressen er toegang tot hebben. Met de service-eind punten voor virtuele netwerken voor Azure Key Vault kunt u de toegang tot een opgegeven virtueel netwerk beperken. Met de eind punten kunt u ook de toegang beperken tot een lijst met IPv4-adresbereiken (Internet Protocol versie 4). Gebruikers die verbinding maken met uw sleutel kluis van buiten deze bronnen, krijgen geen toegang.

Nadat de firewall regels van kracht zijn, kunnen gebruikers alleen gegevens van Key Vault lezen wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit geldt ook voor toegang tot Key Vault vanuit de Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutel kluis van de Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weer geven als hun client computer niet in de lijst met toegestane clients staat. Dit is ook van invloed op de Key Vault kiezer door andere Azure-Services. Gebruikers kunnen mogelijk een lijst met sleutel kluizen zien, maar geen lijst met sleutels als firewall regels hun client computer verhinderen.

Raadpleeg voor meer informatie over Azure Key Vault netwerk adres [service-eind punten voor virtuele netwerken voor Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Bewaking

Met Key Vault logboek registratie wordt informatie opgeslagen over de activiteiten die zijn uitgevoerd op uw kluis. Key Vault logboeken:

- Alle geverifieerde REST API aanvragen, met inbegrip van mislukte aanvragen
  - Bewerkingen op de sleutel kluis zelf. Tot deze bewerkingen behoren het maken, verwijderen, instellen van toegangs beleid en het bijwerken van sleutel kluis kenmerken, zoals Tags.
  - Bewerkingen voor sleutels en geheimen in de sleutel kluis, waaronder:
    - Deze sleutels of geheimen maken, wijzigen of verwijderen.
    - Ondertekenen, controleren, versleutelen, ontsleutelen, decoderen en inpakken van sleutels, geheimen ophalen en sleutels en geheimen weer geven (en hun versie).
- Niet-geverifieerde aanvragen die in een 401-respons resulteren. Voor beelden zijn aanvragen die geen Bearer-token hebben, die ongeldig of verlopen zijn of die een ongeldig token hebben.

Logboek informatie kan worden geopend binnen 10 minuten na de sleutel kluis bewerking. Het is aan u om uw logboeken in uw opslag account te beheren. 

- Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
- Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Raadpleeg de [Azure Storage-beveiligings handleiding](../storage/common/storage-security-guide.md) voor aanbevelingen voor het veilig beheren van opslag accounts.

## <a name="next-steps"></a>Volgende stappen

- [Virtuele netwerk service-eind punten voor Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Ingebouwde rollen](../role-based-access-control/built-in-roles.md)
- [virtuele netwerk service-eind punten voor Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
