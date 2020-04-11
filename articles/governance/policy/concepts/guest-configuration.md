---
title: Leer de inhoud van virtuele machines te controleren
description: Lees hoe Azure Policy de gastconfiguratieagent gebruikt om instellingen in virtuele machines te controleren.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4a2989badc099a199bf21f7e020ca8e6256ddaf0
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113419"
---
# <a name="understand-azure-policys-guest-configuration"></a>De gastconfiguratie van Azure Policy begrijpen

Naast het controleren en [herstellen van](../how-to/remediate-resources.md) Azure-resources kan Azure Policy instellingen in een machine controleren. De validatie wordt uitgevoerd door de extensie en client voor gastconfiguratie. De extensie valideert, via de client, instellingen zoals:

- De configuratie van het besturingssysteem
- Configuratie of aanwezigheid van toepassingen
- Omgevingsinstellingen

Op dit moment controleert het meeste Azure Policy Guest Configuration-beleid alleen de instellingen in de machine. Ze passen geen configuraties toe. De uitzondering is een ingebouwd beleid [waarnaar hieronder wordt verwezen](#applying-configurations-using-guest-configuration).

## <a name="extension-and-client"></a>Uitbreiding en client

Als u instellingen in een machine wilt controleren, is een [extensie voor virtuele machines](../../../virtual-machines/extensions/overview.md) ingeschakeld. De extensie downloadt de toepasselijke beleidstoewijzing en de bijbehorende configuratiedefinitie.

### <a name="limits-set-on-the-extension"></a>Limieten voor de extensie

Om de extensie te beperken voor het beïnvloeden van toepassingen die in de machine worden uitgevoerd, mag de gastconfiguratie niet meer dan 5% van de CPU bedragen. Deze beperking bestaat voor zowel ingebouwde als aangepaste definities.

## <a name="register-guest-configuration-resource-provider"></a>Gastconfiguratieresourceprovider registreren

Voordat u Gastconfiguratie gebruiken, moet u de resourceprovider registreren. U zich registreren via de [portal,](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)of [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli). De resourceprovider wordt automatisch geregistreerd als toewijzing van een gastconfiguratiebeleid via de portal wordt uitgevoerd.

## <a name="validation-tools"></a>Validatiegereedschappen

In de machine gebruikt de gastconfiguratieclient lokale hulpprogramma's om de controle uit te voeren.

In de volgende tabel ziet u een lijst met de lokale hulpprogramma's die op elk ondersteund besturingssysteem worden gebruikt:

|Besturingssysteem|Validatie, gereedschap|Opmerkingen|
|-|-|-|
|Windows|[Windows PowerShell gewenste statusconfiguratie](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef-kok Inspec](https://www.chef.io/inspec/)| Als Ruby en Python niet op de machine staan, worden ze geïnstalleerd door de gastconfiguratie-extensie. |

### <a name="validation-frequency"></a>Validatiefrequentie

De gastconfiguratieclient controleert elke 5 minuten op nieuwe inhoud. Zodra een gasttoewijzing is ontvangen, worden de instellingen voor die configuratie opnieuw gecontroleerd op een interval van 15 minuten.
Resultaten worden verzonden naar de gastconfiguratie resource provider wanneer de audit is voltooid. Wanneer een [beleidsevaluatietrigger](../how-to/get-compliance-data.md#evaluation-triggers) optreedt, wordt de status van de machine geschreven naar de resourceprovider gastconfiguratie. Met deze update wordt azure-beleid de eigenschappen van Azure Resource Manager geëvalueerd. Met een evaluatie van het Azure-beleid op aanvraag wordt de meest recente waarde opgehaald bij de gastconfiguratiebronprovider. Het leidt echter niet tot een nieuwe audit van de configuratie binnen de machine.

## <a name="supported-client-types"></a>Ondersteunde clienttypen

In de volgende tabel ziet u een lijst met ondersteunde besturingssysteems op Azure-afbeeldingen:

|Uitgever|Name|Versies|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ Credativ|Debian|8, 9|
|Microsoft|Windows Server|Datacenter 2012, 2012 R2 Datacenter, datacenter 2016, datacenter 2019|
|Microsoft|Windows-client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7|
|Suse|SLES|12 SP3|

### <a name="unsupported-client-types"></a>Niet-ondersteunde clienttypen

Windows Server Nano Server wordt in geen enkele versie ondersteund.

## <a name="guest-configuration-extension-network-requirements"></a>Netwerkvereisten voor gastconfiguratie-extensie

Om te communiceren met de gastconfiguratiebronprovider in Azure, hebben machines uitgaande toegang tot Azure-datacenters op poort **443**nodig. Als een netwerk in Azure geen uitgaand verkeer toestaat, configureert u uitzonderingen met regels [voor netwerkbeveiligingsgroepen.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
De [servicetag](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" kan worden gebruikt om naar de gastconfiguratieservice te verwijzen.

## <a name="azure-managed-identity-requirements"></a>Azure beheerde identiteitsvereisten

Met het beleid **DeployIfNotExists** dat de extensie toevoegt aan virtuele machines, u ook een beheerde identiteit met systeemtoegewezen inschakelen als deze niet bestaat.

> [!WARNING]
> Vermijd het inschakelen van door de gebruiker toegewezen beheerde identiteit aan virtuele machines in het bereik voor beleidsregels die beheerde identiteit met systeemtoegewezen status inschakelen. De door de gebruiker toegewezen identiteit wordt vervangen en kan niet meer reageren.

## <a name="guest-configuration-definition-requirements"></a>Vereisten voor de definitie van gastconfiguratie

Voor elke audit die wordt uitgevoerd op gastconfiguratie zijn twee beleidsdefinities vereist, een definitie **van DeployIfNotExists** en een **definitie van AuditIfNotExists.** 

De beleidsdefinitie **DeployIfNotExists** valideert en corrigeert de volgende items:

- Valideren van de machine is een configuratie toegewezen om te evalueren. Als er momenteel geen toewijzing aanwezig is, krijgt u de opdracht en bereidt u de machine voor door:
  - Authenticeren aan de machine met behulp van een [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md)
  - De nieuwste versie van de **Microsoft.GuestConfiguration-extensie** installeren
  - Indien nodig [validatietools](#validation-tools) en afhankelijkheden installeren

Als de toewijzing **DeployIfNotExists** niet-compatibel is, kan een [hersteltaak](../how-to/remediate-resources.md#create-a-remediation-task) worden gebruikt.

Zodra de toewijzing **DeployIfNotExists** voldoet, bepaalt de beleidstoewijzing **AuditIfNotExists** of de gasttoewijzing voldoet of niet-compatibel is. Het validatieprogramma geeft de resultaten aan de gastconfiguratieclient. De client stuurt de resultaten door naar de gastextensie, waardoor ze beschikbaar zijn via de gastconfiguratiebronprovider.

Azure Policy gebruikt de **eigenschap ComplianceStatus** van de gastconfiguratiebronproviders om naleving te melden in het **nalevingsknooppunt.** Zie Voor meer informatie [nalevingsgegevens](../how-to/get-compliance-data.md).

> [!NOTE]
> Het beleid **DeployIfNotExists** is vereist om het beleid **AuditIfNotExists** resultaten te kunnen retourneren. Zonder de **DeployIfNotExists**geeft het beleid **AuditIfNotExists** '0 van 0' als status weer.

Alle ingebouwde beleidsregels voor gastconfiguratie zijn opgenomen in een initiatief om de definities voor gebruik in toewijzingen te groeperen. Het ingebouwde initiatief met de naam _ \[Preview:\]Audit Password security inside Linux and Windows machines_ bevat 18 beleidsregels. Er zijn zes **DeployIfNotExists** en **AuditIfNotExists** paren voor Windows en drie paren voor Linux. De [beleidsdefinitielogica](definition-structure.md#policy-rule) valideert dat alleen het doelbesturingssysteem wordt geëvalueerd.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Controle van de instellingen van het besturingssysteem volgens de basislijnen van de branche

Eén initiatief in Azure Policy biedt de mogelijkheid om de instellingen van het besturingssysteem te controleren na een 'basislijn'. De _ \[definitie,\]Voorbeeld: Controleer Windows VM's die niet overeenkomen met de basislijninstellingen van Azure-beveiliging_ bevat een set regels op basis van Active Directory Group Policy.

De meeste instellingen zijn beschikbaar als parameters. Met parameters u aanpassen wat wordt gecontroleerd. Stem het beleid af op uw vereisten of wijs het beleid in kaart met informatie van derden, zoals regelgevingsstandaarden voor de industrie.

Sommige parameters ondersteunen een getalwaardebereik. De instelling Maximumwachtwoordleeftijd kan bijvoorbeeld de effectieve groepsbeleidsinstelling controleren. Een "1,70" bereik zou bevestigen dat gebruikers verplicht zijn om hun wachtwoorden te wijzigen ten minste om de 70 dagen, maar niet minder dan een dag.

Als u het beleid toewijst met een Azure Resource Manager-implementatiesjabloon, gebruikt u een parametersbestand om uitzonderingen te beheren. Controleer de bestanden op een versiebeheersysteem zoals Git. Opmerkingen over bestandswijzigingen leveren aan waarom een toewijzing een uitzondering is op de verwachte waarde.

#### <a name="applying-configurations-using-guest-configuration"></a>Configuraties toepassen met gastconfiguratie

De nieuwste functie van Azure Policy configureert instellingen in machines. De definitie _De tijdzone configureren op Windows-machines_ brengt wijzigingen aan in de machine door de tijdzone te configureren.

Wanneer u definities toewijst die beginnen met _Configureren,_ moet u ook de vereisten voor het implementeren van de definitie toewijzen _om gastconfiguratiebeleid in te schakelen op Windows VM's._ U deze definities combineren in een initiatief als u dat wilt.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Beleidsregels toewijzen aan machines buiten Azure

Het controlebeleid dat beschikbaar is voor gastconfiguratie omvat het resourcetype **Microsoft.HybridCompute/machines.** Alle machines die zijn aangesloten bij [Azure Arc voor servers](../../../azure-arc/servers/overview.md) die zich in het toepassingsgebied van de beleidstoewijzing bevinden, worden automatisch opgenomen.

### <a name="multiple-assignments"></a>Meerdere opdrachten

Gastconfiguratiebeleid ondersteunt momenteel alleen het toewijzen van dezelfde gasttoewijzing eenmaal per machine, zelfs als de beleidstoewijzing verschillende parameters gebruikt.

## <a name="client-log-files"></a>Clientlogboekbestanden

De extensie Gastconfiguratie schrijft logboekbestanden naar de volgende locaties:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Waar `<version>` verwijst naar het huidige versienummer.

### <a name="collecting-logs-remotely"></a>Logboeken op afstand verzamelen

De eerste stap bij het oplossen van problemen `Test-GuestConfigurationPackage` met configuratieconfiguraties of modules voor gastconfiguratie moet zijn om de cmdlet te gebruiken volgens de stappen om [een aangepast controlebeleid voor gastconfiguratie voor Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)te maken.
Als dat niet lukt, kan het verzamelen van clientlogboeken helpen bij het diagnosticeren van problemen.

#### <a name="windows"></a>Windows

Vastleggen van informatie uit logboekbestanden met [Azure VM Run Command](../../../virtual-machines/windows/run-command.md), het volgende voorbeeld PowerShell-script kan nuttig zijn.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Vastleggen van informatie uit logboekbestanden met [Azure VM Run Command](../../../virtual-machines/linux/run-command.md), het volgende voorbeeld Bash script kan nuttig zijn.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Gastconfiguratievoorbeelden

Ingebouwde beleidsvoorbeelden voor gastconfiguratie zijn beschikbaar op de volgende locaties:

- [Ingebouwde beleidsdefinities - Gastconfiguratie](../samples/built-in-policies.md#guest-configuration)
- [Ingebouwde initiatieven - Gastconfiguratie](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy samples GitHub repo](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het weergeven van de details van elke instelling in de [nalevingsweergave gastconfiguratie](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](../how-to/programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](../how-to/remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
