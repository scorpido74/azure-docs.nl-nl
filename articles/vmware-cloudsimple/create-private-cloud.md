---
title: Azure VMware-oplossingen (AVS)-Create AVS Privécloud (persoonlijke Cloud)
description: Hierin wordt beschreven hoe u een Privécloud kunt maken om VMware-workloads uit te breiden naar de Cloud met operationele flexibiliteit en continuïteit
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024786"
---
# <a name="create-an-avs-private-cloud"></a>Een Privécloud maken

Een AVS-Privécloud is een geïsoleerde VMware-stack die ondersteuning biedt voor ESXi-hosts, vCenter, vSAN en NSX. Persoonlijke Clouds van AVS worden beheerd via de AVS-Portal. Ze hebben hun eigen vCenter-Server in een eigen beheer domein. De stack wordt uitgevoerd op toegewezen knoop punten en geïsoleerde bare metal hardware-knoop punten.

Het maken van een AVS-Privécloud helpt u bij het oplossen van een aantal algemene behoeften voor de netwerk infrastructuur:

* **Groei**. Als u een hardware-vernieuwings punt hebt bereikt voor uw bestaande infra structuur, kunt u met een Privécloud een persoonlijke Cloud uitbreiden zonder dat er nieuwe hardware-investering vereist is.

* **Snelle uitbrei ding**. Als er sprake is van tijdelijke of niet-geplande capaciteit, kunt u de extra capaciteit zonder vertraging maken met een nieuwe AVS-Cloud.

* **Betere beveiliging**. Met een Privécloud van drie of meer knoop punten krijgt u automatische redundantie en bescherming met hoge Beschik baarheid.

* **Vereisten voor de infra structuur op lange termijn**. Als uw data centers een capaciteit hebben of als u zich wilt herstructureren om uw kosten te verlagen, kunt u met een automatische AVS-Cloud data centers buiten gebruik stellen en migreren naar een Cloud oplossing terwijl deze compatibel zijn met uw bedrijfs activiteiten.

Wanneer u een Privécloud maakt, krijgt u één vSphere-cluster en alle beheer-Vm's die in het cluster zijn gemaakt.

## <a name="before-you-begin"></a>Voordat u begint

Knoop punten moeten worden ingericht voordat u de Privécloud van uw AVS kunt maken. Zie [knoop punten inrichten voor Azure VMware-oplossingen (AVS)](create-nodes.md)voor meer informatie over het inrichten van knoop punten.

Wijs een CIDR-bereik toe voor vSphere/vSAN-subnetten voor de Privécloud in de Cloud. Een AVS-Privécloud wordt gemaakt als een geïsoleerde VMware-stack omgeving (met ESXi-hosts, vCenter, vSAN en NSX) die worden beheerd door een vCenter-Server. Beheer onderdelen worden geïmplementeerd in het netwerk dat is geselecteerd voor vSphere/vSAN-subnets CIDR. Het netwerk-CIDR-bereik is tijdens de implementatie onderverdeeld in verschillende subnetten. De adres ruimte van het vSphere/vSAN-subnet moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de AVS-omgeving. De netwerken die communiceren met AVS, zijn onder andere on-premises netwerken en Azure Virtual Networks. Zie voor meer informatie over vSphere-en vSAN-subnetten VLAN'S en subnetten-overzicht.

* Minimum aantal vSphere/vSAN-subnetten CIDR-bereik voor voegsel:/24
* Maximum aantal vSphere/vSAN-subnetten voor voegsel van CIDR-bereik:/21


## <a name="access-the-avs-portal"></a>Toegang tot de AVS-Portal

Toegang tot de [AVS-Portal](access-cloudsimple-portal.md).

## <a name="create-a-new-avs-private-cloud"></a>Een nieuwe automatische AVS-Cloud maken

1. Selecteer **Alle services**.
2. Zoeken naar **AVS-Services**.
3. Selecteer de AVS-service waarop u de Privécloud van uw AVS wilt maken.
4. Klik in **overzicht**op **AVS-privécloud maken** om een nieuw browser tabblad voor de AVS-portal te openen. Meld u aan met uw aanmeldings referenties voor Azure als u hierom wordt gevraagd.

    ![Een Privécloud maken op basis van Azure](media/create-private-cloud-from-azure.png)

5. Geef in de AVS-Portal een naam op voor de Privécloud van uw Cloud.
6. Selecteer een **locatie** voor de privécloud van uw AVS.
7. Selecteer het **knooppunt type**in overeenstemming met wat u hebt gekocht in Azure.
8. Geef het **aantal knoop punten**op. Ten minste drie knoop punten zijn vereist voor het maken van een AVS-Privécloud.
5. Geef in de CloudSimple-Portal een naam op voor de Privécloud.
6. Selecteer een **locatie** voor de privécloud.
7. Selecteer het **knooppunt type**in overeenstemming met wat u in azure hebt ingericht.
8. Geef het **aantal knoop punten**op.  Er zijn ten minste drie knoop punten vereist voor het maken van een Privécloud.

    ![Een persoonlijke AVS-Cloud maken-basis informatie](media/create-private-cloud-basic-info.png)

9. Klik op **volgende: geavanceerde opties**.
10. Voer het CIDR-bereik in voor vSphere/vSAN-subnetten. Zorg ervoor dat het CIDR-bereik niet overlapt met een van uw on-premises of andere Azure-subnetten (virtuele netwerken) of met het gateway-subnet.

    **Opties voor het CIDR-bereik:** /24,/23,/22 of/21. Een/24 CIDR-bereik ondersteunt Maxi maal negen knoop punten, een/23 CIDR-bereik ondersteunt Maxi maal 41 knoop punten en een/22-en/21 CIDR-bereik ondersteunt Maxi maal 64 knoop punten (het maximum aantal knoop punten in een Privécloud in de Cloud).

    > [!IMPORTANT]
    > IP-adressen in het vSphere/vSAN CIDR-bereik zijn gereserveerd voor gebruik door de Cloud infrastructuur voor de Privécloud. Gebruik het IP-adres in dit bereik op geen enkele virtuele machine.

11. Klik op **volgende: controleren en maken**.
12. Controleer de instellingen. Als u instellingen wilt wijzigen, klikt u op **vorige**.
13. Klik op **Maken**.

Het inrichtings proces voor de Privécloud van de Cloud wordt gestart. Het kan Maxi maal twee uur duren voordat de persoonlijke cloud van de AVS is ingericht.

Zie [een Privécloud uitbreiden](expand-private-cloud.md)voor meer informatie over het uitbreiden van een bestaande AVS-privécloud.
