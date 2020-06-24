---
title: Aangepaste afbeeldingen en formules vergelijken in DevTest Labs | Microsoft Docs
description: Meer informatie over de verschillen tussen aangepaste installatie kopieën en formules als VM-basis, zodat u kunt bepalen welke methode het beste past bij uw omgeving.
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
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896324"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Aangepaste afbeeldingen en formules in DevTest Labs vergelijken
Zowel [aangepaste installatie kopieën](devtest-lab-create-template.md) als [formules](devtest-lab-manage-formulas.md) kunnen worden gebruikt als basis voor het [maken van nieuwe vm's](devtest-lab-add-vm.md). Het belangrijkste onderscheid tussen aangepaste afbeeldingen en formules is echter dat een aangepaste installatie kopie een afbeelding is op basis van een VHD, terwijl een formule een afbeelding is op basis van een VHD *naast* vooraf geconfigureerde instellingen, zoals de VM-grootte, het virtuele netwerk, het subnet en de artefacten. Deze vooraf geconfigureerde instellingen worden ingesteld met standaard waarden die kunnen worden overschreven op het moment dat de VM wordt gemaakt. In dit artikel worden enkele voor delen (voor-en nadelen) beschreven voor het gebruik van aangepaste installatie kopieën en het gebruik van formules.

## <a name="custom-image-pros-and-cons"></a>Voor beelden van aangepaste afbeeldingen en nadelen
Aangepaste installatie kopieën bieden een statische, onveranderlijke manier om Vm's te maken op basis van een gewenste omgeving. 

**-Professionals**

* Het inrichten van een virtuele machine vanuit een aangepaste installatie kopie is snel omdat er niets verandert nadat de virtuele machine van de installatie kopie is gemaakt. Met andere woorden, er zijn geen instellingen die kunnen worden toegepast als de aangepaste installatie kopie alleen een installatie kopie zonder instellingen is. 
* Vm's die zijn gemaakt op basis van één aangepaste installatie kopie, zijn identiek.

**Nadelen**

* Als u een aspect van de aangepaste installatie kopie moet bijwerken, moet de afbeelding opnieuw worden gemaakt.  

## <a name="formula-pros-and-cons"></a>Voor-en nadelen van formules
Formules bieden een dynamische manier om Vm's te maken op basis van de gewenste configuratie/instellingen.

**-Professionals**

* Wijzigingen in de omgeving kunnen worden vastgelegd via artefacten. Als u bijvoorbeeld een VM wilt installeren met de meest recente bits van uw release pijplijn of als u de meest recente code van uw opslag plaats wilt inschrijven, kunt u gewoon een artefact opgeven dat de meest recente bits implementeert of de meest recente code in de formule aanmeldt samen met een doel basis installatie kopie. Wanneer deze formule wordt gebruikt voor het maken van Vm's, worden de meest recente bits/code geïmplementeerd/aangemeld bij de VM. 
* Formules kunnen standaard instellingen definiëren die aangepaste installatie kopieën niet kunnen bieden, zoals VM-grootten en instellingen voor virtuele netwerken. 
* De instellingen die zijn opgeslagen in een formule worden weer gegeven als standaard waarden, maar kunnen worden gewijzigd wanneer de virtuele machine wordt gemaakt. 

**Nadelen**

* Het maken van een VM op basis van een formule kan meer tijd in beslag nemen dan het maken van een virtuele machine vanuit een aangepaste installatie kopie.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blog berichten
* [Aangepaste afbeeldingen of formules?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Volgende stappen
- [Veelgestelde vragen over DevTest Labs](devtest-lab-faq.md)