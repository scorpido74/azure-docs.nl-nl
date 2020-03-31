---
title: Leer de inhoud van virtuele machines te controleren
description: Lees hoe Azure Policy de gastconfiguratieagent gebruikt om instellingen in virtuele machines te controleren.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: cc2ba11f75da5f993b99c90e5d0cc1030003203e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257253"
---
# <a name="understand-azure-policys-guest-configuration"></a>De gastconfiguratie van Azure Policy begrijpen

Naast het controleren en [herstellen van](../how-to/remediate-resources.md) Azure-resources kan Azure Policy instellingen in een machine controleren. De validatie wordt uitgevoerd door de extensie en client voor gastconfiguratie. De extensie valideert, via de client, instellingen zoals:

- De configuratie van het besturingssysteem
- Configuratie of aanwezigheid van toepassingen
- Omgevingsinstellingen

Op dit moment worden met de functie voor gastconfiguratie van Azure Policy alleen instellingen van de machine gecontroleerd. Het is niet mogelijk om configuraties toe te passen.

## <a name="extension-and-client"></a>Uitbreiding en client

Als u instellingen in een machine wilt controleren, is een [extensie voor virtuele machines](../../../virtual-machines/extensions/overview.md) ingeschakeld. De extensie downloadt de toepasselijke beleidstoewijzing en de bijbehorende configuratiedefinitie.

### <a name="limits-set-on-the-extension"></a>Limieten voor de extensie

Om de extensie te beperken voor het beïnvloeden van toepassingen die in de machine worden uitgevoerd, mag de gastconfiguratie niet meer dan 5% van het CPU-gebruik bedragen. Deze beperking bestaat voor zowel ingebouwde als aangepaste definities.

## <a name="register-guest-configuration-resource-provider"></a>Gastconfiguratieresourceprovider registreren

Voordat u Gastconfiguratie gebruiken, moet u de resourceprovider registreren. U zich registreren via de portal of via PowerShell. De resourceprovider wordt automatisch geregistreerd als toewijzing van een gastconfiguratiebeleid via de portal wordt uitgevoerd.

### <a name="registration---portal"></a>Registratie - Portal

Voer de volgende stappen uit om de bronprovider voor gastconfiguratie te registreren via de Azure-portal:

1. Start de Azure-portal en klik op **Alle services**. Zoeken naar en selecteer **Abonnementen**.

1. Zoek en klik op het abonnement waarvoor u gastconfiguratie wilt inschakelen.

1. Klik in het linkermenu van de pagina **Abonnement** op **Resourceproviders**.

1. Filter voor of scrol totdat u **Microsoft.GuestConfiguration**hebt gevonden en klik op **Registreren** op dezelfde rij.

### <a name="registration---powershell"></a>Registratie - PowerShell

Voer de volgende opdracht uit om de resourceprovider voor gastconfiguratie via PowerShell te registreren:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Validatiegereedschappen

In de machine gebruikt de gastconfiguratieclient lokale hulpprogramma's om de controle uit te voeren.

In de volgende tabel ziet u een lijst met de lokale hulpprogramma's die op elk ondersteund besturingssysteem worden gebruikt:

|Besturingssysteem|Validatie, gereedschap|Opmerkingen|
|-|-|-|
|Windows|[Windows PowerShell gewenste statusconfiguratie](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef-kok Inspec](https://www.chef.io/inspec/)| Ruby en Python worden geïnstalleerd door de guest configuration extensie. |

### <a name="validation-frequency"></a>Validatiefrequentie

De gastconfiguratieclient controleert elke 5 minuten op nieuwe inhoud. Zodra een gastopdracht is ontvangen, worden de instellingen gecontroleerd op een interval van 15 minuten. De resultaten worden verzonden naar de gastconfiguratiebronprovider zodra de controle is voltooid. Wanneer een [beleidsevaluatietrigger](../how-to/get-compliance-data.md#evaluation-triggers) optreedt, wordt de status van de machine geschreven naar de resourceprovider gastconfiguratie. Met deze update wordt azure-beleid de eigenschappen van Azure Resource Manager geëvalueerd. Met een evaluatie van het Azure-beleid op aanvraag wordt de meest recente waarde opgehaald bij de gastconfiguratiebronprovider. Het leidt echter niet tot een nieuwe audit van de configuratie binnen de machine.

## <a name="supported-client-types"></a>Ondersteunde clienttypen

In de volgende tabel ziet u een lijst met ondersteunde besturingssysteems op Azure-afbeeldingen:

|Uitgever|Name|Versies|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ Credativ|Debian|8, 9|
|Microsoft|Windows Server|Datacenter 2012, 2012 R2 Datacenter, datacenter 2016, datacenter 2019|
|Microsoft|Windows-client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> Gastconfiguratie kan knooppunten met een ondersteund besturingssysteem controleren. Als u virtuele machines wilt controleren die een aangepaste afbeelding gebruiken, moet u de definitie **DeployIfNotExists** dupliceren en de sectie **Als** wijzigen om uw afbeeldingseigenschappen op te nemen.

### <a name="unsupported-client-types"></a>Niet-ondersteunde clienttypen

Windows Server Nano Server wordt in geen enkele versie ondersteund.

## <a name="guest-configuration-extension-network-requirements"></a>Netwerkvereisten voor gastconfiguratie-extensie

Om te communiceren met de gastconfiguratiebronprovider in Azure, hebben machines uitgaande toegang tot Azure-datacenters op poort **443**nodig. Als u een virtueel privénetwerk in Azure gebruikt dat uitgaand verkeer niet toestaat, configureert u uitzonderingen met regels [voor netwerkbeveiliging.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
De [servicetag](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" kan worden gebruikt om naar de gastconfiguratieservice te verwijzen.

## <a name="guest-configuration-definition-requirements"></a>Vereisten voor de definitie van gastconfiguratie

Voor elke audit die wordt uitgevoerd op gastconfiguratie zijn twee beleidsdefinities vereist, een definitie **van DeployIfNotExists** en een **definitie van AuditIfNotExists.** De definitie **DeployIfNotExists** wordt gebruikt om de machine voor te bereiden met de gastconfiguratieagent en andere componenten om de [validatiegereedschappen](#validation-tools)te ondersteunen.

De beleidsdefinitie **DeployIfNotExists** valideert en corrigeert de volgende items:

- Valideren van de machine is een configuratie toegewezen om te evalueren. Als er momenteel geen toewijzing aanwezig is, krijgt u de opdracht en bereidt u de machine voor door:
  - Authenticeren aan de machine met behulp van een [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md)
  - De nieuwste versie van de **Microsoft.GuestConfiguration-extensie** installeren
  - Indien nodig [validatietools](#validation-tools) en afhankelijkheden installeren

Als de toewijzing **DeployIfNotExists** niet-compatibel is, kan een [hersteltaak](../how-to/remediate-resources.md#create-a-remediation-task) worden gebruikt.

Zodra de toewijzing **DeployIfNotExists** voldoet, gebruikt de beleidstoewijzing **AuditIfNotExists** de lokale validatietools om te bepalen of de configuratietoewijzing compatibel of niet-compatibel is. Het validatieprogramma geeft de resultaten aan de gastconfiguratieclient. De client stuurt de resultaten door naar de gastextensie, waardoor ze beschikbaar zijn via de gastconfiguratiebronprovider.

Azure Policy gebruikt de **eigenschap ComplianceStatus** van de gastconfiguratiebronproviders om naleving te melden in het **nalevingsknooppunt.** Zie Voor meer informatie [nalevingsgegevens](../how-to/get-compliance-data.md).

> [!NOTE]
> Het beleid **DeployIfNotExists** is vereist om het beleid **AuditIfNotExists** resultaten te kunnen retourneren. Zonder de **DeployIfNotExists**geeft het beleid **AuditIfNotExists** '0 van 0' als status weer.

Alle ingebouwde beleidsregels voor gastconfiguratie zijn opgenomen in een initiatief om de definities voor gebruik in toewijzingen te groeperen. Het ingebouwde initiatief met de naam _ \[Preview:\]Audit Password security settings inside Linux and Windows machines_ contains 18 policies. Er zijn zes **DeployIfNotExists** en **AuditIfNotExists** paren voor Windows en drie paren voor Linux. De [beleidsdefinitielogica](definition-structure.md#policy-rule) valideert dat alleen het doelbesturingssysteem wordt geëvalueerd.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Controle van de instellingen van het besturingssysteem volgens de basislijnen van de branche

Een van de initiatieven die beschikbaar zijn in Azure Policy biedt de mogelijkheid om de instellingen van het besturingssysteem in virtuele machines te controleren na een 'basislijn' van Microsoft. De _ \[definitie,\]Voorbeeld: Controleer Windows VM's die niet overeenkomen met azure-beveiligingsbasislijninstellingen_ bevat een volledige set controleregels op basis van instellingen uit Active Directory-groepsbeleid.

De meeste instellingen zijn beschikbaar als parameters. Met deze functionaliteit u aanpassen wat wordt gecontroleerd om het beleid af te stemmen op uw organisatievereisten of om het beleid in kaart te brengen op informatie van derden, zoals regelgevingsnormen in de branche.

Sommige parameters ondersteunen een getalwaardebereik. De parameter Maximum Password Age kan bijvoorbeeld worden ingesteld met behulp van een bereikoperator om machine-eigenaren flexibiliteit te bieden. U controleren of de effectieve groepsbeleidsinstelling die gebruikers verplicht hun wachtwoorden te wijzigen niet langer dan 70 dagen mag zijn, maar niet minder dan één dag mag zijn. Zoals beschreven in de info-bubble voor de parameter, om dit bedrijfsbeleid de effectieve auditwaarde te maken, stelt u de waarde in op "1,70".

Als u het beleid toewijst met behulp van een Azure Resource Manager-implementatiesjabloon, u een parametersbestand gebruiken om deze instellingen te beheren vanuit bronbeheer. Met behulp van een tool zoals Git om wijzigingen in controlebeleid te beheren met opmerkingen bij elke incheckdocumenten, wordt aangetoond waarom een toewijzing een uitzondering moet zijn op de verwachte waarde.

#### <a name="applying-configurations-using-guest-configuration"></a>Configuraties toepassen met gastconfiguratie

De nieuwste functie van Azure Policy configureert instellingen in machines. De definitie _De tijdzone configureren op Windows-machines_ brengt wijzigingen aan in de machine door de tijdzone te configureren.

Wanneer u definities toewijst die beginnen met _Configureren,_ moet u ook de vereisten voor het implementeren van de definitie toewijzen _om gastconfiguratiebeleid in te schakelen op Windows VM's._ U deze definities combineren in een initiatief als u dat wilt.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Beleidsregels toewijzen aan machines buiten Azure

Het controlebeleid dat beschikbaar is voor gastconfiguratie omvat het resourcetype **Microsoft.HybridCompute/machines.** Alle machines die zijn aangesloten bij [Azure Arc voor servers](../../../azure-arc/servers/overview.md) die zich in het toepassingsgebied van de beleidstoewijzing bevinden, worden automatisch opgenomen.

### <a name="multiple-assignments"></a>Meerdere opdrachten

Gastconfiguratiebeleid ondersteunt momenteel alleen het toewijzen van dezelfde gasttoewijzing eenmaal per machine, zelfs als de beleidstoewijzing verschillende parameters gebruikt.

## <a name="built-in-resource-modules"></a>Ingebouwde resourcemodules

Bij het installeren van de gastconfiguratie-extensie wordt de PowerShell-module 'GuestConfiguration' meegeleverd met de nieuwste versie van DSC-bronmodules. Deze module kan worden gedownload van de PowerShell Gallery met behulp van de 'Manual Download' link van de module pagina [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). De bestandsindeling '.nupkg' kan worden omgedoopt tot '.zip' om de gecomprimeerde en beoordeling ongedaan te maken.

## <a name="client-log-files"></a>Clientlogboekbestanden

De extensie Gastconfiguratie schrijft logboekbestanden naar de volgende locaties:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Waar `<version>` verwijst naar het huidige versienummer.

### <a name="collecting-logs-remotely"></a>Logboeken op afstand verzamelen

De eerste stap bij het oplossen van problemen `Test-GuestConfigurationPackage` met configuratieconfiguraties of modules voor gastconfiguratie moet zijn om de cmdlet te gebruiken volgens de stappen om [een aangepast controlebeleid voor gastconfiguratie voor Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)te maken.
Als dat niet lukt, kan het verzamelen van clientlogboeken helpen bij het diagnosticeren van problemen.

#### <a name="windows"></a>Windows

Als u de Azure VM Run Command-mogelijkheid wilt gebruiken om informatie uit logboekbestanden in Windows-machines vast te leggen, kan het volgende voorbeeld PowerShell-script nuttig zijn. Zie [PowerShell-scripts uitvoeren in uw Windows VM met Opdracht Uitvoeren voor](../../../virtual-machines/windows/run-command.md)meer informatie.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Als u de Azure VM Run Command-mogelijkheid wilt gebruiken om informatie uit logbestanden in Linux-machines vast te leggen, kan het volgende voorbeeld Bash-script nuttig zijn. Zie [Shell-scripts uitvoeren in uw Linux VM met Opdracht Uitvoeren voor](../../../virtual-machines/linux/run-command.md) meer informatie

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Gastconfiguratievoorbeelden

Bron voor de ingebouwde beleidsgastconfiguratie-initiatieven zijn beschikbaar op de volgende locaties:

- [Ingebouwde beleidsdefinities - Gastconfiguratie](../samples/built-in-policies.md#guest-configuration)
- [Ingebouwde initiatieven - Gastconfiguratie](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy samples GitHub repo](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](../how-to/programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](../how-to/remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
