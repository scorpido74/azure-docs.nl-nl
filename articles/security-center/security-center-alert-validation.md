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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 10ea15168d61d5e73aff976ef641e07b6327dbca
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604567"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Waarschuwings validatie (EICAR-test bestand) in Azure Security Center
In dit document leest u hoe u kunt controleren of uw systeem op de juiste manier is geconfigureerd voor waarschuwingen van Azure Security Center.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?
Waarschuwingen zijn de meldingen die Security Center gegenereerd wanneer er bedreigingen voor uw resources worden gedetecteerd. Er worden prioriteiten gegeven en de waarschuwingen worden vermeld samen met de informatie die nodig is om snel het probleem te onderzoeken. Security Center biedt ook aanbevelingen voor het oplossen van een aanval.
Zie [beveiligings waarschuwingen in Security Center](security-center-alerts-overview.md) en [beheren en reageren op beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md) voor meer informatie.

## <a name="alert-validation"></a>Waarschuwingen valideren

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## Waarschuwingen op Windows-Vm's valideren<a name="validate-windows"></a>

Nadat Security Center agent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt ontvangen:

1. Kopieer een uitvoerbaar bestand (bijvoorbeeld **calc. exe**) naar het bureau blad van de computer of een andere map met uw gemak, en wijzig de naam van de server als **ASC_AlertTest_662jfi039N. exe**.
1. Open de opdracht prompt en voer dit bestand uit met een argument (alleen een valse argument naam), zoals: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Wacht 5 tot 10 minuten en open Security Center. Er moet een waarschuwing worden weer gegeven die vergelijkbaar is met het [voor beeld](#alert-validate) hieronder:

> [!NOTE]
> Wanneer u deze test waarschuwing voor Windows bekijkt **, moet u**controleren of de controle van de veld **argumenten is ingeschakeld** . Als deze **False**is, moet u controle van opdracht regel argumenten inschakelen. Gebruik de volgende opdracht om deze functie in te scha kelen:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Waarschuwingen valideren op virtuele Linux-machines<a name="validate-linux"></a>

Nadat Security Center agent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt ontvangen:
1. Kopieer een uitvoerbaar bestand naar een handige locatie en wijzig de naam in **./asc_alerttest_662jfi039n**, bijvoorbeeld:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Open de opdracht prompt en voer dit bestand uit:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Wacht 5 tot 10 minuten en open Security Center. Er moet een waarschuwing worden weer gegeven die vergelijkbaar is met het [voor beeld](#alert-validate) hieronder:

### Voor beeld van waarschuwing<a name="alert-validate"></a>

![Voor beeld van waarschuwings validatie](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## Waarschuwingen valideren op Kubernetes<a name="validate-kubernetes"></a>

Als u de preview-functie Security Center van het integreren van de Azure Kubernetes-service gebruikt, voert u de volgende kubectl-opdracht uit om te testen of uw waarschuwingen werken:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Raadpleeg [dit artikel](azure-kubernetes-service-integration.md)voor meer informatie over de integratie van de Azure Kubernetes-Service en Azure Security Center.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebben we aandacht besteed aan het valideren van waarschuwingen. Raadpleeg de volgende artikelen als u meer over dit onderwerp wilt weten:

* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) -meer informatie over het beheren van waarschuwingen en het reageren op beveiligings incidenten in Security Center.
* [Beveiligings status bewaken in azure Security Center](security-center-monitoring.md) -meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligings waarschuwingen in azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) -meer informatie over de verschillende typen beveiligings waarschuwingen.
* [Azure Security Center probleemoplossings handleiding](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) : informatie over het oplossen van veelvoorkomende problemen in Security Center.
* [Blog over Azure-beveiliging](https://blogs.msdn.com/b/azuresecurity/) : vind blog berichten over beveiliging en naleving van Azure.
