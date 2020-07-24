---
title: Azure Key Vault beveiliging | Microsoft Docs
description: Toegangs machtigingen voor Azure Key Vault, sleutels en geheimen beheren. Behandelt het verificatie-en autorisatie model voor Key Vault en hoe u uw sleutel kluis kunt beveiligen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 8d6dd38c3d1802c3b5bec82edbda4f9d344fa28b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090515"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-beveiliging

U moet de versleutelings sleutels en geheimen zoals certificaten, verbindings reeksen en wacht woorden in de Cloud beveiligen, zodat u Azure Key Vault gebruikt. Omdat u gevoelige en bedrijfskritische gegevens opslaat, moet u stappen ondernemen om de beveiliging van uw kluizen en de gegevens die erin zijn opgeslagen te maximaliseren. In dit artikel worden enkele van de concepten besproken die u moet overwegen bij het ontwerpen van uw Azure Key Vault-beveiliging.

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

Wanneer u een sleutel kluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-Tenant van het abonnement. Iedereen die inhoud probeert te beheren of ophalen uit een kluis, moet worden geverifieerd door Azure AD.

- Met verificatie wordt de identiteit van de aanroeper bepaald.
- Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Autorisatie in Key Vault gebruikt een combi natie van [op rollen gebaseerd toegangs beheer](../../role-based-access-control/overview.md) (RBAC) en Azure Key Vault toegangs beleid.

### <a name="access-model-overview"></a>Overzicht van toegangs modellen

De toegang tot kluizen vindt plaats via twee interfaces of-abonnementen. Deze vlakken zijn het beheer vlak en het gegevens vlak.

- Het *beheer vlak* is waar u Key Vault zelf beheert en het is de interface die wordt gebruikt om kluizen te maken en verwijderen. U kunt ook sleutel kluis eigenschappen lezen en toegangs beleid beheren.
- Met het *gegevens vlak* kunt u werken met de gegevens die zijn opgeslagen in een sleutel kluis. U kunt sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Voor toegang tot een sleutel kluis in een van beide vlieg tuigen moeten alle bellers (gebruikers of toepassingen) worden geverifieerd en geautoriseerd. Beide plannen gebruiken Azure Active Directory (Azure AD) voor verificatie. Voor autorisatie gebruikt het beheer vlak op rollen gebaseerd toegangs beheer (RBAC) en gebruikt het gegevens vlak een Key Vault toegangs beleid.

Het model van één mechanisme voor verificatie voor beide abonnementen heeft verschillende voor delen:

- Organisaties kunnen de toegang centraal beheren voor alle sleutel kluizen in hun organisatie.
- Als een gebruiker deze verlaat, gaan ze onmiddellijk toegang tot alle sleutel kluizen in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in azure AD, zoals om multi-factor Authentication in te scha kelen voor extra beveiliging.

### <a name="managing-administrative-access-to-key-vault"></a>Beheer toegang tot Key Vault beheren

Wanneer u een sleutel kluis maakt in een resource groep, beheert u de toegang met behulp van Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutel kluizen in een resource groep te beheren. U kunt toegang verlenen op een specifiek Scope niveau door de juiste RBAC-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om sleutel kluizen te beheren, wijst u een vooraf gedefinieerde `key vault Contributor` rol toe aan de gebruiker op een specifiek bereik. De volgende Scope niveaus kunnen worden toegewezen aan een RBAC-rol:

- **Abonnement**: een RBAC-rol die is toegewezen op abonnements niveau, is van toepassing op alle resource groepen en resources in dat abonnement.
- **Resource groep**: een RBAC-rol die is toegewezen op het niveau van de resource groep, is van toepassing op alle resources in die resource groep.
- **Specifieke resource**: een RBAC-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutel kluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, kunt u uw eigen rol definiëren. Zie [RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie.

> [!IMPORTANT]
> Als een gebruiker `Contributor` machtigingen heeft voor een sleutel kluis beheer vlak, kan de gebruiker zichzelf toegang verlenen tot het gegevens vlak door een Key Vault toegangs beleid in te stellen. U moet nauw keurig bepalen wie `Contributor` rollen toegang heeft tot uw sleutel kluizen. Zorg ervoor dat alleen geautoriseerde personen uw sleutel kluizen, sleutels, geheimen en certificaten kunnen gebruiken en beheren.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Toegang tot Key Vault gegevens beheren

Key Vault toegangs beleid worden machtigingen afzonderlijk verleend aan sleutels, geheimen of certificaat. U kunt een gebruiker alleen toegang geven tot sleutels en niet op geheimen. Toegangs machtigingen voor sleutels, geheimen en certificaten worden beheerd op het niveau van de kluis.

> [!IMPORTANT]
> Key Vault toegangs beleid biedt geen ondersteuning voor granulaire machtigingen op object niveau, zoals een specifieke sleutel, geheim of certificaat. Wanneer een gebruiker gemachtigd is om sleutels te maken en te verwijderen, kunnen ze deze bewerkingen uitvoeren op alle sleutels in die sleutel kluis.

Als u toegangs beleid wilt instellen voor een sleutel kluis, gebruikt u de [Azure Portal](https://portal.azure.com/), de [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/)of de [rest api's van Key Vault beheer](/rest/api/keyvault/).

U kunt de toegang tot het gegevens vlak beperken met behulp van de [service-eind punten voor virtuele netwerken voor Azure Key Vault](overview-vnet-service-endpoints.md)). U kunt [firewalls en regels voor virtuele netwerken](network-security.md) configureren voor een extra beveiligingslaag.

## <a name="network-access"></a>Netwerktoegang

U kunt de bloot stelling van uw kluizen verminderen door op te geven welke IP-adressen er toegang tot hebben. Met de service-eind punten voor virtuele netwerken voor Azure Key Vault kunt u de toegang tot een opgegeven virtueel netwerk beperken. Met de eind punten kunt u ook de toegang beperken tot een lijst met IPv4-adresbereiken (Internet Protocol versie 4). Gebruikers die verbinding maken met uw sleutel kluis van buiten deze bronnen, krijgen geen toegang.

Nadat de firewall regels van kracht zijn, kunnen gebruikers alleen gegevens van Key Vault lezen wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit is tevens van toepassing voor toegang tot Key Vault vanuit Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutelkluis van Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weergeven als hun clientcomputer niet in de lijst met toegestane clients staat. Dit is ook van invloed op de Key Vault-kiezer door andere Azure-Services. Gebruikers zien mogelijk een lijst met sleutelkluizen, maar geen lijst met sleutels als firewallregels hun clientcomputer weigeren.

Raadpleeg voor meer informatie over Azure Key Vault netwerk adres [service-eind punten voor virtuele netwerken voor Azure Key Vault](overview-vnet-service-endpoints.md))

## <a name="monitoring"></a>Bewaking

Met Key Vault logboek registratie wordt informatie opgeslagen over de activiteiten die zijn uitgevoerd op uw kluis. Key Vault logboeken:

- Alle geverifieerde REST API aanvragen, met inbegrip van mislukte aanvragen
  - Bewerkingen op de sleutel kluis zelf. Tot deze bewerkingen behoren het maken, verwijderen, instellen van toegangs beleid en het bijwerken van sleutel kluis kenmerken, zoals Tags.
  - Bewerkingen van sleutels en geheimen in de sleutelkluis, waaronder:
    - Het maken, wijzigen of verwijderen van die sleutels of geheimen.
    - Het ondertekenen, verifiëren, versleutelen, ontsleutelen, verpakken en uitpakken van sleutels, het ophalen van geheimen en het vermelden van sleutels en geheimen (en hun versies).
- Niet-geverifieerde aanvragen die in een 401-respons resulteren. Voorbeelden daarvan zijn aanvragen die geen Bearer-token hebben, die ongeldig of verlopen zijn, of die een ongeldig token hebben.

Logboek informatie kan worden geopend binnen 10 minuten na de sleutel kluis bewerking. Het is aan u om uw logboeken in uw opslag account te beheren.

- Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
- Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Raadpleeg de [Azure Storage-beveiligings handleiding](../../storage/blobs/security-recommendations.md) voor aanbevelingen voor het veilig beheren van opslag accounts.

## <a name="next-steps"></a>Volgende stappen

- [Virtuele netwerk service-eind punten voor Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)
- [virtuele netwerk service-eind punten voor Azure Key Vault](overview-vnet-service-endpoints.md)
