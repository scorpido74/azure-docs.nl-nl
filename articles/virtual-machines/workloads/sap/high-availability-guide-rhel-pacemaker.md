---
title: Pacemaker instellen voor Red Hat Enterprise Linux in azure | Microsoft Docs
description: Pacemaker instellen voor Red Hat Enterprise Linux in azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: 4e12ad64ef277396a101aab6d1bb8f3cc6079cf9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099597"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Pacemaker instellen voor Red Hat Enterprise Linux in azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Lees eerst de volgende SAP-opmerkingen en-documenten:

* SAP-opmerking [1928533], die:
  * De lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software.
  * Belang rijke informatie over de capaciteit van Azure VM-grootten.
  * De ondersteunde SAP-software en besturings systemen (OS) en database combinaties.
  * De vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure.
* SAP-opmerking [2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2002167] heeft aanbevolen instellingen voor het besturings systeem voor Red Hat Enterprise Linux
* SAP Note [2009879] heeft SAP Hana richt lijnen voor Red Hat Enterprise Linux
* SAP Note [2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SAP HANA systeem replicatie in pacemaker-cluster](https://access.redhat.com/articles/3004101)
* Algemene documentatie voor RHEL
  * [Overzicht van Maxi maal beschik bare invoeg toepassingen](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Beheer van Maxi maal beschik bare invoeg toepassingen](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Naslag informatie voor de invoeg toepassing met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentatie voor Azure-specifieke RHEL:
  * [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden](https://access.redhat.com/articles/3131341)
  * [Installeren en configureren van een cluster met hoge Beschik baarheid van Red Hat Enterprise Linux 7,4 (en hoger) op Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [SAP S/4HANA ASCS/ERS met zelfstandige server 2 (ENSA2) configureren in pacemaker op RHEL 7,6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Clusterinstallatie van

![Overzicht van pacemaker op RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

De volgende items worden voorafgegaan door een **[A]** : van toepassing op alle knooppunten **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

1. **[A]** registreren

   Registreer uw virtuele machines en koppel deze aan een groep die opslag plaatsen voor RHEL 7 bevat.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Als u een pool koppelt aan een Azure Marketplace PAYG RHEL-installatie kopie, wordt u in feite gefactureerd voor uw RHEL-gebruik: eenmaal voor de PAYG-installatie kopie en eenmaal voor het RHEL recht van de groep die u koppelt. Azure biedt nu BYOS RHEL-installatie kopieën om dit te verhelpen. Meer informatie is [hier](https://aka.ms/rhel-byos)beschikbaar.

1. **[A]** RHEL inschakelen voor SAP opslag plaatsen

   Schakel de volgende opslag plaatsen in om de vereiste pakketten te installeren.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** INVOEG toepassing RHEL ha installeren

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > We raden u aan de volgende versies van de Azure Fence-agent (of hoger) voor klanten te laten profiteren van een snellere failover, als het stoppen van een resource mislukt of als de cluster knooppunten niet meer kunnen communiceren.  
   > RHEL 7,6: Fence-agents-4.2.1 -11. EL7 _ 6,8  
   > RHEL 7,5: Fence-agents-4.0.11 -86. EL7 _ 5,8  
   > RHEL 7,4: Fence-agents-4.0.11 -66. EL7 _ 4.12  
   > Zie voor meer informatie [Azure VM die wordt uitgevoerd als een RHEL-lid van een cluster met hoge Beschik baarheid neemt erg veel tijd in beslag om te worden geomheiningd of mislukt het uitzetten van een storing of een time-out voordat de virtuele machine wordt afgesloten](https://access.redhat.com/solutions/3408711).

   Controleer de versie van de Azure Fence-agent. Indien nodig moet u deze bijwerken naar een versie die gelijk is aan of hoger dan de hierboven vermelde.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Als u de Azure Fence-agent wilt bijwerken, en als u aangepaste rol gebruikt, moet u ervoor zorgen dat u de aangepaste rol bijwerkt om actie **uitgeschakeld**op te nemen. Zie [een aangepaste rol maken voor de Fence-agent](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent)voor meer informatie.  

1. **[A]**  Omzetten van de hostnaam instellen

   U kunt een DNS-server gebruiken of aanpassen van de/etc/hosts op alle knooppunten. In dit voorbeeld laat zien hoe u het bestand/etc/hosts gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten. Het voordeel van het gebruik van/etc/hosts is dat het cluster wordt onafhankelijk van DNS, wat erop kan een single point of fouten te.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels/etc/hosts. De IP-adres en hostnaam zodat deze overeenkomen met uw omgeving wijzigen

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]**  Hacluster wachtwoord wijzigen naar hetzelfde wachtwoord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** firewall regels voor pacemaker toevoegen

   Voeg de volgende firewall regels toe aan alle cluster communicatie tussen de cluster knooppunten.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** basis Cluster Services inschakelen

   Voer de volgende opdrachten uit om de pacemaker-service in te scha kelen en te starten.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** pacemaker-cluster maken

   Voer de volgende opdrachten uit om de knoop punten te verifiëren en het cluster te maken. Stel het token in op 30000 om onderhoud van het geheugen mogelijk te maken. Zie [dit artikel voor Linux][virtual-machines-linux-maintenance]voor meer informatie.

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** verwachte stemmen instellen

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>STONITH-apparaat maken

Het stonith instellen-apparaat maakt gebruik van een Service-Principal te autoriseren op basis van Microsoft Azure. Volg deze stappen voor het maken van een Service-Principal.

1. Ga naar <https://portal.azure.com>
1. Open de Azure Active Directory-blade  
   Ga naar eigenschappen en noteer de map-ID. Dit is de **tenant-ID**.
1. Klik op App-registraties
1. Klik op nieuwe registratie
1. Voer een naam in, selecteer alleen accounts in deze organisatie Directory 
2. Selecteer het toepassings type ' Web ', voer een aanmeldings-URL in (bijvoorbeeld http:\//localhost) en klik op toevoegen.  
   De aanmeldings-URL wordt niet gebruikt en kan geldige URL zijn
1. Selecteer certificaten en geheimen en klik vervolgens op nieuw client geheim
1. Voer een beschrijving in voor een nieuwe sleutel, selecteer nooit verloopt en klik op toevoegen
1. Noteer de waarde in. Deze wordt gebruikt als de **wachtwoord** voor de Service-Principal
1. Selecteer overzicht. Noteer de toepassings-ID. Deze wordt gebruikt als de gebruikersnaam (**aanmeldings-ID** in de onderstaande stappen) van de Service-Principal

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Een aangepaste rol maken voor de agent omheining

De Service-Principal heeft geen machtigingen voor toegang tot uw Azure-resources standaard. U moet de Service-Principal machtigingen geven om alle virtuele machines van het cluster te starten en te stoppen. Als u de aangepaste rol die niet al hebt gemaakt, kunt u maken met behulp van [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) of [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Gebruik de volgende inhoud voor het invoerbestand. U moet de inhoud voor uw abonnementen die is aangepast, c276fc76-9cd4-44c9-99a7-4fd71546436e en e91d47c4-76f3-4271-a796-21b4ecfe3624 vervangen door de id's van uw abonnement. Als u slechts één abonnement hebt, verwijdert u de tweede vermelding in AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** de aangepaste rol toewijzen aan de Service-Principal

De aangepaste rol 'Linux omheining Agent rol' die is gemaakt in het vorige hoofdstuk aan de Service-Principal toewijzen. De rol van eigenaar niet meer gebruiken.

1. Ga naar https://portal.azure.com
1. Open de blade alle resources
1. Selecteer de virtuele machine van het eerste clusterknooppunt
1. Klik op de Access control (IAM)
1. Klik op de roltoewijzing toevoegen
1. Selecteer de rol 'Linux omheining Agent rol'
1. Voer de naam van de toepassing die u hierboven hebt gemaakt
1. Op Opslaan klikken

Herhaal de bovenstaande stappen voor het tweede clusterknooppunt.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Maken de apparaten stonith instellen

Nadat u de machtigingen voor de virtuele machines hebt bewerkt, kunt u de apparaten stonith instellen in het cluster configureren.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Gebruik de volgende opdracht om het Fence-apparaat te configureren.

> [!NOTE]
> De optie pcmk_host_map is alleen vereist in de opdracht als de RHEL-hostnamen en de namen van de Azure-knoop punten niet identiek zijn. Raadpleeg de sectie vet in de opdracht.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** het gebruik van een STONITH-apparaat inschakelen

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
