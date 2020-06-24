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
ms.openlocfilehash: cf732b92c1a208dd4c312ae442969ef958a021b4
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791177"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Waarschuwingen valideren (EICAR-testbestand) in Azure Security Center
In dit document leest u hoe u kunt controleren of uw systeem op de juiste manier is geconfigureerd voor waarschuwingen van Azure Security Center.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?
Waarschuwingen zijn de meldingen die Security Center genereert wanneer het bedreigingen op uw resources detecteert. Er worden prioriteiten gegeven en de waarschuwingen worden vermeld samen met de informatie die nodig is om snel het probleem te onderzoeken. Security Center geeft ook aanbevelingen voor hoe u een aanval kunt oplossen.
Zie [beveiligings waarschuwingen in Security Center](security-center-alerts-overview.md) en [beheren en reageren op beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md) voor meer informatie.

## <a name="alert-validation"></a>Waarschuwingen valideren

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Waarschuwingen op Windows-Vm's valideren<a name="validate-windows"></a>

Nadat Security Center agent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt ontvangen:

1. Kopieer een uitvoerbaar bestand (bijvoorbeeld **calc.exe**) naar het bureau blad van de computer of een andere map met uw gemak en wijzig de naam ervan in **ASC_AlertTest_662jfi039N.exe**.
1. Open de opdracht prompt en voer dit bestand uit met een argument (alleen een valse argument naam), zoals:```ASC_AlertTest_662jfi039N.exe -foo```
1. Wacht 5 tot 10 minuten en open Security Center. Er moet een waarschuwing worden weer gegeven.

> [!NOTE]
> Wanneer u deze test waarschuwing voor Windows bekijkt **, moet u**controleren of de controle van de veld **argumenten is ingeschakeld** . Als deze **False**is, moet u controle van opdracht regel argumenten inschakelen. Gebruik de volgende opdracht om deze functie in te scha kelen:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Waarschuwingen valideren op virtuele Linux-machines<a name="validate-linux"></a>

Nadat Security Center agent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt ontvangen:
1. Kopieer een uitvoerbaar bestand naar een handige locatie en wijzig de naam in **./asc_alerttest_662jfi039n**, bijvoorbeeld:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Open de opdracht prompt en voer dit bestand uit:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Wacht 5 tot 10 minuten en open Security Center. Er moet een waarschuwing worden weer gegeven.


## <a name="validate-alerts-on-kubernetes"></a>Waarschuwingen valideren op Kubernetes<a name="validate-kubernetes"></a>

Als u de preview-functie Security Center van het integreren van de Azure Kubernetes-service gebruikt, voert u de volgende kubectl-opdracht uit om te testen of uw waarschuwingen werken:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Raadpleeg [dit artikel](azure-kubernetes-service-integration.md)voor meer informatie over de integratie van de Azure Kubernetes-Service en Azure Security Center.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebben we aandacht besteed aan het valideren van waarschuwingen. Raadpleeg de volgende artikelen als u meer over dit onderwerp wilt weten:

* [Azure Key Vault detectie van dreigingen in Azure Security Center valideren](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) -meer informatie over het beheren van waarschuwingen en het reageren op beveiligings incidenten in Security Center.
* [Beveiligings status bewaken in azure Security Center](security-center-monitoring.md) -meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligings waarschuwingen in azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) -meer informatie over de verschillende typen beveiligings waarschuwingen.
