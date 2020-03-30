---
title: Waarschuwingen onderzoeken met Azure Sentinel| Microsoft Documenten
description: Gebruik deze zelfstudie voor meer informatie over het onderzoeken van waarschuwingen met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585200"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Zelfstudie: Bedreigingen out-of-the-box detecteren


> [!IMPORTANT]
> Out-of-the-box bedreigingdetectie is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Nadat u [uw gegevensbronnen](quickstart-onboard.md) hebt verbonden met Azure Sentinel, wilt u een melding ontvangen wanneer er iets verdachts gebeurt. Om u in staat te stellen dit te doen, biedt Azure Sentinel u kant-en-klare ingebouwde sjablonen. Deze sjablonen zijn ontworpen door het team van beveiligingsexperts en analisten van Microsoft op basis van bekende bedreigingen, veelvoorkomende aanvalsvectoren en verdachte escalatieketens voor activiteiten. Nadat deze sjablonen zijn ingemaakt, wordt automatisch gezocht naar activiteiten die er in uw omgeving verdacht uitzien. Veel van de sjablonen kunnen worden aangepast om activiteiten te zoeken of uit te filteren, afhankelijk van uw behoeften. De waarschuwingen die door deze sjablonen worden gegenereerd, leiden tot incidenten die u in uw omgeving toewijzen en onderzoeken.

Met deze zelfstudie u bedreigingen detecteren met Azure Sentinel:

> [!div class="checklist"]
> * Out-of-the-box detecties gebruiken
> * Dreigingsreacties automatiseren

## <a name="about-out-of-the-box-detections"></a>Over standaarddetectie

Als u alle kant-en-klare detecties wilt weergeven, gaat u naar **Analytics** en **vervolgens regelsjablonen**. Dit tabblad bevat alle ingebouwde Azure Sentinel-regels.

   ![Gebruik ingebouwde detecties om bedreigingen te vinden met Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

De volgende sjabloontypen zijn beschikbaar:

- **Microsoft security** - Microsoft security templates automatisch maken Azure Sentinel incidenten van de waarschuwingen gegenereerd in andere Microsoft beveiligingsoplossingen, in real time. U Microsoft-beveiligingsregels als sjabloon gebruiken om nieuwe regels met vergelijkbare logica te maken. Zie Incidenten automatisch maken [op uit beveiligingswaarschuwingen van Microsoft](create-incidents-from-alerts.md)voor meer informatie over beveiligingsregels.
- **Fusion** - Op basis van Fusion-technologie maakt geavanceerde multistage-aanvalsdetectie in Azure Sentinel gebruik van schaalbare machine learning-algoritmen die veel waarschuwingen en gebeurtenissen met lage betrouwbaarheid in meerdere producten kunnen correleren in hifi-aanvallen en bruikbare incidenten. Fusion is standaard ingeschakeld. Omdat de logica verborgen is, u deze niet als sjabloon gebruiken om meer dan één regel te maken.
- **Machine learning behavioral analytics** - Deze sjablonen zijn gebaseerd op eigen Microsoft machine learning-algoritmen, zodat u de interne logica van hoe ze werken en wanneer ze worden uitgevoerd niet zien. Omdat de logica verborgen is, u deze niet als sjabloon gebruiken om meer dan één regel te maken.
-   **Gepland** : geplande analytische regels zijn geplande query's die zijn geschreven door beveiligingsexperts van Microsoft. U de querylogica bekijken en deze aanbrengen. U geplande regels als sjabloon gebruiken om nieuwe regels met vergelijkbare logica te maken.

## <a name="use-out-of-the-box-detections"></a>Out-of-the-box detecties gebruiken

1. Als u een ingebouwde sjabloon wilt gebruiken, klikt u op **Regel maken** om een nieuwe actieve regel te maken op basis van die sjabloon. Elk item heeft een lijst met vereiste gegevensbronnen die automatisch worden gecontroleerd en dit kan ertoe leiden **dat de regel Maken** wordt uitgeschakeld.
  
   ![Gebruik ingebouwde detecties om bedreigingen te vinden met Azure Sentinel](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Hiermee wordt de wizard Regelcreatie geopend op basis van de geselecteerde sjabloon. Alle details worden automatisch ingevuld en voor **geplande regels** of **Microsoft-beveiligingsregels**u de logica aanpassen aan uw organisatie of aanvullende regels maken op basis van de ingebouwde sjabloon. Nadat u de stappen in de wizard Regelcreatie hebt gevolgd en een regel hebt gemaakt op basis van de sjabloon, wordt de nieuwe regel weergegeven op het tabblad **Actieve regels.**

Zie [Zelfstudie: Aangepaste analytische regels maken om verdachte bedreigingen te detecteren](tutorial-detect-threats-custom.md)voor meer informatie over de velden in de wizard.



## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u aan de slag met het detecteren van bedreigingen met Azure Sentinel. 

Als u wilt weten hoe u uw reacties op bedreigingen automatiseren, [stelt u geautomatiseerde bedreigingsreacties in Azure Sentinel](tutorial-respond-threats-playbook.md)in.

