---
title: Beveiligingsaanbevelingen voor Azure Marketplace-afbeeldingen | Microsoft Documenten
description: Dit artikel bevat aanbevelingen voor afbeeldingen die op de markt zijn opgenomen
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: b82cf957f4bd74cb2c63bfd5a7fe73899b395df6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795816"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Beveiligingsaanbevelingen voor Azure Marketplace-afbeeldingen

Uw afbeelding moet voldoen aan deze aanbevelingen voor beveiligingsconfiguratie. Dit helpt bij het handhaven van een hoog beveiligingsniveau voor afbeeldingen van partneroplossingen in de Azure Marketplace.

Voer altijd een beveiligingsdetectie uit op uw afbeelding voordat u deze indient. Als u een beveiligingslek in uw eigen gepubliceerde afbeelding detecteert, moet u uw klanten tijdig informeren over zowel het beveiligingslek als hoe u dit corrigeren.

## <a name="open-source-based-images"></a>Afbeeldingen op basis van open source

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categorie**                                                 | **Controleren**                                                                                                                                                                                                                                                                              |
| Beveiliging                                                     | Installeer de nieuwste beveiligingspatches voor de Linux-distributie.                                                                                                                                                                                                              |
| Beveiliging                                                     | Volg de richtlijnen van de branche om de VM-afbeelding voor de specifieke Linux-distributie te beveiligen.                                                                                                                                                                                     |
| Beveiliging                                                     | Beperk het aanvalsoppervlak door een minimale voetafdruk te behouden met alleen de benodigde Windows Server-rollen, -functies, -services en -netwerkpoorten.                                                                                                                                               |
| Beveiliging                                                     | Scan broncode en resulterende VM-afbeelding op malware.                                                                                                                                                                                                                                   |
| Beveiliging                                                     | De VHD-afbeelding bevat alleen de nodige vergrendelde accounts die geen standaardwachtwoorden hebben die interactief inloggen mogelijk maken; geen achterdeuren.                                                                                                                                           |
| Beveiliging                                                     | Firewallregels uitschakelen, tenzij de toepassing er functioneel op vertrouwt, zoals een firewalltoestel.                                                                                                                                                                             |
| Beveiliging                                                     | Verwijder alle gevoelige informatie uit de VHD-afbeelding, zoals test SSH-toetsen, bekende hosts-bestanden, logboekbestanden en onnodige certificaten.                                                                                                                                       |
| Beveiliging                                                     | Vermijd het gebruik van LVM.                                                                                                                                                                                                                                            |
| Beveiliging                                                     | Neem de nieuwste versies van vereiste bibliotheken op: </br> - OpenSSL v1.0 of hoger </br> - Python 2.5 of hoger (Python 2.6+ is een aanrader) </br> - Python pyasn1 pakket als dit nog niet is geïnstalleerd </br> - d.OpenSSL v 1.0 of hoger                                                                |
| Beveiliging                                                     | Duidelijke Bash / Shell geschiedenis vermeldingen.                                                                                                                                                                                                                                             |
| Netwerken                                                   | Neem standaard de SSH-server op. Stel SSH in leven om config te sshd met de volgende optie: ClientAliveInterval 180.                                                                                                                                                        |
| Netwerken                                                   | Verwijder elke aangepaste netwerkconfiguratie uit de afbeelding. Verwijder de resolv.conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Implementatie                                                   | Installeer de nieuwste Azure Linux Agent.</br> - Installeren met behulp van het RPM- of Deb-pakket.  </br> - U ook gebruik maken van de handmatige installatieproces, maar de installateur pakketten worden aanbevolen en de voorkeur. </br> - Als u de agent handmatig installeert vanuit de `waagent` GitHub-opslagplaats, kopieert u het bestand eerst naar `/usr/sbin` en wordt uitgevoerd (als hoofd): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Het configuratiebestand van `/etc/waagent.conf`de agent wordt geplaatst op . |
| Implementatie                                                   | Zorg ervoor dat Azure Support onze partners seriële console-uitvoer kan bieden wanneer dat nodig is en een voldoende time-out kan bieden voor het monteren van os-schijven vanuit cloudopslag. Voeg de volgende parameters toe aan `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`de afbeeldingkernelopstartlijn: . |
| Implementatie                                                   | Geen swap partitie op de OS schijf. Swap kan worden aangevraagd voor creatie op de lokale resourceschijf door de Linux-agent.         |
| Implementatie                                                   | Maak één hoofdpartitie voor de OS-schijf.      |
| Implementatie                                                   | Alleen 64-bits besturingssysteem.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Afbeeldingen op basis van Windows Server

|||
|-------------| -------------------------|
| **Categorie**                                                     | **Controleren**                                                                                                                                                                |
| Beveiliging                                                         | Gebruik een beveiligde os-basisafbeelding. De VHD die wordt gebruikt voor de bron van een afbeelding op basis van Windows Server moet afkomstig zijn van de Windows Server OS-afbeeldingen die via Microsoft Azure worden geleverd. |
| Beveiliging                                                         | Installeer alle nieuwste beveiligingsupdates.                                                                                                                                     |
| Beveiliging                                                         | Toepassingen mogen niet afhankelijk zijn van beperkte gebruikersnamen zoals beheerder, root of beheerder.                                                                |
| Beveiliging                                                         | BitLocker-stationsversleuteling inschakelen voor zowel harde schijven als gegevensharde schijven.                                                             |
| Beveiliging                                                         | Beperk het aanvalsoppervlak door een minimale voetafdruk te behouden met alleen de benodigde Windows Server-rollen, -functies, -services en-netwerkpoorten ingeschakeld.                         |
| Beveiliging                                                         | Scan broncode en resulterende VM-afbeelding op malware.                                                                                                                     |
| Beveiliging                                                         | Stel de beveiligingsupdate van Windows Server-afbeeldingen in op automatische update.                                                                                                                |
| Beveiliging                                                         | De VHD-afbeelding bevat alleen de nodige vergrendelde accounts die geen standaardwachtwoorden hebben die interactief inloggen mogelijk maken; geen achterdeuren.                             |
| Beveiliging                                                         | Firewallregels uitschakelen, tenzij de toepassing er functioneel op vertrouwt, zoals een firewalltoestel.                                                               |
| Beveiliging                                                         | Verwijder alle gevoelige informatie uit de VHD-afbeelding, inclusief HOSTS-bestanden, logboekbestanden en onnodige certificaten.                                              |
| Implementatie                                                       | Alleen 64-bits besturingssysteem.                            |

Zelfs als uw organisatie geen afbeeldingen in de Azure-marktplaats heeft, u overwegen uw Windows- en Linux-afbeeldingsconfiguraties te controleren op basis van deze aanbevelingen.

## <a name="contacting-customers"></a>Klanten contact opnemen

Ga als contact op met klanten en hun e-mails met contactpersonen:

1.  Selecteer **Insights**in Cloud Partner Portal aan de linkerkant.
2.  Gebruik op het tabblad **Orders en gebruik** de velden **Begindatum** en **Einddatum** om het gebruik binnen het vereiste datumbereik op te vragen. Hieruit blijkt welke Azure-abonnementen dagelijks voor de aanbieding zijn gebruikt. Exporteer deze gegevens. 
3.  Op dezelfde manier u op het tabblad **Klant** uw klantenbestand opvragen en exporteren.
4.  Koppel de abonnements-id van stap 2 naar de abonnements-id van stap 3 aan om de benodigde klantgegevens te vinden.
