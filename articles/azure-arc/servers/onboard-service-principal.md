---
title: Hybride machines op schaal verbinden met Azure
description: In dit artikel leert u hoe u machines met Azure verbinden met Azure Arc voor servers (preview) met behulp van een serviceprincipal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192270"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Hybride machines op schaal verbinden met Azure

U Azure Arc inschakelen voor servers (preview) voor meerdere Windows- of Linux-machines in uw omgeving met verschillende flexibele opties, afhankelijk van uw vereisten. Met behulp van het sjabloonscript dat we leveren, u elke stap van de installatie automatiseren, inclusief het tot stand brengen van de verbinding met Azure Arc. U bent echter verplicht om dit script interactief uit te voeren met een account met verhoogde machtigingen op de doelmachine en in Azure. Als u de machines wilt verbinden met Azure Arc voor servers, u een Azure Active [Directory-serviceprincipal](../../active-directory/develop/app-objects-and-service-principals.md) gebruiken in plaats van uw geprivilegieerde identiteit te gebruiken om de machine interactief met elkaar te [verbinden.](onboard-portal.md) Een serviceprincipal is een speciale beperkte beheeridentiteit die alleen de minimale `azcmagent` toestemming krijgt die nodig is om machines met Azure te verbinden met de opdracht. Dit is veiliger dan het gebruik van een account met een hogere bevoegde waarde zoals een tenantbeheerder en volgt onze best practices voor toegangsbeveiliging. De service principal wordt alleen gebruikt tijdens het onboardingen, het wordt niet gebruikt voor andere doeleinden.  

De installatiemethoden voor het installeren en configureren van de agent Connected Machine vereisen dat de geautomatiseerde methode die u gebruikt beheerdersmachtigingen op de machines heeft. Op Linux, door het root-account en op Windows te gebruiken, als lid van de groep Lokale beheerders.

Voordat u aan de slag gaat, moet u de [vereisten](overview.md#prerequisites) bekijken en controleren of uw abonnement en resources aan de vereisten voldoen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Aan het einde van dit proces hebt u uw hybride machines met succes verbonden met Azure Arc voor servers.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Een serviceprincipal maken voor onboarding op schaal

U [Azure PowerShell](/powershell/azure/install-az-ps) gebruiken om een serviceprincipal te maken met de cmdlet [Nieuw-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) U de stappen volgen die worden vermeld onder [Een serviceprincipal maken met behulp van de Azure-portal](../../active-directory/develop/howto-create-service-principal-portal.md) om deze taak te voltooien.

> [!NOTE]
> Wanneer u een serviceprincipal maakt, moet uw account een eigenaar of gebruikerstoegangsbeheerder zijn in het abonnement dat u wilt gebruiken voor onboarding. Als u niet over voldoende machtigingen beschikt om roltoewijzingen te maken, wordt de serviceprincipal mogelijk gemaakt, maar kan deze niet aan boord van machines.
>

Voer het volgende uit om de serviceprincipal te maken met PowerShell.

1. Voer de volgende opdracht uit. U moet de uitvoer [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) van de cmdlet opslaan in een variabele, anders u het wachtwoord dat nodig is in een latere stap niet ophalen.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Voer de volgende opdracht `$sp` uit om het wachtwoord op te halen dat in de variabele is opgeslagen:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Zoek in de uitvoer de wachtwoordwaarde onder het **veldwachtwoord** en kopieer deze. Zoek ook de waarde onder het veld **ApplicationId** en kopieer het ook. Bewaar ze voor later op een veilige plaats. Als u uw servicehoofdwachtwoord vergeet of verliest, [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) u het opnieuw instellen met behulp van de cmdlet.

De waarden van de volgende eigenschappen worden `azcmagent`gebruikt met parameters die worden doorgegeven aan :

* De waarde van de eigenschap **ApplicationId** wordt gebruikt voor de `--service-principal-id` parameterwaarde
* De waarde **password** van de eigenschap `--service-principal-secret` wachtwoord wordt gebruikt voor de parameter die wordt gebruikt om de agent te verbinden.

> [!NOTE]
> Zorg ervoor dat u de eigenschap **ApplicationId** van de serviceprincipal gebruikt, niet de eigenschap **Id.**
>

De rol **Azure Connected Machine Onboarding** bevat alleen de machtigingen die nodig zijn om aan boord van een machine te gaan. U de serviceprincipal-machtiging toewijzen om het bereik ervan toe te staan een resourcegroep of een abonnement op te nemen. Als u roltoewijzing wilt toevoegen, [raadpleegt u Roltoewijzingen toevoegen of verwijderen met Azure RBAC en de Azure-portal](../../role-based-access-control/role-assignments-portal.md) of [Roltoewijzingen toevoegen of verwijderen met Azure RBAC en Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>De agent installeren en verbinding maken met Azure

De volgende stappen installeren en configureren van de agent Verbonden machine op uw hybride machines met behulp van de scriptsjabloon, die vergelijkbare stappen uitvoert die zijn beschreven in de [hybride machines verbinden met Azure in het Azure-portalartikel.](onboard-portal.md) Het verschil zit in de laatste stap waarin u `azcmagent` de verbinding met Azure Arc tot stand brengen met behulp van de opdracht met behulp van de serviceprincipal. 

Hieronder volgen de instellingen die `azcmagent` u configureert voor de serviceprincipal.

* `tenant-id`: De unieke id (GUID) die uw specifieke exemplaar van Azure AD vertegenwoordigt.
* `subscription-id`: De abonnements-ID (GUID) van uw Azure-abonnement waarin u de machines wilt hebben.
* `resource-group`: De naam van de resourcegroep waartoe u wilt dat uw aangesloten machines behoren.
* `location`: Bekijk [ondersteunde Azure-regio's](overview.md#supported-regions). Deze locatie kan hetzelfde of anders zijn als de locatie van de resourcegroep.
* `resource-name`:*( Optioneel*) Gebruikt voor de Azure-bronweergave van uw on-premises machine. Als u deze waarde niet opgeeft, wordt de hostnaam van de machine gebruikt.

U meer `azcmagent` informatie krijgen over het opdrachtregelgereedschap door de [Verwijzing naar azcmagent](azcmagent-reference.md)te bekijken.

### <a name="windows-installation-script"></a>Windows-installatiescript

Het volgende is een voorbeeld van de agent Connected Machine voor Windows-installatiescript dat is gewijzigd om de serviceprincipal te gebruiken om een volledig geautomatiseerde, niet-interactieve installatie van de agent te ondersteunen.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Linux installatiescript

Het volgende is een voorbeeld van de Connected Machine-agent voor Linux-installatiescript dat is gewijzigd om de serviceprincipal te gebruiken om een volledig geautomatiseerde, niet-interactieve installatie van de agent te ondersteunen.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc voor servers (voorbeeld), gaat u naar de Azure-portal om te controleren of de server is verbonden. Bekijk uw machines in de [Azure-portal.](https://aka.ms/hybridmachineportal)

![Een geslaagde serververbinding](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u uw machine beheert met [Azure Policy](../../governance/policy/overview.md), voor zaken als [vm-gastconfiguratie,](../../governance/policy/concepts/guest-configuration.md)het verifiëren van de machine naar de verwachte Log Analytics-werkruimte, het inschakelen van bewaking [met Azure Monitor met VM's](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)en nog veel meer.

- Meer informatie over de [log analytics-agent](../../azure-monitor/platform/log-analytics-agent.md). De agent Logboekanalyse voor Windows en Linux is vereist wanneer u het besturingssysteem en de workloads die op de machine worden uitgevoerd proactief wilt controleren, deze wilt beheren met behulp van automatiseringsrunboeken of oplossingen zoals Updatebeheer of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-intro.md)wilt gebruiken.
