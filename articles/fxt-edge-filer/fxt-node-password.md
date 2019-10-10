---
title: Hardware-Microsoft Azure-FXT-rand bestand initialiseren
description: Een eerste wacht woord instellen voor Azure FXT Edge-knoop punten
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 080aa05af77b996bc0eb71287a3dfef25c24629a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256014"
---
# <a name="tutorial-set-hardware-passwords"></a>Zelf studie: Hardware-wacht woorden instellen

De eerste keer dat u een Azure FXT Edge-knoop punt uitschakelt, moet u een Hoofdwacht woord instellen. De hardwarestuurprogramma's worden niet geleverd met een standaard wachtwoord. 

Netwerk poorten worden uitgeschakeld totdat het wacht woord is ingesteld en de hoofd gebruiker zich aanmeldt.

Voer deze stap na het installeren en bekabelen van het knoop punt uit, maar voordat u probeert het cluster te maken. 

In deze zelf studie wordt uitgelegd hoe u verbinding maakt met het hardwareprofiel en hoe u het wacht woord instelt. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Een toetsen bord en monitor aansluiten op het knoop punt en deze inschakelen
> * Wacht woorden instellen voor de iDRAC-poort en hoofd gebruiker op dit knoop punt
> * Aanmelden als root 

Herhaal deze stappen voor elk knoop punt dat u in uw cluster wilt gebruiken. 

Het volt ooien van deze zelf studie duurt ongeveer 15 minuten. 

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, voert u de volgende stappen uit: 

* [Installeer](fxt-install.md) elk Azure FXT Edge-knoop punt in een materieel rek en koppel energie kabels en netwerk toegang zoals beschreven in de [vorige zelf studie](fxt-network-power.md). 
* Zoek een toetsen bord met USB-verbinding en een met VGA verbonden monitor die u aan de hardwareprofielen kunt koppelen. (De seriële poort van het knoop punt is niet actief voordat u het wacht woord instelt.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Een toetsen bord en monitor aansluiten op het knoop punt

Verbind een monitor en toetsen bord fysiek met het knoop punt Azure FXT Edge-bestand. 

* Verbind de monitor met de VGA-poort.
* Verbind het toetsen bord met een van de USB-poorten. 

Gebruik dit referentie diagram om de poorten op de achterkant van het chassis te vinden. 

> [!NOTE]
> De seriële poort is pas actief nadat het wacht woord is ingesteld. 

![diagram van de back-upbestanden van de Azure FXT-rand met serie-, VGA-en USB-poorten met label](media/fxt-back-serial-vga-usb.png)

U kunt een KVM-switch gebruiken als u meer dan één knoop punt wilt verbinden met dezelfde rand apparatuur. 

Schakel het knoop punt uit door op de aan/uit-knop aan de voor grond te drukken. 

![diagram van front van Azure FXT Edge-beknopte aan de rechter bovenhoek](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Initiële wacht woorden instellen 

In het knoop punt Azure FXT Edge-bestand worden verschillende berichten naar de monitor afgedrukt tijdens het opstarten. Na enkele ogen blikken wordt een eerste installatie scherm als volgt weer gegeven:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

Het wacht woord dat u invoert, wordt gebruikt voor twee dingen: 

* Het is het tijdelijke wacht woord voor dit knoop punt van de Azure FXT Edge-Bestands server. 

  Dit wacht woord wordt gewijzigd wanneer u een cluster met dit knoop punt maakt of wanneer u dit knoop punt aan het cluster toevoegt. Het wacht woord voor cluster beheer (gekoppeld aan de gebruiker ``admin``) is ook het hoofd wachtwoord voor alle knoop punten in een cluster.

* Het is het wacht woord voor de lange termijn voor de iDRAC/IPMI-hardwarematige poort.

  Zorg ervoor dat u het wacht woord vergeet om u later aan te melden met IPMI om een hardwareprobleem op te lossen.

Voer het wacht woord in en bevestig dit: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Nadat u het wacht woord hebt ingevoerd, wordt het systeem voortgezet met opstarten. Wanneer deze is voltooid, wordt een ``login:``-prompt. 

## <a name="sign-in-as-root"></a>Aanmelden als root

Meld u aan als ``root`` met het wacht woord dat u zojuist hebt ingesteld. 

```
login: root
Password:**********
```

Nadat u zich hebt aangemeld als basis, zijn de netwerk poorten actief en neemt het contact op met de DHCP-server voor IP-adressen. 

## <a name="next-steps"></a>Volgende stappen

Het knoop punt is klaar om deel uit te maken van een cluster. U kunt deze gebruiken om het Azure FXT Edge-bestands cluster te maken of u kunt [het toevoegen aan een bestaand cluster](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Een cluster maken](fxt-cluster-create.md)
