---
title: Private Cloud-activiteit controleren
titleSuffix: Azure VMware Solution by CloudSimple
description: Beschrijft de informatie die beschikbaar is over activiteit in de Azure VMware-oplossing per CloudSimple-omgeving, inclusief waarschuwingen, gebeurtenissen, taken en audit.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019669"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>VMware-oplossing controleren op cloudEenvoudige activiteit

CloudSimple-activiteitenlogboeken bieden inzicht in bewerkingen op de CloudSimple-portal.  De lijst bevat waarschuwingen, gebeurtenissen, taken en controle.  Gebruik de activiteitslogboeken om te bepalen wie, wanneer en welke bewerkingen zijn uitgevoerd.  Activiteitslogboeken bevatten geen leesbewerkingen die door een gebruiker worden uitgevoerd.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

Toegang tot de [CloudSimple-portal](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Activiteitsinformatie

Als u de activiteitenpagina's wilt openen, selecteert u **Activiteit** in het zijmenu.

![Overzicht van activiteitenpagina](media/activity-page-overview.png)

Als u details wilt weergeven over een van de activiteiten op de activiteitenpagina, selecteert u de activiteit. Aan de rechterkant wordt een detailpaneel geopend. Acties in het deelvenster zijn afhankelijk van het type activiteit. Klik op **X** om het deelvenster te sluiten.

Klik op een kolomkop om de weergave te sorteren.  U kolommen filteren op specifieke waarden die u wilt weergeven.  Download het activiteitenrapport door op **Download als CSV-pictogram** te klikken.

## <a name="alerts"></a>Waarschuwingen

Waarschuwingen zijn meldingen van significante activiteiten in uw CloudSimple-omgeving.  Waarschuwingen bevatten gebeurtenissen die van invloed zijn op facturering of gebruikerstoegang.

Als u waarschuwingen wilt bevestigen en uit de lijst wilt verwijderen, selecteert u een of meer in de lijst en klikt u op **Erkennen**.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **Kolommen bewerken** en selecteer kolommen die u wilt weergeven.

| Kolom | Beschrijving |
------------ | ------------- |
| Waarschuwingstype | Categorie van waarschuwing.|
| Time | Het tijdstip van de waarschuwing is opgetreden. |
| Severity | Betekenis van de waarschuwing.|
| Resourcenaam | Naam die aan de bron is toegewezen, zoals de naam Private Cloud. |
| Resourcetype | Categorie van de bron: Private Cloud, Cloud Rack. |
| Resource-id | Identificatie van de resource. |
| Beschrijving | Beschrijving van wat de waarschuwing heeft geactiveerd. |
| Bevestigd | Vermelding of de waarschuwing wordt erkend. |

## <a name="events"></a>Gebeurtenissen

Gebeurtenissen tonen gebruikers- en systeemactiviteit op de CloudSimple-portal. De pagina Gebeurtenissen geeft een overzicht van de activiteit die is gekoppeld aan een specifieke resource en de ernst van de impact.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **Kolommen bewerken** en selecteer kolommen die u wilt weergeven.

| Kolom | Beschrijving |
------------ | ------------- |
| Time | Datum en tijd van de gebeurtenis. |
| Gebeurtenistype | Numerieke code die de gebeurtenis identificeert. |
| Severity | Ernst van de gebeurtenis.|
| Resourcenaam | Naam die aan de bron is toegewezen, zoals de naam Private Cloud. |
| Resourcetype | Categorie van de bron: Private Cloud, Cloud Rack. |
| Beschrijving | Beschrijving van wat de waarschuwing heeft geactiveerd. |

## <a name="tasks"></a>Taken

Taken zijn Private Cloud-activiteiten die naar verwachting 30 seconden of langer in beslag zullen nemen. (Activiteiten die naar verwachting minder dan 30 seconden in beslag nemen, worden alleen gerapporteerd als gebeurtenissen.) Open de pagina's Taken om de voortgang van taken voor uw private cloud bij te houden.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **Kolommen bewerken** en selecteer kolommen die u wilt weergeven.

| Kolom | Beschrijving |
------------ | ------------- |
| Taak-id | Unieke id voor de taak. |
| Bewerking | Actie die de taak uitvoert. |
| Gebruiker | Gebruiker die is toegewezen om de taak te voltooien. |
| Resourcenaam | Naam toegewezen aan de resource. |
| Resourcetype | Categorie van de bron: Private Cloud, Cloud Rack. |
| Resource-id | Identificatie van de resource. |
| Starten | Begintijd voor de taak. |
| Beëindigen | Eindtijd voor de taak. |
| Status | Huidige taakstatus. |
| Verstreken tijd | Tijd die de taak heeft voltooid (indien voltooid) of momenteel in beslag neemt (als de uitvoering is). |
| Beschrijving | Taakbeschrijving. |

## <a name="audit"></a>Controleren

Controlelogboeken houden de activiteit van gebruikers bij. U controlelogboeken gebruiken om de gebruikersactiviteit voor alle gebruikers te controleren.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **Kolommen bewerken** en selecteer kolommen die u wilt weergeven.

| Kolom | Beschrijving |
------------ | ------------- |
| Time | Tijdstip van de controle-invoer. |
| Bewerking | Actie die de taak uitvoert. |
| Gebruiker | Gebruiker toegewezen aan de taak. |
| Resourcenaam | Naam toegewezen aan de resource. |
| Resourcetype | Categorie van de bron: Private Cloud, Cloud Rack. |
| Resource-id | Identificatie van de resource. |
| Resultaat | Resultaat van de activiteit, zoals **Succes**. |
| Gebruikte tijd | Tijd om de taak te voltooien. |
| Beschrijving | Beschrijving van de actie. |

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [Private Clouds](cloudsimple-private-cloud.md)
