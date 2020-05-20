---
title: Meer informatie over het controleren van de inhoud van virtuele machines
description: Meer informatie over hoe Azure Policy de gast configuratie agent gebruikt om instellingen in virtuele machines te controleren.
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 6ff24f14281712497798f2c5231a8d98d7d89055
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684294"
---
# <a name="understand-azure-policys-guest-configuration"></a>De gast configuratie van Azure Policy begrijpen

Om Azure-resources te controleren en te [herstellen](../how-to/remediate-resources.md) , kunnen Azure Policy instellingen in een machine controleren. De validatie wordt uitgevoerd door de extensie en client voor gastconfiguratie. De extensie valideert, via de client, instellingen zoals:

- De configuratie van het besturings systeem
- Configuratie of aanwezigheid van toepassingen
- Omgevingsinstellingen

Op dit moment worden in de meeste Azure Policy-gast configuratie beleid alleen instellingen in de machine gecontroleerd.
Er worden geen configuraties toegepast. De uitzonde ring hierop is één ingebouwd beleid [waarnaar hieronder wordt verwezen](#applying-configurations-using-guest-configuration).

## <a name="resource-provider"></a>Resourceprovider

Voordat u de gast configuratie kunt gebruiken, moet u de resource provider registreren. De resource provider wordt automatisch geregistreerd als de toewijzing van een gast configuratie beleid wordt uitgevoerd via de portal. U kunt hand matig registreren via de [Portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure POWERSHELL](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)of [Azure cli](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="extension-and-client"></a>Extensie en client

Voor het controleren van instellingen binnen een machine is de extensie van de [virtuele machine](../../../virtual-machines/extensions/overview.md) ingeschakeld. De uitbrei ding downloadt de toepasselijke beleids toewijzing en de bijbehorende configuratie definitie.

> [!IMPORTANT]
> De gast configuratie-uitbrei ding is vereist voor het uitvoeren van controles op virtuele machines van Azure. Als u de uitbrei ding op schaal wilt implementeren, wijst u de volgende beleids definities toe: 
>  - [Vereisten implementeren om gast configuratie beleid in te scha kelen op Windows-Vm's.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>  - [Vereisten implementeren om gast configuratie beleid in te scha kelen op virtuele Linux-machines.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>Limieten die zijn ingesteld voor de uitbrei ding

Als u de uitbrei ding wilt beperken voor toepassingen die worden uitgevoerd op de computer, mag de gast configuratie niet meer dan 5% van de CPU overschrijden. Deze beperking bestaat voor zowel ingebouwde als aangepaste definities.

### <a name="validation-tools"></a>Validatie hulpprogramma's

In de computer gebruikt de gast configuratie-client lokale hulpprogram ma's om de controle uit te voeren.

De volgende tabel bevat een lijst met de lokale hulpprogram ma's die op elk ondersteund besturings systeem worden gebruikt:

|Besturingssysteem|Validatie programma|Opmerkingen|
|-|-|-|
|Windows|[Windows Power shell desired state Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef-specificatie](https://www.chef.io/inspec/)| Als Ruby en python zich niet op de computer bevinden, worden ze geïnstalleerd door de extensie gast configuratie. |

### <a name="validation-frequency"></a>Validatie frequentie

De gast configuratie client controleert elke vijf minuten op nieuwe inhoud. Zodra een gast toewijzing is ontvangen, worden de instellingen voor die configuratie opnieuw gecontroleerd met een interval van vijf tien minuten. Er worden resultaten verzonden naar de provider van de gast configuratie bron wanneer de controle is voltooid. Wanneer er een beleids [evaluatie](../how-to/get-compliance-data.md#evaluation-triggers) wordt uitgevoerd, wordt de status van de machine naar de provider van de gast configuratie genoteerd. Deze update zorgt ervoor Azure Policy de Azure Resource Manager eigenschappen te evalueren. Een Azure Policy evaluatie op aanvraag haalt de meest recente waarde op van de provider van de gast configuratie resource. Er wordt echter geen nieuwe controle van de configuratie op de computer geactiveerd.

## <a name="supported-client-types"></a>Ondersteunde client typen

Gast configuratie beleidsregels zijn inclusief nieuwe versies. Oudere versies van besturings systemen die beschikbaar zijn in azure Marketplace, worden uitgesloten als de gast configuratie agent niet compatibel is.
In de volgende tabel ziet u een lijst met ondersteunde besturings systemen in azure-installatie kopieën:

|Publisher|Name|Versies|
|-|-|-|
|Canonical|Ubuntu Server|14,04 en hoger|
|Credativ|Debian|8 en hoger|
|Microsoft|Windows Server|2012 en hoger|
|Microsoft|Windows-client|Windows 10|
|OpenLogic|CentOS|7,3 en hoger|
|Red Hat|Red Hat Enterprise Linux|7,4 en hoger|
|SuSE|SLES|12 SP3 en hoger|

Aangepaste installatie kopieën van virtuele machines worden ondersteund door gast configuratie beleidsregels, zolang ze een van de besturings systemen in de bovenstaande tabel zijn.

### <a name="unsupported-client-types"></a>Niet-ondersteunde client typen

Windows Server nano server wordt niet ondersteund in een versie.

## <a name="guest-configuration-extension-network-requirements"></a>Netwerk vereisten gast configuratie uitbreiding

Voor de communicatie met de provider van de gast configuratie resource in azure, hebben computers uitgaande toegang tot Azure-data centers op poort **443**. Als een netwerk in azure geen uitgaand verkeer toestaat, moet u uitzonde ringen configureren met de regels voor de [netwerk beveiligings groep](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . De [servicetag ' GuestAndHybridManagement ' kan](../../../virtual-network/service-tags-overview.md) worden gebruikt om te verwijzen naar de gast configuratie service.

## <a name="managed-identity-requirements"></a>Vereisten voor beheerde identiteit

De **DeployIfNotExists** -beleids regels die de uitbrei ding toevoegen aan virtuele machines, kunnen ook een door het systeem toegewezen beheerde identiteit inschakelen, als deze nog niet bestaat.

> [!WARNING]
> Vermijd het inschakelen van door de gebruiker toegewezen beheerde identiteit aan virtuele machines in het bereik voor beleids regels waarmee door het systeem toegewezen beheerde identiteit wordt ingeschakeld. De door de gebruiker toegewezen identiteit wordt vervangen, waardoor de computer niet meer reageert.

## <a name="guest-configuration-definition-requirements"></a>Vereisten voor gast configuratie definitie

Voor elke controle die wordt uitgevoerd per gast configuratie zijn twee beleids definities, een **DeployIfNotExists** -definitie en een **AuditIfNotExists** -definitie vereist.

De **DeployIfNotExists** -beleids definitie valideert en corrigeert de volgende items:

- Controleer of aan de computer een configuratie is toegewezen om te evalueren. Als er momenteel geen toewijzing aanwezig is, haalt u de toewijzing op en bereidt u de machine voor door het volgende te doen:
  - Verifiëren met de computer met behulp van een [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md)
  - De nieuwste versie van de **micro soft. GuestConfiguration** -extensie installeren
  - [Validatie hulpprogramma's](#validation-tools) en afhankelijkheden, indien nodig, installeren

Als de **DeployIfNotExists** -toewijzing niet compatibel is, kan een [herstel taak](../how-to/remediate-resources.md#create-a-remediation-task) worden gebruikt.

Zodra de **DeployIfNotExists** -toewijzing compatibel is, bepaalt de toewijzing van het **AuditIfNotExists** -beleid of de gast toewijzing compatibel of niet-compatibel is. Het validatie hulpprogramma levert de resultaten aan de gast configuratie-client. De-client stuurt de resultaten door naar de gast uitbreiding, waardoor deze beschikbaar worden gemaakt via de provider van de gast configuratie resource.

Azure Policy maakt gebruik van de **complianceStatus** -eigenschap van de gast configuratie resource providers om naleving te rapporteren in het knoop punt **naleving** . Zie [nalevings gegevens ophalen](../how-to/get-compliance-data.md)voor meer informatie.

> [!NOTE]
> Het **DeployIfNotExists** -beleid is vereist voor het **AuditIfNotExists** -beleid om resultaten te retour neren. Zonder de **DeployIfNotExists**wordt met het **AuditIfNotExists** -beleid ' 0 van 0 ' resources weer gegeven als status.

Alle ingebouwde beleids regels voor gast configuratie zijn opgenomen in een initiatief om de definities te groeperen voor gebruik in toewijzingen. Het ingebouwde initiatief met de naam _ \[ Preview \] : wachtwoord beveiliging controleren binnen Linux-en Windows-machines_ bevat 18 beleids regels. Er zijn zes **DeployIfNotExists** -en **AuditIfNotExists** -paren voor Windows en drie paren voor Linux. De [beleids definitie](definition-structure.md#policy-rule) logica valideert dat alleen het doel besturingssysteem wordt geëvalueerd.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>De instellingen van het besturings systeem controleren volgens de industrie basislijnen

Een initiatief in Azure Policy biedt de mogelijkheid om de instellingen van het besturings systeem te controleren na een ' basis lijn '. De definitie, _ \[ Preview \] : controleren van Windows-vm's die niet overeenkomen met de basis instellingen van Azure Security_ bevat een set regels op basis van Active Directory groepsbeleid.

De meeste instellingen zijn beschikbaar als para meters. Met para meters kunt u bepalen wat er wordt gecontroleerd.
Lijn het beleid uit met uw vereisten of wijs het beleid toe aan gegevens van derden, zoals industriële regelgevende normen.

Sommige para meters ondersteunen een bereik van gehele waarden. Met de instelling maximale wachtwoord duur kunt u bijvoorbeeld de instelling effectief groepsbeleid controleren. Een bereik van ' 1, 70 ' zou bevestigen dat gebruikers hun wacht woord moeten wijzigen ten minste elke 70 dagen, maar niet minder dan een dag.

Als u het beleid toewijst met behulp van een Azure Resource Manager-implementatie sjabloon, gebruikt u een parameter bestand voor het beheren van uitzonde ringen. Controleer de bestanden in een versie beheersysteem, zoals git. Opmerkingen over bestands wijzigingen bieden bewijs waarom een toewijzing een uitzonde ring is op de verwachte waarde.

#### <a name="applying-configurations-using-guest-configuration"></a>Configuraties Toep assen met behulp van gast configuratie

Met de nieuwste functie van Azure Policy configureert u instellingen binnen machines. De definitie _configureren van de tijd zone op Windows-machines_ brengt wijzigingen aan op de machine door de tijd zone te configureren.

Bij het toewijzen van definities die beginnen met _configureren_, moet u ook de _vereisten voor definitie-implementatie toewijzen om gast configuratie beleid in te scha kelen op Windows-vm's_. U kunt deze definities in een initiatief combi neren, indien gewenst.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Beleids regels toewijzen aan computers buiten Azure

Het beschik bare controle beleid voor gast configuratie omvat het resource type **micro soft. HybridCompute/machines** . Computers die worden uitgevoerd op [Azure Arc voor servers](../../../azure-arc/servers/overview.md) die zich binnen het bereik van de beleids toewijzing bevinden, worden automatisch opgenomen.

### <a name="multiple-assignments"></a>Meerdere toewijzingen

Gast configuratie beleid biedt momenteel alleen ondersteuning voor het toewijzen van dezelfde gast toewijzing per computer, zelfs als de beleids toewijzing gebruikmaakt van verschillende para meters.

## <a name="client-log-files"></a>Client logboek bestanden

De gast configuratie-extensie schrijft logboek bestanden naar de volgende locaties:

Windows`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Spreek`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Waar `<version>` verwijst naar het huidige versie nummer.

### <a name="collecting-logs-remotely"></a>Logboeken extern verzamelen

De eerste stap in het oplossen van problemen met configuratie van gast configuraties of modules moet zijn om de-cmdlet te gebruiken `Test-GuestConfigurationPackage` volgens de stappen voor het [maken van een aangepaste gast configuratie-controle beleid voor Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Als dat niet lukt, kan het verzamelen van client logboeken helpen bij het vaststellen van problemen.

#### <a name="windows"></a>Windows

Gegevens vastleggen vanuit logboek bestanden met de [opdracht Azure VM run](../../../virtual-machines/windows/run-command.md). het volgende voor beeld van een Power shell-script kan nuttig zijn.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Gegevens van logboek bestanden vastleggen met behulp van de [opdracht Azure VM run](../../../virtual-machines/linux/run-command.md), het volgende voor beeld bash-script kan handig zijn.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Voor beelden van gast configuraties

De ingebouwde beleids voorbeelden van de gast configuratie zijn beschikbaar op de volgende locaties:

- [Ingebouwde beleids definities-gast configuratie](../samples/built-in-policies.md#guest-configuration)
- [Ingebouwde initiatieven-gast configuratie](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy-voor beelden GitHub opslag plaats](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het weer geven van de details van elke instelling in de [weer gave naleving van gast configuratie](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
