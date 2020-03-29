---
title: Aangepaste afbeeldingen en formules vergelijken in DevTest Labs | Microsoft Documenten
description: Meer informatie over de verschillen tussen aangepaste afbeeldingen en formules als VM-bases, zodat u beslissen welke het beste bij uw omgeving past.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: ae7556eda817b9eb7be84f9d4a23ea91d3d5440d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64680304"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Aangepaste afbeeldingen en formules vergelijken in DevTest Labs
Zowel [aangepaste afbeeldingen](devtest-lab-create-template.md) als [formules](devtest-lab-manage-formulas.md) kunnen worden gebruikt als basis voor het maken van [nieuwe VM's.](devtest-lab-add-vm.md) Het belangrijkste onderscheid tussen aangepaste afbeeldingen en formules is echter dat een aangepaste afbeelding gewoon een afbeelding is die is gebaseerd op een VHD, terwijl een formule een afbeelding is die is gebaseerd op een VHD *naast* vooraf geconfigureerde instellingen - zoals VM-grootte, virtueel netwerk, subnet en artefacten. Deze vooraf geconfigureerde instellingen zijn ingesteld met standaardwaarden die kunnen worden overschreven op het moment van het maken van vm's. In dit artikel worden enkele van de voordelen (voor- en nadelen) uitgelegd aan het gebruik van aangepaste afbeeldingen versus het gebruik van formules.

## <a name="custom-image-pros-and-cons"></a>Aangepaste afbeelding svoor- en nadelen
Aangepaste afbeeldingen bieden een statische, onveranderlijke manier om VM's te maken vanuit een gewenste omgeving. 

**Pros**

* VM-inrichten vanuit een aangepaste afbeelding is snel omdat er niets verandert nadat de VM uit de afbeelding is gesponnen. Met andere woorden, er zijn geen instellingen toe te passen als de aangepaste afbeelding is gewoon een afbeelding zonder instellingen. 
* VM's die zijn gemaakt van één aangepaste afbeelding zijn identiek.

**Tegens**

* Als u een bepaald aspect van de aangepaste afbeelding moet bijwerken, moet de afbeelding opnieuw worden gemaakt.  

## <a name="formula-pros-and-cons"></a>Formule voor- en nadelen
Formules bieden een dynamische manier om VM's te maken vanuit de gewenste configuratie/instellingen.

**Pros**

* Veranderingen in de omgeving kunnen worden vastgelegd op de vlieg via artefacten. Als u bijvoorbeeld een VM wilt installeren met de nieuwste bits uit uw releasepijplijn of de nieuwste code uit uw opslagplaats wilt aanvragen, u eenvoudig een artefact opgeven dat de nieuwste bits implementeert of de nieuwste code in de formule inschakelt samen met een doelbasis Afbeelding. Wanneer deze formule wordt gebruikt om VM's te maken, worden de nieuwste bits/code geïmplementeerd/ingeschakeld voor de VM. 
* Formules kunnen standaardinstellingen definiëren die aangepaste afbeeldingen niet kunnen bieden, zoals VM-formaten en virtuele netwerkinstellingen. 
* De instellingen die in een formule zijn opgeslagen, worden weergegeven als standaardwaarden, maar kunnen worden gewijzigd wanneer de vm wordt gemaakt. 

**Tegens**

* Het maken van een VM op basis van een formule kan meer tijd in beslag nemen dan het maken van een VM op basis van een aangepaste afbeelding.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blogberichten
* [Aangepaste afbeeldingen of formules?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Volgende stappen
- [Veelgestelde vragen over DevTest Labs](devtest-lab-faq.md)