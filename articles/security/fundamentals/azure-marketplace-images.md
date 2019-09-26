---
title: Beveiligings aanbevelingen voor installatie kopieën van Azure Marketplace | Microsoft Docs
description: In dit artikel worden aanbevelingen gegeven voor installatie kopieën die zijn opgenomen in de markt plaats
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
ms.openlocfilehash: 3d6b1ed2a3800058acba9fe5df2137811afed74a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300774"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Beveiligings aanbevelingen voor installatie kopieën van Azure Marketplace

U wordt aangeraden elke oplossing te voldoen aan de volgende aanbevelingen voor beveiligings configuraties. Zo kunt u een hoog beveiligings niveau voor installatie kopieën van partner oplossingen in azure Marketplace onderhouden.

Deze aanbevelingen kunnen ook handig zijn voor organisaties die geen installatie kopieën hebben op de Azure Marketplace. U kunt de Windows-en Linux-installatie kopie configuraties van uw bedrijf controleren aan de hand van de richt lijnen in de volgende tabellen:

## <a name="open-source-based-images"></a>Open-source installatie kopieën

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categorie**                                                 | **Kijk**                                                                                                                                                                                                                                                                              |
| Beveiliging                                                     | Alle recente beveiligings patches voor de Linux-distributie zijn geïnstalleerd.                                                                                                                                                                                                              |
| Beveiliging                                                     | Industrie richtlijnen voor het beveiligen van de VM-installatie kopie voor de specifieke Linux-distributie zijn gevolgd.                                                                                                                                                                                     |
| Beveiliging                                                     | Beperk de kwets baarheid door minimale footprint te behouden met alleen de benodigde Windows Server-functies,-onderdelen,-services en-netwerk poorten.                                                                                                                                               |
| Beveiliging                                                     | Scan de bron code en de resulterende VM-installatie kopie voor malware.                                                                                                                                                                                                                                   |
| Beveiliging                                                     | De VHD-installatie kopie bevat alleen de benodigde vergrendelde accounts, die geen standaard wachtwoorden hebben waarmee interactieve aanmelding zou worden toegestaan. geen deuren terug.                                                                                                                                           |
| Beveiliging                                                     | Firewall regels zijn uitgeschakeld, tenzij toepassingen functioneel afhankelijk zijn, zoals een firewall apparaat.                                                                                                                                                                             |
| Beveiliging                                                     | Alle gevoelige informatie is verwijderd uit de VHD-installatie kopie, zoals het testen van SSH-sleutels, bekende hosts-bestanden, logboek bestanden en overbodige certificaten.                                                                                                                                       |
| Beveiliging                                                     | Het is raadzaam om LVM niet te gebruiken.                                                                                                                                                                                                                                            |
| Beveiliging                                                     | De nieuwste versies van de vereiste bibliotheken moeten worden opgenomen: </br> -OpenSSL v 1.0 of hoger </br> -Python 2,5 of hoger (python 2.6 + wordt sterk aanbevolen) </br> -Python pyasn1-pakket als dit nog niet is geïnstalleerd </br> -d. OpenSSL v 1,0 of hoger                                                                |
| Beveiliging                                                     | Bash/shell-geschiedenis vermeldingen moeten worden gewist                                                                                                                                                                                                                                             |
| Netwerken                                                   | SSH-server moet standaard worden opgenomen. Stel SSH in op sshd config met de volgende optie: ClientAliveInterval 180                                                                                                                                                        |
| Netwerken                                                   | De installatie kopie mag geen aangepaste netwerk configuratie bevatten. Verwijder het omgezette. conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Implementatie                                                   | De meest recente Azure Linux-agent moet worden geïnstalleerd </br> -De agent moet worden geïnstalleerd met behulp van het RPM-of deb-pakket.  </br> -U kunt ook het hand matige installatie proces gebruiken, maar de installatie pakketten worden aanbevolen en voor keur. </br> -Als u de agent hand matig installeert vanuit de GitHub-opslag plaats `waagent` , kopieert `/usr/sbin` u het bestand naar en voert u uit (als basis): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Het configuratie bestand van de agent wordt `/etc/waagent.conf`geplaatst op.    |
| Implementatie                                                   | Zorgt ervoor dat de ondersteuning van Azure onze partners kan voorzien van een seriële console-uitvoer wanneer dat nodig is en voldoende time-out bieden voor het koppelen van de besturingssysteem schijf uit de Cloud opslag. Voor de installatie kopie moeten de volgende para meters zijn toegevoegd aan de opstart regel voor de kernel:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Implementatie                                                   | Geen wissel partitie op de besturingssysteem schijf. De Linux-agent kan worden gevraagd om te maken op de lokale bron schijf.         |
| Implementatie                                                   | Het is raadzaam om één hoofd partitie te maken voor de besturingssysteem schijf.      |
| Implementatie                                                   | 64-bits besturings systeem.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Installatie kopieën op basis van Windows Server

|||
|-------------| -------------------------|
| **Categorie**                                                     | **Kijk**                                                                                                                                                                |
| Beveiliging                                                         | Gebruik een veilige basis installatie kopie van het besturings systeem. De VHD die wordt gebruikt voor de bron van een installatie kopie op basis van Windows Server moet afkomstig zijn uit de installatie kopieën van Windows Server-besturings systemen die via Microsoft Azure worden meegeleverd. |
| Beveiliging                                                         | Installeer alle meest recente beveiligings updates.                                                                                                                                     |
| Beveiliging                                                         | Toepassingen mogen geen afhankelijkheid hebben van beperkte gebruikers namen, zoals Administrator, root en admin.                                                                |
| Beveiliging                                                         | BitLocker-stationsversleuteling is ingeschakeld voor harde schijven van het besturings systeem en gegevens harde schijven.                                                             |
| Beveiliging                                                         | Beperk de kwets baarheid door minimale footprint te behouden met alleen de benodigde Windows Server-functies,-onderdelen,-services en-netwerk poorten ingeschakeld.                         |
| Beveiliging                                                         | Scan de bron code en de resulterende VM-installatie kopie voor malware.                                                                                                                     |
| Beveiliging                                                         | Stel de beveiligings update voor Windows Server-installatie kopieën in op automatisch bijwerken.                                                                                                                |
| Beveiliging                                                         | De VHD-installatie kopie bevat alleen de benodigde vergrendelde accounts, die geen standaard wachtwoorden hebben waarmee interactieve aanmelding zou worden toegestaan. geen deuren terug.                             |
| Beveiliging                                                         | Firewall regels zijn uitgeschakeld, tenzij toepassingen functioneel afhankelijk zijn, zoals een firewall apparaat.                                                               |
| Beveiliging                                                         | Alle gevoelige informatie is verwijderd uit de VHD-installatie kopie. Zo moeten HOSTS bestand, logboek bestanden en overbodige certificaten worden verwijderd.                                              |
| Implementatie                                                       | 64-bits besturings systeem.                            |
