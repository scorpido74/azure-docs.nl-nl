---
title: Waarschuwingsvalidatie (EICAR-testbestand) in Azure Security Center | Microsoft Documenten
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
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139994"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Waarschuwingen valideren (EICAR-testbestand) in Azure Security Center
In dit document leest u hoe u kunt controleren of uw systeem op de juiste manier is geconfigureerd voor waarschuwingen van Azure Security Center.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?
Waarschuwingen zijn de meldingen die Security Center genereert wanneer het bedreigingen op uw resources detecteert. Het geeft prioriteit en geeft een overzicht van de waarschuwingen, samen met de informatie die nodig is om het probleem snel te onderzoeken. Security Center geeft ook aanbevelingen voor hoe u een aanval kunt oplossen.
Zie [Beveiligingswaarschuwingen in beveiligingscentrum](security-center-alerts-overview.md) beheren [en reageren op beveiligingswaarschuwingen](security-center-managing-and-responding-alerts.md) voor meer informatie

## <a name="alert-validation"></a>Waarschuwingen valideren

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Waarschuwingen valideren op Windows VM's<a name="validate-windows"></a>

Nadat de beveiligingsagent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt zijn:

1. Kopieer een uitvoerbare (bijvoorbeeld **calc.exe)** naar het bureaublad van de computer of een andere map van uw gemak en wijzig deze als **ASC_AlertTest_662jfi039N.exe**.
1. Open de opdrachtprompt en voer dit bestand uit met een argument (alleen een valse argumentnaam), zoals:```ASC_AlertTest_662jfi039N.exe -foo```
1. Wacht 5 tot 10 minuten en open Security Center. Een waarschuwing die lijkt op het onderstaande [voorbeeld](#alert-validate) moet worden weergegeven:

> [!NOTE]
> Controleer bij het controleren van deze testwaarschuwing voor Windows of het **veld Arguments Auditing Enabled** **true**is. Als dit **niet waar**is, moet u het controleren van opdrachtregelargumenten inschakelen. Als u dit wilt inschakelen, gebruikt u de volgende opdracht:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Waarschuwingen valideren op Linux VM's<a name="validate-linux"></a>

Nadat de beveiligingsagent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt zijn:
1. Kopieer een uitvoerbare naar een geschikte locatie en wijzig deze in **./asc_alerttest_662jfi039n**, bijvoorbeeld:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Open de opdrachtprompt en voer dit bestand uit:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Wacht 5 tot 10 minuten en open Security Center. Een waarschuwing die lijkt op het onderstaande [voorbeeld](#alert-validate) moet worden weergegeven:

### <a name="alert-example"></a>Waarschuwingsvoorbeeld<a name="alert-validate"></a>

![Voorbeeld van waarschuwingvalidatie](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Waarschuwingen op Kubernetes valideren<a name="validate-kubernetes"></a>

Als u de preview-functie Beveiligingscentrum van de integratie van Azure Kubernetes Service gebruikt, voert u de volgende opdracht kubectl uit om te testen of uw waarschuwingen werken:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Zie [dit artikel](azure-kubernetes-service-integration.md)voor meer informatie over de integratie van Azure Kubernetes Service en Azure Security Center.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebben we aandacht besteed aan het valideren van waarschuwingen. Raadpleeg de volgende artikelen als u meer over dit onderwerp wilt weten:

* [Azure Key Vault-bedreigingsdetectie valideren in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Beveiligingswaarschuwingen beheren en beantwoorden in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) - Meer informatie over het beheren van waarschuwingen en reageren op beveiligingsincidenten in het Beveiligingscentrum.
* [Beveiligingsstatusbewaking in Azure Security Center](security-center-monitoring.md) - Meer informatie over het bewaken van de status van uw Azure-resources.
* [Inzicht in beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) - Meer informatie over de verschillende soorten beveiligingswaarschuwingen.
