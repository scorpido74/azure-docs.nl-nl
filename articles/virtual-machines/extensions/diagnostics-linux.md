---
title: Azure Compute - Linux Diagnostische extensie
description: De Azure Linux Diagnostic Extension (LAD) configureren om statistieken te verzamelen en gebeurtenissen van Linux VM's die in Azure worden uitgevoerd te registreren.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 7a7c1af1193ba391550438229a22c4a8c116e6be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289172"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>De diagnostische Linux-extensie gebruiken voor het bewaken van metrische gegevens en logboeken

Dit document beschrijft versie 3.0 en nieuwer van de Linux Diagnostische Extensie.

> [!IMPORTANT]
> Zie [dit document](../linux/classic/diagnostic-extension-v2.md)voor informatie over versie 2.3 en ouder.

## <a name="introduction"></a>Inleiding

Met de Diagnostische Extensie Linux kan een gebruiker de status controleren van een Linux-vm die op Microsoft Azure wordt uitgevoerd. Het heeft de volgende mogelijkheden:

* Verzamelt systeemprestatiestatistieken van de VM en slaat deze op in een specifieke tabel in een aangewezen opslagaccount.
* Haalt logboekgebeurtenissen op uit syslog en slaat ze op in een specifieke tabel in het aangewezen opslagaccount.
* Hiermee kunnen gebruikers de gegevensstatistieken aanpassen die worden verzameld en geüpload.
* Hiermee kunnen gebruikers de syslog-faciliteiten en ernstniveaus van gebeurtenissen aanpassen die worden verzameld en geüpload.
* Hiermee kunnen gebruikers bepaalde logboekbestanden uploaden naar een aangewezen opslagtabel.
* Ondersteunt het verzenden van statistieken en logboekgebeurtenissen naar willekeurige EventHub-eindpunten en blobs met JSON-indeling in het aangewezen opslagaccount.

Deze extensie werkt met beide Azure-implementatiemodellen.

## <a name="installing-the-extension-in-your-vm"></a>De extensie in uw virtuele machine installeren

U deze extensie inschakelen met behulp van de Azure PowerShell-cmdlets, Azure CLI-scripts, ARM-sjablonen of de Azure-portal. Zie [Extensiefuncties voor](features-linux.md)meer informatie.

Deze installatie-instructies en een [downloadbare voorbeeldconfiguratie](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configureren LAD 3.0 om:

* het vastleggen en opslaan van dezelfde statistieken als die door LAD 2.3 werden verstrekt;
* een nuttige set statistieken van bestandssystemen vastleggen, nieuw voor LAD 3.0;
* leg de standaard syslog-verzameling vast die is ingeschakeld door LAD 2.3;
* de Azure-portalervaring inschakelen voor het in kaart brengen en waarschuwen op VM-statistieken.

De downloadbare configuratie is slechts een voorbeeld; aanpassen aan uw eigen behoeften.

### <a name="prerequisites"></a>Vereisten

* **Azure Linux Agent versie 2.2.0 of hoger**. De meeste Azure VM Linux-galerijafbeeldingen bevatten versie 2.2.7 of hoger. Voer `/usr/sbin/waagent -version` deze uit om de versie te bevestigen die op de VM is geïnstalleerd. Als de VM een oudere versie van de gastagent uitvoert, volgt u [deze instructies](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) om deze bij te werken.
* **Azure CLI**. [Stel de Azure CLI-omgeving](https://docs.microsoft.com/cli/azure/install-azure-cli) in op uw machine.
* De opdracht wget, als u deze nog `sudo apt-get install wget`niet hebt: Uitvoeren.
* Een bestaand Azure-abonnement en een bestaand opslagaccount erin om de gegevens op te slaan.
* Lijst met ondersteunde Linux-distributies is ingeschakeldhttps://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Monsterinstallatie

Vul de juiste waarden in voor de variabelen in de eerste sectie voordat u deze uitvoert:

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

De voorbeeldconfiguratie die in deze voorbeelden wordt gedownload, verzamelt een set standaardgegevens en stuurt deze naar tabelopslag. De URL voor de voorbeeldconfiguratie en de inhoud ervan kunnen worden gewijzigd. In de meeste gevallen moet u een kopie van het JSON-bestand van de portalinstellingen downloaden en aanpassen aan uw behoeften, en vervolgens sjablonen of automatisering hebben die u maakt, gebruik uw eigen versie van het configuratiebestand in plaats van die URL telkens te downloaden.

#### <a name="powershell-sample"></a>Voorbeeld van PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>De extensie-instellingen bijwerken

Nadat u de instellingen Voor beveiligd of openbaar hebt gewijzigd, implementeert u deze in de vm door dezelfde opdracht uit te voeren. Als er iets is gewijzigd in de instellingen, worden de bijgewerkte instellingen naar de extensie verzonden. LAD herlaadt de configuratie en start zichzelf opnieuw op.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migratie van eerdere versies van de extensie

De nieuwste versie van de extensie is **3.0**. **Alle oude versies (2.x) worden afgeschaft en kunnen niet worden gepubliceerd op of na 31 juli 2018**.

> [!IMPORTANT]
> Deze extensie introduceert brekende wijzigingen in de configuratie van de extensie. Een van deze wijzigingen werd aangebracht om de veiligheid van de uitbreiding te verbeteren; als gevolg hiervan kon achterwaartse compatibiliteit met 2.x niet worden gehandhaafd. Ook is de Extensie-uitgever voor deze extensie anders dan de uitgever voor de 2.x-versies.
>
> Als u wilt migreren van 2.x naar deze nieuwe versie van de extensie, moet u de oude extensie verwijderen (onder de oude naam van de uitgever) en vervolgens versie 3 van de extensie installeren.

Aanbevelingen:

* Installeer de extensie met automatische kleine versie-upgrade ingeschakeld.
  * Geef op klassieke implementatiemodel VM's '3.*' op als de versie als u de extensie installeert via Azure XPLAT CLI of Powershell.
  * Neem in de VM-implementatiesjabloon 'autoUpgradeMinorVersion': true op in Azure Resource Manager-implementatiemodel VM's.
* Gebruik een nieuw/ander opslagaccount voor LAD 3.0. Er zijn verschillende kleine onverenigbaarheden tussen LAD 2.3 en LAD 3.0 die het delen van een account lastig maken:
  * LAD 3.0 slaat syslog-evenementen op in een tabel met een andere naam.
  * De tellerSpecifier `builtin` strings voor metrics verschillen in LAD 3.0.

## <a name="protected-settings"></a>Beveiligde instellingen

Deze set configuratie-informatie bevat gevoelige informatie die moet worden beschermd tegen openbare weergave, bijvoorbeeld opslagreferenties. Deze instellingen worden door de extensie in versleutelde vorm verzonden en opgeslagen.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Waarde
---- | -----
storageAccountName | De naam van het opslagaccount waarin de gegevens door de extensie zijn geschreven.
storageAccountEndPoint | (facultatief) Het eindpunt dat de cloud identificeert waarin het opslagaccount bestaat. Als deze instelling afwezig is, wordt LAD `https://core.windows.net`standaard ingesteld op de azure public cloud. Als u een opslagaccount wilt gebruiken in Azure Germany, Azure Government of Azure China, stelt u deze waarde dienovereenkomstig in.
storageAccountSasToken | Een [SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) voor blob-`ss='bt'`en tabelservices (`srt='co'`), die van toepassing is op containers en`sp='acluw'`objecten ( ), waarvoor machtigingen voor toevoegen, maken, aanbieden, bijwerken en schrijven ( ) worden toegekend. Neem *niet* het belangrijkste vraagteken (?).
mdsdHttpProxy | (facultatief) HTTP-proxy-informatie die nodig is om de extensie in staat te stellen verbinding te maken met het opgegeven opslagaccount en eindpunt.
sinksConfig | (facultatief) Details van alternatieve bestemmingen waar statistieken en evenementen kunnen worden geleverd. De specifieke details van elke gegevensgootsteen die door de uitbreiding wordt gesteund worden behandeld in de secties die volgen.

Als u een SAS-token wilt krijgen in een resourcemanagersjabloon, gebruikt u de functie **listAccountSas.** Zie Voorbeeld van [de functie Lijst](../../azure-resource-manager/templates/template-functions-resource.md#list-example)voor een voorbeeldsjabloon .

U eenvoudig het vereiste SAS-token maken via de Azure-portal.

1. Selecteer het opslagaccount voor algemene doeleinden waarop u de extensie wilt schrijven
1. Selecteer 'Handtekening voor gedeelde toegang' in het gedeelte Instellingen van het linkermenu
1. De juiste secties maken zoals eerder beschreven
1. Klik op de knop SAS genereren.

![installatiekopie](./media/diagnostics-linux/make_sas.png)

Kopieer de gegenereerde SAS naar het veld storageAccountSasToken; verwijder het vooraanstaande vraagteken ("?").

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

In deze optionele sectie worden aanvullende bestemmingen gedefinieerd waarnaar de extensie de verzamelde informatie verzendt. De array 'sink' bevat een object voor elke extra gegevensgootsteen. Het kenmerk 'type' bepaalt de andere kenmerken in het object.

Element | Waarde
------- | -----
name | Een tekenreeks die wordt gebruikt om naar deze gootsteen elders in de extensieconfiguratie te verwijzen.
type | Het type gootsteen wordt gedefinieerd. Hiermee bepaalt u de andere waarden (indien aanwezig) in gevallen van dit type.

Versie 3.0 van de Linux Diagnostic Extension ondersteunt twee sinktypes: EventHub en JsonBlob.

#### <a name="the-eventhub-sink"></a>De EventHub-gootsteen

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

De "sasURL"-vermelding bevat de volledige URL, inclusief SAS-token, voor de Gebeurtenishub waaraan gegevens moeten worden gepubliceerd. LAD vereist een SAS die een beleid noemt waarmee de claim verzenden mogelijk is. Een voorbeeld:

* Een naamruimte voor gebeurtenishubs maken, aangeroepen`contosohub`
* Een gebeurtenishub maken in de naamruimte die wordt genoemd`syslogmsgs`
* Een beleid voor gedeelde toegang `writer` maken op de gebeurtenishub met de naam Verzenden

Als u op 1 januari 2018 een SAS-good hebt gemaakt tot middernacht UTC, is de sasURL-waarde mogelijk:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Zie [deze webpagina](https://docs.microsoft.com/rest/api/eventhub/generate-sas-token#powershell)voor meer informatie over het genereren en ophalen van informatie over SAS-tokens voor Event Hubs.

#### <a name="the-jsonblob-sink"></a>De JsonBlob-gootsteen

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Gegevens die naar een JsonBlob-sink zijn gericht, worden opgeslagen in blobs in Azure-opslag. Elk exemplaar van LAD maakt elk uur een blob voor elke gootsteennaam. Elke blob bevat altijd een syntactisch geldige JSON-array van object. Nieuwe vermeldingen worden atomisch toegevoegd aan de array. Blobs worden opgeslagen in een container met dezelfde naam als de gootsteen. De Azure-opslagregels voor blobcontainernamen zijn van toepassing op de namen van JsonBlob-sinks: tussen 3 en 63 alfanumerieke ASCII-tekens of -streepjes in kleine letters.

## <a name="public-settings"></a>Openbare instellingen

Deze structuur bevat verschillende blokken van instellingen die de informatie verzameld door de extensie te controleren. Elke instelling is optioneel. Als u `ladCfg`dit opgeeft, moet u ook opgeven `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Waarde
------- | -----
StorageAccount | De naam van het opslagaccount waarin de gegevens door de extensie zijn geschreven. Moet dezelfde naam hebben als is opgegeven in de [beveiligde instellingen](#protected-settings).
mdsdHttpProxy | (facultatief) Hetzelfde als in de [beveiligde instellingen](#protected-settings). De openbare waarde wordt overschreven door de privéwaarde, indien ingesteld. Plaats proxy-instellingen die een geheim bevatten, zoals een wachtwoord, in de [beveiligde instellingen](#protected-settings).

De overige elementen worden in de volgende secties in detail beschreven.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Deze optionele structuur regelt het verzamelen van statistieken en logboeken voor levering aan de Azure Metrics-service en naar andere gegevenssinks. U moet `performanceCounters` een `syslogEvents` of beide opgeven. U moet `metrics` de structuur opgeven.

Element | Waarde
------- | -----
gebeurtenisVolume | (facultatief) Hiermee bepaalt u het aantal partities dat in de opslagtabel is gemaakt. Moet er `"Large"`een `"Medium"`zijn `"Small"`van , , of . Als dit niet is `"Medium"`opgegeven, is de standaardwaarde .
sampleRateInSeconds | (facultatief) Het standaardinterval tussen het verzamelen van ruwe (niet-geaggregeerde) metrische gegevens. De kleinste ondersteunde samplesnelheid is 15 seconden. Als dit niet is `15`opgegeven, is de standaardwaarde .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Element | Waarde
------- | -----
resourceId | De Azure Resource Manager-bron-id van de VM of van de virtuele machineschaal waarin de VM behoort. Deze instelling moet ook worden opgegeven als een JsonBlob-sink wordt gebruikt in de configuratie.
geplandTransferPeriod | De frequentie waarmee geaggregeerde statistieken moeten worden berekend en overgedragen naar Azure Metrics, uitgedrukt als een IS 8601-tijdsinterval. De kleinste transferperiode is 60 seconden, dat wil zeggen PT1M. U moet ten minste één geplande TransferPeriod opgeven.

Monsters van de statistieken die zijn opgegeven in de sectie performanceCounters worden elke 15 seconden of tegen de steekproefsnelheid verzameld die expliciet voor de teller is gedefinieerd. Als er meerdere geplandeTransferPeriod-frequenties worden weergegeven (zoals in het voorbeeld), wordt elke aggregatie onafhankelijk van elkaar berekend.

#### <a name="performancecounters"></a>prestatietellers

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Deze optionele sectie regelt de verzameling metrische gegevens. Ruwe monsters worden geaggregeerd voor elke [geplandeTransferPeriod](#metrics) om deze waarden te produceren:

* gemiddeld
* minimum
* maximum
* laatst verzamelde waarde
* aantal ruwe monsters die worden gebruikt om het aggregaat te berekenen

Element | Waarde
------- | -----
Putten | (facultatief) Een door komma's gescheiden lijst met namen van putten waarnaar LAD geaggregeerde metrische resultaten verzendt. Alle geaggregeerde statistieken worden gepubliceerd in elke vermelde gootsteen. Zie [sinksConfig](#sinksconfig). Bijvoorbeeld: `"EHsink1, myjsonsink"`.
type | Hiermee wordt de werkelijke aanbieder van de statistiek geïdentificeerd.
klasse | Samen met "teller" identificeert u de specifieke statistiek binnen de naamruimte van de provider.
counter | Samen met "klasse" identificeert u de specifieke statistiek binnen de naamruimte van de provider.
counterSpecifier | Hiermee wordt de specifieke statistiek geïdentificeerd in de naamruimte azure metrics.
Voorwaarde | (facultatief) Hiermee selecteert u een specifieke instantie van het object waarop de statistiek van toepassing is of selecteert u de aggregatie in alle instanties van dat object. Zie de `builtin` metrische definities voor meer informatie.
Samplerate | IS 8601-interval waarmee de snelheid wordt ingesteld waarmee ruwe monsters voor deze statistiek worden verzameld. Als dit niet is ingesteld, wordt het verzamelingsinterval ingesteld op basis van de waarde van [sampleRateInSeconds](#ladcfg). De kortste ondersteunde samplerate is 15 seconden (PT15S).
eenheid | Moet een van deze tekenreeksen: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Milliseconde". Hiermee definieert u de eenheid voor de statistiek. Consumenten van de verzamelde gegevens verwachten dat de verzamelde gegevenswaarden overeenkomen met deze eenheid. LAD negeert dit veld.
displayName | Het label (in de taal die is opgegeven door de bijbehorende instelling landinstelling) dat aan deze gegevens moet worden gekoppeld in Azure Metrics. LAD negeert dit veld.

De tellerSpecifier is een willekeurige identificatie. Consumenten van metrische gegevens, zoals de functie Azure-portalgrafieken en -waarschuwingen, gebruiken counterSpecifier als de 'sleutel' die een statistiek of een instantie van een statistiek identificeert. Voor `builtin` statistieken raden we u aan tegenspecifierwaarden te gebruiken die beginnen met `/builtin/`. Als u een specifieke instantie van een statistiek verzamelt, raden we u aan de id van de instantie toe te voegen aan de waarde tegenSpecifier. Een aantal voorbeelden:

* `/builtin/Processor/PercentIdleTime`- Idle tijd gemiddeld over alle vCPU's
* `/builtin/Disk/FreeSpace(/mnt)`- Vrije ruimte voor het /mnt bestandssysteem
* `/builtin/Disk/FreeSpace`- Vrije ruimte gemiddeld over alle gemonteerde bestandssystemen

Lad noch de Azure-portal verwachten dat de waarde van de tellerSpecifier overeenkomt met elk patroon. Wees consistent in de manier waarop u tegenspecifierwaarden construeert.

Wanneer u `performanceCounters`opgeeft, schrijft LAD altijd gegevens naar een tabel in Azure-opslag. U dezelfde gegevens laten schrijven naar JSON-blobs en/of gebeurtenishubs, maar u het opslaan van gegevens naar een tabel niet uitschakelen. Alle exemplaren van de diagnostische extensie die zijn geconfigureerd om dezelfde naam en eindpunt van het opslagaccount te gebruiken, voegen hun statistieken en logboeken toe aan dezelfde tabel. Als er te veel VM's naar dezelfde tabelpartitie worden geschreven, kan Azure het schrijven naar die partitie beperken. Met de instelling gebeurtenisVolume worden items verspreid over 1 (Klein), 10 (Gemiddeld) of 100 (Groot) verschillende partities. Meestal is "Medium" voldoende om ervoor te zorgen dat het verkeer niet wordt beperkt. De functie Azure Metrics van de Azure-portal gebruikt de gegevens in deze tabel om grafieken te produceren of waarschuwingen te activeren. De tabelnaam is de samenvoeging van deze tekenreeksen:

* `WADMetrics`
* De "geplandeTransferPeriod" voor de geaggregeerde waarden die zijn opgeslagen in de tabel
* `P10DV2S`
* Een datum, in de vorm "YYYYMMDD", die elke 10 dagen verandert

Voorbeelden hiervan `WADMetricsPT1HP10DV2S20170410` `WADMetricsPT1MP10DV2S20170609`zijn en .

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Deze optionele sectie regelt de verzameling logboekgebeurtenissen van syslog. Als de sectie wordt weggelaten, worden syslog-gebeurtenissen helemaal niet vastgelegd.

De syslogEventConfiguration collectie heeft één vermelding voor elke syslog faciliteit van belang. Als minSeverity "NONE" is voor een bepaalde faciliteit, of als die faciliteit helemaal niet in het element voorkomt, worden er geen gebeurtenissen uit die faciliteit vastgelegd.

Element | Waarde
------- | -----
Putten | Een door komma's gescheiden lijst met namen van putten waaraan afzonderlijke logboekgebeurtenissen worden gepubliceerd. Alle logboekgebeurtenissen die overeenkomen met de beperkingen in syslogEventConfiguration worden gepubliceerd in elke vermelde gootsteen. Voorbeeld: "EHforsyslog"
facilityName | Een naam van de syslog-faciliteit (zoals 'LOG-gebruiker'\_of 'LOG\_LOCAL0'). Zie de "faciliteit" sectie van de [syslog man pagina](http://man7.org/linux/man-pages/man3/syslog.3.html) voor de volledige lijst.
minErnst | Een syslog ernstniveau (zoals\_'LOG ERR'\_of 'LOG INFO'). Zie de "niveau" sectie van de [syslog man pagina](http://man7.org/linux/man-pages/man3/syslog.3.html) voor de volledige lijst. De extensie legt gebeurtenissen vast die naar de faciliteit op of boven het opgegeven niveau worden verzonden.

Wanneer u `syslogEvents`opgeeft, schrijft LAD altijd gegevens naar een tabel in Azure-opslag. U dezelfde gegevens laten schrijven naar JSON-blobs en/of gebeurtenishubs, maar u het opslaan van gegevens naar een tabel niet uitschakelen. Het partitiegedrag voor deze tabel is `performanceCounters`hetzelfde als beschreven voor . De tabelnaam is de samenvoeging van deze tekenreeksen:

* `LinuxSyslog`
* Een datum, in de vorm "YYYYMMDD", die elke 10 dagen verandert

Voorbeelden hiervan `LinuxSyslog20170410` `LinuxSyslog20170609`zijn en .

### <a name="perfcfg"></a>perfCfg perfCfg

Deze optionele sectie regelt [OMI](https://github.com/Microsoft/omi) de uitvoering van willekeurige OMI-query's.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Element | Waarde
------- | -----
naamruimte | (facultatief) De OMI-naamruimte waarbinnen de query moet worden uitgevoerd. Als deze niet is opgegeven, is de standaardwaarde 'root/scx', geïmplementeerd door de [Providers voor cross-platform van het Systeemcentrum.](https://github.com/Microsoft/SCXcore)
query | De OMI-query die moet worden uitgevoerd.
tabel | (facultatief) De Azure-opslagtabel in het aangewezen opslagaccount (zie [Beveiligde instellingen).](#protected-settings)
frequency | (facultatief) Het aantal seconden tussen de uitvoering van de query. De standaardwaarde is 300 (5 minuten); minimumwaarde is 15 seconden.
Putten | (facultatief) Een door komma's gescheiden lijst met namen van extra putten waaraan ruwe monstermetrische resultaten moeten worden gepubliceerd. Geen enkele aggregatie van deze ruwe voorbeelden wordt berekend door de extensie of door Azure Metrics.

"tabel" of "putten", of beide, moeten worden opgegeven.

### <a name="filelogs"></a>fileLogs

Hiermee bepaalt u het vastleggen van logboekbestanden. LAD legt nieuwe tekstregels vast terwijl ze naar het bestand worden geschreven en schrijft ze naar tabelrijen en/of opgegeven sinks (JsonBlob of EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Waarde
------- | -----
file | De volledige padnaam van het logboekbestand dat moet worden bekeken en vastgelegd. De padnaam moet één bestand een naam geven; het kan geen naam geven aan een map of wildcards bevatten.
tabel | (facultatief) De Azure-opslagtabel, in het aangewezen opslagaccount (zoals opgegeven in de beveiligde configuratie), waarin nieuwe regels van de "staart" van het bestand zijn geschreven.
Putten | (facultatief) Een door komma's gescheiden lijst met namen van extra sinks waarnaar logboekregels zijn verzonden.

"tabel" of "putten", of beide, moeten worden opgegeven.

## <a name="metrics-supported-by-the-builtin-provider"></a>Statistieken ondersteund door de ingebouwde provider

De ingebouwde metrische provider is een bron van statistieken die het meest interessant zijn voor een brede groep gebruikers. Deze statistieken vallen in vijf brede klassen:

* Processor
* Geheugen
* Netwerk
* Bestandssysteem
* Schijf

### <a name="builtin-metrics-for-the-processor-class"></a>ingebouwde statistieken voor de klasse Processor

De klasse Processor met statistieken bevat informatie over het processorgebruik in de VM. Bij het aggregeren van percentages is het resultaat het gemiddelde voor alle CPU's. In een twee-vCPU VM, als een vCPU was 100% druk en de andere was 100% idle, de gerapporteerde PercentIdleTime zou 50. Als elke vCPU was 50% druk voor dezelfde periode, het gerapporteerde resultaat zou ook 50. In een vier-vCPU VM, met een vCPU 100% bezet en de anderen idle, de gerapporteerde PercentIdleTime zou 75.

counter | Betekenis
------- | -------
PercentidleTime PercentidleTime | Tijdspercentage tijdens het aggregatievenster dat processors de niet-actieve kernellus uitvoerden
Percentprocessortijd | Percentage tijd voor het uitvoeren van een niet-actieve thread
PercentIOWaitTime | Percentage tijd dat wacht op het voltooien van IO-bewerkingen
PercentinterruptTime PercentinterruptTime | Percentage tijd dat hardware/software wordt uitgevoerd en DPC's (uitgestelde procedureoproepen)
PercentUserTime PercentuserTime PercentUserTime | Van de niet-inactieve tijd tijdens het aggregatievenster is het percentage tijd dat de gebruiker meer bij de normale prioriteit
PercentNiceTime PercentNiceTime | Van de niet-idle tijd, het percentage besteed aan verlaagde (mooie) prioriteit
PercentPrivilegedTime PercentprivilegedTime | Van de niet-inactieve tijd wordt het percentage dat wordt besteed in de bevoorrechte (kernel)modus

De eerste vier tellers moeten optellen tot 100%. De laatste drie tellers tellen ook op tot 100%; ze verdelen de som van PercentProcessorTime, PercentIOWaitTime en PercentInterruptTime.

Als u één metrische gegevens wilt `"condition": "IsAggregate=TRUE"`verkrijgen die zijn samengevoegd voor alle processors, stelt u in . Als u een statistiek voor een specifieke processor wilt verkrijgen, zoals de `"condition": "Name=\\"1\\""`tweede logische processor van een vm met vier vCPU, stelt u deze in . Logische processornummers bevinden `[0..n-1]`zich in het bereik .

### <a name="builtin-metrics-for-the-memory-class"></a>ingebouwde statistieken voor de klasse Geheugen

De klasse Geheugen met statistieken biedt informatie over geheugengebruik, paging en swapping.

counter | Betekenis
------- | -------
BeschikbaarGeheugen | Beschikbaar fysiek geheugen in MiB
PercentageBeschikbaargeheugen | Beschikbaar fysiek geheugen als een procent van het totale geheugen
Gebruiktgeheugen | Fysiek geheugen in gebruik (MiB)
PercentageGebruikte geheugen | Fysiek geheugen in gebruik als een percentage van het totale geheugen
Pagespersec Paginapersec | Totaal paging (lezen/schrijven)
PagesReadPersec | Pagina's die worden gelezen uit het back-uparchief (swapbestand, programmabestand, toegewezen bestand, enz.)
PagesWrittenPersec PagesWrittenPerSec | Pagina's die naar het back-uparchief zijn geschreven (swapbestand, toegewezen bestand, enz.)
BeschikbaarSwap | Ongebruikte ruilruimte (MiB)
PercentageBeschikbaarSwap | Ongebruikte swapruimte als percentage van de totale swap
UsedSwap | In gebruik gemaakte swapruimte (MiB)
PercentUsedSwap PercentUsedSwap | Ingebruikswisselruimte als percentage van de totale swap

Deze klasse met statistieken heeft slechts één exemplaar. Het kenmerk "voorwaarde" heeft geen nuttige instellingen en moet worden weggelaten.

### <a name="builtin-metrics-for-the-network-class"></a>ingebouwde statistieken voor de klasse Netwerk

De klasse Netwerk met statistieken biedt informatie over netwerkactiviteit op een afzonderlijke netwerkinterfaces sinds het opstarten. LAD legt geen bandbreedtestatistieken bloot, die kunnen worden opgehaald uit hoststatistieken.

counter | Betekenis
------- | -------
Bytes verzonden | Totaal aantal verzonden bytes sinds het opstarten
Ontvangen bytes | Totaal aantal ontvangen bytes sinds het opstarten
BytesTotaal | Totaal aantal verzonden of ontvangen bytes sinds het opstarten
Verzonden pakketten | Totaal aantal pakketten dat sinds het opstarten is verzonden
PakkettenOntvangen | Totaal aantal pakketten ontvangen sinds het opstarten
TotalRxFouten | Aantal ontvangstfouten sinds het opstarten
TotalTxFouten | Aantal zendfouten sinds het opstarten
TotalCollisions TotalCollisions TotalCollisions TotalCollision | Aantal botsingen gemeld door de netwerkpoorten sinds het opstarten

 Hoewel deze klasse is instantie, ondersteunt LAD geen het vastleggen van netwerkstatistieken die zijn samengevoegd op alle netwerkapparaten. Als u de statistieken voor een specifieke interface `"condition": "InstanceID=\\"eth0\\""`wilt verkrijgen, zoals eth0, stelt u in .

### <a name="builtin-metrics-for-the-filesystem-class"></a>ingebouwde statistieken voor de klasse Filesystem

De klasse Filesystem met metrische gegevens bevat informatie over het gebruik van bestandssystemen. Absolute en percentagewaarden worden gerapporteerd omdat ze worden weergegeven aan een gewone gebruiker (niet root).

counter | Betekenis
------- | -------
FreeSpace | Beschikbare schijfruimte in bytes
Gebruikte ruimte | Gebruikte schijfruimte in bytes
PercentFreeSpace PercentFreeSpace | Percentage vrije ruimte
PercentUsedspace Percentusedspace | Percentage gebruikte ruimte
PercentFreeInodes | Percentage ongebruikte inodes
PercentUsedInodes PercentUsedInodes | Percentage toegewezen (in gebruik) inodes samengevat over alle bestandssystemen
BytesReadPerSeconde | Bytes per seconde gelezen
BytesWrittenPerSeconde | Bytes geschreven per seconde
BytesPerSeconde | Bytes gelezen of geschreven per seconde
ReadsPerSeconde | Leesbewerkingen per seconde
WritesperSeconde | Schrijfbewerkingen per seconde
TransfersPerSeconde | Bewerkingen per seconde lezen of schrijven

Geaggregeerde waarden in alle bestandssystemen `"condition": "IsAggregate=True"`kunnen worden verkregen door in te stellen . Waarden voor een specifiek gemonteerd bestandssysteem, zoals "/mnt", `"condition": 'Name="/mnt"'`kunnen worden verkregen door in te stellen . 

**OPMERKING**: Als u de Azure Portal gebruikt in plaats van JSON, is het juiste voorwaardeveldformulier Name='/mnt'

### <a name="builtin-metrics-for-the-disk-class"></a>ingebouwde statistieken voor de klasse Schijf

De klasse Schijf met statistieken bevat informatie over het gebruik van schijfapparaten. Deze statistieken zijn van toepassing op het hele station. Als er meerdere bestandssystemen op een apparaat zijn, worden de tellers voor dat apparaat effectief over alle apparaten samengevoegd.

counter | Betekenis
------- | -------
ReadsPerSeconde | Leesbewerkingen per seconde
WritesperSeconde | Schrijfbewerkingen per seconde
TransfersPerSeconde | Totaal aantal bewerkingen per seconde
Gemiddelde leestijd | Gemiddelde seconden per leesbewerking
Gemiddelde schrijftijd | Gemiddelde seconden per schrijfbewerking
Gemiddelde transfertijd | Gemiddelde seconden per bewerking
Gemiddelde Schijfwachtrijlengte | Gemiddeld aantal schijfbewerkingen in de wachtrij
ReadBytesPerSeconde | Aantal gelezen bytes per seconde
SchrijfbytesPerSeconde | Aantal geschreven bytes per seconde
BytesPerSeconde | Aantal gelezen of geschreven bytes per seconde

Geaggregeerde waarden voor alle schijven `"condition": "IsAggregate=True"`kunnen worden verkregen door in te stellen . Om informatie te krijgen voor een specifiek apparaat (bijvoorbeeld `"condition": "Name=\\"/dev/sdf1\\""`/dev/sdf1), stel je in .

## <a name="installing-and-configuring-lad-30-via-cli"></a>LAD 3.0 via de CLI installeren en configureren

Als u uw beveiligde instellingen in het bestand PrivateConfig.json vindt en uw openbare configuratiegegevens zich in PublicConfig.json bevinden, voert u de opdracht uit:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

De opdracht gaat ervan uit dat u de Azure Resource Management-modus (arm) van de Azure CLI gebruikt. Als u LAD wilt configureren voor VM's voor het klassieke`azure config mode asm`implementatiemodel (ASM), schakelt u over naar de asm-modus () en laat u de naam van de resourcegroep in de opdracht weg. Zie voor meer informatie de [cross-platform CLI-documentatie.](https://docs.microsoft.com/azure/xplat-cli-connect)

## <a name="an-example-lad-30-configuration"></a>Een voorbeeld van LAD 3.0-configuratie

Op basis van de voorgaande definities, hier is een voorbeeld LAD 3.0 extensie configuratie met enige uitleg. Als u dit voorbeeld wilt toepassen op uw aanvraag, moet u uw eigen opslagaccountnaam, account SAS-token en EventHubs SAS-tokens gebruiken.

### <a name="privateconfigjson"></a>PrivateConfig.json

Deze privé-instellingen configureren:

* een opslagaccount
* een overeenkomend SAS-token voor account
* verschillende sinks (JsonBlob of EventHubs met SAS-tokens)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Deze openbare instellingen zorgen ervoor dat LAD:

* Statistieken over procent-processoren en statistieken voor `WADMetrics*` gebruikte schijfruimte naar de tabel uploaden
* Berichten uploaden van syslog facility "user" en `LinuxSyslog*` ernst "info" naar de tabel
* Onbewerkte OMI-queryresultaten (PercentProcessorTime en PercentIdleTime) uploaden naar de benoemde `LinuxCPU` tabel
* Toegevoegde regels in `/var/log/myladtestlog` bestand `MyLadTestLog` uploaden naar de tabel

In elk geval worden de gegevens ook geüpload naar:

* Azure Blob-opslag (containernaam is zoals gedefinieerd in de JsonBlob-sink)
* EventHubs-eindpunt (zoals opgegeven in de gebeurteniszinktafel)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

De `resourceId` in de configuratie moet overeenkomen met die van de VM of de virtuele machine schaal set.

* Azure-platformstatistieken die in kaart brengen en waarschuwen, kent de resourceId van de VM waar u aan werkt. Het verwacht de gegevens voor uw VM te vinden met behulp van de resourceId de opzoeksleutel.
* Als u Azure-autoscale gebruikt, moet de resourceId in de configuratie van de automatische schaal overeenkomen met de resourceId die door LAD wordt gebruikt.
* De resourceId is ingebouwd in de namen van JsonBlobs geschreven door LAD.

## <a name="view-your-data"></a>Uw gegevens weergeven

Gebruik de Azure-portal om prestatiegegevens weer te geven of waarschuwingen in te stellen:

![installatiekopie](./media/diagnostics-linux/graph_metrics.png)

De `performanceCounters` gegevens worden altijd opgeslagen in een Azure Storage-tabel. Azure Storage API's zijn beschikbaar voor veel talen en platforms.

Gegevens die naar JsonBlob-sinks worden verzonden, worden opgeslagen in blobs in het opslagaccount met de naam [Beveiligde instellingen](#protected-settings). U de blobgegevens gebruiken met azure blob-opslag-API's.

Bovendien u deze ui-hulpprogramma's gebruiken om toegang te krijgen tot de gegevens in Azure Storage:

* Visual Studio Server Explorer.
* [Microsoft Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Opslagverkenner").

Deze momentopname van een Microsoft Azure Storage Explorer-sessie toont de gegenereerde Azure Storage-tabellen en -containers van een correct geconfigureerde LAD 3.0-extensie op een test-vm. De afbeelding komt niet precies overeen met de [voorbeeldconfiguratie LAD 3.0.](#an-example-lad-30-configuration)

![installatiekopie](./media/diagnostics-linux/stg_explorer.png)

Bekijk de relevante [EventHubs-documentatie](../../event-hubs/event-hubs-what-is-event-hubs.md) voor meer informatie over het consumeren van berichten die zijn gepubliceerd op een EventHubs-eindpunt.

## <a name="next-steps"></a>Volgende stappen

* Maak metrische waarschuwingen in [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) voor de statistieken die u verzamelt.
* Maak [bewakingsdiagrammen](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) voor uw statistieken.
* Meer informatie over het [maken van een virtuele machineschaalset](../linux/tutorial-create-vmss.md) met behulp van uw statistieken om automatisch schalen te beheren.
