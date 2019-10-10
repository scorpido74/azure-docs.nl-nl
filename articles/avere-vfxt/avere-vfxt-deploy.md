---
title: AVERE vFXT voor Azure implementeren
description: Stappen voor het implementeren van het avere vFXT-cluster in azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: rohogue
ms.openlocfilehash: 6ddf950bf2d138a94675ee394109a0d227ea206b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255454"
---
# <a name="deploy-the-vfxt-cluster"></a>Het vFXT-cluster implementeren

Deze procedure begeleidt u bij het gebruik van de implementatie wizard die beschikbaar is via Azure Marketplace. De wizard implementeert automatisch het cluster met behulp van een Azure Resource Manager sjabloon. Nadat u de para meters in het formulier hebt ingevoerd en op **maken**hebt geklikt, worden deze stappen automatisch door Azure voltooid:

* Maakt de cluster controller, een basis-VM die de software bevat die nodig is voor het implementeren en beheren van het cluster.
* Hiermee stelt u de resource groep en de infra structuur van het virtuele netwerk in, inclusief het maken van nieuwe elementen.
* Maakt de cluster knooppunt-Vm's en configureert deze als het avere-cluster.
* Hiermee wordt indien aangevraagd een nieuwe Azure Blob-container gemaakt en geconfigureerd als een kern Bestands server van het cluster.

Nadat u de instructies in dit document hebt gevolgd, hebt u een virtueel netwerk, een subnet, een controller en een vFXT-cluster zoals in het volgende diagram wordt weer gegeven. Dit diagram toont de optionele Azure Blob core-bestands extensie, die een nieuwe Blob Storage-container (in een nieuw opslag account, niet weer gegeven) en een service-eind punt voor micro soft Storage in het subnet bevat. 

![diagram met drie concentrische rechthoeken met avere-cluster onderdelen. De buitenste rechthoek heet ' resource groep ' en bevat een zeshoek met de naam ' Blob Storage (optional) '. De volgende rechthoek in heeft het label virtueel netwerk: 10.0.0.0/16 en bevat geen unieke onderdelen. De binnenste rechthoek heeft het label subnet: 10.0.0.0/24 en bevat een virtuele machine met de naam ' cluster controller ', een stapel van drie virtuele machines met het label ' vFXT nodes (vFXT cluster) ' en een zeshoek met het label ' service-eind punt '. Er is een pijl verbonden met het service-eind punt (dat zich binnen het subnet bevindt) en de Blob-opslag (die zich buiten het subnet en vnet bevindt in de resource groep). De pijl geeft de grenzen van het subnet en het virtuele netwerk door.](media/avere-vfxt-deployment.png)  

Voordat u de sjabloon voor maken gebruikt, moet u ervoor zorgen dat u deze vereisten hebt aangepakt:  

1. [Nieuw abonnement](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Machtigingen voor abonnements eigenaar](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Quota voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Opslag service-eind punt (indien nodig)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) : vereist voor implementaties met behulp van een bestaand virtueel netwerk en Blob-opslag maken

Lees voor meer informatie over de stappen voor het implementeren van clusters en het plannen van het [overzicht](avere-vfxt-deploy-overview.md)van [uw avere vFXT-systeem](avere-vfxt-deploy-plan.md) en-implementatie.

## <a name="create-the-avere-vfxt-for-azure"></a>De avere-vFXT voor Azure maken

Open de sjabloon maken in de Azure Portal door te zoeken naar avere en de optie ' avere vFXT for Azure ARM Temp late ' te selecteren. 

![Browser venster met de Azure Portal met brood crumbs ' New > Marketplace > alles '. Op de pagina alles heeft het zoek veld de term ' avere ' en het tweede resultaat ' avere vFXT for Azure ARM Temp late ' wordt in rood beschreven om deze te markeren.](media/avere-vfxt-template-choose.png)

Nadat u de details op de pagina avere vFXT voor Azure ARM-sjabloon hebt gelezen, klikt u op **maken** om te beginnen. 

![Azure Marketplace met de eerste pagina van de implementatie sjabloon met](media/avere-vfxt-deploy-first.png)

De sjabloon is onderverdeeld in vier stappen: twee pagina's voor het verzamelen van gegevens, plus validatie-en bevestigings stappen. 

* Pagina één focus op instellingen voor de virtuele machine van de cluster controller. 
* Op de pagina twee worden para meters verzameld voor het maken van het cluster en de bijbehorende resources, zoals subnetten en opslag. 
* Pagina drie bevat een overzicht van de instellingen en valideert de configuratie. 
* In pagina vier worden de software voorwaarden en-voor waarden uitgelegd en kunt u het proces voor het maken van het cluster starten. 

## <a name="page-one-parameters---cluster-controller-information"></a>Pagina één para meters-cluster controller gegevens

Op de eerste pagina van de implementatie sjabloon wordt informatie over de cluster controller verzameld. 

![Eerste pagina van de implementatie sjabloon](media/avere-vfxt-deploy-1.png)

Vul de volgende gegevens in:

* **Naam van de cluster controller** : Stel de naam in voor de virtuele machine van de cluster controller.

* **Controller gebruikers naam** : Vul de hoofdmap in voor de virtuele machine van de cluster controller. 

* **Verificatie type** : Kies voor het maken van verbinding met de controller een wacht woord of een open bare SSH-sleutel verificatie. De open bare SSH-sleutel methode wordt aanbevolen; meer informatie [over het maken en gebruiken van SSH-sleutels](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) als u hulp nodig hebt.

* **Wacht woord** of **open bare SSH-sleutel** : afhankelijk van het verificatie type dat u hebt geselecteerd, moet u een open bare RSA-sleutel of een wacht woord in de volgende velden opgeven. Deze referentie wordt gebruikt met de gebruikers naam die u eerder hebt ingesteld.

* **Abonnement** : Selecteer het abonnement voor de avere vFXT. 

* **Resource groep** : Selecteer een bestaande lege resource groep voor het avere vFXT-cluster of klik op nieuwe maken en voer een nieuwe naam voor de resource groep in. 

* **Locatie** : Selecteer de Azure-locatie voor uw cluster en bronnen.

Klik op **OK** wanneer u klaar bent. 

> [!NOTE]
> Als u wilt dat de cluster controller een openbaar IP-adres heeft, maakt u een nieuw virtueel netwerk voor het cluster in plaats van een bestaand netwerk te selecteren. Deze instelling is op pagina twee.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Pagina twee para meters-vFXT-cluster informatie

Op de tweede pagina van de implementatie sjabloon kunt u de cluster grootte, het type knoop punt, de cache grootte en de opslag parameters instellen, onder andere instellingen. 

![Tweede pagina van de implementatie sjabloon](media/avere-vfxt-deploy-2.png)

* **Avere vFXT aantal cluster knooppunten** : Kies het aantal knoop punten dat u in het cluster wilt gebruiken. Het minimum aantal is drie knoop punten en het maximum is twaalf. 

* **Wacht woord voor cluster beheer** : het wacht woord voor cluster beheer maken. Dit wacht woord wordt gebruikt met de gebruikers naam ```admin``` om u aan te melden bij het configuratie scherm van het cluster om het cluster te controleren en instellingen te configureren.

* **Avere vFXT-cluster naam** : Geef het cluster een unieke naam. 

* **Grootte** : in deze sectie wordt het VM-type weer gegeven dat wordt gebruikt voor de cluster knooppunten. Hoewel er slechts één aanbevolen optie is, opent de koppeling **wijzigings grootte** een tabel met details over dit type exemplaar en een koppeling naar een prijs calculator.  

* **Cache grootte per knoop punt** -de cluster cache wordt verspreid over de cluster knooppunten, zodat de totale cache grootte van uw avere vFXT-cluster de cache grootte per knoop punt is vermenigvuldigd met het aantal knoop punten. 

  De aanbevolen configuratie is het gebruik van 4 TB per knoop punt voor Standard_E32s_v3-knoop punten.

* **Virtueel netwerk** : Definieer een nieuw vnet voor het gebruik van het cluster of selecteer een bestaand vnet dat voldoet aan de vereisten die worden beschreven in [uw avere VFXT-systeem plannen](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Als u een nieuw vnet maakt, heeft de cluster controller een openbaar IP-adres, zodat u toegang hebt tot het nieuwe particuliere netwerk. Als u een bestaand vnet kiest, wordt de cluster controller geconfigureerd zonder een openbaar IP-adres. 
  > 
  > Een openbaar zichtbaar IP-adres op de cluster controller biedt eenvoudiger toegang tot het vFXT-cluster, maar maakt een klein beveiligings risico. 
  >  * Met een openbaar IP-adres op de cluster controller kunt u deze gebruiken als een Jump-host om verbinding te maken met het avere vFXT-cluster van buiten het particuliere subnet.
  >  * Als u geen openbaar IP-adres op de controller hebt ingesteld, moet u een andere Jump host, een VPN-verbinding of een ExpressRoute gebruiken om toegang te krijgen tot het cluster. Maak bijvoorbeeld de controller in een virtueel netwerk waarop al een VPN-verbinding is geconfigureerd.
  >  * Als u een controller met een openbaar IP-adres maakt, moet u de controller-VM beveiligen met een netwerk beveiligings groep. De avere vFXT voor Azure-implementatie maakt standaard een netwerk beveiligings groep en beperkt de inkomende toegang tot alleen poort 22 voor controllers met open bare IP-adressen. U kunt het systeem verder beveiligen door de toegang tot uw bereik van IP-bron adressen te vergren delen, dat wil zeggen, alleen verbindingen toestaan van computers die u wilt gebruiken voor toegang tot het cluster.

  Met de sjabloon implementeren configureert u ook de nieuwe vnet met een opslag service-eind punt voor Azure Blob Storage en met netwerk toegangs beheer dat is vergrendeld tot alleen IP-adressen van het subnet van het cluster. 

* **Subnet** : Kies een subnet in uw bestaande virtuele netwerk, of maak een nieuwe. 

* **Blob Storage maken en gebruiken** : Kies **waar** om een nieuwe Azure Blob-container te maken en configureer deze als back-end-opslag voor het nieuwe avere vFXT-cluster. Met deze optie wordt ook een nieuw opslag account gemaakt in dezelfde resource groep als het cluster en een micro soft Storage service-eind punt in het subnet van het cluster. 
  
  Als u een bestaand virtueel netwerk opgeeft, moet het een opslag service-eind punt hebben voordat u het cluster maakt. (Zie [uw avere vFXT-systeem plannen](avere-vfxt-deploy-plan.md)voor meer informatie.)

  Stel dit veld in op **Onwaar** als u geen nieuwe container wilt maken. In dit geval moet u de opslag koppelen en configureren nadat u het cluster hebt gemaakt. Lees [opslag configureren](avere-vfxt-add-storage.md) voor instructies. 

* **(Nieuw) opslag account** : als u een nieuwe Azure Blob-container maakt, voert u een naam in voor het nieuwe opslag account. 

## <a name="validation-and-purchase"></a>Validatie en aankoop

Pagina drie geeft een overzicht van de configuratie en valideert de para meters. Nadat de validatie is voltooid, klikt u op de knop **OK** om door te gaan. 

![Derde pagina van de implementatie sjabloon-validatie](media/avere-vfxt-deploy-3.png)

Voer op pagina vier de vereiste contact gegevens in en klik op de knop **maken** om de voor waarden te accepteren en de avere VFXT voor Azure-cluster te maken. 

![Vierde pagina van de implementatie sjabloon-voor waarden, knop maken](media/avere-vfxt-deploy-4.png)

De cluster implementatie neemt 15-20 minuten in beslag.

## <a name="gather-template-output"></a>Sjabloon uitvoer verzamelen

Wanneer de avere vFXT-sjabloon het maken van het cluster voltooit, wordt er een deel van belang rijke informatie over het nieuwe cluster uitgevoerd. 

> [!TIP]
> Zorg ervoor dat u het IP-adres van het beheer kopieert vanuit de sjabloon uitvoer. U hebt dit adres nodig voor het beheren van het cluster.

Voer de volgende procedure uit om deze informatie te vinden:

1. Ga naar de resource groep voor de cluster controller.

1. Klik aan de linkerkant op **implementaties**en vervolgens op **micro soft-avere. vfxt-Temp late**.

   ![De portal pagina van de resource groep met implementaties geselecteerd aan de linkerkant en micro soft-avere. vfxt: de sjabloon die wordt weer gegeven in een tabel onder implementatie naam](media/avere-vfxt-outputs-deployments.png)

1. Klik aan de linkerkant op **uitvoer**. Kopieer de waarden in elk van de velden. 

   ![uitvoer pagina met de waarden SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs en MGMT_IP in velden rechts van de labels](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Volgende stap

Nu het cluster wordt uitgevoerd en u weet wat het beheer-IP-adres is, kunt u [verbinding maken met het hulp programma voor cluster configuratie](avere-vfxt-cluster-gui.md) om ondersteuning in te scha kelen, opslag ruimte toe te voegen, indien nodig en andere cluster instellingen aan te passen.
