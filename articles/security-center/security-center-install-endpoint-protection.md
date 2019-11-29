---
title: Endpoint Protection-problemen beheren met Azure Security Center | Microsoft Docs
description: Meer informatie over het bewaken van de Security Center van uw Endpoint Protection en het oplossen van problemen die zich voordoen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: memildin
ms.openlocfilehash: 70b7f511c32013a5dbc9369a2dfba2b8cd5f9145
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558615"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Endpoint Protection-problemen met Azure Security Center beheren
Azure Security Center bewaakt de status van antimalware Protection en meldt dit onder de pagina met problemen met Endpoint Protection. Security Center markeert problemen, zoals gedetecteerde bedreigingen en onvoldoende beveiliging, waardoor uw virtuele machines (Vm's) en computers kwetsbaar zijn voor aanvallen op malware. Door de informatie onder **problemen met Endpoint Protection**te gebruiken, kunt u een plan identificeren om eventuele gevonden problemen op te lossen.

Security Center worden de volgende Endpoint Protection-problemen gerapporteerd:

- Endpoint Protection is niet geïnstalleerd op virtuele machines van Azure: er is geen ondersteunde antimalware-oplossing geïnstalleerd op deze Azure-Vm's.
- Endpoint Protection is niet geïnstalleerd op niet-Azure-computers: er is geen ondersteunde antimalware geïnstalleerd op deze niet-Azure-computers.
- Endpoint Protection-status:

  - De hand tekening is verouderd: er is een antimalware-oplossing geïnstalleerd op deze Vm's en computers, maar de oplossing heeft niet de nieuwste antimalware-hand tekeningen.
  - Geen real-time beveiliging: er is een antimalware-oplossing geïnstalleerd op deze Vm's en computers, maar deze is niet geconfigureerd voor realtime-beveiliging. De service is mogelijk uitgeschakeld of Security Center kan de status mogelijk niet verkrijgen omdat de oplossing niet wordt ondersteund. Zie [partner integratie](security-center-services.md#endpoint-supported) voor een lijst met ondersteunde oplossingen.
  - Niet rapporteren: er is een antimalware-oplossing geïnstalleerd, maar geen rapport gegevens.
  - Onbekend: er is een antimalware-oplossing geïnstalleerd, maar de status is onbekend of er wordt een onbekende fout gerapporteerd.

    > [!NOTE]
    > Zie [beveiligings oplossingen integreren](security-center-services.md#endpoint-supported) voor een lijst met beveiligings oplossingen voor Endpoint Protection die zijn geïntegreerd met Security Center.
    >
    >

## <a name="implement-the-recommendation"></a>De aanbeveling implementeren
Endpoint Protection-problemen worden als aanbeveling in Security Center gepresenteerd. Als uw omgeving kwetsbaar is voor aanvallen op malware, wordt deze aanbeveling weer gegeven onder **aanbevelingen** en onder **reken kracht**. Als u het **dash board Endpoint Protection-problemen**wilt zien, moet u de reken werk stroom volgen.

In dit voor beeld gebruiken we **reken kracht**.  We gaan kijken hoe u antimalware installeert op virtuele machines van Azure en op niet-Azure-computers.

## <a name="install-antimalware-on-azure-vms"></a>Antimalware installeren op virtuele Azure-machines

1. Selecteer **compute & apps** in het hoofd menu van Security Center of **overzicht**.

   ![Reken proces selecteren][1]

2. Onder **Compute**selecteert u **Endpoint Protection-problemen**. Het dash board **Endpoint Protection-problemen** wordt geopend.

   ![Endpoint Protection-problemen selecteren][2]

   De bovenkant van het dash board biedt:

   - Geïnstalleerde Endpoint Protection-providers: geeft een lijst van de verschillende providers die door Security Center worden geïdentificeerd.
   - Geïnstalleerde status van Endpoint Protection: toont de status van de Vm's en computers waarop een Endpoint Protection-oplossing is geïnstalleerd. In de grafiek ziet u het aantal Vm's en computers die in orde zijn en het aantal met onvoldoende beveiliging.
   - Gedetecteerde malware: toont het aantal Vm's en computers waarop Security Center gedetecteerde malware meldt.
   - Aangevallen computers: toont het aantal Vm's en computers waarop Security Center aanvallen door malware meldt.

   Onder aan het dash board bevindt zich een lijst met Endpoint Protection-problemen die de volgende informatie bevatten:  

   - **Totaal** : het aantal vm's en computers waarop het probleem invloed heeft.
   - Een balk die het aantal Vm's en computers die door het probleem worden beïnvloed, samenvoegt. De kleuren in de balk bepalen prioriteit:

      - Rood-hoge prioriteit en moet onmiddellijk worden opgelost
      - Oranje-gemiddelde prioriteit en moet zo snel mogelijk worden opgelost

3. Selecteer **Endpoint Protection is niet geïnstalleerd op virtuele machines van Azure**.

   ![Selecteer Endpoint Protection is niet geïnstalleerd op virtuele machines van Azure][3]

4. Onder **Endpoint Protection is niet geïnstalleerd op virtuele machines van Azure** , is dit een lijst met virtuele Azure-machines waarop geen anti-malware is geïnstalleerd.  U kunt ervoor kiezen om antimalware te installeren op alle virtuele machines in de lijst of door afzonderlijke Vm's te selecteren voor het installeren van antimalware door te klikken op de specifieke virtuele machine.
5. Selecteer onder **Endpoint Protection selecteren**de Endpoint Protection-oplossing die u wilt gebruiken. In dit voor beeld selecteert u **micro soft antimalware**.
6. Er wordt aanvullende informatie over de Endpoint Protection-oplossing weer gegeven. Selecteer **Maken**.

## <a name="install-antimalware-on-non-azure-computers"></a>Antimalware installeren op niet-Azure-computers

1. Ga terug naar **Endpoint Protection-problemen** en selecteer **Endpoint Protection is niet geïnstalleerd op niet-Azure-computers**.

   ![Selecteer Endpoint Protection is niet geïnstalleerd op niet-Azure-computers][4]

2. Onder **Endpoint Protection is niet geïnstalleerd op niet-Azure-computers**, selecteert u een werk ruimte. Een Azure Monitor Zoek query in Logboeken gefilterd op de werk ruimte wordt geopend en bevat een lijst met computers die malware missen. Selecteer een computer in de lijst voor meer informatie.

   ![Azure Monitor logboeken zoeken][5]

Er wordt een ander Zoek resultaat geopend met informatie die alleen voor die computer wordt gefilterd.

  ![Azure Monitor logboeken zoeken][6]

> [!NOTE]
> U kunt het beste Endpoint Protection inrichten voor alle Vm's en computers om virussen, spyware en andere schadelijke software te identificeren en verwijderen.
>
>

## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt uitgelegd hoe u de Security Center aanbeveling installeren Endpoint Protection kunt implementeren. Raadpleeg het volgende document voor meer informatie over het inschakelen van micro soft antimalware in Azure:

* [Micro soft antimalware voor Cloud Services en virtual machines](../security/fundamentals/antimalware.md) --meer informatie over het implementeren van micro soft antimalware.

Raadpleeg de volgende documenten voor meer informatie over Security Center:

* [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md) --meer informatie over het configureren van beveiligings beleid.
* [Aanbevelingen voor beveiliging in azure Security Center](security-center-recommendations.md) : Ontdek hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md) --meer informatie over het controleren van de status van uw Azure-resources.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Partneroplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
* [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/) : vind blog berichten over beveiliging en naleving van Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
