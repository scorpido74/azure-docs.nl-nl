---
title: Beschikbaarheids groepen configureren voor SQL Server op virtuele RHEL-machines in azure-Linux Virtual Machines | Microsoft Docs
description: Meer informatie over het instellen van een hoge Beschik baarheid in een RHEL-cluster omgeving en het instellen van STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 01/27/2020
ms.openlocfilehash: 0eaff1685cea88d352f1a22f382b7af2ed0ed6cb
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252209"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Zelf studie: beschikbaarheids groepen configureren voor SQL Server op virtuele RHEL-machines in azure 

> [!NOTE]
> De zelf studie wordt weer gegeven in de **open bare preview**. 
>
> In deze zelf studie gebruiken we SQL Server 2017 met RHEL 7,6, maar het is wel mogelijk om SQL Server 2019 te gebruiken in RHEL 7 of RHEL 8 om HA te configureren. De opdrachten voor het configureren van resources van de beschikbaarheids groep zijn gewijzigd in RHEL 8 en u wilt het artikel bekijken, [resource voor de beschikbaarheids groep maken](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) en RHEL 8 resources voor meer informatie over de juiste opdrachten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Een nieuwe resource groep, Beschikbaarheidsset en Azure Linux Virtual Machines maken (VM)
> - Hoge Beschik baarheid inschakelen (HA)
> - Een pacemaker-cluster maken
> - Een omheinings agent configureren door een STONITH-apparaat te maken
> - SQL Server en MSSQL-hulpprogram ma's installeren op RHEL
> - SQL Server AlwaysOn-beschikbaarheids groep configureren
> - De resources van de beschikbaarheids groep (AG) configureren in het pacemaker-cluster
> - Een failover en de omheinings agent testen

In deze zelf studie wordt gebruikgemaakt van de Azure-opdracht regel interface (CLI) voor het implementeren van resources in Azure.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u liever de CLI lokaal wilt installeren en gebruiken, is voor deze zelf studie Azure CLI-versie 2.0.30 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Als u meer dan één abonnement hebt, [stelt u het abonnement](/cli/azure/manage-azure-subscriptions-azure-cli) in waarvoor u deze resources wilt implementeren.

Gebruik de volgende opdracht om een resource groep te maken `<resourceGroupName>` in een regio. Vervang `<resourceGroupName>` door de naam van uw keuze. We gebruiken `East US 2` voor deze zelf studie. Zie de volgende [Snelstartgids](../quick-create-cli.md)voor meer informatie.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Een Beschikbaarheidsset maken

De volgende stap is het maken van een Beschikbaarheidsset. Voer de volgende opdracht uit in Azure Cloud Shell en vervang `<resourceGroupName>` door de naam van de resource groep. Kies een naam voor `<availabilitySetName>`.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Zodra de opdracht is voltooid, krijgt u de volgende resultaten:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>RHEL Vm's maken in de Beschikbaarheidsset

> [!WARNING]
> Als u een PAYG-installatie kopie (betalen per gebruik) kiest en hoge Beschik baarheid (HA) configureert, moet u mogelijk uw abonnement registreren. Dit kan ertoe leiden dat u twee keer betaalt voor het abonnement, omdat er kosten in rekening worden gebracht voor het Microsoft Azure RHEL-abonnement voor de virtuele machine en een abonnement op Red Hat. Zie https://access.redhat.com/solutions/2458541 voor meer informatie.
>
> Gebruik een RHEL HA-afbeelding bij het maken van de Azure-VM om te voor komen dat u ' dubbel gefactureerd ' wordt. Afbeeldingen die worden aangeboden als RHEL-HA-afbeeldingen zijn ook PAYG installatie kopieën met HA opslag plaats vooraf ingeschakeld.

1. Een lijst met installatie kopieën van virtuele machines (VM) ophalen die RHEL bieden met HA:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    U ziet de volgende resultaten:

    ```output
    [
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.4",
              "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
              "version": "7.4.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.5",
              "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
              "version": "7.5.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.6",
              "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
              "version": "7.6.2019062019"
            }
    ]
    ```

    Voor deze zelf studie kiezen we de installatie kopie `RedHat:RHEL-HA:7.6:7.6.2019062019`.

    > [!IMPORTANT]
    > Computer namen moeten uit minder dan vijf tien tekens bestaan om de beschikbaarheids groep in te stellen. De gebruikers naam mag geen hoofd letters en wacht woorden bevatten die uit meer dan 12 tekens bestaan.

1. We willen drie Vm's maken in de Beschikbaarheidsset. Vervang het volgende in de onderstaande opdracht:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>`-een voor beeld is ' Standard_D16_v3 '
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Met de bovenstaande opdracht maakt u de virtuele machines en maakt u een standaard-VNet voor deze Vm's. Zie het artikel [AZ VM Create](https://docs.microsoft.com/cli/azure/vm) voor meer informatie over de verschillende configuraties.

Als de opdracht voor elke virtuele machine is voltooid, krijgt u de volgende resultaten te zien:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Met de standaard installatie kopie die wordt gemaakt met de bovenstaande opdracht maakt standaard een besturingssysteem schijf van 32 GB. Mogelijk is er onvoldoende schijf ruimte beschikbaar bij deze standaard installatie. U kunt de volgende para meter toevoegen aan de bovenstaande `az vm create` opdracht voor het maken van een besturingssysteem schijf met 128 GB als voor beeld: `--os-disk-size-gb 128`.
>
> U kunt vervolgens [Logical Volume Manager (LVM) configureren](../../../virtual-machines/linux/configure-lvm.md) als u de juiste mappen wilt uitbreiden voor uw installatie.

### <a name="test-connection-to-the-created-vms"></a>Verbinding met de gemaakte Vm's testen

Maak verbinding met VM1 of de andere virtuele machines met behulp van de volgende opdracht in Azure Cloud Shell. Als u uw VM-Ip's niet kunt vinden, volgt u deze [Snelstartgids op Azure Cloud shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Als de verbinding is geslaagd, ziet u de volgende uitvoer van de Linux-Terminal:

```output
[<username>@<VM1> ~]$
```

Typ `exit` om de SSH-sessie te verlaten.

## <a name="enable-high-availability"></a>Hoge Beschik baarheid inschakelen

> [!IMPORTANT]
> Als u dit gedeelte van de zelf studie wilt volt ooien, moet u beschikken over een abonnement voor RHEL en de invoeg toepassing met hoge Beschik baarheid. Als u een installatie kopie gebruikt die wordt aanbevolen in de vorige sectie, hoeft u geen ander abonnement te registreren.
 
Maak verbinding met elk VM-knoop punt en volg de onderstaande gids om HA in te scha kelen. Zie [abonnement met hoge Beschik baarheid inschakelen voor RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel)voor meer informatie.

> [!TIP]
> Het is eenvoudiger als u een SSH-sessie op elk van de virtuele machines tegelijk opent, omdat dezelfde opdrachten moeten worden uitgevoerd op elke VM in het hele artikel.
>
> Als u meerdere `sudo`-opdrachten kopieert en plakt en u wordt gevraagd om een wacht woord, worden de extra opdrachten niet uitgevoerd. Voer elke opdracht afzonderlijk uit.


1. Voer de volgende opdrachten uit op elke virtuele machine om de pacemaker-firewall poorten te openen:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Update en installeer pacemaker-pakketten op alle knoop punten met behulp van de volgende opdrachten:

    > [!NOTE]
    > **nmap** wordt geïnstalleerd als onderdeel van dit opdracht blok als een hulp programma voor het vinden van beschik bare IP-adressen in uw netwerk. U hoeft **nmap**niet te installeren, maar is later in deze zelf studie nuttig.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Stel het wacht woord in voor de standaard gebruiker die wordt gemaakt bij het installeren van pacemaker-pakketten. Gebruik hetzelfde wacht woord op alle knoop punten.

    ```bash
    sudo passwd hacluster
    ```

1. Gebruik de volgende opdracht om het hosts-bestand te openen en de omzetting van hostnamen in te stellen. Zie [AG configureren](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) voor het configureren van het hosts-bestand voor meer informatie.

    ```
    sudo vi /etc/hosts
    ```

    Voer in de **VI** -editor `i` in om tekst in te voegen en voeg op een lege regel het **privé-IP-adres** van de bijbehorende virtuele machine toe. Voeg vervolgens de naam van de virtuele machine toe na een spatie naast het IP-adres. Elke regel moet een afzonderlijke vermelding bevatten.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > U wordt aangeraden uw **privé-IP-** adres hierboven te gebruiken. Het gebruik van het open bare IP-adres in deze configuratie zorgt ervoor dat de installatie mislukt. het wordt niet aanbevolen uw VM beschikbaar te stellen voor externe netwerken.

    Als u de **VI** -editor wilt afsluiten, klikt u eerst op **ESC** en voert u vervolgens de opdracht `:wq` om het bestand te schrijven en af te sluiten.

## <a name="create-the-pacemaker-cluster"></a>Het pacemaker-cluster maken

In deze sectie wordt de pcsd-service ingeschakeld en gestart. vervolgens configureert u het cluster. Voor SQL Server on Linux worden de cluster bronnen niet automatisch gemaakt. U moet de pacemaker-resources hand matig inschakelen en maken. Zie het artikel over het [configureren van een failover-cluster exemplaar voor RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node) voor meer informatie.

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Pcsd-service en pacemaker inschakelen en starten

1. Voer de opdrachten uit op alle knoop punten. Met deze opdrachten kunnen de knoop punten na het opnieuw opstarten opnieuw lid worden van het cluster.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Verwijder alle bestaande cluster configuraties van alle knoop punten. Voer de volgende opdracht uit:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. Voer de volgende opdrachten uit op het primaire knoop punt om het cluster in te stellen.

    - Wanneer u de `pcs cluster auth` opdracht uitvoert om de cluster knooppunten te verifiëren, wordt u gevraagd een wacht woord op te vragen. Voer het wacht woord in voor de **hacluster** -gebruiker die u eerder hebt gemaakt.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Voer de volgende opdracht uit om te controleren of alle knoop punten online zijn.

    ```bash
    sudo pcs status
    ```

    Als alle knoop punten online zijn, ziet u een uitvoer die vergelijkbaar is met de volgende:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```

1. Stel de verwachte stemmen in het Live-cluster in op 3. Deze opdracht is alleen van invloed op het Live-cluster en wijzigt de configuratie bestanden niet.

    Stel op alle knoop punten de verwachte stemmen in met de volgende opdracht:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>De omheinings agent configureren

Een STONITH-apparaat biedt een omheinings agent. De onderstaande instructies zijn gewijzigd voor deze zelf studie. Zie [een STONITH-apparaat maken](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device)voor meer informatie.
 
[Controleer de versie van de Azure Fence-agent om er zeker van te zijn dat deze is bijgewerkt](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Gebruik de volgende opdracht:

```bash
sudo yum info fence-agents-azure-arm
```

In het onderstaande voor beeld ziet u een vergelijk bare uitvoer.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Een nieuwe toepassing registreren in Azure Active Directory
 
 1. Ga naar https://portal.azure.com
 2. Open de [blade Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Ga naar eigenschappen en noteer de map-ID. Dit is de `tenant ID`
 3. Klik op [ **app-registraties**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Klik op **nieuwe registratie**
 5. Voer een **naam** in, zoals `<resourceGroupName>-app`, selecteer **alleen accounts in deze organisatie Directory**
 6. Selecteer het **Web**van het type toepassing, voer een AANMELDINGS-URL in (bijvoorbeeld http://localhost) en klik op toevoegen. De aanmeldings-URL wordt niet gebruikt en kan geldige URL zijn
 7. Selecteer **certificaten en geheimen**en klik vervolgens op **Nieuw client geheim**
 8. Voer een beschrijving in voor een nieuwe sleutel (client geheim), selecteer **nooit verloopt** en klik op **toevoegen**
 9. Noteer de waarde van het geheim. Dit wordt gebruikt als het wacht woord voor de Service-Principal
10. Selecteer **Overzicht**. Noteer de toepassings-ID. Deze wordt gebruikt als de gebruikers naam (aanmeldings-ID in de onderstaande stappen) van de Service-Principal
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Een aangepaste rol maken voor de Fence-agent

Volg de zelf studie voor het [maken van een aangepaste rol voor Azure-resources met behulp van Azure cli](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Het JSON-bestand moet er ongeveer als volgt uitzien:

- Vervang `<username>` door een naam van uw keuze. Dit is om te voor komen dat u dupliceert bij het maken van deze roldefinitie.
- Vervang `<subscriptionId>` door de ID van uw Azure-abonnement.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Voer de volgende opdracht uit om de rol toe te voegen:

- Vervang `<filename>` door de naam van het bestand.
- Als u de opdracht uitvoert vanaf een ander pad dan de map waarin het bestand is opgeslagen, neemt u het mappad van het bestand op in de opdracht.

```bash
az role definition create --role-definition "<filename>.json"
```

In dat geval moet de volgende uitvoer worden weergegeven:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>De aangepaste rol toewijzen aan de Service-Principal

Wijs de aangepaste functie `Linux Fence Agent Role-<username>` die in de laatste stap is gemaakt, toe aan de Service-Principal. De rol van eigenaar niet meer gebruiken.
 
1. Ga naar https://portal.azure.com
2. Open de [Blade alle resources](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Selecteer de virtuele machine van het eerste clusterknooppunt
4. Klik op **toegangs beheer (IAM)**
5. Klik op **een roltoewijzing toevoegen**
6. Selecteer de rol `Linux Fence Agent Role-<username>` uit de lijst met **rollen**
7. Voer in de **selectie** lijst de naam in van de toepassing die u hierboven hebt gemaakt, `<resourceGroupName>-app`
8. Klik op **Opslaan**.
9. Herhaal de bovenstaande stappen voor het cluster knooppunt alle.

### <a name="create-the-stonith-devices"></a>De STONITH-apparaten maken

Voer de volgende opdrachten uit op het knoop punt 1:

- Vervang de `<ApplicationID>` door de ID-waarde uit de registratie van uw toepassing.
- Vervang de `<servicePrincipalPassword>` door de waarde uit het client geheim.
- Vervang de `<resourceGroupName>` door de resource groep uit uw abonnement dat voor deze zelf studie wordt gebruikt.
- Vervang de `<tenantID>` en het `<subscriptionId>` van uw Azure-abonnement.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Omdat we al een regel aan onze firewall hebben toegevoegd om de HA-service (`--add-service=high-availability`) toe te staan, is het niet nodig om de volgende firewall poorten te openen op alle knoop punten: 2224, 3121, 21064, 5405. Als u echter een type verbindings problemen met HA ondervindt, gebruikt u de volgende opdracht om deze poorten te openen die zijn gekoppeld aan HA.

> [!TIP]
> U kunt eventueel alle poorten in deze zelf studie tegelijk toevoegen om tijd te besparen. De poorten die moeten worden geopend, worden uitgelegd in hun relatieve secties hieronder. Als u nu alle poorten wilt toevoegen, voegt u de extra poorten toe: 1433 en 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>SQL Server en MSSQL-hulpprogram ma's installeren
 
Gebruik de onderstaande sectie om SQL Server en MSSQL-tools op de Vm's te installeren. Voer elk van deze acties uit op alle knoop punten. Zie [install SQL Server a Red Hat VM](/sql/linux/quickstart-install-connect-red-hat)(Engelstalig) voor meer informatie.

### <a name="installing-sql-server-on-the-vms"></a>SQL Server op de Vm's installeren

De volgende opdrachten worden gebruikt voor het installeren van SQL Server:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Open firewall poort 1433 voor externe verbindingen

U moet poort 1433 openen op de VM om extern verbinding te kunnen maken. Gebruik de volgende opdrachten om poort 1433 te openen in de firewall van elke VM:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>SQL Server opdracht regel Programma's installeren

De volgende opdrachten worden gebruikt om SQL Server opdracht regel Programma's te installeren. Zie [de SQL Server opdracht regel Programma's installeren](/sql/linux/quickstart-install-connect-red-hat#tools)voor meer informatie.

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Voor het gemak voegt u/opt/MSSQL-tools/bin/toe aan de omgevings variabele PATH. Hierdoor kunt u de hulpprogram ma's uitvoeren zonder het volledige pad op te geven. Voer de volgende opdrachten uit om het pad voor zowel aanmeldings sessies als interactieve/niet-aanmeldings sessies te wijzigen:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Controleer de status van de SQL Server

Wanneer u klaar bent met de configuratie, kunt u de status van SQL Server controleren en controleren of deze wordt uitgevoerd:

```bash
systemctl status mssql-server --no-pager
```

In dat geval moet de volgende uitvoer worden weergegeven:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-sql-server-always-on-availability-group"></a>SQL Server AlwaysOn-beschikbaarheids groep configureren

Gebruik de volgende stappen om SQL Server AlwaysOn-beschikbaarheids groep voor uw virtuele machines te configureren. Zie SQL Server AlwaysOn- [beschikbaarheids groep configureren voor hoge Beschik baarheid in Linux](/sql/linux/sql-server-linux-availability-group-configure-ha) voor meer informatie

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>AlwaysOn Availability groups inschakelen en MSSQL-server opnieuw starten

Schakel AlwaysOn-beschikbaarheids groepen in op elk knoop punt dat als host fungeert voor een SQL Server-exemplaar. Start vervolgens MSSQL-server opnieuw. Voer het volgende script uit:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Een certificaat maken

AD-verificatie wordt momenteel niet ondersteund voor het AG-eind punt. Daarom moeten we een certificaat gebruiken voor AG-eindpunt versleuteling.

1. Verbinding maken met **Alle knoop punten** met behulp van SQL Server Management Studio (SSMS) of SQL cmd. Voer de volgende opdrachten uit om AlwaysOn_health-sessie in te scha kelen en een hoofd sleutel te maken:

    > [!IMPORTANT]
    > Als u extern verbinding maakt met uw SQL Server-exemplaar, moet u poort 1433 op uw firewall hebben geopend. U moet ook binnenkomende verbindingen met poort 1433 in uw NSG toestaan voor elke VM. Zie [een beveiligings regel](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) voor het maken van een regel voor binnenkomende beveiliging maken voor meer informatie.

    - Vervang de `<Master_Key_Password>` door uw eigen wacht woord.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Maak verbinding met de primaire replica met behulp van SSMS of SQL CMD. Met de onderstaande opdrachten maakt u een certificaat op `/var/opt/mssql/data/dbm_certificate.cer` en een persoonlijke sleutel op `var/opt/mssql/data/dbm_certificate.pvk` op uw primaire SQL Server replica:

    - Vervang de `<Private_Key_Password>` door uw eigen wacht woord.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO
BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
```

Sluit de SQL-CMD-sessie af door de `exit` opdracht uit te voeren en terug te keren naar uw SSH-sessie.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Het certificaat kopiëren naar de secundaire replica's en de certificaten maken op de server

1. Kopieer de twee bestanden die zijn gemaakt op dezelfde locatie op alle servers die de beschikbaarheids replica's zullen hosten.
 
    Voer op de primaire server de volgende `scp` opdracht uit om het certificaat naar de doel servers te kopiëren:

    - Vervang `<username>` en `<VM2>` door de naam van de gebruikers naam en de doel-VM die u gebruikt.
    - Voer deze opdracht uit voor alle secundaire replica's.

    > [!NOTE]
    > U hoeft `sudo -i`niet uit te voeren. Dit geeft u de hoofd omgeving. U kunt de `sudo`-opdracht vóór elke opdracht uitvoeren zoals eerder in deze zelf studie.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Voer op de doel server de volgende opdracht uit:

    - Vervang `<username>` door uw gebruikers naam.
    - Met de `mv` opdracht worden de bestanden of mappen verplaatst van de ene locatie naar een andere.
    - De opdracht `chown` wordt gebruikt om de eigenaar en de groep met bestanden, mappen of koppelingen te wijzigen.
    - Voer deze opdrachten uit voor alle secundaire replica's.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Met het volgende Transact-SQL-script maakt u een certificaat van de back-up die u hebt gemaakt op de primaire SQL Server replica. Werk het script bij met sterke wacht woorden. Het wacht woord voor ontsleuteling is hetzelfde als het wacht woord dat u hebt gebruikt voor het maken van het PVK-bestand in de vorige stap. Als u het certificaat wilt maken, voert u het volgende script uit met behulp van SQL CMD of SSMS op alle secundaire servers:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>De data base mirroring-eind punten maken op alle replica's

Voer het volgende script uit op alle SQL-exemplaren met behulp van SQL CMD of SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
```

### <a name="create-the-availability-group"></a>De beschikbaarheids groep maken

Maak verbinding met het SQL Server-exemplaar dat als host fungeert voor de primaire replica met behulp van SQL CMD of SSMS. Voer de volgende opdracht uit om de beschikbaarheids groep te maken:

- Vervang `ag1` door de naam van de gewenste beschikbaarheids groep.
- Vervang de waarden `<VM1>`, `<VM2>`en `<VM3>` door de namen van de SQL Server instanties die de replica's hosten.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Een SQL Server aanmelding maken voor pacemaker

Maak op alle SQL-servers een SQL-aanmelding voor pacemaker. Met de volgende Transact-SQL-instructie maakt u een aanmelding.

- Vervang `<password>` door uw eigen complexe wacht woord.

```sql
USE [master]
GO
CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO
ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
```

Sla op alle SQL-servers de referenties op die worden gebruikt voor de SQL Server aanmelding. 

1. Het bestand maken:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Voeg de volgende twee regels toe aan het bestand:

    ```bash
    pacemakerLogin
    <password>
    ```

    Als u de **VI** -editor wilt afsluiten, klikt u eerst op **ESC** en voert u vervolgens de opdracht `:wq` om het bestand te schrijven en af te sluiten.

1. Het bestand alleen leesbaar maken voor de hoofdmap:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Secundaire replica's toevoegen aan de beschikbaarheids groep

1. Als u de secundaire replica's wilt toevoegen aan de AG, moet u poort 5022 op de firewall openen voor alle servers. Voer de volgende opdracht uit in uw SSH-sessie:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Voer op uw secundaire replica's de volgende opdrachten uit om ze toe te voegen aan de AG:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    ```

1. Voer het volgende Transact-SQL-script uit op de primaire replica en de secundaire replica's:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    ```

1. Zodra de secundaire replica's zijn gekoppeld, kunt u ze zien in SSMS Objectverkenner door het knoop punt **altijd op Maxi maal Beschik baarheid** uit te vouwen:

    ![Availability-Group-joined. png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Een Data Base toevoegen aan de beschikbaarheids groep

We volgen het [artikel beschikbaarheids groep configureren voor het toevoegen van een Data Base](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

In deze stap worden de volgende Transact-SQL-opdrachten gebruikt. Voer deze opdrachten uit op de primaire replica:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Controleer of de data base is gemaakt op de secundaire servers

Voer op elke secundaire SQL Server replica de volgende query uit om te zien of de db1-data base is gemaakt en de status GESYNCHRONISEERD heeft:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Als de `synchronization_state_desc` lijst is GESYNCHRONISEERD voor `db1`, betekent dit dat de replica's zijn gesynchroniseerd. De secundaire zones worden weer gegeven `db1` in de primaire replica.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Resources van een beschikbaarheids groep maken in het pacemaker-cluster

We volgen de hand leiding voor het [maken van de resources van de beschikbaarheids groep in het pacemaker-cluster](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only).

### <a name="create-the-ag-cluster-resource"></a>De AG-cluster resource maken

1. Gebruik de volgende opdracht om de resource `ag_cluster` te maken in de beschikbaarheids groep `ag1`.

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Controleer uw resource en zorg ervoor dat deze online zijn voordat u doorgaat met de volgende opdracht:

    ```bash
    sudo pcs resource
    ```

    In dat geval moet de volgende uitvoer worden weergegeven:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM1>
    ```

### <a name="create-a-virtual-ip-resource"></a>Een virtuele IP-bron maken

1. Gebruik een beschikbaar statisch IP-adres van uw netwerk om een virtuele IP-bron te maken. U kunt er een vinden met behulp van de opdracht `nmap`.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. Stel de eigenschap **stonith** in op False

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Maak de virtuele IP-bron met behulp van de volgende opdracht:

    - Vervang de `<availableIP>` waarde hieronder door een ongebruikt IP-adres.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Beperkingen toevoegen

1. Om ervoor te zorgen dat het IP-adres en de AG-bron op hetzelfde knoop punt worden uitgevoerd, moet u de beperking van een co-locatie configureren. Voer de volgende opdracht uit:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Maak een ordenings beperking om ervoor te zorgen dat de AG-resource vóór het IP-adres actief is en wordt uitgevoerd. Hoewel de beperking voor de co-locatie een volgorde beperking impliceert, wordt deze afgedwongen.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. Voer de volgende opdracht uit om de beperkingen te controleren:

    ```bash
    sudo pcs constraint list --full
    ```

    In dat geval moet de volgende uitvoer worden weergegeven:

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Stonith opnieuw inschakelen

We zijn klaar om te testen. Schakel stonith in het cluster opnieuw in door de volgende opdracht uit te voeren op knoop punt 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>De clusterstatus controleren

U kunt de status van uw cluster bronnen controleren met de volgende opdracht:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Testfailover

Om ervoor te zorgen dat de configuratie tot nu toe is geslaagd, zullen we een failover testen. Zie [Always on-failover van de beschikbaarheids groep in Linux](/sql/linux/sql-server-linux-availability-group-failover-ha)voor meer informatie.

1. Voer de volgende opdracht uit om de primaire replica hand matig te failoveren naar `<VM2>`. Vervang `<VM2>` door de waarde van de server naam.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Als u de beperkingen opnieuw controleert, ziet u dat er een andere beperking is toegevoegd vanwege de hand matige failover:

    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

1. Verwijder de beperking met ID `cli-prefer-ag_cluster-master` met behulp van de volgende opdracht:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Controleer uw cluster bronnen met behulp van de opdracht `sudo pcs resource`en u ziet dat het primaire exemplaar nu `<VM2>`is.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
     
    ```

## <a name="test-fencing"></a>Test omheining

U kunt STONITH testen door de volgende opdracht uit te voeren. Probeer de onderstaande opdracht uit te voeren vanuit `<VM1>` voor `<VM3>`.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> De actie Fence brengt standaard het knoop punt uit en vervolgens aan. Als u het knoop punt alleen offline wilt plaatsen, gebruikt u de optie `--off` in de opdracht.

U moet de volgende uitvoer ophalen:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Zie het volgende artikel over [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) voor meer informatie over het testen van een Fence-apparaat.

## <a name="next-steps"></a>Volgende stappen

Als u een beschikbaarheids groep-listener wilt gebruiken voor uw SQL-servers die u in azure hebt gemaakt, moet u eerst een load balancer maken en configureren.

> [!div class="nextstepaction"]
> [De load balancer in de Azure Portal maken en configureren](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal)
