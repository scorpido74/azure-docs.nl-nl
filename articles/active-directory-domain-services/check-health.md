---
title: De status van de Azure Active Directory Domain Services controleren | Microsoft Docs
description: Meer informatie over het controleren van de status van een beheerd domein van Azure Active Directory Domain Services (Azure AD DS) en het begrijpen van status berichten met behulp van de Azure Portal.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: fa1cba2d791cd40a46f8ad182c123a726143faec
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614252"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>De status controleren van een door Azure Active Directory Domain Services beheerd domein

Azure Active Directory Domain Services (Azure AD DS) voert enkele achtergrond taken uit om het beheerde domein in orde en up-to-date te houden. Deze taken omvatten het maken van back-ups, het Toep assen van beveiligings updates en het synchroniseren van gegevens vanuit Azure AD. Als er problemen zijn met het door Azure AD DS beheerde domein, worden deze taken mogelijk niet voltooid. Als u problemen wilt controleren en oplossen, kunt u de status van een door Azure AD DS beheerd domein controleren met behulp van de Azure Portal.

In dit artikel leest u hoe u de status van Azure AD DS kunt bekijken en hoe u de informatie of waarschuwingen kunt zien.

## <a name="view-the-health-status"></a>De status weer geven

De status van een beheerd domein van Azure AD DS wordt weer gegeven met behulp van de Azure Portal. Informatie over de tijd van de laatste back-up en synchronisatie met Azure AD kan worden weer gegeven, samen met eventuele waarschuwingen die duiden op een probleem met de status van het beheerde domein. Voer de volgende stappen uit om de status van een beheerd domein van Azure AD DS te bekijken:

1. Zoek in het Azure Portal naar en selecteer **Azure AD Domain Services**.
1. Selecteer uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer aan de linkerkant van het venster Azure AD DS resource de optie **status**. De volgende voorbeeld scherm afbeelding toont een gezonde Azure AD DS beheerde domein en de status van de laatste back-up en Azure AD-synchronisatie:

    ![Overzicht van status pagina's in de Azure Portal de Azure Active Directory Domain Services status weer geven](./media/check-health/health-page.png)

De *laatste geëvalueerde* tijds tempel van de status pagina wordt weer gegeven wanneer de Azure AD DS beheerde domein de laatste keer is gecontroleerd. De status van een beheerd domein van Azure AD DS wordt elk uur geëvalueerd. Als u wijzigingen aanbrengt in een door Azure AD DS beheerd domein, wacht u tot de volgende evaluatie cyclus de bijgewerkte status weergeeft.

Met de status in de rechter bovenhoek wordt de algemene status van het beheerde domein van Azure AD DS aangeduid. Met de status worden alle bestaande waarschuwingen in uw domein gemeten. De volgende tabel bevat een overzicht van de beschik bare status indicatoren:

| Status | Pictogram | Uitleg |
| --- | :----: | --- |
| In uitvoering | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Het beheerde domein van Azure AD DS wordt correct uitgevoerd en heeft geen kritieke of waarschuwings meldingen. Het domein heeft mogelijk informatieve waarschuwingen. |
| Aandacht vereist (waarschuwing) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Er zijn geen kritieke waarschuwingen op het beheerde domein van Azure AD DS, maar er zijn een of meer waarschuwingen die moeten worden behandeld. |
| Aandacht vereist (kritiek) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Er zijn een of meer kritieke waarschuwingen op de Azure AD DS beheerde domein die moeten worden geadresseerd. Mogelijk hebt u ook waarschuwings-en/of informatieve waarschuwingen. |
| Implementeren | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Het Azure AD DS domein wordt geïmplementeerd. |

## <a name="understand-monitors-and-alerts"></a>Monitors en waarschuwingen begrijpen

Met de status voor een beheerd domein van Azure AD DS worden twee soorten informatie weer gegeven: *monitors*en *waarschuwingen*. Monitors tonen het tijdstip waarop de basis achtergrond taken zijn voltooid. Waarschuwingen bieden informatie of suggesties voor het verbeteren van de stabiliteit van het beheerde domein.

### <a name="monitors"></a>Monitors

Monitors zijn gebieden van een Azure AD DS beheerde domein die regel matig worden gecontroleerd. Als er actieve waarschuwingen zijn voor het beheerde domein van Azure AD DS, kan dit ertoe leiden dat een van de monitors een probleem meldt. Azure AD Domain Services heeft momenteel monitors voor de volgende gebieden:

* Back-up maken
* Synchronisatie met Azure AD

#### <a name="backup-monitor"></a>Back-upmonitor

De back-upmonitor controleert of geautomatiseerde reguliere back-ups van het beheerde Azure AD DS-domein met succes worden uitgevoerd. De volgende tabel bevat een overzicht van de beschik bare status van back-upmonitor:

| Detail waarde | Uitleg |
| --- | --- |
| Geen back-up maken | Deze status is normaal voor nieuwe, door Azure AD DS beheerde domeinen. De eerste back-up moet 24 uur na de implementatie van het beheerde domein van Azure AD DS worden gemaakt. Als deze status zich blijft voordoen, [opent u een ondersteunings aanvraag voor Azure][azure-support]. |
| De laatste back-up is 1 tot 14 dagen geleden genomen | Dit tijds bereik is de verwachte status voor de back-upmonitor. Automatische reguliere back-ups moeten in deze periode optreden. |
| De laatste back-up is meer dan 14 dagen geleden gemaakt. | Een time span die langer is dan twee weken geeft aan dat er een probleem is met de automatische reguliere back-ups. Actieve kritieke waarschuwingen kunnen verhinderen dat er een back-up wordt gemaakt van het beheerde Azure AD DS-domein. Los eventuele actieve waarschuwingen voor het beheerde domein van Azure AD DS op. Als de back-upcontrole de status niet bijwerkt om een recente back-up te melden, [opent u een ondersteunings aanvraag voor Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synchronisatie met Azure AD monitor

Een door Azure AD DS beheerd domein wordt regel matig gesynchroniseerd met Azure Active Directory. Het aantal gebruikers en groeps objecten, en het aantal wijzigingen dat sinds de laatste synchronisatie in de Azure AD-adres lijst is aangebracht, is van invloed op hoe lang het duurt om te synchroniseren. Als de Azure AD DS Managed domain voor het laatst drie dagen geleden is gesynchroniseerd, moet u alle actieve waarschuwingen controleren en oplossen. Als de synchronisatie monitor de status niet bijwerkt om een recente synchronisatie weer te geven nadat u actieve waarschuwingen hebt geadresseerd, [opent u een ondersteunings aanvraag voor Azure][azure-support].

### <a name="alerts"></a>Waarschuwingen

Er worden waarschuwingen gegenereerd voor problemen in een Azure AD DS beheerde domein dat moet worden geadresseerd om de service goed uit te voeren. Elke waarschuwing legt het probleem uit en geeft een URL die specifieke stappen bevat om het probleem op te lossen. Zie [probleemoplossings waarschuwingen](troubleshoot-alerts.md)voor meer informatie over de mogelijke waarschuwingen en hun oplossingen.

Waarschuwingen voor de status worden gecategoriseerd in de volgende Ernst niveaus:

 * **Kritieke waarschuwingen** zijn problemen die een ernstige invloed hebben op het door Azure AD DS beheerde domein. Deze waarschuwingen moeten onmiddellijk worden opgelost. Het Azure-platform kan het beheerde domein pas controleren, beheren, bijwerken en synchroniseren als de problemen zijn opgelost.
 * **Waarschuwingen** worden weer gegeven over problemen die van invloed kunnen zijn op de bewerkingen van het Azure AD DS beheerde domein als het probleem zich blijft voordoen. Deze waarschuwingen bieden ook aanbevelingen voor het beveiligen van het beheerde domein.
 * **Informatieve waarschuwingen** zijn meldingen die geen negatieve invloed hebben op het door Azure AD DS beheerde domein. Informatieve waarschuwingen bieden inzicht in wat er gebeurt in het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen op uw beheerde domein oplossen][troubleshoot-alerts] voor meer informatie over waarschuwingen die worden weer gegeven op de pagina status status.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
