---
title: 'Azure VMware-oplossingen (AVS): DNS configureren voor de Privécloud-Cloud'
description: Hierin wordt beschreven hoe u een DNS-naam omzetting instelt voor toegang tot de vCenter-Server op een AVS-Privécloud vanuit on-premises werk stations
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 12c4362ae1b075af132d5971f4fe0461c9d91733
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083078"
---
# <a name="configure-dns-for-name-resolution-for-avs-private-cloud-vcenter-access-from-on-premises-workstations"></a>DNS configureren voor het omzetten van namen voor de automatische AVS-toegang via de Cloud vCenter vanaf on-premises werk stations

Als u toegang wilt krijgen tot de vCenter-Server op een AVS-Privécloud vanuit on-premises werk stations, moet u de DNS-adres omzetting configureren zodat de vCenter-Server kan worden geadresseerd aan de hand van hostnaam en IP-adres.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-avs-private-cloud"></a>Het IP-adres van de DNS-server voor de Privécloud van uw AVS ophalen

1. Meld u aan bij de [AVS-Portal](access-cloudsimple-portal.md).

2. Navigeer naar **Resources** > automatische **AVS-Clouds** en selecteer de AVS-privécloud waarmee u verbinding wilt maken.

3. Op de pagina **samen vatting** van de privécloud van de AVS onder **basis informatie**kopieert u het IP-adres van de DNS-server van de AVS-privécloud.

    ![DNS-servers voor de Privécloud in de Cloud](media/private-cloud-dns-server.png)


Gebruik een van deze opties voor de DNS-configuratie.

* [Maak een zone op de DNS-server voor *. cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Een voorwaardelijke doorstuur server maken op uw lokale DNS-servers om *. cloudsimple.io te kunnen oplossen](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Maak een zone op de DNS-server voor *. cloudsimple.io

U kunt een zone instellen als stub-zone en verwijzen naar de DNS-servers in de Privécloud voor naam omzetting. Deze sectie bevat informatie over het gebruik van een BIND-DNS-server of een micro soft Windows DNS-server.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Een zone maken op een BIND-DNS-server

Het specifieke bestand en de para meters die moeten worden geconfigureerd, kunnen variëren op basis van de afzonderlijke DNS-instellingen.

Voor de standaard configuratie van de BIND-server bewerkt u bijvoorbeeld/etc/named.conf-bestand op uw DNS-server en voegt u de volgende zone gegevens toe.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Een zone maken op een micro soft Windows DNS-server

1. Klik met de rechter muisknop op de DNS-server en selecteer **nieuwe zone**. 
  
    ![Nieuwe zone](media/DNS01.png)
2. Selecteer **stub-zone** en klik op **volgende**.

    ![Nieuwe zone](media/DNS02.png)
3. Selecteer de gewenste optie, afhankelijk van uw omgeving en klik op **volgende**.

    ![Nieuwe zone](media/DNS03.png)
4. Selecteer **zone voor forward lookup** en klik op **volgende**.

    ![Nieuwe zone](media/DNS01.png)
5. Voer de zone naam in en klik op **volgende**.

    ![Nieuwe zone](media/DNS05.png)
6. Voer de IP-adressen in van de DNS-servers voor de Privécloud die u hebt verkregen via de AVS-Portal.

    ![Nieuwe zone](media/DNS06.png)
7. Klik op **volgende** als dat nodig is om de installatie van de wizard te volt ooien.

## <a name="create-a-conditional-forwarder"></a>Een voorwaardelijke doorstuur server maken

Een voorwaardelijke doorstuur server stuurt alle aanvragen voor DNS-naam omzetting door naar de aangewezen server. Bij deze installatie wordt elke aanvraag voor *. cloudsimple.io doorgestuurd naar de DNS-servers die zich bevinden in de Privécloud van de AVS. In de volgende voor beelden ziet u hoe u doorstuur servers instelt op verschillende typen DNS-server.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Voorwaardelijke doorstuur servers maken op een BIND-DNS-server

Het specifieke bestand en de para meters die moeten worden geconfigureerd, kunnen variëren op basis van de afzonderlijke DNS-instellingen.

Bijvoorbeeld: voor de standaard configuratie van de BIND-server bewerkt u/etc/named.conf-bestand op uw DNS-server en voegt u de volgende informatie over voorwaardelijke door sturing toe.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Voorwaardelijke doorstuur servers maken op een micro soft Windows DNS-server

1. Open DNS-beheer op de DNS-server.
2. Klik met de rechter muisknop op **voorwaardelijke doorstuur servers** en selecteer de optie om een nieuwe voorwaardelijke doorstuur server toe te voegen.

    ![Voorwaardelijke doorstuur server 1 Windows DNS](media/DNS08.png)
3. Voer het DNS-domein en het IP-adres in van de DNS-servers in de cloud van de AVS en klik op **OK**.
