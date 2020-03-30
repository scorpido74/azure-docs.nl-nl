---
title: De Azure Storage-bronprovider gebruiken om toegang te krijgen tot beheerbronnen
description: De Azure Storage-bronprovider is een service die toegang biedt tot beheerbronnen voor Azure Storage. U de Azure Storage-bronprovider gebruiken om bronnen zoals opslagaccounts, privéeindpunten en accounttoegangssleutels te maken, bij te werken, te beheren en te verwijderen.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972350"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>De Azure Storage-bronprovider gebruiken om toegang te krijgen tot beheerbronnen

Azure Resource Manager is de implementatie- en beheersservice voor Azure. De Azure Storage-bronprovider is een service die is gebaseerd op Azure Resource Manager en die toegang biedt tot beheerbronnen voor Azure Storage. U de Azure Storage-bronprovider gebruiken om bronnen zoals opslagaccounts, privéeindpunten en accounttoegangssleutels te maken, bij te werken, te beheren en te verwijderen. Zie overzicht azure [resource manager](/azure/azure-resource-manager/resource-group-overview)voor meer informatie over Azure Resource Manager.

U de Azure Storage-bronprovider gebruiken om acties uit te voeren, zoals het maken of verwijderen van een opslagaccount of het verkrijgen van een lijst met opslagaccounts in een abonnement. Als u aanvragen wilt autoriseren tegen de Azure Storage-bronprovider, gebruikt u Azure Active Directory (Azure AD). In dit artikel wordt beschreven hoe u machtigingen toewijst aan beheerbronnen en wordt ingegeven aan voorbeelden die laten zien hoe u aanvragen indienen tegen de Azure Storage-bronprovider.

## <a name="management-resources-versus-data-resources"></a>Beheerresources versus gegevensbronnen

Microsoft biedt twee REST-API's voor het werken met Azure Storage-bronnen. Deze API's vormen de basis van alle acties die u uitvoeren tegen Azure Storage. Met de Azure Storage REST API u werken met gegevens in uw opslagaccount, waaronder blob-, wachtrij-, bestands- en tabelgegevens. Met de REST API voor Azure Storage-bronnen u werken met het opslagaccount en de bijbehorende resources.

Een aanvraag die blobgegevens leest of schrijft, vereist andere machtigingen dan een aanvraag die een beheerbewerking uitvoert. RBAC biedt fijnmazige controle over machtigingen voor beide typen resources. Wanneer u een RBAC-rol toewijst aan een beveiligingsprincipal, moet u ervoor zorgen dat u begrijpt welke machtigingen die principal zal krijgen. Zie [Ingebouwde rollen voor Azure-resources voor](../../role-based-access-control/built-in-roles.md)een gedetailleerde referentie waarin wordt beschreven welke acties zijn gekoppeld aan elke ingebouwde RBAC-rol.

Azure Storage ondersteunt het gebruik van Azure AD om aanvragen te autoriseren tegen Blob- en Queue-opslag. Zie [Toegang tot blobs en wachtrijen beheren met Active Directory](storage-auth-aad.md)voor informatie over RBAC-rollen voor blob- en wachtrijgegevensbewerkingen.

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Beheermachtigingen toewijzen met op rollen gebaseerd toegangscontrolebeheer (RBAC)

Elk Azure-abonnement heeft een bijbehorende Azure Active Directory die gebruikers, groepen en toepassingen beheert. Een gebruiker, groep of toepassing wordt ook wel een beveiligingsprincipal genoemd in de context van het [Microsoft-identiteitsplatform.](/azure/active-directory/develop/) U toegang verlenen tot bronnen in een abonnement op een beveiligingsprincipal die is gedefinieerd in de Active Directory met behulp van rbac (Role-based access control).

Wanneer u een RBAC-rol toewijst aan een beveiligingsprincipal, geeft u ook het bereik aan waarop de machtigingen die door de rol worden verleend, van kracht zijn. Voor beheerbewerkingen u een rol toewijzen op het niveau van het abonnement, de resourcegroep of het opslagaccount. U een RBAC-rol toewijzen aan een beveiligingsprincipal met behulp van de [Azure-portal,](https://portal.azure.com/)de [Azure CLI-hulpprogramma's,](../../cli-install-nodejs.md) [PowerShell](/powershell/azureps-cmdlets-docs)of de REST API van azure [storage-bronprovider](/rest/api/storagerp).

Zie [Wat is rbac (Role-based access control) voor Azure-resources en](../../role-based-access-control/overview.md) [Classic-abonnementsbeheerdersrollen, Azure RBAC-rollen en Azure AD-beheerdersrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie over RBAC.

### <a name="built-in-roles-for-management-operations"></a>Ingebouwde rollen voor beheerbewerkingen

Azure biedt ingebouwde rollen waarmee machtigingen worden verleend voor oproepbeheerbewerkingen. Azure Storage biedt ook ingebouwde rollen die specifiek zijn voor gebruik met de Azure Storage-bronprovider.

Ingebouwde rollen die machtigingen verlenen voor opslagbeheerbewerkingen, bevatten de rollen die in de volgende tabel worden beschreven:

|    RBAC-rol    |    Beschrijving    |    Inclusief toegang tot accountsleutels?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Eigenaar** | Kan alle opslagbronnen en toegang tot bronnen beheren.  | Ja, biedt machtigingen voor het bekijken en regenereren van de opslagaccountsleutels. |
| **Inzender**  | Kan alle opslagresources beheren, maar kan niet worden toegewezen aan resources. | Ja, biedt machtigingen voor het bekijken en regenereren van de opslagaccountsleutels. |
| **Lezer** | Kan informatie over het opslagaccount weergeven, maar kan de accountsleutels niet weergeven. | Nee. |
| **Inzender voor opslagaccounts** | Kan het opslagaccount beheren, informatie krijgen over de brongroepen en resources van het abonnement en implementaties van abonnementsbronnen maken en beheren. | Ja, biedt machtigingen voor het bekijken en regenereren van de opslagaccountsleutels. |
| **Beheerder voor gebruikerstoegang** | Kan de toegang tot het opslagaccount beheren.   | Ja, hiermee kan een beveiligingsprincipal alle machtigingen aan zichzelf en anderen toewijzen. |
| **Inzender voor virtuele machines** | Kan virtuele machines beheren, maar niet het opslagaccount waarmee ze zijn verbonden.   | Ja, biedt machtigingen voor het bekijken en regenereren van de opslagaccountsleutels. |

De derde kolom in de tabel geeft aan of de ingebouwde rol de **Microsoft.Storage/storageAccounts/listkeys/action**ondersteunt. Met deze actie worden machtigingen verleend voor het lezen en regenereren van de opslagaccountsleutels. Machtigingen voor toegang tot Azure Storage Management resources bevatten ook geen machtigingen voor toegang tot gegevens. Als een gebruiker echter toegang heeft tot de accountsleutels, kan hij de accountsleutels gebruiken om toegang te krijgen tot Azure Storage-gegevens via de autorisatie van Gedeelde sleutel.

### <a name="custom-roles-for-management-operations"></a>Aangepaste rollen voor beheerbewerkingen

Azure ondersteunt ook het definiëren van aangepaste RBAC-rollen voor toegang tot beheerbronnen. Zie Aangepaste rollen voor [Azure-resources voor](../../role-based-access-control/custom-roles.md)meer informatie over aangepaste rollen.

## <a name="code-samples"></a>Codevoorbeelden

Zie de volgende voorbeelden voor codevoorbeelden die laten zien hoe u beheerbewerkingen van de Azure Storage-beheerbibliotheken autoriseert en aanroept:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager versus klassieke implementaties

Het implementatiemodel van Resource Manager en het klassieke implementatiemodel zijn twee verschillende manieren voor het implementeren en beheren van uw Azure-oplossingen. Microsoft raadt aan het implementatiemodel azure resource manager te gebruiken wanneer u een nieuw opslagaccount maakt. Indien mogelijk raadt Microsoft u ook aan bestaande klassieke opslagaccounts opnieuw te maken met het Resource Manager-model. Hoewel u een opslagaccount maken met behulp van het klassieke implementatiemodel, is het klassieke model minder flexibel en zal het uiteindelijk worden afgeschaft.

Zie [Resourcebeheer en klassieke implementatie](../../azure-resource-manager/management/deployment-models.md)voor meer informatie over Azure-implementatiemodellen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
- [Wat is op rollen gebaseerd toegangsbeheer (RBAC) voor Azure-resources?](../../role-based-access-control/overview.md)
- [Schaalbaarheidsdoelen voor de Azure Storage-bronprovider](scalability-targets-resource-provider.md)
