---
title: De Batch Management .NET-bibliotheek gebruiken voor het beheren van account resources
description: U kunt Azure Batch account resources maken, verwijderen en wijzigen met behulp van de Batch Management .NET-bibliotheek.
ms.topic: article
ms.date: 04/24/2017
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 3cb44b89cacd9e597766ca8df3e4826dfabdb2bd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201641"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Batch-accounts en-quota's beheren met de batch-beheer-client bibliotheek voor .NET

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

U kunt onderhouds overhead in uw Azure Batch-toepassingen verlagen door de [Batch Management .net][api_mgmt_net] -bibliotheek te gebruiken voor het automatiseren van het maken van batch-accounts, verwijderen, sleutel beheer en quotum detectie.

* **Maak en verwijder batch-accounts** binnen een wille keurige regio. Als u als een onafhankelijke software leverancier (ISV) bijvoorbeeld een service voor uw clients hebt waarin elk een apart batch-account voor facturerings doeleinden wordt toegewezen, kunt u het maken en verwijderen van accounts toevoegen aan uw klanten Portal.
* De account sleutels op een programmatische manier **ophalen en opnieuw genereren** voor uw batch-accounts. Dit kan u helpen om te voldoen aan het beveiligings beleid dat periodieke rollover of verval datum van account sleutels afdwingt. Wanneer u meerdere batch-accounts hebt in verschillende Azure-regio's, verhoogt de automatisering van dit rollover proces de efficiëntie van uw oplossing.
* **Controleer de account quota's** en neem de proef versie en de fout bij het bepalen van welke batch-accounts zijn beperkt. Door uw account quota's te controleren voordat u taken start, Pools maakt of reken knooppunten toevoegt, kunt u proactief aanpassen waar of wanneer deze reken bronnen worden gemaakt. U kunt bepalen welke accounts quota moeten overschrijden voordat u extra resources in deze accounts toewijst.
* **Combi neer functies van andere Azure-Services** voor een volledig functionele beheer ervaring, met behulp van Batch Management .net, [Azure Active Directory][aad_about]en de [Azure Resource Manager][resman_overview] samen in dezelfde toepassing. Door deze functies en hun api's te gebruiken, kunt u een wrijvings verificatie-ervaring bieden, de mogelijkheid om resource groepen te maken en te verwijderen en de mogelijkheden die hierboven worden beschreven voor een end-to-end-beheer oplossing.

> [!NOTE]
> Hoewel dit artikel gericht is op het programmatisch beheer van uw batch-accounts, sleutels en quota's, kunt u veel van deze activiteiten uitvoeren met behulp van de [Azure Portal][azure_portal]. Zie [een Azure batch-account maken met behulp van de Azure Portal](batch-account-create-portal.md) en [quota en limieten voor de Azure batch-service](batch-quota-limit.md)voor meer informatie.
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Batch-accounts maken en verwijderen
Zoals gezegd, is een van de primaire functies van de API voor batch beheer het maken en verwijderen van batch-accounts in een Azure-regio. Gebruik hiervoor [BatchManagementClient. account. CreateAsync][net_create] en [DeleteAsync][net_delete], of hun synchrone tegen hangers.

Met het volgende code fragment wordt een account gemaakt, wordt het zojuist gemaakte account opgehaald uit de batch-service en vervolgens verwijderd. In dit code fragment en de andere in dit artikel `batchManagementClient` is een volledig geïnitialiseerd exemplaar van [BatchManagementClient][net_mgmt_client].

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
> Toepassingen die gebruikmaken van de Batch Management .NET-bibliotheek en de BatchManagementClient-klasse, hebben een **service beheerder** of mede **beheerder** toegang tot het abonnement dat eigenaar is van het batch-account dat moet worden beheerd. Zie de sectie Azure Active Directory en het code voorbeeld [AccountManagement][acct_mgmt_sample] voor meer informatie.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Account sleutels ophalen en opnieuw genereren
Primaire en secundaire account sleutels verkrijgen van een batch-account in uw abonnement met behulp van [ListKeysAsync][net_list_keys]. U kunt deze sleutels opnieuw genereren met behulp van [RegenerateKeyAsync][net_regenerate_keys].

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
> U kunt een gestroomlijnde verbindings werk stroom maken voor uw beheer toepassingen. U moet eerst een account sleutel verkrijgen voor het batch-account dat u wilt beheren met [ListKeysAsync][net_list_keys]. Gebruik deze sleutel bij het initialiseren van de [BatchSharedKeyCredentials][net_sharedkeycred] -klasse van de batch .net-bibliotheek, die wordt gebruikt bij het initialiseren van [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure-abonnement en batch-account quota's controleren
Azure-abonnementen en de afzonderlijke Azure-Services, zoals batch, hebben allemaal standaard quota's die het aantal bepaalde entiteiten in de service beperken. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor de standaard Quota's voor Azure-abonnementen. Zie [quota's en limieten voor de Azure batch-service](batch-quota-limit.md)voor de standaard quota's van de batch-service. U kunt deze quota's in uw toepassingen controleren met behulp van de Batch Management .NET-bibliotheek. Zo kunt u toewijzings beslissingen nemen voordat u accounts toevoegt of resources zoals groepen en reken knooppunten.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Een Azure-abonnement voor batch-account quota's controleren
Voordat u een batch-account in een regio maakt, kunt u uw Azure-abonnement controleren om te zien of u een account in die regio kunt toevoegen.

In het onderstaande code fragment gebruiken we eerst [BatchManagementClient. account. ListAsync][net_mgmt_listaccounts] om een verzameling van alle batch-accounts in een-abonnement op te halen. Zodra deze verzameling is opgehaald, bepalen we hoeveel accounts zich in de doel regio bevinden. Vervolgens gebruiken we [BatchManagementClient. abonnementen][net_mgmt_subscriptions] om het batch-account quotum te verkrijgen en te bepalen hoeveel accounts (indien van toepassing) in die regio kunnen worden gemaakt.

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

In het bovenstaande code fragment `creds` is een instantie van [TokenCloudCredentials][azure_tokencreds]. Zie het [AccountManagement][acct_mgmt_sample] -code voorbeeld op github voor een voor beeld van het maken van dit object.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Een batch-account voor reken resource quota's controleren
Voordat u de reken resources in uw batch-oplossing verhoogt, kunt u controleren of de resources die u wilt toewijzen, de quota van het account niet overschrijden. In het onderstaande code fragment worden de quotum gegevens voor het batch-account met de naam afgedrukt `mybatchaccount` . In uw eigen toepassing kunt u deze informatie gebruiken om te bepalen of het account kan omgaan met de extra resources die moeten worden gemaakt.

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
> Hoewel er standaard quota's voor Azure-abonnementen en-services zijn, kunnen veel van deze limieten worden verhoogd door een aanvraag uit te geven in de [Azure Portal][azure_portal]. Zie bijvoorbeeld [quota's en limieten voor de Azure batch-service](batch-quota-limit.md) voor instructies voor het verhogen van uw batch-account quota's.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Azure AD gebruiken met Batch Management .NET

De Batch Management .NET-bibliotheek is een Azure resource provider-client en wordt gebruikt in combi natie met [Azure Resource Manager][resman_overview] om account bronnen programmatisch te beheren. Azure AD is vereist voor de verificatie van aanvragen die worden gedaan via elke client van een Azure-resource provider, met inbegrip van de Batch Management .NET-bibliotheek en via [Azure Resource Manager][resman_overview]. Zie [Azure Active Directory gebruiken om batch-oplossingen te verifiëren](batch-aad-auth.md)voor meer informatie over het gebruik van Azure AD met de Batch Management .net-bibliotheek. 

## <a name="sample-project-on-github"></a>Voorbeeld project op GitHub

Bekijk het [AccountManagement][acct_mgmt_sample] -voorbeeld project op github voor meer informatie over Batch Management .net in actie. De voorbeeld toepassing AccountManagement demonstreert de volgende bewerkingen:

1. Verkrijg een beveiligings token van Azure AD met behulp van [ADAL][aad_adal]. Als de gebruiker nog niet is aangemeld, wordt deze gevraagd naar hun Azure-referenties.
2. Met het beveiligings token dat is verkregen van Azure AD, maakt u een [SubscriptionClient][resman_subclient] om een lijst met abonnementen te zoeken die aan het account zijn gekoppeld. De gebruiker kan een abonnement in de lijst selecteren als deze meer dan één abonnement bevat.
3. Referenties ophalen die zijn gekoppeld aan het geselecteerde abonnement.
4. Maak een [ResourceManagementClient][resman_client] -object met behulp van de referenties.
5. Gebruik een [ResourceManagementClient][resman_client] -object om een resource groep te maken.
6. Gebruik een [BatchManagementClient][net_mgmt_client] -object om verschillende batch-account bewerkingen uit te voeren:
   * Maak een batch-account in de nieuwe resource groep.
   * Haal het zojuist gemaakte account uit de batch-service.
   * De account sleutels voor het nieuwe account afdrukken.
   * Genereer een nieuwe primaire sleutel voor het account.
   * De quotum gegevens voor het account afdrukken.
   * De quotum gegevens voor het abonnement afdrukken.
   * Alle accounts in het abonnement afdrukken.
   * Nieuw gemaakt account verwijderen.
7. Verwijder de resource groep.

Voordat u het zojuist gemaakte batch-account en de resource groep verwijdert, kunt u deze bekijken in de [Azure Portal][azure_portal]:

Als u de voorbeeld toepassing wilt uitvoeren, moet u deze eerst registreren bij uw Azure AD-Tenant in de Azure Portal en machtigingen verlenen voor de Azure Resource Manager-API. Volg de stappen in [Batch Management-oplossingen verifiëren met Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Wat is Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Verificatie Scenario's voor Azure AD"
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
