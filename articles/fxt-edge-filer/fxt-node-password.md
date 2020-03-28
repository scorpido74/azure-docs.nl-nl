---
title: 'Zelfstudie: Hardware initialiseren - Azure FXT Edge Filer'
description: Een eerste wachtwoord instellen op Azure FXT Edge Filer-knooppunten
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8cb5f639deb0630575c46db30efe70ad967324a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550888"
---
# <a name="tutorial-set-hardware-passwords"></a>Zelfstudie: Hardwarewachtwoorden instellen

De eerste keer dat u een Azure FXT Edge Filer-knooppunt opvoert, moet u een hoofdwachtwoord instellen. De hardwareknooppunten worden niet verzonden met een standaardwachtwoord. 

Netwerkpoorten zijn uitgeschakeld tot nadat het wachtwoord is ingesteld en de hoofdgebruiker zich aanmeldt.

Doe deze stap na het installeren en bezetten van het knooppunt, maar voordat u probeert het cluster te maken. 

In deze zelfstudie wordt uitgelegd hoe u verbinding maken met het hardwareknooppunt en het wachtwoord instellen. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Sluit een toetsenbord en monitor aan op het knooppunt en deze in
> * Wachtwoorden instellen voor de iDRAC-poort en rootgebruiker op dit knooppunt
> * Aanmelden als root 

Herhaal deze stappen voor elk knooppunt dat u in uw cluster gebruikt. 

Deze tutorial duurt ongeveer 15 minuten. 

## <a name="prerequisites"></a>Vereisten

Voer de volgende stappen uit voordat u met deze zelfstudie begint: 

* [Installeer](fxt-install.md) elke Azure FXT Edge Filer-node in een apparaatrack en bevestig stroomkabels en netwerktoegang zoals beschreven in de [eerdere zelfstudie.](fxt-network-power.md) 
* Zoek een usb-aangesloten toetsenbord en een VGA-aangesloten monitor die u koppelen aan de hardwareknooppunten. (De seriële poort van het knooppunt is inactief voordat u het wachtwoord instelt.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Een toetsenbord en monitor aansluiten op het knooppunt

Sluit een monitor en toetsenbord fysiek aan op het Azure FXT Edge Filer-knooppunt. 

* Sluit de monitor aan op de VGA-poort.
* Sluit het toetsenbord aan op een van de USB-poorten. 

Gebruik dit referentiediagram om de poorten aan de achterkant van het chassis te vinden. 

> [!NOTE]
> De seriële poort is inactief tot nadat het wachtwoord is ingesteld. 

![diagram met achterkant van Azure FXT Edge Filer met seriële, VGA- en USB-poorten met het label](media/fxt-back-serial-vga-usb.png)

U een KVM-schakelaar gebruiken als u meer dan één knooppunt op dezelfde randapparatuur wilt aansluiten. 

Zet het knooppunt aan door op de aan/uit-knop aan de voorkant te drukken. 

![diagram voor Azure FXT Edge Filer - ronde aan/uit-knop wordt in de buurt van rechtsboven gelabeld](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Eerste wachtwoorden instellen 

Het Azure FXT Edge Filer-knooppunt drukt tijdens het opstarten verschillende berichten af op de monitor. Na een paar ogenblikken, toont het een aanvankelijk opstellingsscherm als dit:

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

Het wachtwoord dat u invoert, wordt voor twee dingen gebruikt: 

* Het is het tijdelijke hoofdwachtwoord voor dit Azure FXT Edge Filer-knooppunt. 

  Dit wachtwoord wordt gewijzigd wanneer u een cluster maakt met dit knooppunt of wanneer u dit knooppunt aan het cluster toevoegt. Het wachtwoord voor clusterbeheer ``admin``(gekoppeld aan de gebruiker) is ook het hoofdwachtwoord voor alle knooppunten in een cluster.

* Het is het wachtwoord voor de lange termijn wachtwoord voor de iDRAC / IPMI hardware management poort.

  Zorg ervoor dat u het wachtwoord onthoudt voor het geval u zich later bij IPMI moet aanmelden om een hardwareprobleem op te lossen.

Voer het wachtwoord in en bevestig het: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Nadat u het wachtwoord hebt ingevoerd, wordt het systeem verder opgestart. Wanneer het klaar is, ``login:`` geeft het een prompt. 

## <a name="sign-in-as-root"></a>Aanmelden als root

Meld u ``root`` aan zoals bij het wachtwoord dat u zojuist hebt ingesteld. 

```
login: root
Password:**********
```

Nadat u zich als hoofdhebt aangemeld, zijn de netwerkpoorten actief en nemen ze contact op met de DHCP-server voor IP-adressen. 

## <a name="next-steps"></a>Volgende stappen

Het knooppunt is klaar om deel uit te maken van een cluster. U het gebruiken om het Azure FXT Edge Filer-cluster te maken of u [het toevoegen aan een bestaand cluster.](fxt-add-nodes.md) 

> [!div class="nextstepaction"]
> [Een cluster maken](fxt-cluster-create.md)
