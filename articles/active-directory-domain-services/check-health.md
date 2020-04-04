---
title: Controleer de status van Azure Active Directory Domain Services | Microsoft Documenten
description: Meer informatie over het controleren van de status van een Azure Active Directory Domain Services (Azure AD DS) beheerd domein en het begrijpen van statusberichten met behulp van de Azure-portal.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655641"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>De status van een beheerd Beheerd Azure Directory Domain Services-domein controleren

Azure Active Directory Domain Services (Azure AD DS) voert een aantal achtergrondtaken uit om het beheerde domein gezond en up-to-date te houden. Deze taken omvatten het maken van back-ups, het toepassen van beveiligingsupdates en het synchroniseren van gegevens uit Azure AD. Als er problemen zijn met het beheerde Azure AD DS-domein, kunnen deze taken mogelijk niet worden voltooid. Als u eventuele problemen wilt controleren en oplossen, u de status van een door Azure AD DS beheerd domein controleren met behulp van de Azure-portal.

In dit artikel ziet u hoe u de status van Azure AD DS-status weergeven en inzicht krijgt in de weergegeven informatie of waarschuwingen.

## <a name="view-the-health-status"></a>De status van de status weergeven

De status van een door Azure AD DS beheerd domein wordt weergegeven met de Azure-portal. Informatie over de laatste back-uptijd en synchronisatie met Azure AD kan worden gezien, samen met eventuele waarschuwingen die wijzen op een probleem met de status van het beheerde domein. Voer de volgende stappen uit om de status van een door Azure AD DS beheerd domein weer te geven:

1. Zoek en selecteer Azure AD Domain Services in de **Azure-portal.**
1. Selecteer uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer Aan de linkerkant van het Azure AD DS-bronvenster De optie **Status**. In de volgende voorbeeldschermafbeelding wordt een door Azure AD DS beheerd domein en de status van de laatste back-up en Azure AD-synchronisatie weergegeven:

    ![Overzicht van de status status van de status van de status van de status van de status van de status van de Status van de status van de status van de status van de status van de status van de status van de status van de status](./media/check-health/health-page.png)

De *laatst geëvalueerde* tijdstempel van de statuspagina geeft aan wanneer het door Azure AD DS beheerde domein voor het laatst is gecontroleerd. De status van een door Azure AD DS beheerd domein wordt elk uur geëvalueerd. Als u wijzigingen aanbrengt in een door Azure AD DS beheerd domein, wacht u tot de volgende evaluatiecyclus om de bijgewerkte status weer te geven.

De status rechtsboven geeft de algehele status van het beheerde Azure AD DS-domein aan. De statusfactoren alle bestaande waarschuwingen op uw domein. In de volgende tabel worden de beschikbare statusindicatoren beschreven:

| Status | Pictogram | Uitleg |
| --- | :----: | --- |
| In uitvoering | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Het door Azure AD DS beheerde domein wordt correct uitgevoerd en heeft geen kritieke of waarschuwingswaarschuwingen. Het domein kan informatieve waarschuwingen bevatten. |
| Aandacht nodig (waarschuwing) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Er zijn geen kritieke waarschuwingen op het beheerde Azure AD DS-domein, maar er zijn een of meer waarschuwingswaarschuwingen die moeten worden aangepakt. |
| Aandacht nodig (kritisch) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Er zijn een of meer kritieke waarschuwingen op het beheerde Azure AD DS-domein die moeten worden aangepakt. U ook waarschuwingen en/of informatieve waarschuwingen hebben. |
| Implementeren | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Het Azure AD DS-domein wordt geïmplementeerd. |

## <a name="understand-monitors-and-alerts"></a>Informatie over monitoren en waarschuwingen

De status van een door Azure AD DS beheerd domein toont twee soorten informatie : *monitors*en *waarschuwingen*. Monitors tonen de tijd dat de belangrijkste achtergrondtaken zijn voltooid. Waarschuwingen bieden informatie of suggesties om de stabiliteit van het beheerde domein te verbeteren.

### <a name="monitors"></a>Monitors

Monitoren zijn gebieden van een door Azure AD DS beheerd domein dat regelmatig wordt gecontroleerd. Als er actieve waarschuwingen zijn voor het beheerde Azure AD DS-domein, kan dit ertoe leiden dat een van de monitors een probleem meldt. Azure AD Domain Services heeft momenteel monitoren voor de volgende gebieden:

* Backup
* Synchronisatie met Azure AD

#### <a name="backup-monitor"></a>Back-upmonitor

De back-upmonitor controleert of automatische regelmatige back-ups van het beheerde Azure AD DS-domein succesvol worden uitgevoerd. In de volgende tabel wordt de beschikbare status van back-upmonitor beschreven:

| Detailwaarde | Uitleg |
| --- | --- |
| Nooit een back-up | Deze status is normaal voor nieuwe door Azure AD DS beheerde domeinen. De eerste back-up moet 24 uur nadat het beheerde Azure AD DS-beheerde domein is geïmplementeerd, worden gemaakt. Als deze status blijft bestaan, [opent u een Azure-ondersteuningsaanvraag][azure-support]. |
| Laatste back-up is genomen 1 tot 14 dagen geleden | Dit tijdsbereik is de verwachte status voor de back-upmonitor. Geautomatiseerde regelmatige back-ups moeten in deze periode optreden. |
| De laatste back-up is meer dan 14 dagen geleden genomen. | Een tijdspanne langer dan twee weken geeft aan dat er een probleem is met de geautomatiseerde reguliere back-ups. Actieve kritieke waarschuwingen kunnen voorkomen dat er een back-up van het door Azure AD DS beheerde domein wordt gemaakt. Los actieve waarschuwingen op voor het beheerde Azure AD DS-domein. Als de back-upmonitor de status niet bijwerkt om een recente back-up te melden, [opent u een Azure-ondersteuningsverzoek][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synchronisatie met Azure AD-monitor

Een door Azure AD DS beheerd domein synchroniseert regelmatig met Azure Active Directory. Het aantal gebruikers en groepsobjecten en het aantal wijzigingen dat is aangebracht in de Azure AD-map sinds de laatste synchronisatie, is van invloed op hoe lang het duurt om te synchroniseren. Als het door Azure AD DS beheerde domein voor het laatst is gesynchroniseerd, controleert u of u actieve waarschuwingen hebt ingeschakeld en oplost. Als de synchronisatiemonitor de status niet bijwerkt om een recente synchronisatie weer te geven nadat u actieve waarschuwingen hebt geadresseerd, [opent u een Azure-ondersteuningsverzoek][azure-support].

### <a name="alerts"></a>Waarschuwingen

Waarschuwingen worden gegenereerd voor problemen in een door Azure AD DS beheerd domein dat moet worden aangepakt om de service correct uit te voeren. Elke waarschuwing legt het probleem uit en geeft een URL die specifieke stappen schetst om het probleem op te lossen. Zie [Meldingen voor probleemoplossing voor](troubleshoot-alerts.md)meer informatie over de mogelijke waarschuwingen en hun oplossingen.

Waarschuwingen voor statusstatus worden gecategoriseerd in de volgende ernstniveaus:

 * **Kritieke waarschuwingen** zijn problemen die ernstige gevolgen hebben voor het beheerde Azure AD DS-domein. Deze waarschuwingen moeten onmiddellijk worden aangepakt. Het Azure-platform kan het beheerde domein niet controleren, beheren, patchen en synchroniseren totdat de problemen zijn opgelost.
 * **Waarschuwingswaarschuwingen** waarschuwen u voor problemen die van invloed kunnen zijn op de beheerde domeinbewerkingen van Azure AD DS als het probleem blijft bestaan. Deze waarschuwingen bieden ook aanbevelingen om het beheerde domein te beveiligen.
 * **Informatieve waarschuwingen** zijn meldingen die geen negatieve invloed hebben op het door Azure AD DS beheerde domein. Informatieve waarschuwingen geven enig inzicht in wat er gebeurt in het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

Zie [Waarschuwingen op uw beheerde domein oplossen voor][troubleshoot-alerts] meer informatie over waarschuwingen die worden weergegeven op de pagina statusstatus.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
