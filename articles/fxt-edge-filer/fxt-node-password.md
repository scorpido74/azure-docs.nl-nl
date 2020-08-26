---
title: 'Zelfstudie: Hardware initialiseren: Azure FXT Edge Filer'
description: Lees hoe u verbinding maakt met het hardwareknooppunt en een initieel wachtwoord instelt voor Azure FXT Edge Filer-knooppunten.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4fae7795cdd61e3eb465285163bcd6c0dd9f7db2
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184921"
---
# <a name="tutorial-set-hardware-passwords"></a>Zelfstudie: Hardwarewachtwoorden instellen

De eerste keer dat u een Azure FXT Edge Filer-knooppunt inschakelt, moet u een hoofdwachtwoord instellen. De hardwareknooppunten worden geleverd zonder standaardwachtwoord. 

De netwerkpoorten zijn uitgeschakeld totdat het wachtwoord is ingesteld en de rootgebruiker zich aanmeldt.

Voer deze stap uit na het installeren en bekabelen van het knooppunt, maar voordat u probeert het cluster te maken. 

In deze zelfstudie wordt uitgelegd hoe u verbinding maakt met het hardwareknooppunt en hoe u een wachtwoord instelt. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Een toetsenbord en monitor aansluiten op het knooppunt en deze inschakelen
> * Wachtwoorden instellen voor de iDRAC-poort en rootgebruiker op dit knooppunt
> * Aanmelden als rootgebruiker 

Herhaal deze stappen voor elk knooppunt dat u in uw cluster wilt gebruiken. 

Het voltooien van deze zelfstudie duurt ongeveer 15 minuten. 

## <a name="prerequisites"></a>Vereisten

Voltooi deze stappen voordat u aan deze zelfstudie begint: 

* [Installeer](fxt-install.md) elk Azure FXT Edge Filer-knooppunt in een rek, en sluit netsnoeren en netwerkkabels aan zoals wordt beschreven in deze [eerdere zelfstudie](fxt-network-power.md). 
* Zoek een toetsenbord met USB-aansluiting en een VGA-monitor die u kunt aansluiten op de hardwareknooppunten. (De seriële poort van het knooppunt is inactief totdat u een wachtwoord instelt.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Een toetsenbord en monitor aansluiten op het knooppunt

Sluit een monitor en toetsenbord aan op het Azure FXT Edge Filer-knooppunt. 

* Sluit de monitor aan op de VGA-poort.
* Sluit het toetsenbord aan op een van de USB-poorten. 

Gebruik dit achteraanzicht om de poorten te vinden op de achterzijde van het chassis. 

> [!NOTE]
> De seriële poort is inactief totdat er een wachtwoord is ingesteld. 

![Achteraanzicht van Azure FXT Edge Filer met seriële poort, VGA-poort en USB-poorten aangegeven](media/fxt-back-serial-vga-usb.png)

U kunt een KVM-switch gebruiken als u meer dan een knooppunt wilt aansluiten op dezelfde randapparatuur. 

Schakel het knooppunt in door aan de voorzijde op de aan/uit-knop te drukken. 

![Vooraanzicht van Azure FXT Edge Filer - de ronde aan/uit-knop zit rechtsboven, zoals hier is aangegeven](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Initiële wachtwoorden instellen 

Tijdens het opstarten van het Azure FXT Edge Filer-knooppunt verschijnen er verschillende berichten op de monitor. Na enige tijd ziet u een eerste configuratiescherm zoals dit:

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

Het wachtwoord dat u invoert, wordt gebruikt voor twee dingen: 

* Het is het tijdelijke hoofdwachtwoord voor dit Azure FXT Edge Filer-knooppunt. 

  Dit wachtwoord wordt gewijzigd wanneer u een cluster met dit knooppunt maakt of wanneer u dit knooppunt aan het cluster toevoegt. Het wachtwoord voor clusterbeheer (gekoppeld aan de gebruiker ``admin``) is ook het hoofdwachtwoord voor alle knooppunten in een cluster.

* Het is het langetermijnwachtwoord voor de iDRAC/IPMI-poort voor hardwarebeheer.

  Zorg ervoor dat u het wachtwoord niet vergeet, zodat u zich later kunt aanmelden bij IPMI om een hardwareprobleem op te lossen.

Voer het wachtwoord in en bevestig dit: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Nadat u het wachtwoord hebt ingevoerd, wordt het systeem verder opgestart. Als alles klaar is, ziet u de prompt ``login:``. 

## <a name="sign-in-as-root"></a>Aanmelden als rootgebruiker

Meld u aan als ``root`` met het wachtwoord dat u zojuist hebt ingesteld. 

```
login: root
Password:**********
```

Nadat u zich hebt aangemeld als rootgebruiker, zijn de netwerkpoorten actief en wordt er contact opgenomen met de DHCP-server voor IP-adressen. 

## <a name="next-steps"></a>Volgende stappen

Het knooppunt is klaar om te worden toegevoegd aan een cluster. U kunt het knooppunt gebruiken om een Azure FXT Edge Filer-cluster te maken of u kunt [het knooppunt toevoegen aan een bestaand cluster](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Een cluster maken](fxt-cluster-create.md)
