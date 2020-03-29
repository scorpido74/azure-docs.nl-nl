---
title: Het Avere vFXT-cluster beheren - Azure
description: Avere-cluster beheren - knooppunten toevoegen of verwijderen, het vFXT-cluster opnieuw opstarten, stoppen of vernietigen
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 94db4a93025b6e3d633368d924e3e0c518d108ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153476"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Het Avere vFXT-cluster beheren

Op een bepaald punt in de levenscyclus van uw Avere vFXT voor Azure-cluster moet u mogelijk clusterknooppunten toevoegen of het cluster starten of opnieuw opstarten. Wanneer uw project is voltooid, moet u weten hoe u het cluster stoppen en het permanent verwijderen.

In dit artikel wordt uitgelegd hoe u clusterknooppunten en andere basisclusterbewerkingen toevoegen of verwijderen. Als u de clusterinstellingen wilt wijzigen of het werk ervan moet controleren, gebruikt u het [Configuratiescherm van Avere](avere-vfxt-cluster-gui.md).

Afhankelijk van de beheertaak moet u mogelijk een van de drie verschillende hulpprogramma's gebruiken: Het Configuratiescherm van Avere, het clusterbeheerscript van de vfxt.py opdrachtregel en de Azure-portal.

Deze tabel geeft een overzicht van welke gereedschappen voor elke taak kunnen worden gebruikt.

| Actie | Configuratiescherm van Avere | vfxt.py  | Azure Portal |
| --- | --- | --- | --- |
| Clusterknooppunten toevoegen | nee | ja | nee |
| Clusterknooppunten verwijderen | ja | nee | nee |
| Een clusterknooppunt stoppen | ja (kan ook services opnieuw opstarten of opnieuw opstarten) | nee | het uitschakelen van een node VM van de portal wordt geïnterpreteerd als een knooppunt fout |
| Een gestopt knooppunt starten | nee | nee | ja |
| Eén clusterknooppunt vernietigen | nee | nee | ja |
| Het cluster opnieuw opstarten |  |  |  |
| Het cluster afsluiten of veilig stoppen | ja | ja | nee |
| Het cluster vernietigen  | nee | ja | Ja, maar data-integriteit is niet gegarandeerd. |

Gedetailleerde instructies voor elk gereedschap zijn hieronder opgenomen.

## <a name="about-stopped-instances-in-azure"></a>Over gestopte exemplaren in Azure

Wanneer u een Azure VM afsluit of stopt, worden er geen rekenkosten meer in rekening gebracht, maar moet u nog steeds betalen voor de opslag ervan. Als u een vFXT-knooppunt of het gehele vFXT-cluster afsluit en u niet van plan bent het opnieuw op te starten, moet u de Azure-portal gebruiken om de gerelateerde VM's te verwijderen.

In de Azure-portal wordt in een *gestopt* knooppunt (dat opnieuw kan worden gestart) de status **weergegeven die is gestopt** in de Azure-portal. Een *verwijderd* knooppunt toont de **status gestopt (deallocated)** en het maakt niet langer reken- of opslagkosten.

Voordat u de vm verwijdert, moet u ervoor zorgen dat alle gewijzigde gegevens zijn geschreven vanuit de cache naar back-endopslag met behulp van het Configuratiescherm van Avere of vfxt.py opties om het cluster te stoppen of af te sluiten.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Het cluster beheren met het Configuratiescherm van Avere

Het Configuratiescherm van Avere kan voor deze taken worden gebruikt:

* Afzonderlijke knooppunten stoppen of opnieuw opstarten
* Een knooppunt uit het cluster verwijderen
* Het hele cluster stoppen of opnieuw opstarten

Avere Control Panel geeft prioriteit aan gegevensintegriteit, dus het probeert gewijzigde gegevens te schrijven naar back-endopslag vóór een mogelijk destructieve bewerking. Dit maakt het een veiligere optie dan de Azure-portal.

Toegang tot het Configuratiescherm van Avere vanuit een webbrowser. Volg de instructies in [Access the vFXT-cluster](avere-vfxt-cluster-gui.md) als u hulp nodig hebt.

### <a name="manage-nodes-with-avere-control-panel"></a>Knooppunten beheren met het Configuratiescherm van Avere

De instellingenpagina **van FXT-knooppunten** heeft besturingselementen voor het beheren van afzonderlijke knooppunten.

Als u een knooppunt wilt afsluiten, opnieuw opstarten of verwijderen, zoekt u het knooppunt in de lijst op de pagina **FXT-knooppunten** en klikt u op de juiste knop in de kolom **Handelingen.**

> [!NOTE]
> IP-adressen kunnen tussen clusterknooppunten worden verplaatst wanneer het aantal actieve knooppunten verandert.

Lees [cluster> FXT-knooppunten](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) in de handleiding voor avere-clusterinstellingen voor meer informatie.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Het cluster stoppen of opnieuw opstarten met het Configuratiescherm van Avere

De pagina Instellingen voor **systeemonderhoud** heeft opdrachten voor het opnieuw opstarten van clusterservices, het opnieuw opstarten van het cluster of het veilig van stroom voorzien van het cluster. Lees [Beheer > Systeemonderhoud](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (in de clusterinstellingengids van Avere) voor meer informatie.

Wanneer een cluster begint af te sluiten, worden statusberichten op het tabblad **Dashboard** geplaatst. Na een paar ogenblikken stoppen de berichten en uiteindelijk reageert de Sessie van het Configuratiescherm van Avere niet meer, wat betekent dat het cluster is afgesloten.

## <a name="manage-the-cluster-with-vfxtpy"></a>Het cluster beheren met vfxt.py

vfxt.py is een opdrachtregeltool voor het maken en beheren van clusteren.

vfxt.py is vooraf geïnstalleerd op de vm van de clustercontroller. Als u het op een ander systeem wilt <https://github.com/Azure/AvereSDK>installeren, raadpleegt u de documentatie op .

Het vfxt.py script kan worden gebruikt voor deze clusterbeheertaken:

* Nieuwe knooppunten toevoegen aan een cluster
* Een cluster stoppen of starten  
* Een cluster vernietigen

Net als het Configuratiescherm van Avere proberen vfxt.py bewerkingen ervoor te zorgen dat gewijzigde gegevens permanent worden opgeslagen in back-endopslag voordat u het cluster of knooppunt afsluit of vernietigt. Dit maakt het een veiligere optie dan de Azure-portal.

Een complete vfxt.py gebruiksgids is beschikbaar op GitHub: [Cloudclusterbeheer met vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Clusterknooppunten toevoegen met vfxt.py

Op de clustercontroller is een voorbeeldopdrachtscript voor het toevoegen van clusterknooppunten opgenomen. Zoek ``./add-nodes`` op de controller en open deze in een editor om deze aan te passen met uw clustergegevens.

Het cluster moet worden uitgevoerd om deze opdracht te kunnen gebruiken.

De volgende waarden opgeven:

* Naam van de brongroep voor het cluster en ook voor netwerk- en opslagbronnen als deze zich niet in dezelfde resourcegroep bevinden als het cluster
* Clusterlocatie
* Clusternetwerk en subnet
* Toegangsrol voor clusterknooppunt (gebruik de ingebouwde rol [Avere Operator)](../role-based-access-control/built-in-roles.md#avere-operator)
* IP-adres en beheerderswachtwoord voor clusterbeheer
* Aantal knooppunten dat moet worden toegevoegd (1, 2 of 3)
* Knooppuntinstantietype en cachegroottewaarden

Als u het prototype niet gebruikt, moet u een opdracht als de volgende maken, inclusief alle hierboven beschreven informatie.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Lees [Knooppunten toevoegen aan een cluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) in de vfxt.py gebruikshandleiding voor meer informatie.

### <a name="stop-a-cluster-with-vfxtpy"></a>Een cluster met vfxt.py stoppen

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Een gestopt cluster starten met vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Omdat het cluster is gestopt, moet u instantie-id's doorgeven om de clusterknooppunten op te geven. Lees [Opgeven welk cluster u wilt wijzigen](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) in de vfxt.py gebruikshandleiding voor meer informatie.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Een cluster vernietigen met vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

De ``--quick-destroy`` optie kan worden gebruikt als u geen gewijzigde gegevens uit de clustercache wilt opslaan.

Lees de [vfxt.py gebruiksgids](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) voor meer informatie.

## <a name="manage-cluster-vms-from-the-azure-portal"></a>ClusterVM's beheren vanuit de Azure-portal

De Azure-portal kan worden gebruikt om cluster-VM's afzonderlijk te vernietigen, maar gegevensintegriteit is niet gegarandeerd als het cluster niet eerst netjes wordt afgesloten.

De Azure-portal kan worden gebruikt voor deze clusterbeheertaken:

* Een gestopt vFXT-knooppunt starten
* Een individueel vFXT-knooppunt stoppen (het cluster interpreteert dit als een knooppuntfout)
* Een vFXT-cluster vernietigen *als* u er niet voor hoeft te zorgen dat gewijzigde gegevens in de clustercache naar de kernfiler worden geschreven
* Verwijder vFXT-knooppunten en andere clusterbronnen permanent nadat ze veilig zijn uitgeschakeld

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>VFXT-exemplaren opnieuw starten vanaf de Azure-portal

Als u een gestopt knooppunt opnieuw wilt starten, moet u de Azure-portal gebruiken. Selecteer **Virtuele machines** in het linkermenu en klik op de VM-naam in de lijst om de overzichtspagina te openen.

Klik op de knop **Start** boven aan de overzichtspagina om de VM opnieuw te activeren.

![Azure-portalscherm met de optie om een gestopte vm te starten](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Clusterknooppunten verwijderen

Als u één knooppunt uit het vFXT-cluster wilt verwijderen, maar de rest van het cluster wilt behouden, moet u eerst [het knooppunt uit het cluster verwijderen](#manage-nodes-with-avere-control-panel) met het Configuratiescherm van Avere.

> [!CAUTION]
> Als u een knooppunt verwijdert zonder het eerst uit het vFXT-cluster te verwijderen, kunnen gegevens verloren gaan.

Als u een of meer exemplaren die als vFXT-knooppunt worden gebruikt, permanent wilt vernietigen, gebruikt u de Azure-portal.
Selecteer **Virtuele machines** in het linkermenu en klik op de VM-naam in de lijst om de overzichtspagina te openen.

Klik op de knop **Verwijderen** boven aan de overzichtspagina om de VM permanent te vernietigen.

U deze methode gebruiken om clusterknooppunten permanent te verwijderen nadat ze veilig zijn uitgeschakeld.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Het cluster vernietigen vanuit de Azure-portal

> [!NOTE]
> Als u wilt dat de resterende clientwijzigingen in de cache worden `--destroy` geschreven naar back-endopslag, gebruikt u de optie vfxt.py of gebruikt u het Configuratiescherm van Avere om het cluster netjes af te sluiten voordat de knooppuntinstanties in de Azure-portal worden verwijderd.

U knooppuntinstanties permanent vernietigen door ze te verwijderen in de Azure-portal. U ze één voor één verwijderen, zoals hierboven beschreven, of u de pagina **Virtuele machines** gebruiken om alle cluster-VM's te vinden, ze met de selectievakjes in te sluiten en op de knop **Verwijderen** klikken om ze allemaal in één actie te verwijderen.

![Lijst van VM's in de portal, gefilterd op de term "cluster", met drie van de vier gecontroleerd en gemarkeerd](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Extra clusterbronnen verwijderen uit de Azure-portal

Als u extra bronnen hebt gemaakt speciaal voor het vFXT-cluster, u deze verwijderen als onderdeel van het afbreken van het cluster. Vernietig geen elementen die gegevens bevatten die u nodig hebt, of items die worden gedeeld met andere projecten.

Naast het verwijderen van de clusterknooppunten, u overwegen deze onderdelen te verwijderen:

* De clustercontroller-VM
* Gegevensschijven die zijn gekoppeld aan clusterknooppunten
* Netwerkinterfaces en openbare IP's die zijn gekoppeld aan clustercomponenten
* Virtuele netwerken
* Opslagcontainers en opslagaccounts **(alleen** als ze geen belangrijke gegevens bevatten)
* Beschikbaarheidsset

![Lijst met Azure-portal 'alle bronnen' met resources die zijn gemaakt voor een testcluster](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>De brongroep van een cluster verwijderen uit de Azure-portal

Als u een resourcegroep hebt gemaakt die specifiek is gemaakt om het cluster te huisvesten, u alle gerelateerde bronnen voor het cluster vernietigen door de brongroep te vernietigen.

> [!Caution]
> Vernietig de resourcegroep alleen als u er zeker van bent dat er niets van waarde in de groep zit. Zorg er bijvoorbeeld voor dat u de benodigde gegevens uit opslagcontainers binnen de resourcegroep hebt verplaatst.  

Als u een brongroep wilt verwijderen, klikt u op **Resourcegroepen** in het linkermenu van de portal en filtert u de lijst met brongroepen om de groep te zoeken die u hebt gemaakt voor het vFXT-cluster. Selecteer de brongroep en klik op de drie puntjes rechts van het deelvenster. Kies **Resourcegroep verwijderen**. Het portaal zal u vragen om de verwijdering te bevestigen, die onomkeerbaar is.

![Resourcegroep met de actie Resourcegroep verwijderen](media/avere-vfxt-delete-resource-group.png)
