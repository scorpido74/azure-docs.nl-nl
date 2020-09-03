---
title: 'Zelfstudie: De geprioriteerde verkeersrouteringsmethode configureren met Azure Traffic Manager'
description: In deze zelfstudie wordt uitgelegd hoe u de methode voor de routering van prioriteitsverkeer configureert in Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 404338c3e36216833d39c3551ae2dee0be304d24
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401007"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Zelfstudie: De geprioriteerde verkeersrouteringsmethode configureren in Traffic Manager

Azure Websites bieden al failover-functionaliteit voor websites in een datacentrum (ook wel een regio genoemd), ongeacht de websitemodus. Traffic Manager biedt failover voor websites in verschillende datacenters.

Een algemeen patroon voor failover van services is dat verkeer naar een primaire service wordt verzonden en een set identieke back-upservices voor failover wordt geboden. In de volgende stappen wordt uitgelegd hoe u deze failover met prioriteit configureert met Azure-cloudservices en websites:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Ga als volgt te werk om geprioriteerde verkeersrouteringsmethode te configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoekbalk van de portal naar de **Traffic Manager-profielen** en klik vervolgens op de profielnaam waarvoor u de routeringsmethode wilt configureren.
3. Verifieer in de blade van het **Traffic Manager-profiel** of zowel de cloudservices als websites die u in uw configuratie wilt opnemen aanwezig zijn.
4. Klik in de sectie **Instellingen** op **Configuratie** en voer in de blade **Configuratie** de volgende configuratie uit:
    1. Controleer voor **instellingen voor de methode voor verkeersroutering** of de methode voor verkeersroutering op **Prioriteit** is ingesteld. Klik op **Prioriteit** in de vervolgkeuzelijst als dat niet het geval is.
    2. Stel de **Controle-instellingen voor het eindpunt** voor alle eindpunten in dit profiel hetzelfde in en doe dit als volgt:
        1. Selecteer het juiste **Protocol** en geef het **Poortnummer** op. 
        2. Typ bij **Pad** een slash */* . Als u eindpunten wilt controleren, moet u een pad en bestandsnaam opgeven. Een slash (/) is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap bevindt (standaard).
        3. Klik bovenaan de pagina op **Opslaan**.
5. Klik in de sectie **Instellingen** op **Eindpunten**.
6. Controleer op de blade **Eindpunten** de volgorde van prioriteit voor uw eindpunten. Als u **Prioriteit** als methode voor verkeersroutering hebt geselecteerd, is de volgorde van de geselecteerde eindpunten van belang. Controleer de prioriteitsvolgorde van eindpunten.  Het primaire eindpunt staat bovenaan. Controleer de volgorde waarin deze wordt weergegeven. alle aanvragen worden doorgestuurd naar het eerste eindpunt. Als Traffic Manager detecteert dat dit eindpunt een slechte status heeft, wordt automatisch een failover uitgevoerd naar het volgende eindpunt. 
7. U kunt de volgorde van de prioriteit van het eindpunt wijzigen door op het eindpunt te klikken en in de blade **Eindpunt** die wordt weergegeven, **Bewerken** te selecteren en zo nodig de waarde voor **Prioriteit** te wijzigen. 
8. Klik op **Opslaan** om de instellingen voor het eindpunt op te slaan.
9. Klik na het voltooien van uw configuratiewijzigingen op **Opslaan** onderaan de pagina.
10. Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoekbalk van de portal naar de naam van het Traffic Manager-profiel en klik in de weergegeven resultaten op het Traffic Manager-profiel.
    2.  Klik in de blade met het **Traffic Manager**-profiel op **Overzicht**.
    3.  De blade **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Dit kan door clients worden gebruikt (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eindpunt te gaan, zoals wordt bepaald door het routeringstype. In dit geval worden alle aanvragen doorgestuurd naar het eerste eindpunt. Als Traffic Manager detecteert dat dit eindpunt een slechte status heeft, wordt automatisch een failover uitgevoerd naar het volgende eindpunt.
11. Als uw Traffic Manager-profiel werkt, kunt u de DNS-record op uw gezaghebbende DNS-server zo bewerken dat de domeinnaam van uw bedrijf verwijst naar de Traffic Manager-domeinnaam.

![De geprioriteerde verkeersrouteringsmethode configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen


- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [routeringsmethode op basis van prestaties](traffic-manager-configure-performance-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het [testen van Traffic Manager-instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png