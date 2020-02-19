---
title: Azure Analysis Services-taken automatiseren met Service-principals | Microsoft Docs
description: Meer informatie over het maken van een service-principal voor het automatiseren van Azure Analysis Services beheer taken.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc163de9a7fb46d62f4bc2983e040e68bbf9231c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461671"
---
# <a name="automation-with-service-principals"></a>Automatisering met service-principals

Service-principals zijn een Azure Active Directory-toepassingsresource die u in uw tenant maakt om onbeheerde bewerkingen op resource- en serviceniveau uit te voeren. Ze zijn een uniek type *gebruikers identiteit* met een toepassings-id en wacht woord of certificaat. Een service-principal heeft alleen de machtigingen die nodig zijn om taken uit te voeren die zijn gedefinieerd door de rollen en machtigingen waarvoor deze is toegewezen. 

In Analysis Services worden service-principals gebruikt met Azure Automation, de Power Shell-modus voor installatie zonder toezicht, aangepaste client toepassingen en web-apps voor het automatiseren van algemene taken. Bijvoorbeeld: servers inrichten, modellen implementeren, gegevens vernieuwen, omhoog/omlaag schalen en onderbreken/hervatten kunnen allemaal worden geautomatiseerd met Service-principals. Machtigingen worden toegewezen aan service-principals via het lidmaatschap van de rol, net als bij gewone UPN-accounts voor Azure AD.

Analysis Services biedt ook ondersteuning voor bewerkingen die worden uitgevoerd door beheerde identiteiten met Service-principals. Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) en Azure- [Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)voor meer informatie.  

## <a name="create-service-principals"></a>Service-principals maken
 
Service-principals kunnen worden gemaakt in de Azure Portal of met behulp van Power shell. Raadpleeg voor meer informatie:

Een [Service-Principal maken-Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Service-principal maken - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Referentie-en certificaat assets opslaan in Azure Automation

Referenties van de Service-Principal en certificaten kunnen veilig worden opgeslagen in Azure Automation voor runbook-bewerkingen. Raadpleeg voor meer informatie:

[Referentie-assets in Azure Automation](../automation/automation-credentials.md)   
[Verbindingsassets in Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Service-principals toevoegen aan de server beheerdersrol

Voordat u een service-principal voor Analysis Services server beheer bewerkingen kunt gebruiken, moet u deze toevoegen aan de rol Server Administrators. Zie [een Service-Principal toevoegen aan de rol Server beheerder](analysis-services-addservprinc-admins.md)voor meer informatie.

## <a name="service-principals-in-connection-strings"></a>Service-principals in verbindings reeksen

De Service-Principal-appID en het wacht woord of-certificaat kunnen worden gebruikt in verbindings reeksen die veel hetzelfde zijn als een UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />de module AZ. AnalysisServices gebruiken

Gebruik `Connect-AzAccount`-cmdlet als u een Service-Principal gebruikt voor resource beheer bewerkingen met de module [AZ. AnalysisServices](/powershell/module/az.analysisservices) . 

In het volgende voor beeld worden appID en een wacht woord gebruikt voor het uitvoeren van beheer bewerkingen voor synchronisatie met alleen-lezen replica's en omhoog/omlaag schalen:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>De module SQLServer gebruiken

In het volgende voor beeld worden appID en een wacht woord gebruikt voor het uitvoeren van een vernieuwings bewerking van een model database:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO en ADOMD 

Bij het maken van verbinding met client toepassingen en web apps, [AMO en ADOMD-client bibliotheken](analysis-services-data-providers.md) versie 15.0.2 en hogere Installeer bare pakketten van NuGet ondersteunings service-principals in verbindings reeksen met de volgende syntaxis: `app:AppID` en wacht woord of `cert:thumbprint`. 

In het volgende voor beeld worden `appID` en een `password` gebruikt voor het uitvoeren van een vernieuwings bewerking voor een model database:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Volgende stappen
[Meld u aan met Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Een Service-Principal toevoegen aan de rol Server beheerder](analysis-services-addservprinc-admins.md)   
