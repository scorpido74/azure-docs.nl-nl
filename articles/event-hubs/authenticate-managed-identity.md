---
title: Een beheerde identiteit verifiëren met Azure Active Directory
description: Dit artikel bevat informatie over het verifiëren van een beheerde identiteit met Azure Active Directory om toegang te krijgen tot Azure Event Hubs-resources
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992833"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Event Hubs bronnen
Azure Event Hubs ondersteunt Azure Active Directory (Azure AD)-verificatie met [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md). Beheerde identiteiten voor Azure-resources kunnen toegang tot Event Hubs resources toestaan met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in azure Virtual Machines (Vm's), functie-apps, Virtual Machine Scale Sets en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.

In dit artikel wordt beschreven hoe u toegang kunt verlenen tot een Event Hub met behulp van een beheerde identiteit van een Azure-VM.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen
Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om Event Hubs-resources van uw virtuele machine te autoriseren, moet u eerst beheerde identiteiten voor Azure-resources inschakelen op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Machtigingen verlenen aan een beheerde identiteit in azure AD
Als u een aanvraag wilt indienen voor het Event Hubs service van een beheerde identiteit in uw toepassing, moet u eerst RBAC-instellingen (op rollen gebaseerd toegangs beheer) configureren voor die beheerde identiteit. Met Azure Event Hubs worden RBAC-rollen gedefinieerd die machtigingen bevatten voor het verzenden en lezen van Event Hubs. Wanneer de RBAC-rol is toegewezen aan een beheerde identiteit, krijgt de beheerde identiteit toegang tot Event Hubs gegevens in het juiste bereik.

Zie [verifiëren met Azure Active Directory voor toegang tot Event hubs bronnen](authorize-access-azure-active-directory.md)voor meer informatie over het toewijzen van RBAC-rollen.

## <a name="use-event-hubs-with-managed-identities"></a>Event Hubs met beheerde identiteiten gebruiken
Als u Event Hubs met beheerde identiteiten wilt gebruiken, moet u de identiteit van de rol en het juiste bereik toewijzen. De procedure in deze sectie maakt gebruik van een eenvoudige toepassing die wordt uitgevoerd onder een beheerde identiteit en die toegang heeft tot Event Hubs resources.

Hier gebruiken we een voor beeld-webtoepassing die wordt gehost in [Azure app service](https://azure.microsoft.com/services/app-service/). Zie [een ASP.net core web-app maken in azure](../app-service/app-service-web-get-started-dotnet.md) voor stapsgewijze instructies voor het maken van een webtoepassing

Wanneer de toepassing is gemaakt, voert u de volgende stappen uit: 

1. Ga naar **instellingen** en selecteer **identiteit**. 
1. Selecteer de **status** die moet worden **ingeschakeld**. 
1. Selecteer **Opslaan** om de instelling op te slaan. 

    ![Beheerde identiteit voor een web-app](./media/authenticate-managed-identity/identity-web-app.png)

Zodra u deze instelling hebt ingeschakeld, wordt er een nieuwe service-identiteit gemaakt in uw Azure Active Directory (Azure AD) en geconfigureerd in de App Service host.

Wijs deze service-identiteit nu toe aan een rol in het vereiste bereik in uw Event Hubs resources.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toewijzen met behulp van de Azure Portal
Als u een rol wilt toewijzen aan Event Hubs resources, gaat u naar die resource in de Azure Portal. Geef de Access Control (IAM)-instellingen voor de resource weer en volg deze instructies voor het beheren van roltoewijzingen:

> [!NOTE]
> Met de volgende stappen wordt een service-identiteits functie toegewezen aan uw Event Hubs-naam ruimten. U kunt dezelfde stappen volgen om een rollen bereik toe te wijzen aan een Event Hubs resource. 

1. Ga in het Azure Portal naar uw Event Hubs naam ruimte en geef het **overzicht** voor de naam ruimte weer. 
1. Selecteer **Access Control (IAM)** in het menu links om de instellingen voor toegangs beheer voor de Event hub weer te geven.
1.  Selecteer het tabblad roltoewijzingen om de lijst met roltoewijzingen weer te geven.
3.  Selecteer **toevoegen** om een nieuwe rol toe te voegen.
4.  Selecteer op de pagina **roltoewijzing toevoegen** de Event hubs rollen die u wilt toewijzen. Zoek vervolgens naar de service-identiteit die u hebt geregistreerd om de rol toe te wijzen.
    
    ![Pagina roltoewijzing toevoegen](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Selecteer **Opslaan**. De identiteit waaraan u de rol hebt toegewezen, wordt weer gegeven onder die rol. In de volgende afbeelding ziet u bijvoorbeeld dat de service-identiteit de Event Hubs gegevens eigenaar heeft.
    
    ![Identiteit die aan een rol is toegewezen](./media/authenticate-managed-identity/role-assigned.png)

Zodra u de rol hebt toegewezen, heeft de webtoepassing toegang tot de Event Hubs resources onder het gedefinieerde bereik. 

### <a name="test-the-web-application"></a>De webtoepassing testen
U kunt nu de webtoepassing starten en uw browser naar de aspx-voorbeeld pagina wijzen. U kunt de voor beeld-webtoepassing vinden waarmee gegevens worden verzonden en ontvangen van Event Hubs-resources in de [github opslag plaats](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Installeer het meest recente pakket van [Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)en begin met het verzenden naar en ontvangen van gegevens van Event hubs met behulp van de EventHubClient, zoals wordt weer gegeven in de volgende code: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>Volgende stappen
- Down load het voor [beeld](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) van github.
- Zie het volgende artikel voor meer informatie over beheerde identiteiten voor Azure-resources: [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
- Raadpleeg de volgende verwante artikelen:
    - [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met behulp van Azure Active Directory](authenticate-application.md)
    - [Aanvragen voor Azure Event Hubs verifiëren met behulp van hand tekeningen voor gedeelde toegang](authenticate-shared-access-signature.md)
    - [Toegang tot Event Hubs resources autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Toegang tot Event Hubs resources autoriseren met behulp van hand tekeningen voor gedeelde toegang](authorize-access-shared-access-signature.md)