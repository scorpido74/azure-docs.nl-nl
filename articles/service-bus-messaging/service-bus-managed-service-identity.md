---
title: Beheerde identiteiten voor Azure-resources met Azure Service Bus | Microsoft Docs
description: Beheerde identiteiten gebruiken voor Azure-resources met Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: a671b2ddd3cfa1237b6d843369e78233960f1c14
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013189"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Azure Service Bus bronnen
[Identiteiten voor een Azure-resources beheerd](../active-directory/managed-identities-azure-resources/overview.md) is een cross-Azure-functie die u kunt maken van een veilige identiteit die is gekoppeld aan de implementatie waarmee uw toepassingscode wordt uitgevoerd. Daarna kunt u die identiteit koppelen met access-control-rollen die aangepaste machtigingen voor toegang tot specifieke Azure-resources die uw toepassing nodig heeft.

Met beheerde identiteiten beheert het Azure-platform deze runtime-identiteit. U hoeft niet voor het opslaan en toegang tot sleutels in uw toepassingscode of de configuratie voor de identiteit zelf, of voor de resources die u nodig hebt om toegang te beveiligen. Een Service Bus client-app die wordt uitgevoerd in een Azure App Service toepassing of op een virtuele machine met ingeschakelde beheerde entiteiten voor Azure-bronnen ondersteuning, hoeft geen SAS-regels en sleutels of andere toegangs tokens te verwerken. De client-app heeft alleen het eindpunt adres van de Service Bus Messa ging-naam ruimte nodig. Wanneer de app verbinding maakt, Service Bus de context van de beheerde entiteit koppelen aan de client in een bewerking die verderop in dit artikel wordt weer gegeven. Zodra deze is gekoppeld aan een beheerde identiteit, kan uw Service Bus-client alle geautoriseerde bewerkingen uitvoeren. Autorisatie wordt verleend door een beheerde entiteit te koppelen aan Service Bus rollen. 

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een Service Bus entiteit, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een resource een proces dat uit twee stappen bestaat. 

 1. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2,0-token geretourneerd. 
 1. Vervolgens wordt het token door gegeven als onderdeel van een aanvraag aan de Service Bus-service om toegang tot de opgegeven bron te autoriseren.

De verificatie stap vereist dat een toepassings aanvraag een OAuth 2,0-toegangs token bevat tijdens runtime. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure-VM, een schaalset voor virtuele machines of een Azure function-app, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de resources. Zie [toegang verifiëren voor Azure service bus resources met Azure Active Directory en beheerde identiteiten voor Azure-resources](service-bus-managed-service-identity.md)voor meer informatie over het verifiëren van aanvragen die door een beheerde identiteit worden door gegeven aan service bus service. 

Voor de autorisatie stap moeten een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure Service Bus biedt RBAC-rollen die sets machtigingen voor Service Bus bronnen omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal, bepalen de machtigingen die de principal heeft. Zie voor meer informatie over het toewijzen van RBAC-rollen aan Azure Service Bus [Ingebouwde RBAC-rollen voor Azure service bus](#built-in-rbac-roles-for-azure-service-bus). 

Systeem eigen toepassingen en webtoepassingen die aanvragen indienen bij Service Bus kunnen ook worden geautoriseerd met Azure AD. In dit artikel leest u hoe u een toegangs token aanvraagt en hoe u het kunt gebruiken om aanvragen voor Service Bus-resources te autoriseren. 


## <a name="assigning-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangs rechten
Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [op rollen gebaseerd toegangs beheer (RBAC)](../role-based-access-control/overview.md). Azure Service Bus definieert een set ingebouwde RBAC-rollen die algemene sets machtigingen bevatten die worden gebruikt voor toegang tot Service Bus entiteiten, en u kunt ook aangepaste rollen definiëren voor toegang tot de gegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep of de Service Bus naam ruimte. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een beheerde identiteit voor Azure-resources zijn.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Ingebouwde RBAC-rollen voor Azure Service Bus
Voor Azure Service Bus is het beheer van naam ruimten en alle gerelateerde resources via de Azure Portal en de Azure Resource Management-API al beveiligd met behulp van het RBAC-model ( *op rollen gebaseerd toegangs beheer* ). Azure biedt de onderstaande ingebouwde RBAC-rollen voor het verlenen van toegang tot een Service Bus naam ruimte:

- [Azure Service Bus gegevens eigenaar](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Hiermee wordt gegevens toegang tot Service Bus naam ruimte en de bijbehorende entiteiten (wacht rijen, onderwerpen, abonnementen en filters) ingeschakeld
- [Azure Service Bus gegevens afzender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Gebruik deze rol om toegang te geven tot Service Bus naam ruimte en de bijbehorende entiteiten.
- [Azure Service Bus gegevens ontvanger](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Gebruik deze rol om toegang te krijgen tot Service Bus naam ruimte en de bijbehorende entiteiten. 

## <a name="resource-scope"></a>Bron bereik 
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u het bereik van toegang dat de beveiligingsprincipal moet hebben. Aanbevolen procedures bepalen dat het altijd het beste is om alleen het smalle mogelijke bereik toe te kennen.

In de volgende lijst worden de niveaus beschreven waarmee u toegang tot Service Bus resources kunt bereiken, te beginnen met het smalle bereik:

- **Wachtrij**, **onderwerp**of **abonnement**: De roltoewijzing is van toepassing op de specifieke Service Bus entiteit. Op dit moment biedt de Azure Portal geen ondersteuning voor het toewijzen van gebruikers/groepen/beheerde identiteiten aan Service Bus RBAC-rollen op abonnements niveau. 
- **Naam ruimte service bus**: Roltoewijzing omvat de volledige topologie van Service Bus onder de naam ruimte en aan de Consumer-groep die eraan is gekoppeld.
- **Resourcegroep**: De roltoewijzing is van toepassing op alle Service Bus resources onder de resource groep.
- **Abonnement**: De roltoewijzing is van toepassing op alle Service Bus resources in alle resource groepen in het abonnement.

> [!NOTE]
> Houd er rekening mee dat de toewijzing van RBAC-rollen tot vijf minuten kan duren. 

Zie voor meer informatie over hoe ingebouwde rollen worden gedefinieerd [begrijpen functie definities](../role-based-access-control/role-definitions.md#management-and-data-operations). Zie voor meer informatie over het maken van aangepaste RBAC-rollen [aangepaste rollen maken voor op rollen gebaseerd Azure-Access Control](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen
Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om Service Bus-resources van uw virtuele machine te autoriseren, moet u eerst beheerde identiteiten voor Azure-resources inschakelen op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Machtigingen verlenen aan een beheerde identiteit in azure AD
Als u een aanvraag voor de Service Bus-service wilt machtigen vanuit een beheerde identiteit in uw toepassing, moet u eerst instellingen voor op rollen gebaseerde toegangs beheer (RBAC) configureren voor die beheerde identiteit. Azure Service Bus worden RBAC-rollen gedefinieerd met machtigingen voor het verzenden en lezen van Service Bus. Wanneer de RBAC-rol is toegewezen aan een beheerde identiteit, wordt aan de beheerde identiteit toegang verleend tot Service Bus entiteiten op het juiste bereik.

Zie [verifiëren en autoriseren met Azure Active Directory voor toegang tot Service Bus resources](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)voor meer informatie over het toewijzen van RBAC-rollen.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus met beheerde identiteiten gebruiken voor Azure-resources
Als u Service Bus met beheerde identiteiten wilt gebruiken, moet u de identiteit van de rol en het juiste bereik toewijzen. De procedure in deze sectie maakt gebruik van een eenvoudige toepassing die wordt uitgevoerd onder een beheerde identiteit en die toegang heeft tot Service Bus resources.

Hier gebruiken we een voor beeld-webtoepassing die wordt gehost in [Azure app service](https://azure.microsoft.com/services/app-service/). Zie [een ASP.net core web-app maken in azure](../app-service/app-service-web-get-started-dotnet.md) voor stapsgewijze instructies voor het maken van een webtoepassing

Wanneer de toepassing is gemaakt, voert u de volgende stappen uit: 

1. Ga naar **instellingen** en selecteer **identiteit**. 
1. Selecteer de **status** die moet worden **ingeschakeld**. 
1. Selecteer **Opslaan** om de instelling op te slaan. 

    ![Beheerde identiteit voor een web-app](./media/service-bus-managed-service-identity/identity-web-app.png)

Zodra u deze instelling hebt ingeschakeld, wordt er een nieuwe service-identiteit gemaakt in uw Azure Active Directory (Azure AD) en geconfigureerd in de App Service host.

Wijs deze service-identiteit nu toe aan een rol in het vereiste bereik in uw Service Bus resources.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toewijzen met behulp van de Azure Portal
Als u een rol aan een Service Bus naam ruimte wilt toewijzen, gaat u naar de naam ruimte in de Azure Portal. Geef de Access Control (IAM)-instellingen voor de resource weer en volg deze instructies voor het beheren van roltoewijzingen:

> [!NOTE]
> Met de volgende stappen wordt een service-identiteits functie toegewezen aan uw Service Bus-naam ruimten. U kunt dezelfde stappen volgen om een rol toe te wijzen aan andere ondersteunde bereiken (resource groep en-abonnement). 
> 
> [Maak een service bus Messa ging-naam ruimte](service-bus-create-namespace-portal.md) als u er geen hebt. 

1. Ga in het Azure Portal naar uw Service Bus naam ruimte en geef het **overzicht** voor de naam ruimte weer. 
1. Selecteer **Access Control (IAM)** in het menu links om instellingen voor toegangs beheer voor de naam ruimte service bus weer te geven.
1.  Selecteer het tabblad roltoewijzingen om de lijst met roltoewijzingen weer te geven.
3.  Selecteer **toevoegen** om een nieuwe rol toe te voegen.
4.  Selecteer op de pagina **roltoewijzing toevoegen** de Azure service bus rollen die u wilt toewijzen. Zoek vervolgens naar de service-identiteit die u hebt geregistreerd om de rol toe te wijzen.
    
    ![Pagina roltoewijzing toevoegen](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Selecteer **Opslaan**. De identiteit waaraan u de rol hebt toegewezen, wordt weer gegeven onder die rol. In de volgende afbeelding ziet u bijvoorbeeld dat de service-identiteit de Azure Service Bus gegevens eigenaar heeft.
    
    ![Identiteit die aan een rol is toegewezen](./media/service-bus-managed-service-identity/role-assigned.png)

Zodra u de rol hebt toegewezen, heeft de webtoepassing toegang tot de Service Bus entiteiten onder het gedefinieerde bereik. 

### <a name="run-the-app"></a>De app uitvoeren

Wijzig nu de standaard pagina van de ASP.NET-toepassing die u hebt gemaakt. U kunt de code van de webtoepassing van [deze github-opslag plaats](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)gebruiken.  

De pagina default. aspx is uw landings pagina. De code vindt u in het Default.aspx.cs-bestand. Het resultaat is een minimale webtoepassing met enkele invoer velden en met de knoppen **verzenden** en **ontvangen** die verbinding maken met Service Bus om berichten te verzenden of te ontvangen.

Houd er rekening mee hoe de [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) -object is geïnitialiseerd. In plaats van de tokenprovider Shared Access Token (SAS), de code maakt u een token-provider voor de beheerde identiteit met de `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` aanroepen. Er zijn dus geen geheimen om te behouden en te gebruiken. De stroom van de beheerde identiteits context naar Service Bus en de autorisatie-Handshake worden automatisch verwerkt door de token provider. Het is een eenvoudiger model dan het gebruik van SAS.

Nadat u deze wijzigingen aanbrengt, publiceren en de toepassing wordt uitgevoerd. U kunt eenvoudig de juiste publicatie gegevens verkrijgen door een publicatie profiel te downloaden en vervolgens te importeren in Visual Studio:

![Publicatieprofiel ophalen](./media/service-bus-managed-service-identity/msi3.png)
 
Als u berichten wilt verzenden of ontvangen, voert u de naam van de naam ruimte en de naam van de entiteit die u hebt gemaakt in. Klik vervolgens op **verzenden** of **ontvangen**.


> [!NOTE]
> - De beheerde identiteit werkt alleen in de Azure-omgeving, op app-Services, virtuele Azure-machines en schaal sets. Voor .NET-toepassingen is de bibliotheek micro soft. Azure. Services. AppAuthentication, die wordt gebruikt door het Service Bus NuGet-pakket, een abstractie van dit protocol en wordt een lokale ontwikkel ervaring ondersteund. Met deze bibliotheek kunt u uw code ook lokaal op uw ontwikkel computer testen met behulp van uw gebruikers account uit Visual Studio, Azure CLI 2,0 of Active Directory geïntegreerde verificatie. Zie [service-to-service-verificatie voor Azure Key Vault met .net](../key-vault/service-to-service-authentication.md)voor meer informatie over de lokale ontwikkelings opties voor deze bibliotheek.  
> 
> - Beheerde identiteiten werken momenteel niet met App Service implementatie sleuven.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
