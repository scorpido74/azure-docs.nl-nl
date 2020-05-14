---
title: Beheerervaring in meerdere tenants
description: Azure delegated Resource Management maakt een cross-Tenant beheer mogelijk.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: ad8fc7452a704a4a030e7a6eb45a5ba397912ef1
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402377"
---
# <a name="cross-tenant-management-experiences"></a>Beheerervaring in meerdere tenants

Als service provider kunt u [Azure delegated resource management](../concepts/azure-delegated-resource-management.md) gebruiken voor het beheren van Azure-resources voor meerdere klanten vanuit uw eigen Tenant in de [Azure Portal](https://portal.azure.com). De meeste taken en services kunnen worden uitgevoerd op gedelegeerde Azure-resources in beheerde tenants. In dit artikel worden enkele van de verbeterde scenario's beschreven waarbij Azure delegated resource management kan worden effectief.

> [!NOTE]
> Azure delegated resource management kan ook worden gebruikt [binnen een onderneming met meerdere Azure AD-tenants](enterprise.md) , om cross-Tenant beheer te vereenvoudigen.

## <a name="understanding-customer-tenants"></a>Informatie over de tenants van klanten

Een Azure Active Directory-Tenant (Azure AD) is een representatie van een organisatie. Het is een toegewezen exemplaar van Azure AD dat een organisatie ontvangt wanneer ze een relatie met micro soft maken door zich aan te melden voor Azure, Microsoft 365 of andere services. Elke Azure AD-Tenant is uniek en gescheiden van andere Azure AD-tenants en heeft een eigen Tenant-ID (een GUID). Zie [Wat is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md) voor meer informatie.

Om Azure-resources voor een klant te kunnen beheren, moeten service providers zich dus aanmelden bij de Azure Portal met behulp van een account dat is gekoppeld aan de Tenant van die klant, waarbij een beheerder in de Tenant van de klant gebruikers accounts voor de service provider kan maken en beheren.

Met Azure delegated resource management geeft het voorbereidings proces gebruikers in de Tenant van de service provider op die abonnementen, resource groepen en resources in de Tenant van de klant kunnen openen en beheren. Deze gebruikers kunnen zich vervolgens aanmelden bij de Azure Portal met hun eigen referenties. Binnen het Azure Portal kunnen ze resources beheren die horen bij alle klanten waartoe ze toegang hebben. U kunt dit doen door de pagina [mijn klanten](../how-to/view-manage-customers.md) te bezoeken in het Azure portal of door rechtstreeks te werken in de context van het abonnement van de klant, hetzij in de Azure portal-of via-api's.

Azure delegated Resource Management biedt meer flexibiliteit voor het beheren van resources voor meerdere klanten, zonder dat u zich hoeft aan te melden bij verschillende accounts in verschillende tenants. Een service provider kan bijvoorbeeld twee klanten hebben met verschillende verantwoordelijkheden en toegangs niveaus. Met behulp van Azure delegated resource management kunnen geautoriseerde gebruikers zich aanmelden bij de Tenant van de service provider om toegang te krijgen tot deze bronnen.

![Klant bronnen die worden beheerd via een Tenant van een service provider](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Ondersteuning voor Api's en beheer hulpprogramma's

U kunt Beheer taken op gedelegeerde resources rechtstreeks uitvoeren in de portal of met behulp van Api's en beheer hulpprogramma's (zoals Azure CLI en Azure PowerShell). Alle bestaande Api's kunnen worden gebruikt bij het werken met gedelegeerde resources, zolang de functionaliteit wordt ondersteund voor cross-Tenant beheer en de gebruiker de juiste machtigingen heeft.

De Azure PowerShell [Get-AzSubscription cmdlet](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) geeft de **tenantID** voor elk abonnement weer, zodat u kunt bepalen of een geretourneerd abonnement hoort bij de Tenant van uw service provider of een beheerde klant Tenant.

Net als bij Azure CLI-opdrachten, zoals [AZ account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) , worden de **HomeTenantId** -en **managedByTenants** -kenmerken weer gegeven.

> [!TIP]
> Als u deze waarden niet ziet wanneer u Azure CLI gebruikt, probeert u de cache te wissen door uit te voeren, `az account clear` gevolgd door `az login --identity` .

We bieden ook Api's die specifiek zijn voor het uitvoeren van Azure gedelegeerde resource beheer taken. Zie de sectie **Naslag informatie** voor meer informatie.

## <a name="enhanced-services-and-scenarios"></a>Verbeterde services en scenario's

De meeste taken en services kunnen worden uitgevoerd op gedelegeerde resources in beheerde tenants. Hieronder vindt u enkele van de belangrijkste scenario's waarbij beheer van meerdere tenants effectief kan zijn.

[Azure-Arc voor servers (preview-versie)](../../azure-arc/servers/overview.md):

- [Windows Server-of Linux-computers buiten Azure verbinden](../../azure-arc/servers/quickstart-onboard-portal.md) met gedelegeerde abonnementen en/of resource groepen in azure
- Verbonden computers beheren met Azure-constructs, zoals Azure Policy en tagging

[Azure Automation](../../automation/index.yml):

- Automation-accounts gebruiken om toegang te krijgen tot en te werken met gedelegeerde klant resources

[Azure backup](../../backup/index.yml):

- Back-ups maken en herstellen van klant gegevens in de tenants van de klant
- Gebruik de [back-upverkenner](../../backup/monitor-azure-backup-with-backup-explorer.md) voor het weer geven van operationele gegevens van back-upitems (inclusief Azure-resources die nog niet zijn geconfigureerd voor back-up) en controle-informatie (taken en waarschuwingen) voor gedelegeerde abonnementen. Backup Explorer is momenteel alleen beschikbaar voor Azure VM-gegevens.
- Gebruik [back-uprapporten](../../backup/configure-reports.md) over gedelegeerde abonnementen om historische trends bij te houden, het verbruik van back-ups te analyseren en back-ups te controleren en te herstellen.

[Azure Kubernetes service (AKS)](../../aks/index.yml):

- Gehoste Kubernetes-omgevingen beheren en toepassingen in containers implementeren en beheren binnen de tenants van de klant

[Azure monitor](../../azure-monitor/index.yml):

- Waarschuwingen voor gedelegeerde abonnementen weer geven, met de mogelijkheid om waarschuwingen voor alle abonnementen weer te geven
- Details van het activiteiten logboek voor gedelegeerde abonnementen weer geven
- Log Analytics: gegevens opvragen uit externe werk ruimten van klanten in meerdere tenants
- Waarschuwingen maken in de tenants van de klant die automatisering activeren, zoals Azure Automation runbooks of Azure Functions, in de Tenant van de service provider via webhooks

[Azure-netwerken](../../networking/networking-overview.md):

- [Azure Virtual Network (VNet)](../../virtual-network/index.yml) en virtuele netwerk interface kaarten (vnic's) implementeren en beheren binnen de tenants van de klant
- [Azure firewall](../../firewall/overview.md) implementeren en configureren om Virtual Network Resources van klanten te beveiligen
- Connectiviteits services zoals [Azure Virtual WAN](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md)en [VPN-gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md) voor klanten beheren
- Azure Lighthouse gebruiken ter ondersteuning van belang rijke scenario's voor het [MSP-programma van Azure Networking](../../networking/networking-partners-msp.md)


[Azure Policy](../../governance/policy/index.yml):

- Voor compatibiliteits momentopnamen worden details weer gegeven voor toegewezen beleid in gedelegeerde abonnementen
- Beleids definities maken en bewerken binnen een gedelegeerd abonnement
- Door de klant gedefinieerde beleids definities toewijzen binnen het gedelegeerde abonnement
- Klanten zien beleid dat door de service provider is ontworpen naast het beleid dat ze zelf hebben ontworpen
- Kan [deployIfNotExists herstellen of toewijzingen wijzigen binnen de Tenant van de klant](../how-to/deploy-policy-remediation.md)

[Azure-resource grafiek](../../governance/resource-graph/index.yml):

- Bevat nu de Tenant-ID in geretourneerde query resultaten, zodat u kunt bepalen of een abonnement behoort tot de Tenant van de klanten Tenant of service provider

[Azure Security Center](../../security-center/index.yml):

- Zicht baarheid van meerdere tenants
  - Naleving van beveiligings beleid controleren en de beveiliging van alle resources van de Tenant garanderen
  - Continue naleving van regelgeving over meerdere klanten in één weer gave
  - Bewaak, sorteren en prioriteit geven aan bruikbare beveiligings aanbevelingen met een beveiligde score berekening
- Beheer van cross-Tenant beveiliging postuur
  - Beveiligings beleid beheren
  - Onderneem actie ondernemen voor bronnen die niet meer compatibel zijn met voor actie bare beveiligings aanbevelingen
  - Aan beveiliging gerelateerde gegevens verzamelen en opslaan
- Bedreigings detectie en-beveiliging tussen tenants
  - Bedreigingen detecteren voor bronnen van tenants
  - Geavanceerde beveiliging tegen bedreigingen Toep assen, zoals just-in-time-VM-toegang
  - Configuratie van beveiligings groep met adaptieve netwerk beveiliging
  - Zorg ervoor dat servers alleen de toepassingen en processen uitvoeren die ze moeten hebben met adaptieve toepassings besturings elementen
  - Wijzigingen in belang rijke bestanden en register vermeldingen bewaken met FIM (File Integrity Monitoring)

[Azure-Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Azure-Sentinel-resources beheren [in de tenants van de klant](../../sentinel/multiple-tenants-service-providers.md)
- [Aanvallen bijhouden en beveiligings waarschuwingen weer geven voor meerdere tenants van klanten](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Incidenten weer geven](../../sentinel/multiple-workspace-view.md) in meerdere Sentinel-werk ruimten die zijn verspreid over de tenants van de klant

[Azure service Health](../../service-health/index.yml):

- Bewaak de status van klant bronnen met Azure Resource Health
- De status bijhouden van de Azure-Services die door uw klanten worden gebruikt

[Azure site Recovery](../../site-recovery/index.yml):

- Opties voor herstel na nood gevallen voor virtuele Azure-machines in de tenants van de klant beheren (u kunt geen runas-accounts gebruiken om VM-extensies te kopiëren)

[Azure virtual machines](../../virtual-machines/index.yml):

- Virtuele-machine uitbreidingen gebruiken om configuratie-en automatiserings taken na de implementatie op te geven op virtuele machines van Azure in de tenants van de klant
- Diagnostische gegevens over opstarten gebruiken om problemen met virtuele Azure-machines in klant tenants op te lossen
- Toegang tot Vm's met een seriële console in de tenants van de klant
- Integreer Vm's met Azure-sleutel kluis voor wacht woorden, geheimen of cryptografische sleutels voor schijf versleuteling met behulp van [beheerde identiteit via beleid](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret), zodat geheimen worden opgeslagen in een Key Vault in de tenants van de klant
- Houd er rekening mee dat u Azure Active Directory niet kunt gebruiken voor externe aanmelding bij Vm's in de tenants van de klant

Ondersteunings aanvragen:

- Open ondersteunings aanvragen voor gedelegeerde resources op de Blade **Help en ondersteuning** in de Azure Portal (Selecteer het ondersteunings plan dat beschikbaar is voor het gedelegeerde bereik)

## <a name="current-limitations"></a>Huidige beperkingen
Houd bij alle scenario's rekening met de volgende beperkingen:

- Aanvragen die door Azure Resource Manager worden verwerkt, kunnen worden uitgevoerd met behulp van Azure delegated resource management. De bewerkings-Uri's voor deze aanvragen beginnen met `https://management.azure.com` . Aanvragen die worden verwerkt door een exemplaar van een bron type (zoals de toegang tot de sleutel kluis geheimen of toegang tot opslag gegevens), worden echter niet ondersteund met het beheer van de gedelegeerde resources van Azure. De bewerkings-Uri's voor deze aanvragen beginnen meestal met een adres dat uniek is voor uw exemplaar, zoals `https://myaccount.blob.core.windows.net` of `https://mykeyvault.vault.azure.net/` . De laatste is ook gegevens bewerkingen in plaats van beheer bewerkingen. 
- Roltoewijzingen moeten gebruikmaken [van ingebouwde rollen](../../role-based-access-control/built-in-roles.md)op basis van op rollen gebaseerd toegangs beheer (RBAC). Alle ingebouwde rollen worden momenteel ondersteund met het beheer van gedelegeerde resources van Azure, met uitzonde ring van eigenaar of ingebouwde rollen met [DataActions](../../role-based-access-control/role-definitions.md#dataactions) -machtiging. De rol beheerder van gebruikers toegang wordt alleen ondersteund voor beperkt gebruik bij het [toewijzen van rollen aan beheerde identiteiten](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Aangepaste rollen en [beheerders rollen voor klassieke abonnementen](../../role-based-access-control/classic-administrators.md) worden niet ondersteund.
- Hoewel u abonnementen kunt opdoen die gebruikmaken van Azure Databricks, kunnen gebruikers in de Tenant beheren Azure Databricks werk ruimten op dit moment niet starten.
- U kunt abonnementen en resource groepen voor Azure delegated resource management zonder resource vergrendeling voor komen. deze vergren delingen verhinderen niet dat de acties worden uitgevoerd door gebruikers in de Tenant beheren. [Weiger toewijzingen](../../role-based-access-control/deny-assignments.md) die door het systeem beheerde bronnen beveiligen, zoals die zijn gemaakt door door Azure beheerde toepassingen of Azure-blauw drukken (door het systeem toegewezen weigerings toewijzingen), voor komen dat gebruikers in de Tenant beheren op deze resources. op dit moment kunnen gebruikers in de Tenant van de klant echter geen eigen weigerings toewijzingen maken (toegewezen weigerings toewijzingen).
- Gebruikers in de Tenant beheren hebben geen toegang tot het weer geven van facturerings gegevens voor een gehoste klant abonnement, zelfs als ze een ingebouwde rol hebben die normaal gesp roken toegang zou toestaan. De reden hiervoor is dat de toegang tot facturerings gegevens extra stappen vereist die momenteel alleen worden ondersteund voor gebruikers binnen dezelfde Tenant.

## <a name="next-steps"></a>Volgende stappen

- Laat uw klanten onboarded resource management ongedaan maken, hetzij met [behulp van Azure Resource Manager sjablonen](../how-to/onboard-customer.md) of door [een aanbieding voor privé-of openbaar beheerde services naar Azure Marketplace te publiceren](../how-to/publish-managed-services-offers.md).
- [Bekijk en beheer klanten](../how-to/view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.
