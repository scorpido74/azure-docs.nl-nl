---
title: Beschikbaarheidsgroepen configureren voor SQL Server op virtuele RHEL-machines in Azure - Virtuele Linux-machines | Microsoft Documenten
description: Meer informatie over het instellen van hoge beschikbaarheid in een RHEL-clusteromgeving en het instellen van STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 02/27/2020
ms.openlocfilehash: 40c91f67231fb6a9d01191ee5215eae8d4dc045b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096695"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Zelfstudie: Beschikbaarheidsgroepen configureren voor SQL Server op virtuele RHEL-machines in Azure 

> [!NOTE]
> De tutorial gepresenteerd is in **openbare preview**. 
>
> We gebruiken SQL Server 2017 met RHEL 7.6 in deze zelfstudie, maar het is mogelijk om SQL Server 2019 te gebruiken in RHEL 7 of RHEL 8 om HA te configureren. De opdrachten voor het configureren van bronnen voor beschikbaarheidsgroepen zijn gewijzigd in RHEL 8 en u wilt het artikel, [Beschikbaarheidsgroepbron maken](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) en RHEL 8-resources bekijken voor meer informatie over de juiste opdrachten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Een nieuwe resourcegroep, beschikbaarheidsset en Virtuele Azure-machines (Azure Linux) maken
> - Ha (Hoge beschikbaarheid) inschakelen
> - Een pacemakercluster maken
> - Een afrasteringsagent configureren door een STONITH-apparaat te maken
> - SQL Server en mssql-tools installeren op RHEL
> - SQL Server Always On Availability Group configureren
> - Ag-resources (availability group) configureren in het cluster Pacemaker
> - Test een failover en de hekwerkagent

In deze zelfstudie wordt de Azure command-line interface (CLI) gebruikt om resources in Azure te implementeren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u de CLI liever lokaal installeert en gebruikt, vereist deze zelfstudie Azure CLI-versie 2.0.30 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Als u meer dan één abonnement hebt, stelt u [het abonnement in](/cli/azure/manage-azure-subscriptions-azure-cli) waarop u deze resources wilt implementeren.

Gebruik de volgende opdracht om `<resourceGroupName>` een resourcegroep in een regio te maken. Vervang `<resourceGroupName>` door een naam van uw keuze. We gebruiken `East US 2` voor deze tutorial. Zie de volgende [Quickstart](../quick-create-cli.md)voor meer informatie.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken

De volgende stap is het maken van een beschikbaarheidsset. Voer de volgende opdracht uit in `<resourceGroupName>` Azure Cloud Shell en vervang de naam van uw Resourcegroep. Kies een `<availabilitySetName>`naam voor .

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

U moet de volgende resultaten krijgen zodra de opdracht is voltooid:

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

## <a name="create-rhel-vms-inside-the-availability-set"></a>RHEL VM's maken in de beschikbaarheidsset

> [!WARNING]
> Als u kiest voor een Pay-As-You-Go (PAYG) RHEL-afbeelding en een HA (High Availability) configureert, moet u mogelijk uw abonnement registreren. Dit kan ertoe leiden dat u twee keer betaalt voor het abonnement, omdat er kosten in rekening worden gebracht voor het Microsoft Azure RHEL-abonnement voor de VM en een abonnement op Red Hat. Zie https://access.redhat.com/solutions/2458541 voor meer informatie.
>
> Gebruik een RHEL HA-afbeelding bij het maken van de Azure VM om te voorkomen dat u 'dubbel gefactureerd' wordt. Afbeeldingen aangeboden als RHEL-HA beelden zijn ook PAYG beelden met HA repo pre-enabled.

1. Ontvang een lijst met VM-afbeeldingen (Virtual Machine) die RHEL met HA aanbieden:

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

    Voor deze zelfstudie kiezen we `RedHat:RHEL-HA:7.6:7.6.2019062019`de afbeelding.

    > [!IMPORTANT]
    > Machinenamen moeten minder dan 15 tekens bevatten om beschikbaarheidsgroep in te stellen. Gebruikersnaam mag geen hoofdletters bevatten en wachtwoorden moeten meer dan 12 tekens bevatten.

1. We willen 3 VM's maken in de beschikbaarheidsset. Vervang het volgende in de onderstaande opdracht:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>`- Een voorbeeld is "Standard_D16_v3"
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

Met de bovenstaande opdracht worden de VM's gemaakt en wordt een standaard VNet gemaakt voor deze VM's. Zie voor meer informatie over de verschillende configuraties het artikel [az vm create.](https://docs.microsoft.com/cli/azure/vm)

U moet resultaten krijgen die vergelijkbaar zijn met de volgende zodra de opdracht voor elke vm is voltooid:

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
> De standaardafbeelding die met de bovenstaande opdracht wordt gemaakt, maakt standaard een schijf van 32 GB os. Met deze standaardinstallatie u mogelijk geen ruimte meer hebben. U de volgende parameter `az vm create` aan de bovenstaande opdracht gebruiken om een OS-schijf met 128 GB als voorbeeld te maken: `--os-disk-size-gb 128`.
>
> U vervolgens [Logische volumebeheer (LVM) configureren](../../../virtual-machines/linux/configure-lvm.md) als u de juiste mapvolumes wilt uitbreiden om uw installatie te kunnen aanpassen.

### <a name="test-connection-to-the-created-vms"></a>Verbinding met de gemaakte VM's testen

Maak verbinding met VM1 of de andere VM's met de volgende opdracht in Azure Cloud Shell. Als u uw VM-IP's niet vinden, volgt u deze [Quickstart op Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Als de verbinding succesvol is, ziet u de volgende uitvoer die de Linux-terminal vertegenwoordigt:

```output
[<username>@<VM1> ~]$
```

Typ `exit` om de SSH-sessie te verlaten.

## <a name="enable-high-availability"></a>Hoge beschikbaarheid inschakelen

> [!IMPORTANT]
> Om dit gedeelte van de zelfstudie te voltooien, moet u een abonnement hebben voor RHEL en de add-on met hoge beschikbaarheid. Als u een afbeelding gebruikt die in de vorige sectie wordt aanbevolen, hoeft u geen ander abonnement te registreren.
 
Maak verbinding met elk VM-knooppunt en volg de onderstaande handleiding om HA in te schakelen. Zie abonnement [met hoge beschikbaarheid inschakelen voor RHEL voor](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel)meer informatie.

> [!TIP]
> Het zal gemakkelijker zijn als u een SSH-sessie tegelijk met elk van de VM's opent, omdat dezelfde opdrachten op elke VM in het hele artikel moeten worden uitgevoerd.
>
> Als u meerdere `sudo` opdrachten kopieert en plakt en om een wachtwoord wordt gevraagd, worden de extra opdrachten niet uitgevoerd. Voer elke opdracht afzonderlijk uit.


1. Voer de volgende opdrachten uit op elke vm om de firewallpoorten van pacemakers te openen:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Pacemakerpakketten op alle knooppunten bijwerken en installeren met de volgende opdrachten:

    > [!NOTE]
    > **nmap** is geïnstalleerd als onderdeel van dit opdrachtblok als hulpmiddel om beschikbare IP-adressen in uw netwerk te vinden. U hoeft geen **nmap**te installeren, maar het zal later nuttig zijn in deze tutorial.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Stel het wachtwoord in voor de standaardgebruiker die wordt gemaakt bij het installeren van Pacemaker-pakketten. Gebruik hetzelfde wachtwoord op alle knooppunten.

    ```bash
    sudo passwd hacluster
    ```

1. Gebruik de volgende opdracht om het hosts-bestand te openen en de oplossing van de hostnaam in te stellen. Zie [AG configureren](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) bij het configureren van het hosts-bestand voor meer informatie.

    ```
    sudo vi /etc/hosts
    ```

    Voeg in de `i` **vi-editor** tekst in en voeg op een lege regel het **privé-IP** van de bijbehorende VM toe. Voeg vervolgens de VM-naam toe na een spatie naast het IP-adres. Elke regel moet een aparte vermelding hebben.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > We raden u aan uw **ip-adres** hierboven te gebruiken. Als u het openbare IP-adres in deze configuratie gebruikt, mislukt de installatie en raden we u aan uw VM bloot te stellen aan externe netwerken.

    Als u de **vi-editor** wilt afsluiten, drukt `:wq` u eerst op de **Esc-toets** en voert u de opdracht in om het bestand te schrijven en af te sluiten.

## <a name="create-the-pacemaker-cluster"></a>Het cluster Pacemaker maken

In deze sectie schakelen we de pcsd-service in en starten we het cluster. Voor SQL Server op Linux worden de clusterbronnen niet automatisch gemaakt. We moeten de pacemakerbronnen handmatig inschakelen en maken. Zie voor meer informatie het artikel over [het configureren van een failoverclusterinstantie voor RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Pcsd-service en Pacemaker inschakelen en starten

1. Voer de opdrachten uit op alle knooppunten. Met deze opdrachten kunnen de knooppunten na het opnieuw opstarten weer bij het cluster worden toegevoegd.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Verwijder bestaande clusterconfiguratie uit alle knooppunten. Voer de volgende opdracht uit:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. Voer op het primaire knooppunt de volgende opdrachten uit om het cluster in te stellen.

    - Wanneer u `pcs cluster auth` de opdracht uitvoert om de clusterknooppunten te verifiëren, wordt u om een wachtwoord gevraagd. Voer het wachtwoord in voor de **hacluster-gebruiker** die eerder is gemaakt.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Voer de volgende opdracht uit om te controleren of alle knooppunten online zijn.

    ```bash
    sudo pcs status
    ```

    Als alle knooppunten online zijn, ziet u een uitvoer die vergelijkbaar is met de volgende:

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

1. Stel verwachte stemmen in het live cluster in op 3. Deze opdracht heeft alleen invloed op het live cluster en wijzigt de configuratiebestanden niet.

    Stel op alle knooppunten de verwachte stemmen in met de volgende opdracht:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>De afrasteringsagent configureren

Een STONITH-apparaat biedt een hekwerk. De onderstaande instructies zijn aangepast voor deze tutorial. Zie [Een STONITH-apparaat maken voor](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device)meer informatie.
 
[Controleer de versie van de Azure Fence Agent om ervoor te zorgen dat deze wordt bijgewerkt.](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation) Gebruik de volgende opdracht:

```bash
sudo yum info fence-agents-azure-arm
```

U ziet een vergelijkbare uitvoer als het onderstaande voorbeeld.

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
 2. Open het [Azure Active Directory-blad](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Ga naar Eigenschappen en noteer de Directory-id. Dit is de`tenant ID`
 3. Klik [ **op App-registraties**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Klik op **Nieuwe registratie**
 5. Een **naam** `<resourceGroupName>-app`als invoeren , **alleen Accounts in deze organisatiemap selecteren**
 6. Selecteer Web van **toepassingstype**, voer een http://localhost) aanmeldings-URL in (bijvoorbeeld en klik op Toevoegen. De aanmeldings-URL wordt niet gebruikt en kan een geldige URL zijn. Eenmaal klaar, klik op **Registreren**
 7. Selecteer **Certificaten en geheimen** voor uw nieuwe app-registratie en klik op Nieuw **clientgeheim**
 8. Voer een beschrijving in voor een nieuwe sleutel (clientgeheim), selecteer **Nooit verloopt** en klik op **Toevoegen**
 9. Noteer de waarde van het geheim. Het wordt gebruikt als wachtwoord voor de Service Principal
10. Selecteer **Overzicht**. Noteer de toepassings-id. Het wordt gebruikt als gebruikersnaam (inlog-id in de onderstaande stappen) van de Service Principal
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Een aangepaste rol voor de afrasteringsagent maken

Volg de zelfstudie om [een aangepaste rol voor Azure-resources](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role)te maken met Azure CLI .

Uw json-bestand moet er hetzelfde uitzien als het volgende:

- Vervang `<username>` door een naam van uw keuze. Dit is om dubbel werk te voorkomen bij het maken van deze roldefinitie.
- Vervang `<subscriptionId>` door uw Azure-abonnements-id.

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
- Als u de opdracht uitvoert vanaf een ander pad dan de map waarop het bestand is opgeslagen, neemt u het mappad van het bestand op in de opdracht.

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

### <a name="assign-the-custom-role-to-the-service-principal"></a>De aangepaste rol toewijzen aan de serviceprincipal

Wijs de `Linux Fence Agent Role-<username>` aangepaste rol die in de laatste stap is gemaakt toe aan de serviceprincipal. Gebruik de rol Eigenaar niet meer!
 
1. Ga naar https://portal.azure.com
2. Het [blad Alle resources openen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. De virtuele machine van het eerste clusterknooppunt selecteren
4. Klik **op Toegangsbesturingselement (IAM)**
5. Klik **op Een roltoewijzing toevoegen**
6. De rol `Linux Fence Agent Role-<username>` selecteren in de **lijst Met rollen**
7. Voer **in** de lijst Selecteren de naam in van de toepassing die u hierboven hebt gemaakt.`<resourceGroupName>-app`
8. Klik **op Opslaan**
9. Herhaal de bovenstaande stappen voor het hele clusterknooppunt.

### <a name="create-the-stonith-devices"></a>De STONITH-apparaten maken

Voer de volgende opdrachten uit op knooppunt 1:

- Vervang `<ApplicationID>` de met de ID-waarde van uw aanvraagregistratie.
- Vervang `<servicePrincipalPassword>` de met de waarde van het clientgeheim.
- Vervang `<resourceGroupName>` de groep met de resourcegroep uit uw abonnement dat voor deze zelfstudie wordt gebruikt.
- Vervang `<tenantID>` de `<subscriptionId>` en de van uw Azure-abonnement.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Aangezien we al een regel aan onze firewall`--add-service=high-availability`hebben toegevoegd om de HA-service toe te staan ( ), is het niet nodig om de volgende firewallpoorten op alle knooppunten te openen: 2224, 3121, 21064, 5405. Als u echter problemen ondervindt met een aantal verbindingsproblemen met HA, gebruikt u de volgende opdracht om deze poorten te openen die zijn gekoppeld aan HA.

> [!TIP]
> U optioneel alle poorten in deze zelfstudie tegelijk toevoegen om wat tijd te besparen. De poorten die moeten worden geopend worden uitgelegd in hun relatieve secties hieronder. Als u nu alle poorten wilt toevoegen, voegt u de extra poorten toe: 1433 en 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>SQL Server en mssql-tools installeren
 
Gebruik de onderstaande sectie om SQL Server en mssql-tools op de VM's te installeren. Voer elk van deze acties uit op alle knooppunten. Zie [SQL Server een Red Hat VM installeren](/sql/linux/quickstart-install-connect-red-hat)voor meer informatie.

### <a name="installing-sql-server-on-the-vms"></a>SQL Server installeren op de VM's

De volgende opdrachten worden gebruikt om SQL Server te installeren:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Firewallpoort 1433 openen voor externe verbindingen

U moet poort 1433 op de VM openen om op afstand verbinding te kunnen maken. Gebruik de volgende opdrachten om poort 1433 te openen in de firewall van elke vm:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>SQL Server-opdrachtregelgereedschappen installeren

De volgende opdrachten worden gebruikt om SQL Server-opdrachtregelgereedschappen te installeren. Zie [de sql server-opdrachtregelgereedschappen installeren](/sql/linux/quickstart-install-connect-red-hat#tools)voor meer informatie.

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Voeg voor het gemak /opt/mssql-tools/bin/ toe aan de variabele PATH-omgeving. Hiermee u de gereedschappen uitvoeren zonder het volledige pad op te geven. Voer de volgende opdrachten uit als u het PAD wilt wijzigen voor zowel aanmeldingssessies als interactieve/niet-aanmeldingssessies:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>De status van de SQL Server controleren

Zodra u klaar bent met de configuratie, u de status van SQL Server controleren en controleren of deze wordt uitgevoerd:

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

## <a name="configure-sql-server-always-on-availability-group"></a>SQL Server Always On Availability Group configureren

Gebruik de volgende stappen om SQL Server Always On Availability Group voor uw VM's te configureren. Zie [SQL Server Always On Availability Group configureren voor hoge beschikbaarheid op Linux voor](/sql/linux/sql-server-linux-availability-group-configure-ha) meer informatie.

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>AlwaysOn-beschikbaarheidsgroepen inschakelen en mssql-server opnieuw starten

AlwaysOn-beschikbaarheidsgroepen inschakelen op elk knooppunt dat een SQL Server-exemplaar host. Start vervolgens mssql-server opnieuw op. Voer het volgende script uit:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Een certificaat maken

We ondersteunen momenteel geen AD-verificatie naar het AG-eindpunt. Daarom moeten we een certificaat gebruiken voor AG-eindpuntversleuteling.

1. Maak verbinding met **alle knooppunten** met SQL Server Management Studio (SSMS) of SQL CMD. Voer de volgende opdrachten uit om AlwaysOn_health sessie in te schakelen en een stramientoets te maken:

    > [!IMPORTANT]
    > Als u op afstand verbinding maakt met uw SQL Server-exemplaar, moet u poort 1433 op uw firewall hebben geopend. U moet ook binnenkomende verbindingen met poort 1433 toestaan in uw NSG voor elke VM. Zie [Een beveiligingsregel maken](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) voor het maken van een binnenkomende beveiligingsregel voor meer informatie.

    - Vervang `<Master_Key_Password>` de met uw eigen wachtwoord.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Maak verbinding met de primaire replica met SSMS of SQL CMD. De onderstaande opdrachten maken `/var/opt/mssql/data/dbm_certificate.cer` een certificaat op `var/opt/mssql/data/dbm_certificate.pvk` en een privésleutel op uw primaire SQL Server-replica:

    - Vervang `<Private_Key_Password>` de met uw eigen wachtwoord.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO

BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
GO
```

Sluit de SQL CMD-sessie af door de `exit` opdracht uit te voeren en keer terug naar uw SSH-sessie.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Het certificaat kopiëren naar de secundaire replica's en de certificaten op de server maken

1. Kopieer de twee bestanden die zijn gemaakt naar dezelfde locatie op alle servers die beschikbaarheidsreplica's hosten.
 
    Voer op de primaire `scp` server de volgende opdracht uit om het certificaat naar de doelservers te kopiëren:

    - Vervang `<username>` `<VM2>` en met de gebruikersnaam en doel-VM-naam die u gebruikt.
    - Voer deze opdracht uit voor alle secundaire replica's.

    > [!NOTE]
    > Je hoeft niet te `sudo -i`lopen, wat je de wortelomgeving geeft. Je kon gewoon `sudo` de opdracht in de voorkant van elke opdracht als we eerder deden in deze tutorial.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Voer op de doelserver de volgende opdracht uit:

    - Vervang `<username>` door uw gebruikersnaam.
    - De `mv` opdracht verplaatst de bestanden of map van de ene plaats naar de andere.
    - De `chown` opdracht wordt gebruikt om de eigenaar en groep bestanden, mappen of koppelingen te wijzigen.
    - Voer deze opdrachten uit voor alle secundaire replica's.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Met het volgende Transact-SQL-script wordt een certificaat gemaakt van de back-up die u hebt gemaakt op de primaire SQL Server-replica. Werk het script bij met sterke wachtwoorden. Het decryptiewachtwoord is hetzelfde wachtwoord dat u hebt gebruikt om het .pvk-bestand in de vorige stap te maken. Als u het certificaat wilt maken, voert u het volgende script uit met SQL CMD of SSMS op alle secundaire servers:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>De database spiegelende eindpunten voor alle replica's maken

Voer het volgende script uit op alle SQL-exemplaren met SQL CMD of SSMS:

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
GO
```

### <a name="create-the-availability-group"></a>De beschikbaarheidsgroep maken

Maak verbinding met de SQL Server-instantie die de primaire replica host met SQL CMD of SSMS. Voer de volgende opdracht uit om de beschikbaarheidsgroep te maken:

- Vervang `ag1` door de gewenste naam van de beschikbaarheidsgroep.
- Vervang `<VM1>`de `<VM2>`, `<VM3>` en waarden door de namen van de SQL Server-exemplaren die de replica's hosten.

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
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Een SQL Server-login voor pacemaker maken

Maak op alle SQL-servers een SQL-login voor Pacemaker. Met de volgende Transact-SQL wordt een login aanmelding.

- Vervang `<password>` door uw eigen complexe wachtwoord.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Sla op alle SQL-servers de referenties op die worden gebruikt voor de SQL Server-aanmelding. 

1. Maak het bestand:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Voeg de volgende 2 regels toe aan het bestand:

    ```bash
    pacemakerLogin
    <password>
    ```

    Als u de **vi-editor** wilt afsluiten, drukt `:wq` u eerst op de **Esc-toets** en voert u de opdracht in om het bestand te schrijven en af te sluiten.

1. Maak het bestand alleen leesbaar door root:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Secundaire replica's aansluiten bij de beschikbaarheidsgroep

1. Om de secundaire replica's aan de AG te kunnen verbinden, moet u poort 5022 op de firewall voor alle servers openen. Voer de volgende opdracht uit in uw SSH-sessie:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Voer op uw secundaire replica's de volgende opdrachten uit om ze aan de AG te koppelen:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Voer het volgende Transact-SQL-script uit op de primaire replica en elke secundaire replica:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Zodra de secundaire replica's zijn samengevoegd, u ze zien in SSMS Object Explorer door het knooppunt **Always On High Availability** uit te breiden:

    ![availability-group-joined.png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Een database toevoegen aan de beschikbaarheidsgroep

We volgen het artikel over de [beschikbaarheidsgroep configureren bij het toevoegen van een database.](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group)

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
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Controleren of de database is gemaakt op de secundaire servers

Voer op elke secundaire SQL Server-replica de volgende query uit om te zien of de db1-database is gemaakt en in een gesynchroniseerde status verkeert:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Als `synchronization_state_desc` de lijst `db1`gesynchroniseerd voor, betekent dit dat de replica's zijn gesynchroniseerd. De secondaries `db1` worden weergegeven in de primaire replica.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Bronnen voor beschikbaarheidsgroepen maken in het cluster Pacemaker

We volgen de handleiding voor [het maken van de bronnen van de beschikbaarheidsgroep in het cluster Pacemaker.](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)

### <a name="create-the-ag-cluster-resource"></a>De AG-clusterbron maken

1. Gebruik de volgende opdracht `ag_cluster` om de resource `ag1`in de beschikbaarheidsgroep te maken.

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Controleer uw bron en zorg ervoor dat deze online zijn voordat u verdergaat met de volgende opdracht:

    ```bash
    sudo pcs resource
    ```

    In dat geval moet de volgende uitvoer worden weergegeven:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```

### <a name="create-a-virtual-ip-resource"></a>Een virtuele IP-bron maken

1. Gebruik een beschikbaar statisch IP-adres uit uw netwerk om een virtuele IP-bron te maken. U er een `nmap`vinden met behulp van het opdrachtgereedschap.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. Stel de eigenschap **stonith** in op false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Maak de virtuele IP-bron met de volgende opdracht:

    - Vervang `<availableIP>` de onderstaande waarde door een ongebruikt IP-adres.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Beperkingen toevoegen

1. Om ervoor te zorgen dat het IP-adres en de AG-bron op hetzelfde knooppunt worden uitgevoerd, moet een colocatiebeperking worden geconfigureerd. Voer de volgende opdracht uit:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Maak een bestelbeperking om ervoor te zorgen dat de AG-bron actief is vóór het IP-adres. Hoewel de colocatiebeperking een bestelbeperking impliceert, dwingt dit deze af.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. Voer de volgende opdracht uit om de beperkingen te verifiëren:

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

We zijn klaar om te testen. Schakel stonith in het cluster opnieuw in door de volgende opdracht op Knooppunt 1 uit te voeren:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>De clusterstatus controleren

U de status van uw clusterbronnen controleren met de volgende opdracht:

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

Om ervoor te zorgen dat de configuratie tot nu toe is geslaagd, zullen we een failover testen. Zie [Always On Availability Group failover op Linux](/sql/linux/sql-server-linux-availability-group-failover-ha)voor meer informatie.

1. Voer de volgende opdracht uit om de `<VM2>`primaire replica handmatig te mislukken naar . Vervang `<VM2>` door de waarde van uw servernaam.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Als u uw beperkingen opnieuw controleert, ziet u dat er een andere beperking is toegevoegd vanwege de handmatige failover:

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

1. Verwijder de beperking `cli-prefer-ag_cluster-master` met ID met de volgende opdracht:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Controleer uw clusterbronnen `sudo pcs resource`met de opdracht en u moet `<VM2>`zien dat de primaire instantie nu .

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

## <a name="test-fencing"></a>Test hekwerk

U STONITH testen door de volgende opdracht uit te voeren. Probeer de onderstaande `<VM1>` `<VM3>`opdracht uit te voeren van voor .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Standaard, de omheining actie brengt het knooppunt uit en vervolgens op. Als u alleen het knooppunt offline wilt `--off` brengen, gebruikt u de optie in de opdracht.

U moet de volgende uitvoer krijgen:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Zie het volgende [Artikel red hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) voor meer informatie over het testen van een hekapparaat.

## <a name="next-steps"></a>Volgende stappen

Als u een Availability Group Listener voor uw SQL-servers wilt gebruiken, moet u een load balancer maken en configureren.

> [!div class="nextstepaction"]
> [Zelfstudie: Beschikbaarheidsgroeplistener configureren voor SQL Server op virtuele RHEL-machines in Azure](sql-server-linux-rhel-ha-listener-tutorial.md)
