---
title: Azure VMware Solution by CloudSimple - DNS configureren voor CloudSimple Private Cloud
description: Beschrijft hoe u DNS-naamomzetting instelt voor toegang tot vCenter-server op een CloudSimple Private Cloud vanaf on-premises werkstations
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246108"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>DNS configureren voor naamomzetting voor Private Cloud vCenter-toegang vanaf on-premises werkstations

Als u de vCenter-server op een CloudSimple Private Cloud wilt openen vanaf on-premises werkstations, moet u dns-adresresolutie configureren, zodat de vCenter-server kan worden geadresseerd aan de hostnaam en het IP-adres.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Het IP-adres van de DNS-server voor uw Private Cloud verkrijgen

1. Meld u aan bij de [CloudSimple-portal.](access-cloudsimple-portal.md)

2. Navigeer naar **Resources** > **Private Clouds** en selecteer de Private Cloud waarmee u verbinding wilt maken.

3. Kopieer op de **overzichtspagina** van de Private Cloud onder **Basisgegevens**het IP-adres van de Private Cloud DNS-server.

    ![Private Cloud DNS-servers](media/private-cloud-dns-server.png)


Gebruik een van deze opties voor de DNS-configuratie.

* [Een zone op de DNS-server maken voor *.cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Maak een voorwaardelijke expediteer op uw on-premises DNS-server om op te lossen *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Een zone op de DNS-server maken voor *.cloudsimple.io

U een zone instellen als een stompzone en de DNS-servers in de Private Cloud aanwijzen voor naamomzetting. In deze sectie vindt u informatie over het gebruik van een BIND DNS-server of een Microsoft Windows DNS-server.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Een zone op een BIND DNS-server maken

De specifieke bestands en parameters die moeten worden geconfigureerd, kunnen variëren op basis van uw individuele DNS-installatie.

Bewerk bijvoorbeeld voor de standaardCONFIGURATIE van de BIND-server het bestand /etc/named.conf op uw DNS-server en voeg de volgende zonegegevens toe.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Een zone maken op een Microsoft Windows DNS-server

1. Klik met de rechtermuisknop op de DNS-server en selecteer **Nieuwe zone**. 
  
    ![Nieuwe zone](media/DNS01.png)
2. Selecteer **Stub Zone** en klik op **Volgende**.

    ![Nieuwe zone](media/DNS02.png)
3. Selecteer de juiste optie, afhankelijk van uw omgeving, en klik op **Volgende**.

    ![Nieuwe zone](media/DNS03.png)
4. Selecteer **Zoekzone doorsturen** en klik op **Volgende**.

    ![Nieuwe zone](media/DNS01.png)
5. Voer de zonenaam in en klik op **Volgende**.

    ![Nieuwe zone](media/DNS05.png)
6. Voer de IP-adressen van de DNS-servers voor uw Private Cloud in die u hebt verkregen via de CloudSimple-portal.

    ![Nieuwe zone](media/DNS06.png)
7. Klik op **Volgende** als dat nodig is om de wizard-instelling te voltooien.

## <a name="create-a-conditional-forwarder"></a>Een voorwaardelijke doorstuurer maken

Een voorwaardelijke expediteer stuurt alle DNS-naamomzettingsaanvragen door naar de aangewezen server. Met deze instelling wordt elk verzoek naar *.cloudsimple.io doorgestuurd naar de DNS-servers in de Private Cloud. In de volgende voorbeelden ziet u hoe u forwarders instelt op verschillende typen DNS-servers.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Een voorwaardelijke doorstuurserver maken op een BIND DNS-server

De specifieke bestands en parameters die moeten worden geconfigureerd, kunnen variëren op basis van uw individuele DNS-installatie.

Bewerk bijvoorbeeld voor de standaardCONFIGURATIE van de BIND-server het bestand /etc/named.conf op uw DNS-server en voeg de volgende voorwaardelijke doorstuurgegevens toe.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Een voorwaardelijke expediteer maken op een Microsoft Windows DNS-server

1. Open de DNS-beheer op de DNS-server.
2. Klik met de rechtermuisknop op **Voorwaardelijke doorstuurers** en selecteer de optie om een nieuwe voorwaardelijke forwarder toe te voegen.

    ![Voorwaardelijke forwarder 1 Windows DNS](media/DNS08.png)
3. Voer het DNS-domein en het IP-adres van de DNS-servers in de Private Cloud in en klik op **OK**.
