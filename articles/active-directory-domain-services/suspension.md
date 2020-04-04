---
title: Opgeschorte domeinen in Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over de verschillende statussen voor een door Azure AD DS beheerd domein en hoe u een opgeschort domein herstellen.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 42b26911c12b1e7c62444a6fb2ee68720b02a56b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654593"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>De statussen begrijpen en opgeschorte domeinen in Azure Active Directory Domain Services oplossen

Wanneer Azure Active Directory Domain Services (Azure AD DS) een beheerd domein gedurende een lange periode niet kan onderhouden, wordt het beheerde domein in een onderbroken status gezet. Als een beheerd domein vervolgens in een opgeschorte status blijft, wordt het automatisch verwijderd. Als u uw door Azure AD DS beheerde domein gezond wilt houden en opschorting wilt voorkomen, u eventuele waarschuwingen zo snel mogelijk oplossen.

In dit artikel wordt uitgelegd waarom beheerde domeinen worden opgeschort en hoe u een opgeschort domein herstellen.

## <a name="overview-of-managed-domain-states"></a>Overzicht van beheerde domeinstatussen

Gedurende de levenscyclus van een door Azure AD DS beheerd domein zijn er verschillende statussen die de status aangeven. Als het beheerde domein een probleem rapporteert, lost u snel de onderliggende oorzaak op om te voorkomen dat de status wordt gedegradeerd.

![De progressie van de status die een door Azure AD DS beheerd domein in de richting van opschorting neemt](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Een door Azure AD DS beheerd domein kan zich in een van de volgende statussen bevinden:

* [Wordt uitgevoerd](#running-state)
* [Heeft aandacht nodig](#needs-attention-state)
* [Onderbroken](#suspended-state)
* [Verwijderd](#deleted-state)

## <a name="running-state"></a>Lopende staat

Een door Azure AD DS beheerd domein dat correct is geconfigureerd en zonder problemen wordt uitgevoerd, bevindt zich in de *status Uitvoeren.* Dit is de gewenste status voor een beheerd domein.

### <a name="what-to-expect"></a>Wat te verwachten

* Het Azure-platform kan regelmatig de status van het beheerde domein controleren.
* Domeincontrollers voor het beheerde domein worden gepatcht en regelmatig bijgewerkt.
* Wijzigingen van Azure Active Directory worden regelmatig gesynchroniseerd met het beheerde domein.
* Er worden regelmatig back-ups gemaakt voor het beheerde domein.

## <a name="needs-attention-state"></a>Moet Aandacht staat

Een door Azure AD DS beheerd domein met een of meer problemen die moeten worden opgelost, bevindt zich in de *aandachtsstatus Behoeften.* Op de statuspagina voor het beheerde domein worden de waarschuwingen weergegeven en wordt aangegeven waar een probleem is. Sommige waarschuwingen zijn van voorbijgaande aard en worden automatisch opgelost door het Azure-platform. Voor andere waarschuwingen u het probleem oplossen door de opgegeven oplossingsstappen te volgen. Er is een kritieke waarschuwing, [open een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

Een voorbeeld van een waarschuwing is wanneer er een beperkende netwerkbeveiligingsgroep is. In deze configuratie kan het Azure-platform het beheerde domein mogelijk niet bijwerken en bewaken. Er wordt een waarschuwing gegenereerd en de status wordt gewijzigd in *de aandacht van de status*.

Zie [Problemen met waarschuwingen voor een door Azure AD DS beheerd domein oplossen voor][resolve-alerts]meer informatie.

### <a name="what-to-expect"></a>Wat te verwachten

Wanneer een door Azure AD DS beheerd domein zich in de status *Aandacht nodig heeft,* kan het Azure-platform mogelijk niet regelmatig gegevens controleren, patchen, bijwerken of back-ups maken. In sommige gevallen, zoals bij een ongeldige netwerkconfiguratie, zijn de domeincontrollers voor het beheerde domein mogelijk onbereikbaar.

* Het beheerde domein is in een ongezonde staat en de voortdurende statusbewaking kan stoppen totdat de waarschuwing is opgelost.
* Domeincontrollers voor het beheerde domein kunnen niet worden gepatcht of bijgewerkt.
* Wijzigingen van Azure Active Directory worden mogelijk niet gesynchroniseerd met het beheerde domein.
* Back-ups voor het beheerde domein worden mogelijk niet gemaakt.
* Als u niet-kritieke waarschuwingen oplost die van invloed zijn op het beheerde domein, moet de status Uitvoeren terugkeren naar de *status Uitvoeren.*
* Kritieke waarschuwingen worden geactiveerd voor configuratieproblemen waarbij het Azure-platform de domeincontrollers niet kan bereiken. Als deze kritieke waarschuwingen niet binnen 15 dagen zijn opgelost, wordt de *status Opgeschort* ingevoerd.

## <a name="suspended-state"></a>Opgeschorte status

Een door Azure AD DS beheerd domein voert de **status Opgeschort** in om een van de volgende redenen:

* Een of meer kritieke waarschuwingen zijn al 15 dagen niet opgelost.
    * Kritieke waarschuwingen kunnen worden veroorzaakt door een verkeerde configuratie die de toegang tot resources blokkeert die nodig zijn voor Azure AD DS. De waarschuwing [AADDS104: Netwerkfout][alert-nsg] is bijvoorbeeld al meer dan 15 dagen niet opgelost in het beheerde domein.
* Er is een factureringsprobleem met het Azure-abonnement of het Azure-abonnement is verlopen.

Beheerde domeinen worden opgeschort wanneer het Azure-platform het domein niet kan beheren, bewaken, patchen of een back-up kan maken. Een beheerd domein blijft 15 dagen in *een status opgeschort.* Als u de toegang tot het beheerde domein wilt behouden, lost u kritieke waarschuwingen onmiddellijk op.

### <a name="what-to-expect"></a>Wat te verwachten

Het volgende gedrag wordt ervaren wanneer een door Azure AD DS beheerd domein de *status Opgeschort* heeft:

* Domeincontrollers voor het beheerde domein zijn gedeprovisioneerd en zijn niet bereikbaar binnen het virtuele netwerk.
* Veilige LDAP-toegang tot het beheerde domein via het internet, indien ingeschakeld, werkt niet meer.
* Er zijn fouten in het authenticeren van het beheerde domein, inloggen op vm's die zijn verbonden met domeinen of verbinding maken via LDAP/LDAPS.
* Back-ups voor het beheerde domein worden niet meer gemaakt.
* Synchronisatie met Azure AD stopt.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Hoe weet u of uw beheerde domein is opgeschort?

U ziet een [waarschuwing][resolve-alerts] op de pagina Azure AD DS-status in de Azure-portal waarin wordt opgemerkt dat het domein is opgeschort. De status van het domein toont ook *Opgeschort*.

### <a name="restore-a-suspended-domain"></a>Een opgeschort domein herstellen

Voer de volgende stappen uit om de status van een door Azure AD DS beheerd domein in de *status Opgeschort te* herstellen:

1. Zoek en selecteer **domeinservices**in de Azure-portal.
1. Kies uw door Azure AD DS beheerde domein in de lijst, zoals *aaddscontoso.com*en selecteer **Gezondheid**.
1. Selecteer de waarschuwing, zoals *AADDS503* of *AADDS504,* afhankelijk van de oorzaak van de opschorting.
1. Kies de resolutiekoppeling die in de waarschuwing wordt gegeven en volg de stappen om deze op te lossen.

Een beheerd domein kan alleen worden hersteld tot de datum van de laatste back-up. De datum van uw laatste back-up wordt weergegeven op de pagina **Status** van het beheerde domein. Wijzigingen die zijn opgetreden na de laatste back-up, worden niet hersteld. Back-ups voor een beheerd domein worden maximaal 30 dagen bewaard. Back-ups die ouder zijn dan 30 dagen worden verwijderd.

Nadat u waarschuwingen hebt opgelost wanneer het *beheerde* domein zich in de status Opgeschort bevindt, [opent u een Azure-ondersteuningsverzoek][azure-support] om terug te keren naar een gezonde status. Als er minder dan 30 dagen een back-up is, kan Azure-ondersteuning het beheerde domein herstellen.

## <a name="deleted-state"></a>Verwijderde status

Als een door Azure AD DS beheerd domein gedurende 15 dagen in de *status Opgeschort* blijft, wordt het verwijderd. Dit proces is onherstelbaar.

### <a name="what-to-expect"></a>Wat te verwachten

Wanneer een door Azure AD DS beheerd domein de *status Verwijderd* invoert, wordt het volgende gedrag gezien:

* Alle bronnen en back-ups voor het beheerde domein worden verwijderd.
* U het beheerde domein niet herstellen en moet een vervangend beheerd domein maken om Azure AD DS opnieuw te gebruiken.
* Nadat het is verwijderd, worden er geen kosten in rekening gebracht voor het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

Als u uw door Azure AD DS beheerde domein gezond wilt houden en het risico dat het wordt opgeschort, wilt u meer informatie over het oplossen van [waarschuwingen voor uw beheerde domein.][resolve-alerts]

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
