---
title: Azure Key Vault-beveiliging | Microsoft Documenten
description: Beheer toegangsmachtigingen voor Azure Key Vault, sleutels en geheimen. Dekt het verificatie- en autorisatiemodel voor Key Vault en hoe u uw sleutelkluis beveiligen.
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
ms.openlocfilehash: dbe13b2d1291f212af7da9d1176bc3d90997978b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428964"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-beveiliging

U moet versleutelingssleutels en -geheimen zoals certificaten, verbindingstekenreeksen en wachtwoorden in de cloud beveiligen, zodat u Azure Key Vault gebruikt. Aangezien u gevoelige en bedrijfskritieke gegevens opslaat, moet u stappen ondernemen om de beveiliging van uw kluizen en de gegevens die erin zijn opgeslagen, te maximaliseren. In dit artikel vindt u een aantal concepten waarmee u rekening moet houden bij het ontwerpen van uw Azure Key Vault-beveiliging.

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

Wanneer u een sleutelkluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-tenant van het abonnement. Iedereen die inhoud uit een kluis probeert te beheren of ophalen, moet worden geverifieerd door Azure AD.

- Verificatie bepaalt de identiteit van de beller.
- Autorisatie bepaalt welke bewerkingen de beller kan uitvoeren. Autorisatie in Key Vault maakt gebruik van een combinatie van [RBAC (Role based access control)](../../role-based-access-control/overview.md) en Azure Key Vault access policies.

### <a name="access-model-overview"></a>Overzicht van toegangsmodel

Toegang tot kluizen vindt plaats via twee interfaces of vliegtuigen. Deze vliegtuigen zijn het management vlak en het data vlak.

- Het *beheervliegtuig* is waar u Key Vault zelf beheert en het is de interface die wordt gebruikt om kluizen te maken en te verwijderen. U ook belangrijke kluiseigenschappen lezen en toegangsbeleid beheren.
- Met *het gegevensvlak* u werken met de gegevens die zijn opgeslagen in een sleutelkluis. U sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Om toegang te krijgen tot een sleutelkluis in beide vliegtuigen, moeten alle bellers (gebruikers of toepassingen) worden geverifieerd en geautoriseerd. Beide vlakken gebruiken Azure Active Directory (Azure AD) voor verificatie. Voor autorisatie maakt het beheervlak gebruik van rbac (role-based access control) en het gegevensvlak maakt gebruik van een key vault-toegangsbeleid.

Het model van één mechanisme voor verificatie voor beide vlakken heeft verschillende voordelen:

- Organisaties kunnen de toegang centraal beheren tot alle belangrijke kluizen in hun organisatie.
- Als een gebruiker vertrekt, verliest hij direct de toegang tot alle belangrijke kluizen in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in Azure AD, bijvoorbeeld om meervoudige verificatie in te schakelen voor extra beveiliging

### <a name="managing-administrative-access-to-key-vault"></a>Beheer van administratieve toegang tot Key Vault

Wanneer u een sleutelkluis maakt in een brongroep, beheert u de toegang met Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutelkluizen in een resourcegroep te beheren. U toegang verlenen op een specifiek scopeniveau door de juiste RBAC-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om `key vault Contributor` sleutelkluizen te beheren, wijst u een vooraf gedefinieerde rol toe aan de gebruiker op een specifiek bereik. De volgende scopeniveaus kunnen worden toegewezen aan een RBAC-rol:

- **Abonnement**: Een RBAC-rol die op abonnementsniveau is toegewezen, is van toepassing op alle resourcegroepen en resources binnen dat abonnement.
- **Resourcegroep**: Een RBAC-rol die is toegewezen op het niveau van de resourcegroep, is van toepassing op alle resources in die resourcegroep.
- **Specifieke resource:** een RBAC-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutelkluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, u uw eigen rol definiëren. Zie [RBAC: Ingebouwde rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie.

> [!IMPORTANT]
> Als een `Contributor` gebruiker machtigingen heeft voor een belangrijk vault management vliegtuig, kan de gebruiker zichzelf toegang verlenen tot het gegevensvlak door een Key Vault toegangsbeleid in te stellen. U moet goed controleren `Contributor` wie roltoegang heeft tot uw sleutelkluizen. Zorg ervoor dat alleen geautoriseerde personen toegang hebben tot uw sleutelkluizen, sleutels, geheimen en certificaten en deze beheren.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Toegang tot Key Vault-gegevens beheren

Key Vault-toegangsbeleid verleent machtigingen afzonderlijk voor sleutels, geheimen of certificaat. U een gebruiker alleen toegang verlenen tot sleutels en niet tot geheimen. Toegangsmachtigingen voor sleutels, geheimen en certificaten worden beheerd op het niveau van de kluis.

> [!IMPORTANT]
> Key Vault-toegangsbeleid biedt geen ondersteuning voor gedetailleerde machtigingen op objectniveau, zoals een specifieke sleutel, geheim of certificaat. Wanneer een gebruiker toestemming krijgt om sleutels te maken en te verwijderen, kunnen deze bewerkingen worden uitgevoerd op alle sleutels in die sleutelkluis.

Als u toegangsbeleidsregels voor een sleutelkluis wilt instellen, gebruikt u de [Azure-portal,](https://portal.azure.com/)de [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de [API's voor het sleutelkluisbeheer](/rest/api/keyvault/).

U de toegang tot gegevensvlak beperken door [eindpunten van virtuele netwerkservices voor Azure Key Vault](overview-vnet-service-endpoints.md)te gebruiken). U [firewalls en virtuele netwerkregels](network-security.md) configureren voor een extra beveiligingslaag.

## <a name="network-access"></a>Netwerktoegang

U de blootstelling van uw kluizen verminderen door op te geven welke IP-adressen er toegang toe hebben. Met de eindpunten voor de virtuele netwerkservice voor Azure Key Vault u de toegang tot een opgegeven virtueel netwerk beperken. Met de eindpunten u ook de toegang tot een lijst met iPv4-adresbereiken (internetprotocol versie 4) beperken. Elke gebruiker die verbinding maakt met uw sleutelkluis van buiten deze bronnen, wordt de toegang geweigerd.

Nadat firewallregels van kracht zijn, kunnen gebruikers alleen gegevens uit Key Vault lezen wanneer hun verzoeken afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit geldt ook voor toegang tot Key Vault vanuit de Azure-portal. Hoewel gebruikers vanuit de Azure-portal naar een sleutelkluis kunnen bladeren, kunnen ze mogelijk geen sleutels, geheimen of certificaten vermelden als hun clientmachine niet in de toegestane lijst staat. Dit heeft ook gevolgen voor de Key Vault Picker van andere Azure-services. Gebruikers kunnen mogelijk een lijst met sleutelkluizen zien, maar geen lijstsleutels, als firewallregels hun clientmachine verhinderen.

Voor meer informatie over Azure Key Vault-netwerkadrescontrole [Eindpunten voor virtuele netwerkservice voor Azure Key Vault](overview-vnet-service-endpoints.md))

## <a name="monitoring"></a>Bewaking

Key Vault-logboekregistratie slaat informatie op over de activiteiten die op uw kluis worden uitgevoerd. Key Vault-logboeken:

- Alle geverifieerde REST API-aanvragen, inclusief mislukte aanvragen
  - Operaties op de sleutelkluis zelf. Deze bewerkingen omvatten het maken, verwijderen, het instellen van toegangsbeleid en het bijwerken van belangrijke vault-kenmerken zoals tags.
  - Bewerkingen op sleutels en geheimen in de sleutelkluis, waaronder:
    - Deze sleutels of geheimen maken, wijzigen of verwijderen.
    - Tekenen, verifiëren, versleutelen, ontsleutelen, inpakken en uitpakken van sleutels, geheimen ophalen en sleutels en geheimen (en hun versies) vermelden.
- Niet-geverifieerde aanvragen die in een 401-respons resulteren. Voorbeelden zijn aanvragen die geen token aan toonder hebben, die misvormd zijn of verlopen of die een ongeldig token hebben.

Logging informatie kan worden geopend binnen 10 minuten na de sleutel kluis operatie. Het is aan jou om je logboeken in je opslagaccount te beheren.

- Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
- Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Voor aanbeveling over het veilig beheren van opslagaccounts controleer u de [beveiligingshandleiding voor Azure Storage](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Volgende stappen

- [Eindpunten voor virtuele netwerkservice voor Azure Key Vault](overview-vnet-service-endpoints.md))
- [RBAC: Ingebouwde rollen](../../role-based-access-control/built-in-roles.md)
- [eindpunten voor virtuele netwerkservice voor Azure Key Vault](overview-vnet-service-endpoints.md))
