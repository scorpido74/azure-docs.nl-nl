---
title: Toegangs beheer voor Azure Managed HSM
description: Toegangs machtigingen voor door Azure beheerde HSM en sleutels beheren. Behandelt het verificatie-en autorisatie model voor beheerde HSM en hoe u uw Hsm's kunt beveiligen.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a21d0db383e8c563f0b187061a95ac818dd2a4f0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995367"
---
# <a name="managed-hsm-access-control"></a>Managed HSM Access Control

> [!NOTE]
> Key Vault resource provider ondersteunt twee resource typen: **kluizen** en **beheerde hsm's**. Toegangs beheer dat in dit artikel wordt beschreven, is alleen van toepassing op **beheerde hsm's**. Zie voor meer informatie over toegangs beheer voor beheerde HSM [toegang bieden tot Key Vault sleutels, certificaten en geheimen met een op rollen gebaseerd toegangs beheer van Azure](../general/rbac-guide.md).

Azure Key Vault beheerde HSM is een Cloud service die versleutelings sleutels beveiligt. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, moet u de toegang tot uw beheerde Hsm's beveiligen door alleen geautoriseerde toepassingen en gebruikers toegang te geven tot de service. Dit artikel bevat een overzicht van het beheerde HSM-toegangs beheer model. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw beheerde Hsm's kunt beveiligen.

## <a name="access-control-model"></a>Model voor toegangs beheer

Toegang tot een beheerde HSM wordt beheerd via twee interfaces: het **beheer vlak** en het **gegevens vlak**. Het beheer vlak is waar u de HSM zelf beheert. Bewerkingen in dit vlak zijn onder andere het maken en verwijderen van beheerde Hsm's en het ophalen van beheerde HSM-eigenschappen. Het gegevens vlak is waar u werkt met de gegevens die zijn opgeslagen in een beheerde HSM. Dit zijn versleutelings sleutels met HSM-back-ups. U kunt sleutels toevoegen, verwijderen, wijzigen en gebruiken voor het uitvoeren van cryptografische bewerkingen, het beheren van roltoewijzingen voor het beheren van de toegang tot de sleutels, het maken van een volledige HSM-back-up, het herstellen van een volledige back-up en het beheren van het beveiligings domein vanuit de gegevenslaag interface.

Voor toegang tot een beheerde HSM in een van beide vlieg tuigen moeten alle bellers de juiste verificatie en autorisatie hebben. Met verificatie wordt de identiteit van de aanroeper bepaald. Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Een aanroeper kan bestaan uit een van de [beveiligings-principals](../../role-based-access-control/overview.md#security-principal) die zijn gedefinieerd in azure Active Directory-gebruiker, groep, Service-Principal of beheerde identiteit.

Beide plannen gebruiken Azure Active Directory voor verificatie. Voor verificatie gebruiken ze verschillende systemen als volgt:
- Het beheer vlak maakt gebruik van Azure op rollen gebaseerd toegangs beheer--Azure RBAC--een autorisatie systeem dat is gebouwd op Azure Azure Resource Manager 
- Het gegevens vlak maakt gebruik van een beheerde RBAC (beheerde HSM Local RBAC): een autorisatie systeem dat is geïmplementeerd en afgedwongen op het niveau van het beheerde HSM.

Wanneer een beheerde HSM wordt gemaakt, biedt de aanvrager ook een lijst met beheerders van gegevens vlak (alle [beveiligings-principals](../../role-based-access-control/overview.md#security-principal) worden ondersteund). Alleen deze beheerders hebben toegang tot het beheerde HSM-gegevens vlak voor het uitvoeren van belang rijke bewerkingen en het beheren van roltoewijzingen (beheerde HSM Local RBAC).

Het machtigings model voor beide plannen maakt gebruik van dezelfde syntaxis (RBAC), maar ze worden afgedwongen op verschillende niveaus en roltoewijzingen gebruiken verschillende bereiken. Het beheer vlak RBAC wordt afgedwongen door Azure Resource Manager terwijl het data-vlak RBAC wordt afgedwongen door de beheerde HSM zelf.

> [!IMPORTANT]
> Het verlenen van een Security Principal Management-vlak toegang tot een beheerde HSM heeft geen toegang tot het gegevens vlak om toegang te krijgen tot sleutels of roltoewijzingen voor gegevenslaag die worden beheerd HSM Local RBAC). Deze isolatie is zo ontworpen dat onbedoelde uitbrei ding van bevoegdheden die invloed hebben op de toegang tot sleutels die zijn opgeslagen in een beheerde HSM wordt voor komen

Zo kan een abonnements beheerder (omdat ze de machtiging ' Inzender ' hebben voor alle resources in het abonnement) een beheerde HSM verwijderen in hun abonnement, maar als ze geen gegevens vlak toegang hebben via beheerde HSM Local RBAC, hebben ze geen toegang tot sleutels of kunnen ze geen roltoewijzing beheren in de beheerde HSM om zichzelf of anderen toegang tot het gegevens vlak te verlenen.

## <a name="azure-active-directory-authentication"></a>Verificatie via Azure Active Directory

Wanneer u een beheerde HSM maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure Active Directory Tenant van het abonnement. Alle bellers in beide abonnementen moeten worden geregistreerd in deze Tenant en moeten worden geverifieerd om toegang te krijgen tot de beheerde HSM.

De toepassing wordt geverifieerd met Azure Active Directory voordat u een van beide vlieg tuigen aanroept. De toepassing kan elke [ondersteunde verificatie methode](../../active-directory/develop/authentication-scenarios.md) gebruiken op basis van het toepassings type. De toepassing verkrijgt een token voor een resource in het vlak om toegang te krijgen. De resource is een eind punt in het beheer-of gegevens vlak, gebaseerd op de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API aanvraag naar het beheerde HSM-eind punt. Bekijk voor meer informatie de [volledige verificatie stroom](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Het gebruik van één verificatie mechanisme voor beide abonnementen heeft verschillende voor delen:

- Organisaties kunnen de toegang centraal beheren voor alle beheerde Hsm's in hun organisatie.
- Als een gebruiker deze verlaat, gaan ze onmiddellijk toegang tot alle beheerde Hsm's in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in Azure Active Directory, zoals om multi-factor Authentication in te scha kelen voor extra beveiliging.

## <a name="resource-endpoints"></a>Resource-eind punten

Beveiligings-principals hebben toegang tot de abonnementen via eind punten. De toegangs controle voor de twee abonnementen werkt afzonderlijk. Als u een toepassing toegang wilt verlenen voor het gebruik van sleutels in een beheerde HSM, geeft u toegang tot het gegevens vlak door beheerde HSM lokale RBAC te gebruiken. Als u een gebruiker toegang wilt verlenen tot een beheerde HSM-resource om de beheerde Hsm's te maken, lezen, verwijderen, verplaatsen en andere eigenschappen en Tags bewerken, gebruikt u Azure RBAC.

De volgende tabel bevat de eind punten voor de beheer-en gegevens abonnementen.

| Toegangs &nbsp; vlak | Eindpunten voor toegang | Operations | Mechanisme voor toegangsbeheer |
| --- | --- | --- | --- |
| Beheerlaag | **Wereldwijd:**<br> management.azure.com:443<br> | Beheerde Hsm's maken, lezen, bijwerken, verwijderen en verplaatsen<br>Beheerde HSM-Tags instellen | Azure RBAC |
| Gegevenslaag | **Wereldwijd:**<br> &lt;HSM-name &gt; . Vault.Azure.net:443<br> | **Sleutels**: ontsleutelen, versleutelen,<br> uitpakken, terugloop, controleren, ondertekenen, ophalen, weer geven, bijwerken, maken, importeren, verwijderen, back-up, herstellen, opschonen<br/><br/> Rol van gegevenslaag **-beheer (beheerde HSM Local RBAC) * * *: roldefinities weer geven, rollen toewijzen, roltoewijzingen verwijderen, aangepaste rollen <br/> <br/> definiëren** Back-ups maken/herstellen: back-up maken **, herstellen, <br/> <br/> status back-up/herstel bewerkingen **beveiligings domein * *: downloaden en uploaden van beveiligings domein | Beheerde HSM lokale RBAC |
|||||
## <a name="management-plane-and-azure-rbac"></a>Beheer vlak en Azure RBAC

In het beheer vlak gebruikt u Azure RBAC om de bewerkingen te autoriseren die een aanroeper kan uitvoeren. In het RBAC-model heeft elk Azure-abonnement een exemplaar van Azure Active Directory. U verleent toegang aan gebruikers, groepen en toepassingen vanuit deze map. Toegang is verleend om resources te beheren in het Azure-abonnement dat gebruikmaakt van het Azure Resource Manager-implementatie model. Als u toegang wilt verlenen, gebruikt u de [Azure Portal](https://portal.azure.com/), de [Azure cli](../../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de [Azure Resource Manager rest-api's](https://msdn.microsoft.com/library/azure/dn906885.aspx).

U maakt een sleutel kluis in een resource groep en beheert de toegang met behulp van Azure Active Directory. U verleent gebruikers of groepen de mogelijkheid om de sleutel kluizen in een resource groep te beheren. U verleent de toegang op een specifiek Scope niveau door de juiste RBAC-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om sleutel kluizen te beheren, wijst u een vooraf gedefinieerde `key vault Contributor` rol toe aan de gebruiker op een specifiek bereik. De volgende Scope niveaus kunnen worden toegewezen aan een RBAC-rol:

- **Beheer groep**: een RBAC-rol die is toegewezen op abonnements niveau, is van toepassing op alle abonnementen in die beheer groep.
- **Abonnement**: een RBAC-rol die is toegewezen op abonnements niveau, is van toepassing op alle resource groepen en resources in dat abonnement.
- **Resource groep**: een RBAC-rol die is toegewezen op het niveau van de resource groep, is van toepassing op alle resources in die resource groep.
- **Specifieke resource**: een RBAC-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutel kluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, kunt u uw eigen rol definiëren. Zie [RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie.

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Gegevens vlak en beheerde HSM lokale RBAC

U verleent een beveiligingsprincipal toegang om specifieke sleutel bewerkingen uit te voeren door een rol toe te wijzen. Voor elke roltoewijzing moet u een rol en bereik opgeven waarop de toewijzing van toepassing is. Voor beheerde HSM Local RBAC twee scopes zijn beschikbaar.

- **"/" of "/Keys"**: het bereik van de HSM-niveau. Beveiligings-principals waaraan een rol in dit bereik is toegewezen, kunnen de bewerkingen uitvoeren die in de rol zijn gedefinieerd voor alle objecten (sleutels) in de beheerde HSM.
- **"/Keys/ &lt; key-name &gt; "**: bereik op sleutel niveau. Beveiligings-principals waaraan een rol in dit bereik is toegewezen, kunnen de bewerkingen uitvoeren die in deze rol zijn gedefinieerd voor alle versies van de opgegeven sleutel.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is beheerde HSM?](overview.md)voor een aan de slag-zelf studie voor een beheerder.
- Zie [beheerde HSM Local RBAC](role-management.md) voor een zelf studie over het beheer van rollen.
- Zie [Managed HSM logging](logging.md)(Engelstalig) voor meer informatie over het gebruik van logboek registratie voor de beheerde HSM-logboek registratie.
