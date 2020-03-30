---
title: Pacemaker instellen op RHEL in Azure | Microsoft Documenten
description: Pacemaker instellen op Red Hat Enterprise Linux in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: radeltch
ms.openlocfilehash: 21c551721815847eea4cb1435298ea6f7bf37966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264477"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Pacemaker instellen op Red Hat Enterprise Linux in Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Lees eerst de volgende SAP Notes en papers:

* SAP Note [1928533], die heeft:
  * De lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software.
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten.
  * De ondersteunde SAP-software en besturingssysteem (OS) en databasecombinaties.
  * De vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure.
* SAP Note [2015553] bevat vereisten voor SAP-ondersteunde SAP-softwareimplementaties in Azure.
* SAP Note [2002167] heeft aanbevolen OS-instellingen voor Red Hat Enterprise Linux
* SAP Note [2009879] heeft SAP HANA Richtlijnen voor Red Hat Enterprise Linux
* SAP Note [2178632] heeft gedetailleerde informatie over alle monitoringstatistieken die voor SAP in Azure worden gerapporteerd.
* SAP Note [2191498] heeft de vereiste SAP Host Agent-versie voor Linux in Azure.
* SAP Note [2243692] heeft informatie over SAP-licenties op Linux in Azure.
* SAP Note [1999351] heeft aanvullende informatie over probleemoplossing voor de Azure Enhanced Monitoring Extension voor SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) heeft alle vereiste SAP Notes voor Linux.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines deployment voor SAP op Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SAP HANA-systeemreplicatie in pacemakercluster](https://access.redhat.com/articles/3004101)
* Algemene RHEL-documentatie
  * [Overzicht van invoegtoepassing met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On-beheer met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Invoegtoepassing met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid - sbd en fence_sbd](https://access.redhat.com/articles/2800691)
* Azure-specifieke RHEL-documentatie:
  * [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid - Microsoft Azure Virtual Machines als clusterleden](https://access.redhat.com/articles/3131341)
  * [Een Red Hat Enterprise Linux 7.4 (en hoger) cluster met hoge beschikbaarheid installeren en configureren op Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [SAP S/4HANA ASCS/ERS configureren met standalone enqueueserver 2 (ENSA2) in Pacemaker op RHEL 7.6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Clusterinstallatie

![Pacemaker op RHEL overzicht](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat ondersteunt geen software-geëmuleerde waakhond. Red Hat ondersteunt geen SBD op cloudplatforms. Zie voor meer informatie [ondersteuningsbeleid voor RHEL-clusters voor hoge beschikbaarheid - sbd en fence_sbd](https://access.redhat.com/articles/2800691).
> Het enige ondersteunde schermmechanisme voor Pacemaker Red Hat Enterprise Linux-clusters op Azure, is Azure-afrasteringsagent.  

De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

1. **[A]** Registreren

   Registreer uw virtuele machines en bevestig deze aan een pool die repositories bevat voor RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Houd er rekening mee dat door een pool aan een Azure Marketplace PAYG RHEL-afbeelding te koppelen, u effectief dubbel wordt gefactureerd voor uw RHEL-gebruik: één keer voor de PAYG-afbeelding en één keer voor het RHEL-recht in de groep die u koppelt. Om dit te beperken biedt Azure nu BYOS RHEL-afbeeldingen. Meer informatie is [hier](../redhat/byos.md)beschikbaar.

1. **[A]** RHEL inschakelen voor SAP-repo's

   Om de vereiste pakketten te installeren, schakelt u de volgende opslagplaatsen in.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** RHEL HA-add-on installeren

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > We raden de volgende versies van azure fence-agent (of hoger) aan voor klanten om te profiteren van een snellere failovertijd, als een resourcestop mislukt of de clusterknooppunten niet meer kunnen communiceren welke elkaar:  
   > RHEL 7.6: hek-agenten-4.2.1-11.el7_6.8  
   > RHEL 7.5: hek-agenten-4.0.11-86.el7_5.8  
   > RHEL 7.4: hek-agenten-4.0.11-66.el7_4.12  
   > Zie [Azure VM die wordt uitgevoerd als een RHEL-clusterlid](https://access.redhat.com/solutions/3408711)met hoge beschikbaarheid zeer lang om te worden omheind of het schermen mislukt / uitvalt voordat de VM wordt afgesloten.

   Controleer de versie van de Azure-afrasteringsagent. Werk deze indien nodig bij naar een versie die gelijk is aan of later dan het bovenstaande.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Als u de Azure Fence-agent moet bijwerken en als u de aangepaste rol gebruikt, moet u de aangepaste rol bijwerken om **actie-powerOff**op te nemen. Zie Voor meer [informatie een aangepaste rol maken voor de hekagent](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten. Het voordeel van het gebruik van / etc / hosts is dat uw cluster onafhankelijk wordt van DNS, wat ook een enkel punt van fouten kan zijn.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Hacluster-wachtwoord wijzigen in hetzelfde wachtwoord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Firewallregels toevoegen voor pacemaker

   Voeg de volgende firewallregels toe aan alle clustercommunicatie tussen de clusterknooppunten.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Basisclusterservices inschakelen

   Voer de volgende opdrachten uit om de Pacemaker-service in te schakelen en deze te starten.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Pacemakercluster maken

   Voer de volgende opdrachten uit om de knooppunten te verifiëren en het cluster te maken. Stel het token in op 30000 zodat het behoud van geheugen behouden blijft. Zie voor meer informatie [dit artikel voor Linux][virtual-machines-linux-maintenance].

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

1. **[A]** Verwachte stemmen instellen

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>STONITH-apparaat maken

Het STONITH-apparaat maakt gebruik van een Service Principal om te autoriseren tegen Microsoft Azure. Volg deze stappen om een serviceprincipal te maken.

1. Ga naar <https://portal.azure.com>
1. Het Azure Active Directory-blad openen  
   Ga naar Eigenschappen en noteer de Directory-id. Dit is de **tenant-id.**
1. Klik op App-registraties
1. Klik op Nieuwe registratie
1. Voer een naam in, selecteer 'Alleen accounts in deze organisatiemap' 
2. Selecteer Toepassingstype 'Web', voer een aanmeldings-URL in (bijvoorbeeld http:\//localhost) en klik op Toevoegen  
   De aanmeldings-URL wordt niet gebruikt en kan een geldige URL zijn
1. Selecteer Certificaten en geheimen en klik op Nieuw clientgeheim
1. Voer een beschrijving in voor een nieuwe sleutel, selecteer 'Nooit verloopt' en klik op Toevoegen
1. Noteer de waarde. Het wordt gebruikt als **wachtwoord** voor de Service Principal
1. Selecteer Overzicht. Noteer de toepassings-id. Het wordt gebruikt als gebruikersnaam **(login-id** in de onderstaande stappen) van de Service Principal

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Maak een aangepaste rol voor de hekagent

De Serviceprincipal heeft standaard geen machtigingen om toegang te krijgen tot uw Azure-bronnen. U moet de serviceprincipal-machtigingen geven om alle virtuele machines van het cluster te starten en te stoppen (uitschakelen). Als u de aangepaste rol nog niet hebt gemaakt, u deze maken met [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) of [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Gebruik de volgende inhoud voor het invoerbestand. U moet de inhoud aanpassen aan uw abonnementen, vervangen c276fc76-9cd4-44c9-99a7-4fd71546436e en e91d47c4-76f3-4271-a796-21b4ecfe3624 met de Ids van uw abonnement. Als u slechts één abonnement hebt, verwijdert u de tweede vermelding in AssignableScopes.

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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** De aangepaste rol toewijzen aan de serviceprincipal

Wijs de aangepaste rol "Linux Fence Agent Role" die in het laatste hoofdstuk is gemaakt, toe aan de Service Principal. Gebruik de rol Eigenaar niet meer!

1. Ga naar https://portal.azure.com
1. Het blad Alle resources openen
1. De virtuele machine van het eerste clusterknooppunt selecteren
1. Klik op Toegangsbesturingselement (IAM)
1. Klik op Roltoewijzing toevoegen
1. Selecteer de rol "Linux Fence Agent Role"
1. Voer de naam in van de toepassing die u hierboven hebt gemaakt
1. Op Opslaan klikken

Herhaal de bovenstaande stappen voor het tweede clusterknooppunt.

### <a name="1-create-the-stonith-devices"></a>**[1]** Maak de STONITH-apparaten

Nadat u de machtigingen voor de virtuele machines hebt bewerkt, u de STONITH-apparaten in het cluster configureren.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Gebruik de volgende opdracht om het hekapparaat te configureren.

> [!NOTE]
> Optie 'pcmk_host_map' is ALLEEN vereist in de opdracht, als de RHEL-hostnamen en de Azure-knooppuntnamen NIET identiek zijn. Raadpleeg de vetgedrukte sectie in de opdracht.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Het gebruik van een STONITH-apparaat inschakelen

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
