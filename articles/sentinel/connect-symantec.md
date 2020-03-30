---
title: Symantec ICDx-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Symantec ICDx-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588090"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Sluit uw Symantec ICDx-toestel aan 



Met de Symantec ICDx-connector u eenvoudig al uw logboeken van uw Symantec-beveiligingsoplossing verbinden met uw Azure Sentinel, dashboards bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Dit geeft u meer inzicht in het netwerk van uw organisatie en verbetert uw beveiligingsmogelijkheden. Integratie tussen Symantec ICDx en Azure Sentinel maakt gebruik van REST API.


> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-symantec-icdx"></a>Symantec ICDx configureren en verbinden 

Symantec ICDx kan logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Open de ICDx Management Console om Microsoft Azure Sentinel (Log Analytics) forwarders toe te voegen.
2. Klik op de ICDx-navigatiebalk op **Configuratie**. 
3. Klik boven aan het **configuratiescherm** op **Doorstuurers**.
4. Klik onder **Forwarders**naast Microsoft Azure Sentinel (Log Analytics) op **Toevoegen**. 
4. Klik in het venster **Microsoft Azure Sentinel (Log Analytics)** op **Geavanceerd weergeven**. 
5. Ga als volgt te werk boven aan het venster uitgebreid naar Microsoft Azure Sentinel (Log Analytics):
    -   **Naam:** Typ een naam voor de expediteer met niet meer dan 30 tekens. Kies een unieke, betekenisvolle naam. Deze naam wordt weergegeven in de lijst met doorstuurgers op het **configuratiescherm** en in de dashboards op het **dashboardscherm.** Bijvoorbeeld: Microsoft Azure Log Analytics Oost. Dit veld is vereist.
    -   **Beschrijving**: Typ een beschrijving voor de expediteer. Deze beschrijving wordt ook weergegeven in de lijst met doorstuurgers op het **configuratiescherm.** Neem details op, zoals het gebeurtenistype dat wordt doorgestuurd en de groep die de gegevens moet inspecteren.
    -   **Opstarttype:** selecteer de methode voor het opstarten voor de forwarder-configuratie. Uw opties zijn handmatig en automatisch.<br>De standaardinstelling is Automatisch. 
6. Ga als volgt te werk onder **Gebeurtenissen:** 
    - **Bron**: Selecteer een of meer archieven van waaruit gebeurtenissen kunnen worden doorgestuurd. U actieve verzamelarchieven (waaronder het Common Archive), verweesde verzamelarchieven (dat wil zeggen archieven voor de verzamelaars die u hebt verwijderd), ICDx-ontvangerarchieven of het systeemarchief selecteren. <br>De standaardinstelling is Common Archive.
      > [!NOTE]
      > ICDx ontvanger archieven worden apart vermeld, op naam. 
 
    - **Filter:** Voeg een filter toe dat de subset van gebeurtenissen opgeeft om door te sturen. Voer een van de volgende handelingen uit:
        - Als u een filtervoorwaarde wilt selecteren, klikt u op een type, kenmerk, operator en waarde. 
        - Controleer in het veld Filter de filtervoorwaarde. U het direct in het veld bewerken of indien nodig verwijderen.
        - Klik op EN of OF om toe te voegen aan de filterconditie.
        - U ook op Opgeslagen query's klikken om een opgeslagen query toe te passen.
    - **Opgenomen kenmerken:** Typ de door komma's afgebakende lijst met kenmerken die in de doorgestuurde gegevens moeten worden opgenomen. De opgenomen kenmerken hebben voorrang op uitgesloten kenmerken.
    - **Uitgesloten kenmerken:** Typ de door komma's afgebakende lijst met kenmerken die u wilt uitsluiten van de doorgestuurde gegevens.
    - **Batchgrootte:** selecteer het aantal gebeurtenissen dat per batch moet worden verzonden. Uw opties zijn 10, 50, 100, 500 en 1000.<br>De standaardwaarde is 100. 
    - **Beoordeellimiet:** selecteer de snelheid waarmee gebeurtenissen worden doorgestuurd, uitgedrukt in gebeurtenissen per seconde. Uw opties zijn Onbeperkt, 500, 1000, 5000, 10000. <br> De standaardinstelling is 5000. 
7. Ga als volgt te werk onder **Azure-bestemming:** 
    - **Werkruimte-id**: Plak de werkruimte-id van onderen. Dit veld is vereist.
    - **Primaire sleutel:** Plak de primaire sleutel van onderen. Dit veld is vereist.
    - **Aangepaste logboeknaam:** typ de aangepaste logboeknaam in de Werkruimte Microsoft Azure Portal Log Analytics waarnaar u gebeurtenissen gaat doorsturen. De standaardinstelling is SymantecICDx. Dit veld is vereist.
8. Klik *op Opslaan* om de forwarderconfiguratie te voltooien. 
9. Als u de doorstuurer wilt starten, klikt u onder **Opties**op **Meer** en **vervolgens op Start**.
10. Als u het relevante schema in Log Analytics wilt gebruiken voor de Symantec ICDx-gebeurtenissen, zoekt u **naar SymantecICDx_CL.**


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan meer dan 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Symantec ICDx verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.


