---
title: Beheerde identiteiten voor Azure-resources
description: Een overzicht van de beheerde identiteiten voor Azure-resources.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f5446e836a65c6d40c2cc6703757670988593bd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896596"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Wat zijn beheerde identiteiten voor Azure-resources?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Een veelvoorkomende uitdaging bij het bouwen van cloud-apps is het beheren van de referenties in uw code voor verificatie bij cloudservices. Het is belangrijk dat de referenties veilig worden bewaard. In het ideale geval worden de referenties nooit weergegeven op werkstations van ontwikkelaars en niet ingecheckt in broncodebeheer. Azure Key Vault biedt een manier voor het veilig opslaan van referenties, geheimen en andere sleutels, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen.

Dit probleem wordt opgelost met de functie Beheerde identiteiten voor Azure-resources in Azure Active Directory (Azure AD). De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in de code.

De functie Beheerde identiteiten voor Azure-resources is gratis bij een abonnement op Azure AD. Er zijn geen extra kosten.

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

## <a name="terminology"></a>Terminologie

De volgende termen worden in de beheerde identiteiten gebruikt voor de documentatieset van Azure-resources:

- **Client-id**: een unieke id die door Azure AD wordt gegenereerd. Deze wordt tijdens de eerste keer inrichten gekoppeld aan een toepassing en service-principal.
- **Principal-id**: de object-id van het service-principal-object van uw beheerde identiteit. Deze wordt gebruikt om op basis van rollen toegang te verlenen tot een Azure-resource.
- **Azure Instance Metadata Service (IMDS)** : een REST-eindpunt dat toegankelijk is voor alle IaaS-VM's die worden gemaakt via Azure Resource Manager. Het eindpunt is beschikbaar via een bekend, niet-routeerbaar IP-adres (169.254.169.254) dat alleen kan worden benaderd vanuit de virtuele machine.

## <a name="how-does-the-managed-identities-for-azure-resources-work"></a>Hoe werken de beheerde identiteiten voor Azure-resources?

Er zijn twee typen beheerde identiteit:

- Een **door het systeem toegewezen beheerde identiteit** wordt rechtstreeks op een Azure-service-exemplaar ingeschakeld. Wanneer de identiteit is ingeschakeld, wordt een identiteit voor het service-exemplaar in de Azure AD-tenant gemaakt, dat wordt vertrouwd door het abonnement van het service-exemplaar. Nadat de identiteit is gemaakt, worden de referenties op het exemplaar ingericht. De levenscyclus van een door het systeem toegewezen identiteit is rechtstreeks gekoppeld aan het Azure-service-exemplaar waarop de identiteit is ingeschakeld. Als het exemplaar wordt verwijderd, ruimt Azure automatisch de referenties en de identiteit in Azure AD op.
- Een **door de gebruiker toegewezen beheerde identiteit** wordt gemaakt als een zelfstandige Azure-resource. Via een productieproces maakt Azure een identiteit in de Azure AD-tenant, die wordt vertrouwd door het gebruikte abonnement. Nadat de identiteit is gemaakt, kan deze worden toegewezen aan een of meer Azure-service-exemplaren. De levenscyclus van een door de gebruiker toegewezen identiteit wordt afzonderlijk beheerd van de levenscyclus van de Azure Service-exemplaren waaraan de identiteit is toegewezen.

Interne beheerde identiteiten zijn service-principals van een speciaal type, die zijn vergrendeld om alleen te worden gebruikt met Azure-resources. Wanneer de beheerde identiteit wordt verwijderd, wordt de bijbehorende service-principal automatisch verwijderd.

De code kan gebruikmaken van een beheerde identiteit om toegangstokens aan te vragen voor services die ondersteuning bieden voor Azure AD-verificatie. Azure zorgt voor het implementeren van de referenties die worden gebruikt door het service-exemplaar.

In het volgende diagram ziet u hoe beheerde service-identiteiten samenwerken met virtuele machines (VM's) van Azure:

![Beheerde service-identiteiten en Azure-VM's](media/overview/msi-vm-vmextension-imds-example.png)

|  Eigenschap    | Door het systeem toegewezen beheerde identiteit | Door een gebruiker toegewezen beheerde identiteit |
|------|----------------------------------|--------------------------------|
| Zelf |  Gemaakt als onderdeel van een Azure-resource (bijvoorbeeld een virtuele machine van Azure of Azure App Service) | Gemaakt als een zelfstandige Azure-resource |
| Levenscyclus | Gedeelde levens cyclus met de Azure-resource waarmee de beheerde identiteit wordt gemaakt. <br/> Wanneer de bovenliggende resource wordt verwijderd, wordt ook de beheerde identiteit verwijderd. | Onafhankelijke levens cyclus. <br/> Moet expliciet worden verwijderd. |
| Delen tussen Azure-resources | Kan niet worden gedeeld. <br/> Deze kan alleen worden gekoppeld aan één Azure-resource. | Kan worden gedeeld <br/> Dezelfde door de gebruiker toegewezen beheerde identiteit kan worden gekoppeld aan meer dan één Azure-resource. |
| Algemene use cases | Werk belastingen die zijn opgenomen in één Azure-resource <br/> Werk belastingen waarvoor u onafhankelijke identiteiten nodig hebt. <br/> Bijvoorbeeld een toepassing die op één virtuele machine wordt uitgevoerd | Workloads die worden uitgevoerd op meerdere resources en die één identiteit kunnen delen. <br/> Workloads waarvoor vooraf autorisatie is vereist voor een beveiligde bron als onderdeel van een inrichtings stroom. <br/> Werk belastingen waarbij resources regel matig worden gerecycled, maar de machtigingen moeten consistent blijven. <br/> Bijvoorbeeld een werk belasting waarbij meerdere virtuele machines toegang moeten hebben tot dezelfde resource |

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Hoe een door het systeem toegewezen beheerde identiteit samenwerkt met een Azure-VM

1. Azure Resource Manager ontvangt een aanvraag voor het inschakelen van de door het systeem toegewezen beheerde identiteit op een VM.

2. Azure Resource Manager maakt een service-principal in Azure AD voor de identiteit van de VM. De service-principal wordt gemaakt in de Azure AD-tenant die wordt vertrouwd door het abonnement.

3. Azure Resource Manager configureert de identiteit op de virtuele machine door het Azure Instance Metadata Service Identity-eind punt bij te werken met de client-ID en het certificaat van de Service-Principal.

4. Als de VM een identiteit heeft, gebruikt u de informatie van de service-principal om de VM toegang te verlenen tot Azure-resources. Voor het aanroepen van Azure Resource Manager gebruikt u op rollen gebaseerd toegangsbeheer (RBAC) in Azure AD om de juiste rol aan de VM-service-principal toe te wijzen. Als u Key Vault wilt aanroepen, geeft u de code toegang tot het specifieke geheim of de specifieke sleutel in Key Vault.

5. Uw code die op de virtuele machine wordt uitgevoerd, kan een token aanvragen bij het Azure instance meta data service-eind punt dat alleen toegankelijk is vanuit de VM: `http://169.254.169.254/metadata/identity/oauth2/token`
    - De resourceparameter geeft de service op waarnaar het token wordt verzonden. Gebruik `resource=https://management.azure.com/` om bij Azure Resource Manager te verificatie uit te voeren.
    - De API-versieparameter specificeert de IMDS-versie, gebruik api-version=2018-02-01 of hoger.

6. Er wordt een aanroep uitgevoerd naar Azure AD om een toegangstoken aan te vragen (zoals beschreven in stap 5) met behulp van de client-id en het certificaat dat in stap 3 is geconfigureerd. Azure AD retourneert een JWT-toegangstoken (JSON Web Token).

7. Uw code verzendt het toegangstoken bij een aanroep naar een service die Azure AD-verificatie ondersteunt.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Hoe een door een gebruiker toegewezen beheerde identiteit samenwerkt met een Azure-VM

1. Azure Resource Manager ontvangt een aanvraag voor het maken van een door de gebruiker toegewezen beheerde identiteit.

2. Azure Resource Manager maakt een service-principal in Azure AD voor de door de gebruiker toegewezen beheerde identiteit. De service-principal wordt gemaakt in de Azure AD-tenant die wordt vertrouwd door het abonnement.

3. Azure Resource Manager ontvangt een aanvraag om de door de gebruiker toegewezen beheerde identiteit op een virtuele machine te configureren en werkt het Azure Instance Metadata Service Identity-eind punt bij met de door de gebruiker toegewezen beheerde ID-client-ID en het certificaat van de Service-Principal.

4. Nadat de door de gebruiker toegewezen beheerde identiteit is gemaakt, gebruikt u de informatie van de service-principal om de identiteit toegang te verlenen tot Azure-resources. Voor het aanroepen van Azure Resource Manager gebruikt u RBAC in Azure AD om de juiste rol aan de service-principal van de door de gebruiker toegewezen identiteit toe te wijzen. Als u Key Vault wilt aanroepen, geeft u de code toegang tot het specifieke geheim of de specifieke sleutel in Key Vault.

   > [!Note]
   > U kunt deze stap ook vóór stap 3 uitvoeren.

5. Uw code die op de virtuele machine wordt uitgevoerd, kan een token aanvragen bij het Azure Instance Metadata Service Identity-eind punt dat alleen toegankelijk is vanuit de VM: `http://169.254.169.254/metadata/identity/oauth2/token`
    - De resourceparameter geeft de service op waarnaar het token wordt verzonden. Gebruik `resource=https://management.azure.com/` om bij Azure Resource Manager te verificatie uit te voeren.
    - De client-id-parameter bevat de identiteit waarvoor het token wordt aangevraagd. Deze waarde is vereist om ambiguïteit op te heffen wanneer meer dan een door de gebruiker toegewezen identiteit aanwezig is op één VM.
    - De parameter voor de API-versie geeft de versie van Azure Instance Metadata Service op. Gebruik `api-version=2018-02-01` of hoger.

6. Er wordt een aanroep uitgevoerd naar Azure AD om een toegangstoken aan te vragen (zoals beschreven in stap 5) met behulp van de client-id en het certificaat dat in stap 3 is geconfigureerd. Azure AD retourneert een JWT-toegangstoken (JSON Web Token).
7. Uw code verzendt het toegangstoken bij een aanroep naar een service die Azure AD-verificatie ondersteunt.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Hoe gebruik ik beheerde identiteiten voor Azure-resources?

In deze zelfstudies leert u hoe u gebruik kunt maken van beheerde identiteiten om toegang te krijgen tot verschillende Azure-resources.

> [!NOTE]
> Bekijk de cursus [Beheerde identiteiten implementeren voor Microsoft Azure-resources](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) voor meer informatie over beheerde identiteiten, inclusief een gedetailleerd video-overzicht van verschillende ondersteunde scenario’s.

Informatie over het gebruik van een beheerde identiteit met een Windows-VM:

* [Toegang tot Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Toegang tot Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Toegang tot Azure SQL](tutorial-windows-vm-access-sql.md)
* [Toegang tot Azure Storage met behulp van een toegangssleutel](tutorial-vm-windows-access-storage.md)
* [Toegang tot Azure Storage met behulp van handtekeningen voor gedeelde toegang](tutorial-windows-vm-access-storage-sas.md)
* [Toegang tot een niet-Azure-resource met Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Informatie over het gebruik van een beheerde identiteit met een Linux-VM:

* [Toegang Azure Container Registry](../../container-registry/container-registry-authentication-managed-identity.md)
* [Toegang tot Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Toegang tot Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Toegang tot Azure Storage met behulp van een toegangssleutel](tutorial-linux-vm-access-storage.md)
* [Toegang tot Azure Storage met behulp van handtekeningen voor gedeelde toegang](tutorial-linux-vm-access-storage-sas.md)
* [Toegang tot een niet-Azure-resource met Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Informatie over het gebruik van een beheerde identiteit met andere Azure-services:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Azure Container Registry taken](../../container-registry/container-registry-tasks-authentication-managed-identity.md)
* [Azure Event Hubs](../../event-hubs/authenticate-managed-identity.md)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Kubernetes Service](/azure/aks/use-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)


## Welke Azure-services bieden ondersteuning voor de functie?<a name="which-azure-services-support-managed-identity"></a>

Beheerde identiteiten voor Azure-resources kunnen worden gebruikt voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie. Zie [Services die ondersteuning bieden voor beheerde identiteiten voor Azure-resources](services-support-msi.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende snelstartgidsen om aan de slag te gaan met de functie Beheerde identiteiten voor Azure-resources:

* [Een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Resource Manager](tutorial-windows-vm-access-arm.md)
* [Een door het Linux-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Resource Manager](tutorial-linux-vm-access-arm.md)
