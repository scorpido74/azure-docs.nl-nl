---
title: De Batchmanagement .NET-bibliotheek gebruiken om accountbronnen te beheren
description: Azure Batch-accountbronnen maken, verwijderen en wijzigen met de Batch Management .NET-bibliotheek.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 79916d769ad8a7228aec8db965c29506ccd78ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023681"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Batchaccounts en quota beheren met de batchbeheerclientbibliotheek voor .NET

> [!div class="op_single_selector"]
> * [Azure-portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

U de onderhoudskosten in uw Azure Batch-toepassingen verlagen door de [Batch Management .NET-bibliotheek][api_mgmt_net] te gebruiken om batchaccountcreatie, verwijdering, sleutelbeheer en quotumdetectie te automatiseren.

* **Batchaccounts** in een regio maken en verwijderen. Als u als onafhankelijke softwareleverancier (ISV) bijvoorbeeld een service levert voor uw klanten, waarbij elk een afzonderlijk Batch-account toegewezen krijgt voor factureringsdoeleinden, u mogelijkheden voor het maken en verwijderen van accounts toevoegen aan uw klantenportal.
* **Accountsleutels** programmatisch ophalen en regenereren voor een van uw Batch-accounts. Dit kan u helpen te voldoen aan beveiligingsbeleid dat periodieke rollover of het verstrijken van accountsleutels afdwingt. Wanneer u meerdere Batch-accounts in verschillende Azure-regio's hebt, verhoogt de automatisering van dit rolloverproces de efficiëntie van uw oplossing.
* **Controleer accountquota** en neem het trial-and-error giswerk uit het bepalen welke Batch-accounts welke limieten hebben. Door uw accountquota te controleren voordat u taken start, groepen maakt of compute nodes toevoegt, u proactief aanpassen waar of wanneer deze rekenbronnen worden gemaakt. U bepalen welke accounts quotaverhogingen vereisen voordat extra resources in die accounts worden toegewezen.
* **Combineer functies van andere Azure-services** voor een volledige beheerervaring: door Batchbeheer .NET, [Azure Active Directory][aad_about]en Azure Resource [Manager][resman_overview] samen te gebruiken in dezelfde toepassing. Door deze functies en hun API's te gebruiken, u een frictieloze verificatie-ervaring bieden, de mogelijkheid om resourcegroepen te maken en te verwijderen en de mogelijkheden die hierboven worden beschreven voor een end-to-end beheeroplossing.

> [!NOTE]
> Hoewel dit artikel zich richt op het programmatisch beheer van uw Batch-accounts, sleutels en quota, u veel van deze activiteiten uitvoeren met behulp van de [Azure-portal.][azure_portal] Zie [Een Azure Batch-account maken met de Azure-portal](batch-account-create-portal.md) en [quota en limieten voor de Azure Batch-service voor](batch-quota-limit.md)meer informatie.
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Batchaccounts maken en verwijderen
Zoals gezegd is een van de belangrijkste functies van de Batch Management API het maken en verwijderen van Batch-accounts in een Azure-regio. Gebruik hiervoor [BatchManagementClient.Account.CreateAsync][net_create] en [DeleteAsync][net_delete]of hun synchrone tegenhangers.

In het volgende codefragment wordt een account gemaakt, wordt het nieuw gemaakte account van de Batch-service opgehaald en wordt het vervolgens verwijderd. In dit fragment en de `batchManagementClient` anderen in dit artikel, is een volledig geïnitialiseerd exemplaar van [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Toepassingen die gebruikmaken van de Batch Management .NET-bibliotheek en de batchmanagementclientklasse vereisen **servicebeheerder** of **medebeheerder** toegang tot het abonnement dat eigenaar is van het Batch-account dat moet worden beheerd. Zie de sectie Azure Active Directory en het voorbeeld van de [AccountManagement-code][acct_mgmt_sample] voor meer informatie.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Accountsleutels ophalen en regenereren
Verkrijg primaire en secundaire accountsleutels van een Batch-account binnen uw abonnement met [ListKeysAsync.][net_list_keys] U deze sleutels regenereren met [RegenerateKeyAsync.][net_regenerate_keys]

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> U een gestroomlijnde verbindingsworkflow maken voor uw beheertoepassingen. Verkrijg eerst een accountsleutel voor het Batch-account dat u wilt beheren met [ListKeysAsync.][net_list_keys] Gebruik deze sleutel vervolgens bij het initialiseren van de batch.NET-bibliotheek [batchsharedkeycredentials,][net_sharedkeycred] die wordt gebruikt bij het initialiseren van [BatchClient.][net_batch_client]
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure-abonnement en batchaccountquota controleren
Azure-abonnementen en de afzonderlijke Azure-services zoals Batch hebben allemaal standaardquota die het aantal bepaalde entiteiten binnen deze abonnementen beperken. Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor de standaardquota voor Azure-abonnementen . Zie Quota en limieten voor [de Azure Batch-service voor](batch-quota-limit.md)de standaardquota van de Batch-service . Met de Batch Management .NET-bibliotheek u deze quota in uw toepassingen controleren. Hiermee u toewijzingsbeslissingen nemen voordat u accounts toevoegt of resources zoals pools en rekenknooppunten berekent.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Een Azure-abonnement controleren op batchaccountquota
Voordat u een Batch-account in een regio maakt, u uw Azure-abonnement controleren om te zien of u een account in die regio toevoegen.

In het onderstaande codefragment gebruiken we [batchmanagementclient.account.listasync][net_mgmt_listaccounts] voor het eerst om een verzameling van alle Batch-accounts te krijgen die deel uitmaken van een abonnement. Zodra we deze verzameling hebben verkregen, bepalen we hoeveel accounts zich in de doelregio bevinden. Vervolgens gebruiken we [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] om het batchaccountquotum te verkrijgen en te bepalen hoeveel accounts (indien aanwezig) in die regio kunnen worden gemaakt.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

In het bovenstaande `creds` fragment is een instantie van [TokenCloudCredentials][azure_tokencreds]. Zie het voorbeeld van de [AccountManagement-code][acct_mgmt_sample] op GitHub om een voorbeeld van het maken van dit object te bekijken.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Een batchaccount controleren op gegevensbronquota
Voordat u de rekenresources in uw batch-oplossing verhoogt, u controleren of de resources die u wilt toewijzen, de quota van het account niet overschrijden. In het onderstaande codefragment drukken we de `mybatchaccount`quotumgegevens af voor het batchaccount met de naam . In uw eigen toepassing u dergelijke informatie gebruiken om te bepalen of het account de extra bronnen kan verwerken die moeten worden gemaakt.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Hoewel er standaardquota zijn voor Azure-abonnementen en -services, kunnen veel van deze limieten worden verhoogd door een aanvraag uit te geven in de [Azure-portal.][azure_portal] Zie bijvoorbeeld [Quota en limieten voor de Azure Batch-service](batch-quota-limit.md) voor instructies voor het verhogen van uw Batch-accountquota.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Azure AD gebruiken met Batchbeheer .NET

De Batch Management .NET-bibliotheek is een Azure-bronproviderclient en wordt samen met [Azure Resource Manager][resman_overview] gebruikt om accountbronnen programmatisch te beheren. Azure AD is vereist om aanvragen te verifiëren die zijn gedaan via een Azure-client voor resourcesprovider, inclusief de Batch Management .NET-bibliotheek en via [Azure Resource Manager.][resman_overview] Zie [Azure Active Directory gebruiken om batchoplossingen te verifiëren voor](batch-aad-auth.md)informatie over het gebruik van Azure AD met de Batch Management .NET-bibliotheek. 

## <a name="sample-project-on-github"></a>Voorbeeldproject op GitHub

Als u Batch Management .NET in actie wilt zien, raadpleegt u het [voorbeeldproject AccountManagement][acct_mgmt_sample] op GitHub. De voorbeeldtoepassing AccountManagement toont de volgende bewerkingen aan:

1. Een beveiligingstoken van Azure AD aanschaffen met [ADAL][aad_adal]. Als de gebruiker nog niet is aangemeld, wordt deze gevraagd om hun Azure-referenties.
2. Met het beveiligingstoken dat is verkregen uit Azure AD, maakt u een [SubscriptionClient][resman_subclient] om Azure op te vragen voor een lijst met abonnementen die aan het account zijn gekoppeld. De gebruiker kan een abonnement in de lijst selecteren als het meer dan één abonnement bevat.
3. Ontvang referenties die zijn gekoppeld aan het geselecteerde abonnement.
4. Maak een [ResourceManagementClient-object][resman_client] met behulp van de referenties.
5. Gebruik een [resourcemanagementclient-object][resman_client] om een resourcegroep te maken.
6. Gebruik een [Object BatchManagementClient][net_mgmt_client] om meerdere batchaccountbewerkingen uit te voeren:
   * Maak een Batch-account in de nieuwe resourcegroep.
   * Haal het nieuw gemaakte account op van de Batch-service.
   * Druk de accountsleutels voor het nieuwe account af.
   * Regenereren van een nieuwe primaire sleutel voor het account.
   * Druk de quotumgegevens voor het account af.
   * Druk de quotumgegevens voor het abonnement af.
   * Print alle accounts binnen het abonnement.
   * Nieuw aangemaakt account verwijderen.
7. Verwijder de brongroep.

Voordat u de nieuw gemaakte Batch-account en resourcegroep verwijderde, u deze bekijken in de [Azure-portal:][azure_portal]

Als u de voorbeeldtoepassing wilt uitvoeren, moet u deze eerst registreren met uw Azure AD-tenant in de Azure-portal en machtigingen verlenen aan de Azure Resource Manager API. Volg de stappen die worden aangeboden in [Batchmanagementoplossingen verifiëren met Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Wat is Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Verificatiescenario's voor Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Toepassingen integreren met Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/management/overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
