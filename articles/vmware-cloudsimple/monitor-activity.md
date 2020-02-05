---
title: Azure VMware-oplossingen (AVS)-activiteit van de persoonlijke cloud van AVS bewaken
description: Hierin wordt de informatie beschreven die beschikbaar is voor activiteiten in de Azure VMware-oplossing per AVS-omgeving, inclusief waarschuwingen, gebeurtenissen, taken en controle.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019669"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>De AVS-activiteit (VMware Solutions) bewaken

AVS-activiteiten logboeken bieden inzicht in bewerkingen die worden uitgevoerd op de AVS-Portal. De lijst bevat waarschuwingen, gebeurtenissen, taken en controle. Gebruik de activiteiten Logboeken om te bepalen wie, wanneer en welke bewerkingen zijn uitgevoerd. De activiteiten logboeken bevatten geen lees bewerkingen die door een gebruiker worden uitgevoerd.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Toegang tot de AVS-Portal

Toegang tot de [AVS-Portal](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Activiteit gegevens

Selecteer **Activity** in het menu aan de zijkant om toegang te krijgen tot de activiteiten pagina's.

![Overzicht van de activiteit pagina](media/activity-page-overview.png)

Als u details over een van de activiteiten op de pagina activiteit wilt weer geven, selecteert u de activiteit. Aan de rechter kant wordt een detail venster geopend. De acties in het deel venster zijn afhankelijk van het type activiteit. Klik op **X** om het deel venster te sluiten.

Klik op de kolomkop om de weer gave te sorteren. U kunt kolommen filteren op specifieke waarden om weer te geven. Down load het activiteiten rapport door te klikken op **downloaden als CSV** -pictogram.

## <a name="alerts"></a>Waarschuwingen

Waarschuwingen zijn meldingen van belang rijke activiteiten in uw AVS-omgeving. Waarschuwingen bevatten gebeurtenissen die van invloed zijn op facturering of gebruikers toegang.

Als u waarschuwingen wilt bevestigen en wilt verwijderen uit de lijst, selecteert u een of meer in de lijst en klikt u op **bevestigen**.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **kolommen bewerken** en selecteer de kolommen die u wilt weer geven.

| Kolom | Beschrijving |
------------ | ------------- |
| Waarschuwings type | Categorie van de waarschuwing.|
| Tijd | Tijdstip waarop de waarschuwing is opgetreden. |
| Ernst | Significantie van de waarschuwing.|
| Naam resource | De naam die is toegewezen aan de resource, zoals de naam van de Privécloud van de Cloud. |
| Resourcetype | Categorie van resource: AVS Privécloud, Cloud rack. |
| Resource-id | De id van de resource. |
| Beschrijving | Beschrijving van de trigger van de waarschuwing. |
| Bevestigd | Indicatie of de waarschuwing wordt bevestigd. |

## <a name="events"></a>Evenements

Gebeurtenissen tonen gebruikers-en systeem activiteiten in de AVS-Portal. Op de pagina gebeurtenissen wordt een lijst weer gegeven met de activiteit die is gekoppeld aan een specifieke resource en de ernst van de impact.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **kolommen bewerken** en selecteer de kolommen die u wilt weer geven.

| Kolom | Beschrijving |
------------ | ------------- |
| Tijd | De datum en tijd waarop de gebeurtenis heeft plaatsgevonden. |
| Gebeurtenis type | Numerieke code waarmee de gebeurtenis wordt aangeduid. |
| Ernst | Ernst van gebeurtenis.|
| Naam resource | De naam die is toegewezen aan de resource, zoals de naam van de Privécloud van de Cloud. |
| Resourcetype | Categorie van resource: AVS Privécloud, Cloud rack. |
| Beschrijving | Beschrijving van de trigger van de waarschuwing. |

## <a name="tasks"></a>Taken

Taken zijn geavse persoonlijke Cloud activiteiten waarvan wordt verwacht dat deze 30 seconden of langer duren. (Activiteiten die naar verwachting minder dan 30 seconden duren, worden alleen gerapporteerd als gebeurtenissen.) Open de pagina taken om de voortgang van taken voor de Privécloud van uw AVS bij te houden.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **kolommen bewerken** en selecteer de kolommen die u wilt weer geven.

| Kolom | Beschrijving |
------------ | ------------- |
| Taak-ID | De unieke id voor de taak. |
| Bewerking | De actie die door de taak wordt uitgevoerd. |
| Gebruiker | De gebruiker is toegewezen om de taak te volt ooien. |
| Naam resource | De naam die is toegewezen aan de resource. |
| Resourcetype | Categorie van resource: AVS Privécloud, Cloud rack. |
| Resource-id | De id van de resource. |
| Beginnen | Begin tijd voor de taak. |
| Beëindigen | De eind tijd voor de taak. |
| Status | Huidige taak status. |
| Verstreken tijd | De tijd die nodig is om de taak te volt ooien (indien voltooid) of op dit moment (als deze wordt uitgevoerd). |
| Beschrijving | Taak beschrijving. |

## <a name="audit"></a>Controleren

Controle logboeken houden de activiteiten van gebruikers bij. U kunt controle Logboeken gebruiken om de gebruikers activiteit voor alle gebruikers te bewaken.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **kolommen bewerken** en selecteer de kolommen die u wilt weer geven.

| Kolom | Beschrijving |
------------ | ------------- |
| Tijd | Tijd van de controle vermelding. |
| Bewerking | De actie die door de taak wordt uitgevoerd. |
| Gebruiker | De gebruiker is toegewezen aan de taak. |
| Naam resource | De naam die is toegewezen aan de resource. |
| Resourcetype | Categorie van resource: AVS Privécloud, Cloud rack. |
| Resource-id | De id van de resource. |
| Resultaat | Resultaat van de activiteit, zoals **geslaagd**. |
| Gebruikte tijd | Tijd om de taak te volt ooien. |
| Beschrijving | Beschrijving van de actie. |

## <a name="next-steps"></a>Volgende stappen

* [VMware-Vm's in azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [persoonlijke Clouds van AVS](cloudsimple-private-cloud.md)
