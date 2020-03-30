---
title: Bronnen, rollen en toegangscontrole in Azure Application Insights | Microsoft Documenten
description: Eigenaren, bijdragers en lezers van de inzichten van uw organisatie.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 1e57af269c4052d0dcd4a8f7970ca23017024299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473129"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Bronnen, rollen en toegangscontrole in Application Insights

U bepalen wie de toegang tot uw gegevens heeft gelezen en bijgewerkt in Azure [Application Insights][start], met behulp van op [rollen gebaseerdtoegangsbeheer in Microsoft Azure.](../../role-based-access-control/role-assignments-portal.md)

> [!IMPORTANT]
> Geef toegang toe aan gebruikers in de **brongroep of het abonnement** waartoe uw toepassingsbron behoort, niet in de bron zelf. Wijs de rol **van componentbijdragemedewerker Application Insights toe.** Dit zorgt voor een uniforme controle van de toegang tot webtests en waarschuwingen samen met uw toepassingsbron. [Meer informatie](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Resources, groepen en abonnementen

Ten eerste enkele definities:

* **Resource** - Een instantie van een Microsoft Azure-service. Uw Application Insights-bron verzamelt, analyseert en toont de telemetriegegevens die vanuit uw toepassing worden verzonden.  Andere typen Azure-bronnen zijn web-apps, databases en VM's.
  
    Als u uw resources wilt zien, opent u de [Azure-portal,][portal]meldt u zich aan en klikt u op Alle bronnen. Als u een resource wilt zoeken, typt u een deel van de naam in het filterveld.
  
    ![Lijst met Azure-bronnen](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Resourcegroep**][group] - Elke resource behoort tot één groep. Een groep is een handige manier om gerelateerde resources te beheren, met name voor toegangscontrole. In één resourcegroep u bijvoorbeeld een web-app, een Application Insights-bron plaatsen om de app te controleren en een opslagbron om geëxporteerde gegevens te bewaren.

* [**Abonnement**](https://portal.azure.com) - Als u Application Insights of andere Azure-bronnen wilt gebruiken, meldt u zich aan bij een Azure-abonnement. Elke brongroep behoort tot één Azure-abonnement, waarbij u uw prijspakket kiest en, als het een organisatieabonnement is, de leden en hun toegangsmachtigingen kiest.
* [**Microsoft-account**][account] : de gebruikersnaam en het wachtwoord waarmee u zich aanmeldt bij Microsoft Azure-abonnementen, XBox Live, Outlook.com en andere Microsoft-services.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a>Toegang beheren in de resourcegroep

Het is belangrijk om te begrijpen dat er naast de bron die u voor uw toepassing hebt gemaakt, ook afzonderlijke verborgen bronnen zijn voor waarschuwingen en webtests. Ze zijn gekoppeld aan dezelfde [resourcegroep](#resource-group) als uw Application Insights-bron. Mogelijk hebt u daar ook andere Azure-services geplaatst, zoals websites of opslag.

Om de toegang tot deze bronnen te beheren, wordt het daarom aanbevolen om:

* Beheer de toegang op **resourcegroep- of abonnementsniveau.**
* Wijs de rol **van de componentbijdrage voor application insights toe** aan gebruikers. Hierdoor kunnen ze webtests, waarschuwingen en Application Insights-bronnen bewerken, zonder toegang te bieden tot andere services in de groep.

## <a name="to-provide-access-to-another-user"></a>Toegang geven aan een andere gebruiker

U moet eigenaarrechten hebben op het abonnement of de brongroep.

De gebruiker moet een [Microsoft-account][account]of toegang tot zijn [of haar Microsoft-account](../../active-directory/fundamentals/sign-up-organization.md)hebben. U toegang bieden tot personen en ook tot gebruikersgroepen die zijn gedefinieerd in Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navigeren naar resourcegroep of rechtstreeks naar de resource zelf

Kies **Toegangsbeheer (IAM)** in het linkermenu.

![Schermafbeelding van de knop Toegangsbeheer in Azure-portal](./media/resources-roles-access-control/0001-access-control.png)

Roltoewijzing **toevoegen selecteren**

![Schermafbeelding van het menu Toegangsbesturingselement met de knop Toevoegen in het rood gemarkeerd](./media/resources-roles-access-control/0002-add.png)

De **onderstaande** weergave Machtigingen toevoegen is voornamelijk specifiek voor toepassingsinzichtenbronnen, als u de machtigingen voor toegangsbeheer van een hoger niveau zoals resourcegroepen bekijkt, ziet u extra niet-toepassingsinzichtengerichte rollen.

Als u informatie wilt weergeven over alle ingebouwde rollen op basis van Azure-rollen op basis van toegangsbeheer, gebruikt u de [officiële referentie-inhoud](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Schermafbeelding van de gebruikersrollijst access-besturingselement](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Een rol selecteren

Indien van toepassing koppelen wij aan de bijbehorende officiële referentiedocumentatie.

| Rol | In de resourcegroep |
| --- | --- |
| [Eigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Kan alles veranderen, inclusief gebruikerstoegang. |
| [Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Kan alles bewerken, inclusief alle bronnen. |
| [Componentbijdragecomponent voor toepassingsinzichten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Kan resources voor Application Insights bewerken. |
| [Lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Kan bekijken, maar niets veranderen. |
| [Momentopnamefoutfout van toepassingsinzichten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Geeft de gebruiker toestemming om functies voor Application Insights Snapshot Debugger te gebruiken. Houd er rekening mee dat deze rol is opgenomen in noch de rollen Eigenaar of Inzender. |
| Medewerker releasebeheer azure-service implementeren | Rol bijcontribuanten voor services die worden geïmplementeerd via Azure Service Deploy. |
| [Gegevenszuiverheid](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Bijzondere rol voor het zuiveren van persoonsgegevens. Zie onze [richtlijnen voor persoonsgegevens](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) voor meer informatie.   |
| ExpressRoute-beheerder | Kan expresroutes verwijderen en beheren.|
| [Inzender van Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Log Analytics-inzender kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. De bewakingsinstellingen voor bewerken omvatten het toevoegen van de VM-extensie aan VM's; leesopslagaccountsleutels om verzameling logboeken uit Azure Storage te kunnen configureren; Het maken en configureren van Automatiseringsaccounts; het toevoegen van oplossingen; en azure-diagnose configureren op alle Azure-resources.  |
| [Lezer van Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Log Analytics Reader kan alle bewakingsgegevens bekijken en doorzoeken en bewakingsinstellingen weergeven, inclusief het bekijken van de configuratie van Azure-diagnoses op alle Azure-bronnen. |
| masterreader | Hiermee kan een gebruiker alles bekijken, maar geen wijzigingen aanbrengen. |
| [Monitorbijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Kan alle monitoringgegevens en update monitoring instellingen lezen.|
| [Uitgever statistieken controleren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Hiermee kunnen publicatiestatistieken worden gepubliceerd tegen Azure-bronnen. |
| [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Kan alle bewakingsgegevens lezen. |
| Bijdrager voor resourcebeleid (voorbeeld) | Backfilled gebruikers van EA, met rechten om resourcebeleid te maken/wijzigen, ondersteuningsticket te maken en resource/hiërarchie te lezen.  |
| [Beheerder voor gebruikerstoegang](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Hiermee kan een gebruiker de toegang voor andere gebruikers tot Azure-bronnen beheren.|
| [Websitebijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Hiermee u websites beheren (geen webplannen), maar geen toegang tot deze websites..|

'Bewerken' omvat het maken, verwijderen en bijwerken:

* Resources
* Webtests
* Waarschuwingen
* Continue export

#### <a name="select-the-user"></a>De gebruiker selecteren

Als de gewenste gebruiker zich niet in de map bevindt, u iedereen met een Microsoft-account uitnodigen.
(Als ze services zoals Outlook.com, OneDrive, Windows Phone of XBox Live gebruiken, hebben ze een Microsoft-account.)

## <a name="related-content"></a>Gerelateerde inhoud

* [Op rollen gebaseerd toegangscontrole in Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>PowerShell-query om het rollidmaatschap te bepalen

Aangezien bepaalde rollen kunnen worden gekoppeld aan meldingen en e-mailwaarschuwingen, kan het handig zijn om een lijst te genereren met gebruikers die tot een bepaalde rol behoren. Om u te helpen bij het genereren van dit soort lijsten bieden we de volgende voorbeeldquery's die kunnen worden aangepast aan uw specifieke behoeften:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Query volledig abonnement voor Beheerdersrollen + Bijdragen aan inzender

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Query in de context van een specifieke Application Insights-bron voor eigenaren en bijdragers

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Query in de context van een specifieke resourcegroep voor eigenaren en bijdragers

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
