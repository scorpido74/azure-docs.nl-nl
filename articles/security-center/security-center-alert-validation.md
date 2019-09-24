---
title: Waarschuwings validatie (EICAR-test bestand) in Azure Security Center | Microsoft Docs
description: In dit document leest u hoe u de beveiligingswaarschuwingen in Azure Security Center valideert.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 32f67fb94b207735e77583a6db62f7c8703dd991
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202736"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Waarschuwings validatie (EICAR-test bestand) in Azure Security Center
In dit document leest u hoe u kunt controleren of uw systeem op de juiste manier is geconfigureerd voor waarschuwingen van Azure Security Center.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?
Waarschuwingen zijn de meldingen die Security Center gegenereerd wanneer er bedreigingen voor uw resources worden gedetecteerd. Er worden prioriteiten gegeven en de waarschuwingen worden vermeld samen met de informatie die u nodig hebt om snel het probleem te onderzoeken. Security Center biedt ook aanbevelingen voor het oplossen van een aanval.
Zie [beveiligings waarschuwingen in azure Security Center](security-center-alerts-overview.md) en [beheren en reageren op beveiligings waarschuwingen in azure Security Center](security-center-managing-and-responding-alerts.md) voor meer informatie.

## <a name="alert-validation"></a>Waarschuwingen valideren

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Waarschuwing valideren op Windows VM<a name="validate-windows"></a>

Nadat Security Center agent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt ontvangen:

1. Kopieer een uitvoerbaar bestand (bijvoorbeeld **calc. exe**) naar het bureau blad van de computer of een andere map met uw gemak en wijzig de naam ervan in **ASC_AlertTest_662jfi039N. exe**.
1. Open de opdracht prompt en voer dit bestand uit met een argument (alleen een valse argument naam), zoals:```ASC_AlertTest_662jfi039N.exe -foo```
1. Wacht 5 tot 10 minuten en open Security Center. Er moet een waarschuwing worden weer gegeven die vergelijkbaar is met het [voor beeld](#alert-validate) hieronder:

> [!NOTE]
> Wanneer u deze test waarschuwing voor Windows bekijkt **, moet u**controleren of de controle van de veld **argumenten is ingeschakeld** . Als deze **False**is, moet u controle van opdracht regel argumenten inschakelen. Als u deze wilt inschakelen, gebruikt u de volgende opdracht regel:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Waarschuwing op virtuele Linux-machine valideren<a name="validate-linux"></a>

Nadat Security Center agent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt ontvangen:
1. Kopieer een uitvoerbaar bestand naar een handige locatie en wijzig de naam in **./asc_alerttest_662jfi039n**, bijvoorbeeld:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Open de opdracht prompt en voer dit bestand uit:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Wacht 5 tot 10 minuten en open Security Center. Er moet een waarschuwing worden weer gegeven die vergelijkbaar is met het [voor beeld](#alert-validate) hieronder:

### Voor beeld van waarschuwing<a name="alert-validate"></a>

![Voor beeld van waarschuwings validatie](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Zie ook
In dit artikel hebben we aandacht besteed aan het valideren van waarschuwingen. Raadpleeg de volgende artikelen als u meer over dit onderwerp wilt weten:

* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Handleiding voor het oplossen van problemen met Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informatie over het oplossen van veelvoorkomende problemen met Security Center.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md). Raadpleeg de veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
