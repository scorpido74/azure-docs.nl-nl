---
title: Azure Analysis Services-taken automatiseren met serviceprincipals | Microsoft Documenten
description: Meer informatie over het maken van een serviceprincipal voor het automatiseren van azure analysis services-beheertaken.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc163de9a7fb46d62f4bc2983e040e68bbf9231c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266141"
---
# <a name="automation-with-service-principals"></a>Automatisering met service-principals

Service-principals zijn een Azure Active Directory-toepassingsresource die u in uw tenant maakt om onbeheerde bewerkingen op resource- en serviceniveau uit te voeren. Ze zijn een uniek type *gebruikersidentiteit* met een toepassings-id en wachtwoord of certificaat. Een serviceprincipal heeft alleen de machtigingen die nodig zijn om taken uit te voeren die zijn gedefinieerd door de rollen en machtigingen waarvoor deze is toegewezen. 

In Analysis Services worden serviceprincipals gebruikt met Azure Automation, PowerShell unattended mode, aangepaste clienttoepassingen en webapps om veelvoorkomende taken te automatiseren. Zo kunnen servers, het implementeren van modellen, het vernieuwen van gegevens, het opschalen/omlaag en pauzeren/hervatten allemaal worden geautomatiseerd met behulp van serviceprincipals. Machtigingen worden toegewezen aan serviceprincipals via rollidmaatschap, net als reguliere Azure AD UPN-accounts.

Analysis Services ondersteunt ook bewerkingen die worden uitgevoerd door beheerde identiteiten met behulp van serviceprincipals. Zie [Beheerde identiteiten voor Azure-bronnen](../active-directory/managed-identities-azure-resources/overview.md) en [Azure-services die Azure AD-verificatie ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)voor meer informatie.  

## <a name="create-service-principals"></a>Service-principals maken
 
Serviceprincipals kunnen worden gemaakt in de Azure-portal of met PowerShell. Voor meer informatie zie:

[Serviceprincipal maken - Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Service-principal maken - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Referenties en certificaatelementen opslaan in Azure Automation

Servicehoofdreferenties en certificaten kunnen veilig worden opgeslagen in Azure Automation voor runbook-bewerkingen. Voor meer informatie zie:

[Referentie-elementen in Azure Automation](../automation/automation-credentials.md)   
[Verbindingsassets in Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Serviceprincipals toevoegen aan de rol serverbeheerder

Voordat u een serviceprincipal gebruiken voor analysis services-serverbeheerbewerkingen, moet u deze toevoegen aan de rol serverbeheerders. Zie Een [serviceprincipal toevoegen aan de serverbeheerderrol](analysis-services-addservprinc-admins.md)voor meer informatie.

## <a name="service-principals-in-connection-strings"></a>Serviceprincipals in verbindingstekenreeksen

Service principal appID en wachtwoord of certificaat kunnen worden gebruikt in verbindingstekenreeksen die vrijwel hetzelfde zijn als een UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule" />Az.AnalysisServices-module gebruiken

Gebruik `Connect-AzAccount` cmdlet bij gebruik van een serviceprincipal voor resourcemanagementbewerkingen met de [az.analysisservices-module.](/powershell/module/az.analysisservices) 

In het volgende voorbeeld worden appID en een wachtwoord gebruikt om besturingsvlakbewerkingen uit te voeren voor synchronisatie naar alleen-lezen replica's en omhoog/uit te schalen:

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

#### <a name="using-sqlserver-module"></a>SQLServer-module gebruiken

In het volgende voorbeeld worden appID en een wachtwoord gebruikt om een vernieuwingsbewerking voor een modeldatabase uit te voeren:

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

Wanneer u verbinding maakt met clienttoepassingen en web-apps, versie 15.0.2 [en](analysis-services-data-providers.md) hogere installeerbare pakketten van NuGet-ondersteuningsserviceprincipals in verbindingstekenreeksen met behulp van de volgende syntaxis: `app:AppID` en wachtwoord of `cert:thumbprint`. 

In het volgende `appID` voorbeeld `password` en een worden gebruikt om een vernieuwingsbewerking voor modelgegevens uit te voeren:

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
[Aanmelden bij Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Een serviceprincipal toevoegen aan de serverbeheerderrol](analysis-services-addservprinc-admins.md)   
