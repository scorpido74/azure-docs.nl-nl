---
title: Beheerervaring in meerdere tenants
description: Azure gedelegeerd resourcebeheer maakt een cross-tenant beheerervaring mogelijk.
ms.date: 04/20/2020
ms.topic: conceptual
ms.openlocfilehash: 107350cdab90751e918d4c7845776464b43abbdd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682820"
---
# <a name="cross-tenant-management-experiences"></a>Beheerervaring in meerdere tenants

Als serviceprovider u [Azure delegated resource beheer](../concepts/azure-delegated-resource-management.md) gebruiken om Azure-resources voor meerdere klanten te beheren vanuit uw eigen tenant in de [Azure-portal.](https://portal.azure.com) De meeste taken en services kunnen worden uitgevoerd op gedelegeerde Azure-resources voor beheerde tenants. In dit artikel worden enkele verbeterde scenario's beschreven waarin Azure gedelegeerd resourcebeheer effectief kan zijn.

> [!NOTE]
> Azure gedelegeerd resourcebeheer kan ook worden gebruikt [binnen een onderneming die meerdere Azure AD-tenants heeft om](enterprise.md) het beheer van cross-tenant te vereenvoudigen.

## <a name="understanding-customer-tenants"></a>Inzicht in huurders van klanten

Een Azure AD-tenant (Azure Directory) is een weergave van een organisatie. Het is een speciaal exemplaar van Azure AD dat een organisatie ontvangt wanneer ze een relatie met Microsoft maken door zich aan te melden voor Azure, Microsoft 365 of andere services. Elke Azure AD-tenant is verschillend en staat los van andere Azure AD-tenants en heeft een eigen tenant-id (een GUID). Zie Wat is Azure Active Directory voor meer [informatie?](../../active-directory/fundamentals/active-directory-whatis.md)

Om Azure-resources voor een klant te beheren, moeten serviceproviders zich doorgaans aanmelden bij de Azure-portal met behulp van een account dat is gekoppeld aan de tenant van die klant, waardoor een beheerder in de tenant van de klant gebruikersaccounts voor de serviceprovider moet maken en beheren.

Met Azure gedelegeerd resourcebeheer geeft het onboardingproces gebruikers binnen de tenant van de serviceprovider op die abonnementen, brongroepen en resources in de tenant van de klant kunnen openen en beheren. Deze gebruikers kunnen zich vervolgens aanmelden bij de Azure-portal met hun eigen referenties. Binnen de Azure-portal kunnen ze resources beheren die behoren tot alle klanten waartoe ze toegang hebben. Dit kan door de pagina [Mijn klanten](../how-to/view-manage-customers.md) in de Azure-portal te bezoeken of door rechtstreeks te werken binnen de context van het abonnement van die klant, in de Azure-portal of via API's.

Azure gedelegeerd resourcebeheer biedt meer flexibiliteit om resources voor meerdere klanten te beheren zonder zich aan te melden bij verschillende accounts in verschillende tenants. Een serviceprovider kan bijvoorbeeld twee klanten hebben met verschillende verantwoordelijkheden en toegangsniveaus. Met Azure delegated resource management kunnen geautoriseerde gebruikers zich aanmelden bij de tenant van de serviceprovider om toegang te krijgen tot deze bronnen.

![Klantbronnen die worden beheerd via één serviceprovidertenant](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API's en ondersteuning voor beheertool

U beheertaken uitvoeren op gedelegeerde resources, rechtstreeks in de portal of met API's en beheerhulpprogramma's (zoals Azure CLI en Azure PowerShell). Alle bestaande API's kunnen worden gebruikt bij het werken met gedelegeerde resources, zolang de functionaliteit wordt ondersteund voor cross-tenantbeheer en de gebruiker over de juiste machtigingen beschikt.

De cmdlet Azure PowerShell [Get-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) toont de **tenant-ID** voor elk abonnement, zodat u bepalen of een geretourneerd abonnement van uw serviceprovidersof van een beheerde klanttenant is.

Op dezelfde manier worden azure CLI-opdrachten, zoals [de lijst met AZ-account,](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) de kenmerken **homeTenantId** en **managedByTenants** weergegeven.

> [!TIP]
> Als u deze waarden niet ziet wanneer u Azure CLI `az account clear` gebruikt, probeert u uw cache te wissen door te worden uitgevoerd, gevolgd door `az login --identity`.

We bieden ook API's die specifiek zijn voor het uitvoeren van azure gedelegeerde resourcebeheertaken. Zie de sectie **Referentie** voor meer informatie.

## <a name="enhanced-services-and-scenarios"></a>Verbeterde services en scenario's

De meeste taken en services kunnen worden uitgevoerd op gedelegeerde resources voor beheerde tenants. Hieronder staan enkele van de belangrijkste scenario's waar cross-tenant management effectief kan zijn.

[Azure Arc voor servers (preview)](../../azure-arc/servers/overview.md):

- [Windows Server- of Linux-machines buiten Azure verbinden](../../azure-arc/servers/quickstart-onboard-portal.md) met gedelegeerde abonnementen en/of brongroepen in Azure
- Verbonden machines beheren met Azure-constructies, zoals Azure-beleid en tagging

[Azure Automation:](../../automation/index.yml)

- Automatiseringsaccounts gebruiken om toegang te krijgen tot en te werken met gedelegeerde klantbronnen

[Azure Backup:](../../backup/index.yml)

- Back-ups maken en klantgegevens herstellen bij klantenhuurders
- Gebruik de [Back-upverkenner](../../backup/monitor-azure-backup-with-backup-explorer.md) om operationele informatie van back-upitems (inclusief Azure-bronnen die nog niet zijn geconfigureerd voor back-up) en controlegegevens (taken en waarschuwingen) voor gedelegeerde abonnementen weer te geven. De Backup Explorer is momenteel alleen beschikbaar voor Azure VM-gegevens.
- Gebruik [back-uprapporten](../../backup/configure-reports.md) voor gedelegeerde abonnementen om historische trends bij te houden, het verbruik van back-upopslag te analyseren en back-ups en herstelte te controleren.

[Azure Kubernetes-service (AKS):](../../aks/index.yml)

- Beheer gehoste Kubernetes-omgevingen en implementeer en beheer gecontaineriseerde toepassingen binnen klanttenants

[Azure-monitor:](../../azure-monitor/index.yml)

- Waarschuwingen voor gedelegeerde abonnementen weergeven, met de mogelijkheid om waarschuwingen voor alle abonnementen weer te geven
- Details van het activiteitenlogboek voor gedelegeerde abonnementen weergeven
- Logboekanalyses: querygegevens van externe klantwerkruimten in meerdere tenants
- Waarschuwingen maken in klanttenants die automatisering activeren, zoals Azure Automation-runbooks of Azure-functies, in de serviceprovidertenant via webhooks

[Azure-netwerken:](../../networking/networking-overview.md)

- Azure [Virtual Network (VNet)](../../virtual-network/index.yml) en virtual network interface cards (vNIC's) implementeren en beheren binnen klanttenants
- [Azure Firewall](../../firewall/overview.md) implementeren en configureren om de virtuele netwerkbronnen van klanten te beschermen
- Connectiviteitsservices beheren, zoals [Azure Virtual WAN,](../../virtual-wan/virtual-wan-about.md) [ExpressRoute](../../expressroute/expressroute-introduction.md)en [VPN-gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md) voor klanten
- Azure Lighthouse gebruiken om belangrijke scenario's voor het [Azure Networking MSP-programma](../../networking/networking-partners-msp.md) te ondersteunen


[Azure-beleid:](../../governance/policy/index.yml)

- Nalevingsmomentopnamen tonen details voor toegewezen beleidsregels binnen gedelegeerde abonnementen
- Beleidsdefinities maken en bewerken binnen een gedelegeerd abonnement
- Door de klant gedefinieerde beleidsdefinities toewijzen binnen het gedelegeerde abonnement
- Klanten zien beleid dat door de serviceprovider is geschreven naast het beleid dat ze zelf hebben geschreven
- Kan [deployIfNotExists herstellen of toewijzingen binnen de klanttenant wijzigen](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph:](../../governance/resource-graph/index.yml)

- Bevat nu de tenant-id in geretourneerde queryresultaten, zodat u bepalen of een abonnement eigendom is van de huurder of serviceprovider

[Azure Security Center:](../../security-center/index.yml)

- Zichtbaarheid van meerdere huurders
  - Toezicht houden op de naleving van het beveiligingsbeleid en zorgen voor een beveiligingsdekking voor alle bronnen van huurders
  - Continue monitoring van de naleving van de regelgeving bij meerdere klanten in één weergave
  - Controleer, triage en prioritaler actiebeveelbare beveiligingsaanbevelingen met een veilige scoreberekening
- Beheer van beveiligingshoudingen tussen meerdere huurders
  - Beveiligingsbeleid beheren
  - Actie ondernemen op resources die niet voldoen aan bruikbare beveiligingsaanbevelingen
  - Beveiligingsgerelateerde gegevens verzamelen en opslaan
- Detectie en bescherming van bedreigingen voor meerdere huurders
  - Bedreigingen detecteren voor de bronnen van huurders
  - Geavanceerde besturingselementen voor bedreigingsbeveiliging toepassen, zoals just-in-time (JIT) VM-toegang
  - Systeemnetwerkbeveiligingsgroepconfiguratie met Adaptive Network Hardening
  - Zorg ervoor dat servers alleen de toepassingen en processen uitvoeren die ze moeten hebben met adaptieve toepassingsbesturingselementen
  - Wijzigingen in belangrijke bestanden en registervermeldingen controleren met File Integrity Monitoring (FIM)

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Azure Sentinel-resources [beheren in klanttenants](../../sentinel/multiple-tenants-service-providers.md)
- [Aanvallen bijhouden en beveiligingswaarschuwingen bekijken voor meerdere klantenhuurders](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure-servicestatus:](../../service-health/index.yml)

- De status van klantgegevens controleren met Azure Resource Health
- De status bijhouden van de Azure-services die door uw klanten worden gebruikt

[Azure-siteherstel:](../../site-recovery/index.yml)

- Opties voor noodherstel voor virtuele Azure-machines beheren in klanttenants (u RunAs-accounts niet gebruiken om VM-extensies te kopiëren)

[Azure Virtual Machines:](../../virtual-machines/index.yml)

- Virtuele machine-extensies gebruiken om configuratie- en automatiseringstaken na implementatie op Azure VM's in klanttenants te bieden
- Opstartdiagnoses gebruiken om Azure VM's op te lossen in klanttenants
- Toegang tot VM's met seriële console in klanthuurders
- Houd er rekening mee dat u Azure Active Directory niet gebruiken voor extern inloggen op een virtuele machine en dat u een vm niet integreren met een sleutelkluis voor wachtwoorden, geheimen of cryptografische sleutels voor schijfversleuteling

Ondersteuningsverzoeken:

- Ondersteuningsaanvragen openen voor gedelegeerde resources uit het **Ondersteuningsblad voor Ondersteuning** in de Azure-portal (het ondersteuningsplan selecteren dat beschikbaar is voor het gedelegeerde bereik)

## <a name="current-limitations"></a>Huidige beperkingen
Bij alle scenario's moet u rekening houden met de volgende huidige beperkingen:

- Aanvragen die door Azure Resource Manager worden verwerkt, kunnen worden uitgevoerd met Azure-gedelegeerd bronbeheer. De bewerking-URI's voor `https://management.azure.com`deze aanvragen beginnen met . Aanvragen die worden verwerkt door een instantie van een resourcetype (zoals toegang tot KeyVault-geheimen of toegang tot opslaggegevens) worden echter niet ondersteund met azure-gedelegeerd bronbeheer. De bewerking-URI's voor deze aanvragen beginnen meestal met een `https://myaccount.blob.core.windows.net` adres `https://mykeyvault.vault.azure.net/`dat uniek is voor uw instantie, zoals of . Deze laatste zijn meestal ook gegevensbewerkingen in plaats van beheerbewerkingen. 
- Roltoewijzingen moeten gebruik maken van [rbac-ingebouwde rollen](../../role-based-access-control/built-in-roles.md)(Role based access control). Alle ingebouwde rollen worden momenteel ondersteund met Azure-gedelegeerd bronbeheer, behalve voor eigenaar of ingebouwde rollen met [toestemming voor DataActions.](../../role-based-access-control/role-definitions.md#dataactions) De functie Administrator voor gebruikerstoegang wordt slechts voor beperkt gebruik ondersteund bij [het toewijzen van rollen aan beheerde identiteiten.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)  Aangepaste rollen en [klassieke abonnementsbeheerdersrollen](../../role-based-access-control/classic-administrators.md) worden niet ondersteund.
- Hoewel u abonnementen aan boord die Azure Databricks gebruiken, kunnen gebruikers in de beheertenant op dit moment geen Azure Databricks-werkruimten starten op een gedelegeerd abonnement.
- Hoewel u abonnementen en resourcegroepen onboarden voor azure-gedelegeerd bronbeheer met resourcevergrendelingen, worden deze vergrendelingen niet voorkomen dat acties worden uitgevoerd door gebruikers in de beheertenant. [Toewijzingen weigeren](../../role-based-access-control/deny-assignments.md) die systeembeheerbronnen beschermen, zoals resources die zijn gemaakt door door Azure beheerde toepassingen of Azure Blueprints (met systeem toegewezen weigeringstoewijzingen), voorkomen dat gebruikers in de beheertenant op deze resources reageren; Op dit moment kunnen gebruikers in de klanttenant echter geen eigen weigeringstoewijzingen maken (door de gebruiker toegewezen weigeringstoewijzingen).
- Gebruikers in de beheertenant hebben geen toegang tot factureringsgegevens voor een gedelegeerd klantabonnement, zelfs niet als ze een ingebouwde rol hebben die doorgaans toegang zou toestaan. Dit komt omdat toegang tot factureringsgegevens extra stappen vereist die momenteel alleen worden ondersteund voor gebruikers binnen dezelfde tenant.

## <a name="next-steps"></a>Volgende stappen

- Uw klanten aan boord van Azure gedelegeerd resourcebeheer, door [Azure Resource Manager-sjablonen](../how-to/onboard-customer.md) te gebruiken of [door een privé- of openbare beheerde servicesaantebieden op Azure Marketplace te publiceren.](../how-to/publish-managed-services-offers.md)
- [Klanten bekijken en beheren](../how-to/view-manage-customers.md) door naar **Mijn klanten** te gaan in de Azure-portal.
