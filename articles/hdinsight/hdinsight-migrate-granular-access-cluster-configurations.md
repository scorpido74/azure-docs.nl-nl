---
title: Op basis van granulaire rollentoegang Azure HDInsight-clusterconfiguraties
description: Meer informatie over de wijzigingen die nodig zijn als onderdeel van de migratie naar gedetailleerde functiegebaseerde toegang voor HDInsight-clusterconfiguraties.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: f1fdb9dffbe06430ea7e3eb9339e23f5239e4e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310829"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migreren naar gedetailleerde, op rollen gebaseerde toegang voor clusterconfiguraties

We introduceren een aantal belangrijke veranderingen ter ondersteuning van meer fijnmazige op rollen gebaseerde toegang tot gevoelige informatie. Als onderdeel van deze wijzigingen kan enige actie vereist zijn **voor 3 september 2019** als u een van de [getroffen entiteiten/scenario's gebruikt.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Wat verandert er?

Voorheen konden geheimen worden verkregen via de HDInsight API door clustergebruikers die de rollen Owner, Contributor `*/read` of Reader [RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)bezitten, omdat ze beschikbaar waren voor iedereen met de toestemming. Geheimen worden gedefinieerd als waarden die kunnen worden gebruikt om meer verhoogde toegang te verkrijgen dan de rol van een gebruiker zou moeten toestaan. Deze omvatten waarden zoals http-referenties van de clustergateway, opslagaccountsleutels en databasereferenties.

Vanaf 3 september 2019 is voor toegang `Microsoft.HDInsight/clusters/configurations/action` tot deze geheimen de toestemming vereist, wat betekent dat gebruikers met de Reader-rol niet langer toegankelijk zijn. De rollen die deze machtiging hebben, zijn inzender, eigenaar en de nieuwe rol van HDInsight Cluster Operator (daarover hieronder meer).

We introduceren ook een nieuwe [HDInsight Cluster Operator-rol](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) die geheimen kan ophalen zonder de administratieve machtigingen van inzender of eigenaar te krijgen. Samenvattend:

| Rol                                  | Eerder                                                                                       | Vooruit gaan       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Lezer                                | - Lees de toegang, inclusief geheimen                                                                   | - Lees toegang, **met uitzondering van** geheimen |           |   |   |
| HDInsight-clusteroperator<br>(Nieuwe rol) | N.v.t.                                                                                              | - Lees/schrijf toegang, inclusief geheimen         |   |   |
| Inzender                           | - Lees/schrijf toegang, inclusief geheimen<br>- Alle typen Azure-resources maken en beheren.     | Geen wijziging |
| Eigenaar                                 | - Lees/schrijf toegang inclusief geheimen<br>- Volledige toegang tot alle bronnen<br>- Delegeren van toegang tot anderen | Geen wijziging |

Zie De roltoewijzing van [de HDInsight Cluster Operator toevoegen aan een gebruiker](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)om deze te lezen/schrijven toegang te geven tot clustergeheimen.

## <a name="am-i-affected-by-these-changes"></a>Heb ik last van deze veranderingen?

De volgende entiteiten en scenario's worden beïnvloed:

- [API](#api): Gebruikers `/configurations` `/configurations/{configurationName}` die de of eindpunten gebruiken.
- [Azure HDInsight-hulpprogramma's voor Visual Studio Code-versie](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1 of lager.
- [Azure Toolkit voor IntelliJ](#azure-toolkit-for-intellij) versie 3.20.0 of lager.
- [Azure Data Lake en Stream Analytics Tools voor Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) hieronder versie 2.3.9000.1.
- [Azure Toolkit voor Eclipse](#azure-toolkit-for-eclipse) versie 3.15.0 of lager.
- [SDK voor .NET](#sdk-for-net)
    - [versies 1.x of 2.x](#versions-1x-and-2x) `GetClusterConfigurations`: `GetConnectivitySettings` `ConfigureHttpSettings`Gebruikers `EnableHttp` `DisableHttp` die de klasse , , of methoden gebruiken uit de klasse ConfigurationsOperationsExtensions.
    - [versies 3.x en hoger](#versions-3x-and-up) `Get`: `Update` `EnableHttp`Gebruikers `DisableHttp` die de `ConfigurationsOperationsExtensions` , , of methoden uit de klasse gebruiken.
- [SDK voor Python:](#sdk-for-python) `get` Gebruikers `update` die de `ConfigurationsOperations` of methoden uit de klasse gebruiken.
- [SDK voor Java:](#sdk-for-java) `update` Gebruikers `get` die de `ConfigurationsInner` of methoden uit de klasse gebruiken.
- [SDK for Go](#sdk-for-go): `Get` `Update` Gebruikers die `ConfigurationsClient` de of methoden uit de struct gebruiken.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) hieronder versie 2.0.0.
Bekijk de onderstaande secties (of gebruik de bovenstaande koppelingen) om de migratiestappen voor uw scenario te bekijken.

### <a name="api"></a>API

De volgende API's worden gewijzigd of afgeschaft:

- [**GET /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (gevoelige informatie verwijderd)
    - Voorheen gebruikt om individuele configuratietypen te verkrijgen (inclusief geheimen).
    - Vanaf 3 september 2019 retourneert deze API-aanroep nu afzonderlijke configuratietypen met weggelaten geheimen. Gebruik de nieuwe POST /configurations call om alle configuraties, inclusief geheimen, te verkrijgen. Als u alleen gateway-instellingen wilt verkrijgen, gebruikt u de nieuwe oproep POST /getGatewaySettings.
- [**GET /configuraties**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (afgeschaft)
    - Voorheen gebruikt om alle configuraties te verkrijgen (inclusief geheimen)
    - Vanaf 3 september 2019 wordt deze API-aanroep afgeschaft en niet meer ondersteund. Gebruik de nieuwe oproep POST /configurations om alle configuraties in de toekomst te verkrijgen. Als u configuraties wilt verkrijgen met gevoelige parameters die zijn weggelaten, gebruikt u de aanroep GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (afgeschaft)
    - Voorheen gebruikt om gatewayreferenties bij te werken.
    - Vanaf 3 september 2019 wordt deze API-aanroep afgeschaft en niet meer ondersteund. Gebruik in plaats daarvan de nieuwe POST /updateGatewaySettings.

De volgende vervangende API's zijn toegevoegd:</span>

- [**POST /configuraties**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Gebruik deze API om alle configuraties te verkrijgen, inclusief geheimen.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Gebruik deze API om gateway-instellingen te verkrijgen.
- [**POST /updateGatewayInstellingen**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Gebruik deze API om gateway-instellingen (gebruikersnaam en/of wachtwoord) bij te werken.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight-hulpprogramma's voor visual studiocode

Als u versie 1.1.1 of lager gebruikt, werkt u bij naar de [nieuwste versie van Azure HDInsight Tools voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) om onderbrekingen te voorkomen.

### <a name="azure-toolkit-for-intellij"></a>Azure-toolkit voor IntelliJ

Als u versie 3.20.0 of lager gebruikt, werkt u bij naar de [nieuwste versie van de Azure Toolkit voor IntelliJ-plug-in](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) om onderbrekingen te voorkomen.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake- en Stream Analytics-hulpprogramma's voor Visual Studio

Update naar versie 2.3.9000.1 of hoger van [Azure Data Lake en Stream Analytics Tools voor Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) om onderbrekingen te voorkomen.  Voor hulp bij het bijwerken, zie onze documentatie, [Update Data Lake Tools voor Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure-toolkit voor Eclipse

Als u versie 3.15.0 of lager gebruikt, werkt u bij naar de [nieuwste versie van de Azure Toolkit voor Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) om onderbrekingen te voorkomen.

### <a name="sdk-for-net"></a>SDK voor .NET

#### <a name="versions-1x-and-2x"></a>Versies 1.x en 2.x

Update naar [versie 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) van de HDInsight SDK voor .NET. Er kunnen minimale codewijzigingen nodig zijn als u een methode gebruikt die door deze wijzigingen wordt beïnvloed:

- `ClusterOperationsExtensions.GetClusterConfigurations`zal **niet langer gevoelige parameters** zoals opslagsleutels (core-site) of HTTP-referenties (gateway) retourneren.
    - Als u alle configuraties wilt ophalen, inclusief gevoelige parameters, gebruikt u `ClusterOperationsExtensions.ListConfigurations` in de toekomst.  Houd er rekening mee dat gebruikers met de rol 'Reader' deze methode niet kunnen gebruiken. Dit maakt gedetailleerde controle mogelijk over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster.
    - Als u alleen HTTP-gatewayreferenties wilt ophalen, gebruikt u `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings`is nu afgeschaft en is vervangen `ClusterOperationsExtensions.GetGatewaySettings`door .

- `ClusterOperationsExtensions.ConfigureHttpSettings`is nu afgeschaft en is vervangen `ClusterOperationsExtensions.UpdateGatewaySettings`door .

- `ConfigurationsOperationsExtensions.EnableHttp`en `DisableHttp` zijn nu afgeschaft. HTTP is nu altijd ingeschakeld, dus deze methoden zijn niet langer nodig.

#### <a name="versions-3x-and-up"></a>Versies 3.x en hoger

Update naar [versie 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) of hoger van de HDInsight SDK voor .NET. Er kunnen minimale codewijzigingen nodig zijn als u een methode gebruikt die door deze wijzigingen wordt beïnvloed:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)zal **niet langer gevoelige parameters** zoals opslagsleutels (core-site) of HTTP-referenties (gateway) retourneren.
    - Als u alle configuraties wilt ophalen, inclusief gevoelige parameters, gebruikt u [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) in de toekomst.Houd er rekening mee dat gebruikers met de rol 'Reader' deze methode niet kunnen gebruiken. Dit maakt gedetailleerde controle mogelijk over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen HTTP-gatewayreferenties wilt ophalen, gebruikt u [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)is nu afgeschaft en is vervangen [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)door . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)en [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) zijn nu afgeschaft. HTTP is nu altijd ingeschakeld, dus deze methoden zijn niet langer nodig.

### <a name="sdk-for-python"></a>SDK voor Python

Update naar [versie 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) of hoger van de HDInsight SDK voor Python. Er kunnen minimale codewijzigingen nodig zijn als u een methode gebruikt die door deze wijzigingen wordt beïnvloed:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)zal **niet langer gevoelige parameters** zoals opslagsleutels (core-site) of HTTP-referenties (gateway) retourneren.
    - Als u alle configuraties wilt ophalen, inclusief gevoelige parameters, gebruikt u [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) in de toekomst.Houd er rekening mee dat gebruikers met de rol 'Reader' deze methode niet kunnen gebruiken. Dit maakt gedetailleerde controle mogelijk over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen HTTP-gatewayreferenties wilt ophalen, gebruikt u [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)is nu afgeschaft en is vervangen [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)door .

### <a name="sdk-for-java"></a>SDK voor Java

Update naar [versie 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) of hoger van de HDInsight SDK voor Java. Er kunnen minimale codewijzigingen nodig zijn als u een methode gebruikt die door deze wijzigingen wordt beïnvloed:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)zal **niet langer gevoelige parameters** zoals opslagsleutels (core-site) of HTTP-referenties (gateway) retourneren.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)is nu afgeschaft.

### <a name="sdk-for-go"></a>SDK voor onderweg

Update naar [versie 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) of hoger van de HDInsight SDK for Go. Er kunnen minimale codewijzigingen nodig zijn als u een methode gebruikt die door deze wijzigingen wordt beïnvloed:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)zal **niet langer gevoelige parameters** zoals opslagsleutels (core-site) of HTTP-referenties (gateway) retourneren.
    - Als u alle configuraties wilt ophalen, inclusief gevoelige parameters, gebruikt u [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) in de toekomst.Houd er rekening mee dat gebruikers met de rol 'Reader' deze methode niet kunnen gebruiken. Dit maakt gedetailleerde controle mogelijk over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen HTTP-gatewayreferenties wilt ophalen, gebruikt u [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)is nu afgeschaft en is vervangen [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)door .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Update naar [Az PowerShell versie 2.0.0](https://www.powershellgallery.com/packages/Az) of hoger om onderbrekingen te voorkomen.  Minimale codewijzigingen kunnen nodig zijn als u een methode gebruikt die door deze wijzigingen wordt beïnvloed.
- `Grant-AzHDInsightHttpServicesAccess`is nu afgeschaft en is vervangen door `Set-AzHDInsightGatewayCredential` de nieuwe cmdlet.
- `Get-AzHDInsightJobOutput`is bijgewerkt om gedetailleerde toegang op basis van rollen tot de opslagsleutel te ondersteunen.
    - Gebruikers met de HDInsight Cluster-rollen Operator, Bijdrager of Eigenaar worden niet beïnvloed.
    - Gebruikers met alleen de readerrol `DefaultStorageAccountKey` moeten de parameter expliciet opgeven.
- `Revoke-AzHDInsightHttpServicesAccess`is nu afgeschaft. HTTP is nu altijd ingeschakeld, dus deze cmdlet is niet meer nodig.
 Zie de [az. HDInsight migratiegids](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) voor meer details.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>De roltoewijzing van de HDInsight-clusteroperator toevoegen aan een gebruiker

Een gebruiker met de rol [Eigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) kan de rol [HDInsight Cluster Operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) toewijzen aan gebruikers die u lees-/schrijftoegang wilt hebben tot gevoelige HDInsight-clusterconfiguratiewaarden (zoals clustergatewayreferenties en opslagaccountsleutels).

### <a name="using-the-azure-cli"></a>Azure CLI gebruiken

De eenvoudigste manier om deze roltoewijzing `az role assignment create` toe te voegen, is door de opdracht in Azure CLI te gebruiken.

> [!NOTE]
> Deze opdracht moet worden uitgevoerd door een gebruiker met de rol Eigenaar, omdat alleen zij deze machtigingen kunnen verlenen. Het `--assignee` is de naam van het serviceprincipal of e-mailadres van de gebruiker aan wie u de rol HDInsight Cluster Operator wilt toewijzen. Als u een fout met onvoldoende machtigingen ontvangt, raadpleegt u de onderstaande veelgestelde vragen.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Subsidierol op resourceniveau (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Subsidierol op het niveau van de resourcegroep

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Subsidierol op abonnementsniveau

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

U de Azure-portal ook gebruiken om de roltoewijzing van de HDInsight Cluster Operator toe te voegen aan een gebruiker. Zie de documentatie, [Toegang tot Azure-bronnen beheren met RBAC en de Azure-portal - Een roltoewijzing toevoegen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>Veelgestelde vragen

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Waarom krijg ik een 403 (Verboden) reactie na het bijwerken van mijn API-aanvragen en/of hulpprogramma?

Clusterconfiguraties zijn nu achter gedetailleerd op rollen `Microsoft.HDInsight/clusters/configurations/*` gebaseerd toegangscontrole en vereisen de toestemming om toegang te krijgen. Als u deze machtiging wilt verkrijgen, wijst u de rol HDInsight-clusteroperator, inzender of eigenaar toe aan de gebruiker of serviceprincipal die toegang probeert te krijgen tot configuraties.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Waarom zie ik 'Onvoldoende bevoegdheden om de bewerking te voltooien' wanneer ik de opdracht Azure CLI uitvoert om de rol HDInsight Cluster Operator toe te wijzen aan een andere gebruiker of serviceprincipal?

Naast de rol Eigenaar moet de gebruiker of serviceprincipal die de opdracht uitvoert, over voldoende AAD-machtigingen beschikken om de object-id's van de cessionaris op te zoeken. Dit bericht geeft onvoldoende AAD-machtigingen aan. Probeer het `-–assignee` argument `–assignee-object-id` te vervangen door en geef de object-ID van de cessionaris als parameter in plaats van de naam (of de hoofd-id in het geval van een beheerde identiteit). Zie de optionele parameters sectie van de [az rol toewijzing documentatie maken](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) voor meer info.

Als dit nog steeds niet werkt, neemt u contact op met uw AAD-beheerder om de juiste machtigingen te verkrijgen.

### <a name="what-will-happen-if-i-take-no-action"></a>Wat gebeurt er als ik geen actie onderneem?

Vanaf 3 september 2019 `GET /configurations` `POST /configurations/gateway` worden er geen informatie `GET /configurations/{configurationName}` meer weergegeven en wordt gevoelige parameters, zoals opslagaccountsleutels of het clusterwachtwoord, niet meer weergegeven. Hetzelfde geldt voor overeenkomstige SDK-methoden en PowerShell-cmdlets.

Als u een oudere versie van een van de tools voor Visual Studio, VSCode, IntelliJ of Eclipse hierboven vermeld gebruikt, zullen ze niet meer functioneren totdat u update.

Zie het overeenkomstige gedeelte van dit document voor uw scenario voor meer gedetailleerde informatie.
