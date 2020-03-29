---
title: DNS-zones en recordsets maken met de .NET SDK
titleSuffix: Azure DNS
description: Ga in dit leerpad aan de slag met het maken van DNS-zones en recordsets in Azure DNS met behulp van de .NET SDK.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.openlocfilehash: c497209e456ff838786edaa19e46ebc5c1858d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938866"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>DNS-zones en recordsets maken met de .NET SDK

U bewerkingen automatiseren om DNS-zones, recordsets en records te maken, te verwijderen of bij te werken met behulp van de DNS SDK met de .NET DNS Management-bibliotheek. Een volledig Visual Studio project is [hier beschikbaar.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Een hoofdaccount voor service maken

Programmatische toegang tot Azure-bronnen wordt meestal verleend via een speciaal account in plaats van uw eigen gebruikersreferenties. Deze speciale accounts worden 'service principal' accounts genoemd. Als u het Azure DNS SDK-voorbeeldproject wilt gebruiken, moet u eerst een hoofdaccount voor services maken en de juiste machtigingen toewijzen.

1. Volg [deze instructies](../active-directory/develop/howto-authenticate-service-principal-powershell.md) om een serviceprincipal-account te maken (het Azure DNS SDK-voorbeeldproject gaat uit van verificatie op basis van wachtwoord.)
2. Maak een resourcegroep[(zo).](../azure-resource-manager/templates/deploy-portal.md)
3. Gebruik Azure RBAC om de machtigingen van het servicehoofdaccount 'DNS Zone Contributor' toe te kennen aan de brongroep[(zo](../role-based-access-control/role-assignments-portal.md).)
4. Als u het voorbeeldproject Azure DNS SDK gebruikt, bewerkt u het bestand 'program.cs' als volgt:

   * Voeg de juiste `tenantId`waarden `clientId` in voor het `secret` (ook wel account-ID) genoemd (servicehoofdaccountwachtwoord) en `subscriptionId` zoals gebruikt in stap 1.
   * Voer de naam van de resourcegroep in die in stap 2 is gekozen.
   * Voer een DNS-zonenaam van uw keuze in.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-pakketten en naamruimte-declaraties

Als u de Azure DNS .NET SDK wilt gebruiken, moet u het **NuGet-pakket azure DNS-beheerbibliotheek** en andere vereiste Azure-pakketten installeren.

1. In **Visual Studio,** open een project of nieuw project.
2. Ga naar **Tools** **>** **NuGet Package Manager** **>** **Beheer NuGet-pakketten voor oplossing...**.
3. Klik **op Bladeren**, schakel het selectievakje **Prerelease opnemen** in en typ **Microsoft.Azure.Management.Dns** in het zoekvak.
4. Selecteer het pakket en klik op **Installeren** om het toe te voegen aan uw Visual Studio-project.
5. Herhaal het bovenstaande proces om ook de volgende pakketten te installeren: **Microsoft.Rest.ClientRuntime.Azure.Authentication** en **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Naamruimtedeclaraties toevoegen

De volgende naamruimtedeclaratie toevoegen

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>De DNS-beheerclient initialiseren

De `DnsManagementClient` bevat de methoden en eigenschappen die nodig zijn voor het beheer van DNS-zones en recordsets.  De volgende code logt in bij `DnsManagementClient` het hoofdaccount van de service en maakt een object.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Een DNS-zone maken of bijwerken

Als u een DNS-zone wilt maken, wordt eerst een object 'Zone' gemaakt om de DNS-zoneparameters te bevatten. Omdat DNS-zones niet zijn gekoppeld aan een specifieke regio, is de locatie ingesteld op 'globaal'. In dit voorbeeld wordt ook een [Azure Resource Manager 'tag'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) aan de zone toegevoegd.

Als u de zone in Azure DNS wilt maken of bijwerken, `DnsManagementClient.Zones.CreateOrUpdateAsyc` wordt het zoneobject met de zoneparameters doorgegeven aan de methode.

> [!NOTE]
> DnsManagementClient ondersteunt drie werkwijzen: synchroon ('CreateOrUpdate'), asynchroon ('CreateOrUpdateAsync') of asynchroon met toegang tot het HTTP-antwoord ('CreateOrUpdateWithHttpMessagesAsync').  U een van deze modi kiezen, afhankelijk van uw toepassingsbehoeften.

Azure DNS ondersteunt optimistische gelijktijdigheid, genaamd [Etags](dns-getstarted-create-dnszone.md). In dit voorbeeld wordt in Azure DNS opgemaakt om '*' op te geven voor de kop 'Als-geen-match' dat azure DNS een DNS-zone moet maken als deze nog niet bestaat.  De aanroep mislukt als er al een zone met de opgegeven naam bestaat in de opgegeven resourcegroep.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>DNS-recordsets en -records maken

DNS-records worden beheerd als een recordset. Een recordset is een set records met dezelfde naam en recordtype binnen een zone.  De recordsetnaam is relatief ten opzichte van de zonenaam, niet de volledig gekwalificeerde DNS-naam.

Als u een recordset wilt maken of bijwerken, wordt `DnsManagementClient.RecordSets.CreateOrUpdateAsync`een object 'RecordSet'-parameters gemaakt en doorgegeven aan . Net als bij DNS-zones zijn er drie werkwijzen: synchroon ('CreateOrUpdate'), asynchroon ('CreateOrUpdateAsync') of asynchroon met toegang tot het HTTP-antwoord ('CreateOrUpdateWithHttpMessagesAsync').

Net als bij DNS-zones omvatten bewerkingen op recordsets ondersteuning voor optimistische gelijktijdigheid.  In dit voorbeeld wordt de recordset altijd gemaakt omdat 'If-Match' of 'If-None-Match' zijn opgegeven.  Deze aanroep overschrijft alle bestaande recordsets met dezelfde naam en recordtype in deze DNS-zone.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Zones en recordsets oppakken

De `DnsManagementClient.Zones.Get` `DnsManagementClient.RecordSets.Get` en methoden halen afzonderlijke zones en recordsets op. RecordSets worden geïdentificeerd aan de andere tekst, naam en de zone- en resourcegroep waarin ze bestaan. Zones worden geïdentificeerd aan de andere naam en de resourcegroep waarin ze bestaan.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Een bestaande recordset bijwerken

Als u een bestaande DNS-recordset wilt bijwerken, haalt u eerst de recordset op, werkt u vervolgens de inhoud van de recordset bij en verzendt u de wijziging.  In dit voorbeeld specificeren we de 'Etag' van de opgehaalde record die is ingesteld in de parameter 'If-Match'. De aanroep mislukt als een gelijktijdige bewerking het record dat in de tussentijd is ingesteld, heeft gewijzigd.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lijstzones en recordsets

Als u zones wilt aanbieden, gebruikt u de methoden *DnsManagementClient.Zones.List.List,* die ondersteuning bieden voor het aanbieden van alle zones in een bepaalde resourcegroep of alle zones in een bepaald Azure-abonnement (tussen resourcegroepen.) Als u recordsets wilt aanbieden, gebruikt u *Methoden dnsmanagementclient.recordsets.list...,* die ondersteuning bieden voor het aanbieden van alle recordsets in een bepaalde zone of alleen die recordsets van een bepaald type.

Houd er rekening mee wanneer aanbiedingszones en recordsets worden weergegeven dat de resultaten kunnen worden weergegeven.  In het volgende voorbeeld ziet u hoe u de pagina's met resultaten herhalen. (Een kunstmatig kleine paginagrootte van '2' wordt gebruikt om paging te forceren; in de praktijk moet deze parameter worden weggelaten en de standaard paginagrootte wordt gebruikt.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Volgende stappen

Download het [Azure DNS .NET SDK-voorbeeldproject](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), dat verdere voorbeelden bevat over het gebruik van de Azure DNS .NET SDK, inclusief voorbeelden voor andere DNS-recordtypen.
