---
title: Azure VMware-oplossing door CloudSimple - CloudSimple Private Cloud maken
description: Beschrijft hoe u een CloudSimple Private Cloud maakt om VMware-workloads uit te breiden naar de cloud met operationele flexibiliteit en continuïteit
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024786"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Een CloudSimple Private Cloud maken

Een Private Cloud is een geïsoleerde VMware-stack die ESXi-hosts, vCenter, vSAN en NSX ondersteunt. Private Clouds worden beheerd via de CloudSimple portal. Ze hebben hun eigen vCenter server in een eigen beheerdomein. De stack draait op speciale knooppunten en geïsoleerde bare metal hardware knooppunten.

Het maken van een Private Cloud helpt u om tegemoet te komen aan een verscheidenheid aan gemeenschappelijke behoeften voor netwerkinfrastructuur:

* **Groei**. Als u een hardwareverversingspunt voor uw bestaande infrastructuur hebt bereikt, u met een Private Cloud uitbreiden zonder dat er nieuwe hardware-investeringen nodig zijn.

* **Snelle uitbreiding**. Als er tijdelijke of ongeplande capaciteitsbehoeften ontstaan, u met een Private Cloud de extra capaciteit zonder vertraging creëren.

* **Meer bescherming**. Met een Private Cloud van drie of meer knooppunten krijgt u automatische redundantie en bescherming met hoge beschikbaarheid.

* **Infrastructuurbehoeften op lange termijn**. Als uw datacenters op capaciteit zijn of als u wilt herstructureren om uw kosten te verlagen, u met een Private Cloud datacenters terugtrekken en migreren naar een cloudgebaseerde oplossing terwijl u compatibel blijft met uw bedrijfsactiviteiten.

Wanneer u een private cloud maakt, krijgt u één vSphere-cluster en alle beheer-VM's die in dat cluster zijn gemaakt.

## <a name="before-you-begin"></a>Voordat u begint

Knooppunten moeten worden ingericht voordat u uw Private Cloud maken. Zie [Knooppunten voor Azure VMware-oplossing door CloudSimple voor](create-nodes.md)meer informatie over het inrichten van knooppunten.

Wijs een CIDR-bereik toe voor vSphere/vSAN-subnetten voor de Private Cloud. Een Private Cloud wordt gemaakt als een geïsoleerde VMware-stackomgeving (met ESXi-hosts, vCenter, vSAN en NSX) die worden beheerd door een vCenter-server. Beheercomponenten worden geïmplementeerd in het netwerk dat is geselecteerd voor vSphere/vSAN-subnetten CIDR. Het CIDR-netwerk-bereik is tijdens de implementatie verdeeld in verschillende subnetten. De subnetadresruimte vSphere/vSAN moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de CloudSimple-omgeving. De netwerken die communiceren met CloudSimple omvatten on-premises netwerken en Virtuele Azure-netwerken. Zie VLAN's en subnetten overzicht voor meer informatie over vSphere/vSAN-subnetten.

* Minimaal vSphere/vSAN-subnetten CIDR-bereikvoorvoegsel: /24
* Maximaal vSphere/vSAN-subnetten CIDR-bereikvoorvoegsel: /21


## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

Toegang tot de [CloudSimple-portal](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Een nieuwe privécloud maken

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
3. Selecteer de CloudSimple-service waarop u uw Private Cloud wilt maken.
4. Klik **in Overzicht**op **Privécloud maken** om een nieuw browsertabblad voor CloudSimple-portal te openen. Meld u desgevraagd aan met uw Azure-aanmeldingsreferenties.

    ![Private Cloud maken vanuit Azure](media/create-private-cloud-from-azure.png)

5. Geef in de CloudSimple-portal een naam voor uw Private Cloud.
6. Selecteer **Locatie** voor uw private cloud.
7. Selecteer **Knooppunttype,** in overeenstemming met wat u op Azure hebt ingericht.
8. Het **aantal nodes opgeven**.  Er zijn ten minste drie knooppunten nodig om een Private Cloud te maken.

    ![Private Cloud maken - Basisinformatie](media/create-private-cloud-basic-info.png)

9. Klik **op Volgende: Geavanceerde opties**.
10. Voer het CIDR-bereik in voor vSphere/vSAN-subnetten. Zorg ervoor dat het CIDR-bereik niet overlapt met een van uw on-premises of andere Azure-subnetten (virtuele netwerken) of met het gateway-subnet.

    **CIDR-bereikopties:** /24, /23, /22 of /21. Een CIDR-bereik van /24 ondersteunt maximaal negen knooppunten, een CIDR-bereik van /23 ondersteunt maximaal 41 knooppunten en een CIDR-bereik van /22 en /21 ondersteunt maximaal 64 knooppunten (het maximum aantal knooppunten in een private cloud).

    > [!IMPORTANT]
    > IP-adressen in het vSphere/vSAN CIDR-bereik zijn gereserveerd voor gebruik door de Private Cloud-infrastructuur.  Gebruik het IP-adres in dit bereik niet op een virtuele machine.

11. Klik **op Volgende: Controleren en maken**.
12. Bekijk de instellingen. Als u instellingen wilt wijzigen, klikt u op **Vorige**.
13. Klik **op Maken**.

Private Cloud provisioning proces begint. Het kan tot twee uur duren voordat de Private Cloud is ingericht.

Zie [Een private cloud uitbreiden](expand-private-cloud.md)voor instructies over het uitbreiden van een bestaande private cloud.
