---
title: Meer informatie over het controleren van de inhoud van virtuele machines
description: Meer informatie over hoe Azure Policy de gast configuratie agent gebruikt om instellingen in virtuele machines te controleren.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f3d99b32b952470f266ed2168d5760c2c72377c4
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666717"
---
# <a name="understand-azure-policys-guest-configuration"></a>Informatie over Azure Policy Gast-configuratie

Om Azure-resources te controleren en te [herstellen](../how-to/remediate-resources.md) , kunnen Azure Policy instellingen in een machine controleren. De validatie wordt uitgevoerd door de extensie en client voor gastconfiguratie. De extensie valideert, via de client, instellingen zoals:

- De configuratie van het besturings systeem
- Configuratie of aanwezigheid van toepassingen
- Omgevingsinstellingen

Op dit moment worden met de functie voor gastconfiguratie van Azure Policy alleen instellingen van de machine gecontroleerd. Het is niet mogelijk om configuraties toe te passen.

## <a name="extension-and-client"></a>Extensie en client

Voor het controleren van instellingen binnen een machine is de extensie van de [virtuele machine](../../../virtual-machines/extensions/overview.md) ingeschakeld. De extensie wordt gedownload voor de toewijzing van configuratiebeleid van toepassing en de bijbehorende definitie van de configuratie.

### <a name="limits-set-on-the-extension"></a>Limieten die zijn ingesteld voor de uitbrei ding

Als u de uitbrei ding wilt beperken voor toepassingen die worden uitgevoerd op de computer, mag de gast configuratie niet meer dan 5% van het CPU-gebruik overschrijden. Deze beperking bestaat voor zowel ingebouwde als aangepaste definities.

## <a name="register-guest-configuration-resource-provider"></a>Configuratie van de Gast-resourceprovider registreren

Voordat u de configuratie van de Gast gebruiken kunt, moet u de resourceprovider registreren. U kunt registreren via de portal of via PowerShell. De resource provider wordt automatisch geregistreerd als de toewijzing van een gast configuratie beleid wordt uitgevoerd via de portal.

### <a name="registration---portal"></a>Registratie - Portal

Volg deze stappen voor het registreren van de resourceprovider voor de configuratie van de Gast via Azure portal:

1. Starten van de Azure portal en klikt u op **alle services**. Zoek en selecteer **abonnementen**.

1. Zoek en klik op het abonnement dat u wilt de configuratie van de Gast voor inschakelen.

1. In het menu links van de **abonnement** pagina, klikt u op **resourceproviders**.

1. Filteren op of blader totdat u **Microsoft.GuestConfiguration**, klikt u vervolgens op **registreren** in dezelfde rij.

### <a name="registration---powershell"></a>Registratie - PowerShell

Voor het registreren van de resourceprovider voor de configuratie van de Gast via PowerShell, voer de volgende opdracht:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Hulpprogramma's voor validatie

In de computer gebruikt de gast configuratie-client lokale hulpprogram ma's om de controle uit te voeren.

De volgende tabel bevat een overzicht van de lokale hulpprogramma's die op elk ondersteund besturingssysteem wordt gebruikt:

|Besturingssysteem|Hulpprogramma voor het valideren|Opmerkingen|
|-|-|-|
|Windows|[Windows Power shell desired state Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef inspectie mogelijk](https://www.chef.io/inspec/)| Ruby en Python worden geïnstalleerd door de configuratie van de Gast-extensie. |

### <a name="validation-frequency"></a>Validatie frequentie

De gast configuratie client controleert elke vijf minuten op nieuwe inhoud. Zodra een gast toewijzing is ontvangen, worden de instellingen gecontroleerd met een interval van 15 minuten. Er worden resultaten verzonden naar de provider van de gast configuratie resource zodra de controle is voltooid. Wanneer er een beleids [evaluatie](../how-to/get-compliance-data.md#evaluation-triggers) wordt uitgevoerd, wordt de status van de machine naar de provider van de gast configuratie genoteerd. Deze update zorgt ervoor Azure Policy de Azure Resource Manager eigenschappen te evalueren. Een Azure Policy evaluatie op aanvraag haalt de meest recente waarde op van de provider van de gast configuratie resource. Er wordt echter geen nieuwe controle van de configuratie op de computer geactiveerd.

## <a name="supported-client-types"></a>Ondersteunde client-typen

De volgende tabel ziet u een lijst met ondersteunde besturingssystemen op Azure-installatiekopieën:

|Uitgever|Name|Versies|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Data Center, 2012 R2 Data Center, 2016 Data Center, 2019 Data Center|
|Microsoft|Windows-client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Met gast configuratie kunt u knoop punten controleren waarop een ondersteund besturings systeem wordt uitgevoerd. Als u virtuele machines wilt controleren die gebruikmaken van een aangepaste installatie kopie, moet u de **DeployIfNotExists** -definitie dupliceren en de **if** -sectie wijzigen zodat de eigenschappen van de installatie kopie worden opgenomen.

### <a name="unsupported-client-types"></a>Niet-ondersteunde client-typen

Windows Server nano server wordt niet ondersteund in een versie.

## <a name="guest-configuration-extension-network-requirements"></a>Netwerk vereisten gast configuratie uitbreiding

Voor de communicatie met de provider van de gast configuratie resource in azure, hebben computers uitgaande toegang tot Azure-data centers op poort **443**. Als u een particulier virtueel netwerk in azure gebruikt dat geen uitgaand verkeer toestaat, moet u uitzonde ringen configureren met de regels voor de [netwerk beveiligings groep](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . Er bestaat momenteel geen servicetag voor Azure Policy-gast configuratie.

Voor IP-adres lijsten kunt u [Azure IP-adresbereiken en service Tags](https://www.microsoft.com/download/details.aspx?id=56519)downloaden. Dit bestand wordt wekelijks bijgewerkt en heeft de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. U hoeft alleen uitgaande toegang toe te staan voor de IP-adressen in de regio's waar uw Vm's worden geïmplementeerd.

> [!NOTE]
> In het JSON-bestand met de IP-bereiken en de service tags van Azure worden de IP-adresbereiken vermeld die worden gebruikt in de Microsoft Azure data centers. Het bestand bevat compute-, SQL-en Storage-bereiken. Er wordt wekelijks een bijgewerkt bestand geplaatst. Het bestand weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weer gegeven, worden gedurende ten minste één week niet gebruikt in de data centers. Het is een goed idee om elke week het nieuwe XML-bestand te downloaden. Werk vervolgens uw site bij om de services die in Azure worden uitgevoerd, correct te identificeren. Gebruikers van Azure ExpressRoute moeten weten dat dit bestand wordt gebruikt om de Border Gateway Protocol (BGP)-advertentie van Azure Space bij te werken in de eerste week van elke maand.

## <a name="guest-configuration-definition-requirements"></a>Configuratie van de Gast de definitie van vereisten

Voor elke controle die wordt uitgevoerd per gast configuratie zijn twee beleids definities, een **DeployIfNotExists** -definitie en een **AuditIfNotExists** -definitie vereist. De definitie van de **DeployIfNotExists** wordt gebruikt om de computer voor te bereiden met de gast configuratie agent en andere onderdelen ter ondersteuning van de [validatie hulpprogramma's](#validation-tools).

De **DeployIfNotExists** beleidsdefinitie valideert en corrigeert de volgende items:

- Controleer of aan de computer een configuratie is toegewezen om te evalueren. Als er momenteel geen toewijzing aanwezig is, haalt u de toewijzing op en bereidt u de machine voor door het volgende te doen:
  - Verifiëren met de computer met behulp van een [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installeer de nieuwste versie van de **Microsoft.GuestConfiguration** extensie
  - Installeren van [-validatiehulpprogramma's](#validation-tools) en afhankelijkheden, indien nodig

Als de **DeployIfNotExists** -toewijzing niet compatibel is, kan een [herstel taak](../how-to/remediate-resources.md#create-a-remediation-task) worden gebruikt.

Zodra de **DeployIfNotExists** -toewijzing compatibel is, gebruikt de **AuditIfNotExists** -beleids toewijzing de lokale validatie hulpprogramma's om te bepalen of de configuratie toewijzing compatibel of niet-compatibel is. Het hulpprogramma voor het valideren biedt de resultaten naar de configuratie van de Gast-client. De client verzendt de resultaten naar de Gast-extensie, waardoor ze beschikbaar zijn via de configuratie van de Gast-resourceprovider.

Azure Policy maakt gebruik van de configuratie van de Gast-resourceproviders **complianceStatus** eigenschap voor rapport naleving in de **naleving** knooppunt. Zie voor meer informatie, [ophalen van Nalevingsgegevens](../how-to/get-compliance-data.md).

> [!NOTE]
> Het **DeployIfNotExists** -beleid is vereist voor het **AuditIfNotExists** -beleid om resultaten te retour neren. Zonder de **DeployIfNotExists**wordt met het **AuditIfNotExists** -beleid ' 0 van 0 ' resources weer gegeven als status.

Alle ingebouwde beleidsregels voor de configuratie van de Gast zijn opgenomen in een initiatief aan groep de definities voor gebruik in toewijzingen. Het ingebouwde initiatief met de naam _\[Preview\]: wachtwoord beveiligings instellingen controleren binnen Linux-en Windows-machines_ bevat 18 beleids regels. Er zijn zes **DeployIfNotExists** en **AuditIfNotExists** paren voor Windows en drie sets voor Linux. De [beleids definitie](definition-structure.md#policy-rule) logica valideert dat alleen het doel besturingssysteem wordt geëvalueerd.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>De instellingen van het besturings systeem controleren volgens de industrie basislijnen

Een van de beschik bare initiatieven in Azure Policy biedt de mogelijkheid om de instellingen van het besturings systeem te controleren binnen virtuele machines na een basis lijn van micro soft. De definitie, _\[Preview\]: controleren van Windows-vm's die niet overeenkomen met de basis instellingen van Azure Security_ bevat een volledige set controle regels op basis van instellingen van Active Directory groepsbeleid.

De meeste instellingen zijn beschikbaar als para meters. Met deze functionaliteit kunt u aanpassen wat er wordt gecontroleerd om het beleid uit te lijnen met de vereisten van uw organisatie, of om het beleid toe te wijzen aan gegevens van derden, zoals industriële regelgevende normen.

Sommige para meters ondersteunen een bereik van gehele waarden. Zo kan de maximale wachtwoord leeftijds parameter worden ingesteld met behulp van een bereik operator om flexibiliteit te bieden aan computer eigenaren. U kunt controleren of de effectief groepsbeleid instelling die gebruikers nodig hebben om hun wacht woord te wijzigen, niet meer dan 70 dagen mag zijn, maar niet minder dan één dag. Zoals beschreven in de info-bubble voor de para meter, stelt u de waarde in op ' 1, 70 ' om dit bedrijfs beleid de juiste controle waarde te geven.

Als u het beleid toewijst met behulp van een Azure Resource Manager-implementatie sjabloon, kunt u een parameter bestand gebruiken om deze instellingen te beheren vanuit broncode beheer. Het gebruik van een hulp programma zoals git voor het beheren van wijzigingen in controle beleid met opmerkingen bij elke check-in documenten bewijst dat een toewijzing een uitzonde ring moet zijn op de verwachte waarde.

#### <a name="applying-configurations-using-guest-configuration"></a>Configuraties Toep assen met behulp van gast configuratie

Met de nieuwste functie van Azure Policy configureert u instellingen binnen machines. De definitie _configureren van de tijd zone op Windows-machines_ brengt wijzigingen aan op de machine door de tijd zone te configureren.

Bij het toewijzen van definities die beginnen met _configureren_, moet u ook de _vereisten voor definitie-implementatie toewijzen om gast configuratie beleid in te scha kelen op Windows-vm's_. U kunt deze definities in een initiatief combi neren, indien gewenst.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Beleids regels toewijzen aan computers buiten Azure

Het beschik bare controle beleid voor gast configuratie omvat het resource type **micro soft. HybridCompute/machines** . Computers die worden uitgevoerd op [Azure Arc voor servers](../../../azure-arc/servers/overview.md) die zich binnen het bereik van de beleids toewijzing bevinden, worden automatisch opgenomen.

### <a name="multiple-assignments"></a>Meerdere toewijzingen

Gast configuratie beleid biedt momenteel alleen ondersteuning voor het toewijzen van dezelfde gast toewijzing per computer, zelfs als de beleids toewijzing gebruikmaakt van verschillende para meters.

## <a name="built-in-resource-modules"></a>Ingebouwde resource modules

Bij de installatie van de gast configuratie-uitbrei ding is de Power shell-module ' GuestConfiguration ' opgenomen in de meest recente versie van DSC-resource modules. Deze module kan vanuit de PowerShell Gallery worden gedownload met behulp van de koppeling hand matig downloaden van de module pagina [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). De bestands indeling '. nupkg ' kan worden gewijzigd in '. zip ' om deze te decomprimeren en te controleren.

## <a name="client-log-files"></a>Client logboek bestanden

De gast configuratie-extensie schrijft logboek bestanden naar de volgende locaties:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Waar `<version>` naar het huidige versie nummer verwijst.

### <a name="collecting-logs-remotely"></a>Logboeken extern verzamelen

De eerste stap bij het oplossen van problemen met configuratie van gast configuraties of modules moet de `Test-GuestConfigurationPackage`-cmdlet gebruiken volgens de stappen in [een gast configuratie pakket testen](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
Als dat niet lukt, kan het verzamelen van client logboeken helpen bij het vaststellen van problemen.

#### <a name="windows"></a>Windows

Het volgende voor beeld van een Power shell-script kan nuttig zijn als u de Azure VM-opdracht uitvoeren wilt gebruiken om gegevens op te nemen uit logboek bestanden op Windows-computers. Zie [Power shell-scripts uitvoeren in uw Windows-VM met de opdracht uitvoeren](../../../virtual-machines/windows/run-command.md)voor meer informatie.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Het volgende voor beeld van een bash-script kan nuttig zijn als u de Azure VM-opdracht Run Command wilt gebruiken om gegevens op te nemen uit logboek bestanden in Linux-machines. Zie [shell scripts uitvoeren in uw Linux-VM met de opdracht uitvoeren](../../../virtual-machines/linux/run-command.md) voor meer informatie.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Voor beelden van gast configuraties

Voor beelden voor beleids gast configuratie zijn beschikbaar op de volgende locaties:

- [Voor beelden van index-gast configuratie](../samples/index.md#guest-configuration)
- [Azure Policy-voor beelden GitHub opslag plaats](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
