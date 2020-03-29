---
title: Aan de slag met TmaxSoft OpenFrame op Azure Virtual Machines
description: Host uw IBM z/OS mainframeworkloads opnieuw met de TmaxSoft OpenFrame-omgeving op Azure Virtual Machines (VM's).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485383"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Aan de slag met TmaxSoft OpenFrame op Azure

Neem uw bestaande mainframe-elementen en verplaats ze naar Microsoft Azure met Behulp van TmaxSoft OpenFrame. Deze populaire rehostingoplossing creëert een emulatieomgeving op Azure, zodat u snel toepassingen migreren. Er is geen heropmaak vereist.

## <a name="openframe-rehosting-environment"></a>OpenFrame rehosting-omgeving

Stel een OpenFrame-omgeving in op Azure voor ontwikkelings-, demo-, test- of productieworkloads. Zoals uit de volgende afbeelding blijkt, bevat OpenFrame meerdere componenten die de mainframe-emulatieomgeving op Azure maken. OpenFrame online services vervangen bijvoorbeeld de mainframe middleware zoals IBM Customer Information Control System (CICS). OpenFrame Batch, met zijn TJES-component, vervangt het IBM mainframe's Job Entry Subsystem (JES). 

![OpenFrame rehostingproces](media/openframe-01.png)

> [!NOTE]
> Als u de OpenFrame-omgeving op Azure wilt uitvoeren, moet u over een geldige productlicentie of proeflicentie van TmaxSoft beschikken.

## <a name="openframe-components"></a>OpenFrame-componenten

De volgende componenten maken deel uit van de OpenFrame-omgeving op Azure:

- **Migratietools,** waaronder OFMiner, een oplossing die de mainframes-assets analyseert en vervolgens migreert naar Azure.
- **Compilers**, waaronder OFCOBOL, een compiler die de COBOL-programma's van het mainframe interpreteert; OFPLI, dat de PL/I-programma's van het mainframe interpreteert; en OFASM, een compiler die de assembleerprogramma's van het mainframe interpreteert.
- **Front-end** componenten, waaronder Java Enterprise User Solution (JEUS), een webapplicatieserver die is gecertificeerd voor Java Enterprise Edition 6.OFGW, en de OpenFrame-gatewaycomponent die een 3270-listener biedt.
- **Toepassingsomgeving.** OpenFrame Base is de middleware die het hele systeem beheert. OpenFrame Server Type C (OSC) vervangt de middleware van het mainframe en IBM CICS.
- **Relationele database**, zoals Tibero (getoond), Oracle Database, Microsoft SQL Server, IBM Db2 of MySQL. De OpenFrame-toepassingen gebruiken ODBC-protocol (Open Database Connectivity) om met de database te communiceren.
- **Beveiliging** via TACF, een servicemodule die de toegang van gebruikers tot systemen en bronnen regelt. 
- **OFManager** is een oplossing die de werkings- en beheerfuncties van OpenFrame biedt in de webomgeving.

![OpenFrame-architectuur](media/openframe-02.png)

## <a name="next-steps"></a>Volgende stappen

- [TmaxSoft OpenFrame installeren op Azure](./install-openframe-azure.md)
