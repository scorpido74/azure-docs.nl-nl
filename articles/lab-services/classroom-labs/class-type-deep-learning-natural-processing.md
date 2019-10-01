---
title: Stel een lab in dat is gericht op diep leren met behulp van Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab voor het leren van shell scripting op Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 19191e84e9af140f78e529398471f0e950b36c8c
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694687"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Stel een lab in dat is gericht op diep gaande lessen in de verwerking van natuurlijke taal met behulp van Azure Lab Services
In dit artikel wordt beschreven hoe u een test omgeving kunt instellen voor een diep gaande cursus in natuurlijke taal verwerking (NLP) met behulp van Azure Lab Services. De verwerking van natuurlijke taal (NLP) is een vorm van kunst matige intelligentie (AI) waarmee computers kunnen beschikken over mogelijkheden voor vertaling, spraak herkenning en andere talen.  

Studenten die een NLP-klasse gebruiken, halen een virtuele Linux-machine (VM) op voor meer informatie over het Toep assen van Neural-netwerk algoritmen voor het ontwikkelen van diepe leer modellen die worden gebruikt voor het analyseren van geschreven menselijke talen. 

## <a name="lab-configuration"></a>Lab-configuratie
Als u dit Lab wilt instellen, hebt u een Azure-abonnement nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt, kunt u een nieuw Lab-account maken in Azure Lab Services of een bestaand Lab-account gebruiken. Raadpleeg de volgende zelf studie voor het maken van een nieuw Lab-account: [Zelf studie voor het instellen van een Lab-account](tutorial-setup-lab-account.md).
 
Nadat u het lab-account hebt gemaakt, schakelt u de volgende instellingen in het lab-account in: 

| Account instelling Lab | Instructies |
| ----------- | ------------ |  
| Marketplace-installatie kopieën | De Data Science Virtual Machine-installatie kopie voor Linux (Ubuntu) inschakelen voor gebruik binnen uw Lab-account.  Raadpleeg de volgende artikelen voor meer informatie: [Marketplace-installatie kopieën opgeven die beschikbaar zijn voor Lab-makers](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators). | 

Volg [deze zelf studie](tutorial-setup-classroom-lab.md) om een nieuw lab te maken en de volgende instellingen toe te passen:

| Lab-instellingen | Waarde/instructies | 
| ------------ | ------------------ |
| Grootte van virtuele machine (VM) | Kleine GPU (Compute). Deze grootte is het meest geschikt voor computerintensieve en netwerkintensieve toepassingen, zoals kunst matige intelligentie en diep gaande lessen. |
| VM-installatie kopie | [Data Science virtual machine voor Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Deze afbeelding biedt diep gaande frameworks en hulp middelen voor machine learning en gegevens wetenschap. Als u de volledige lijst met geïnstalleerde hulpprogram ma's op deze installatie kopie wilt weer geven, raadpleegt u het volgende artikel: [Wat is opgenomen in de DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Verbinding met extern bureau blad inschakelen | Kunt. <p>Als u deze instelling inschakelt, kunnen docenten en studenten verbinding maken met hun Virtual Machines (VM) met behulp van Extern bureaublad (RDP).</p><p>**Belang rijk**: RDP is al geïnstalleerd en geconfigureerd op de installatie kopie van Data Science Virtual Machine voor Linux. Als gevolg hiervan kunnen docenten/studenten zonder extra stappen verbinding maken met Vm's via RDP. Als u verbinding moet maken met het grafische bureau blad, heeft deze installatie kopie al een [X2Go-server](https://wiki.x2go.org/doku.php/doc:newtox2go) geïnstalleerd op de virtuele machine. Studenten moeten de X2Go-client installeren op hun lokale computers en de client gebruiken om verbinding te maken. Zie de volgende hand leidingen voor meer informatie: <ul><li>[Toegang tot de Data Science Virtual Machine voor Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Verbinding maken met de VM van de sjabloon voor het installeren van RDP-en GUI-pakketten](how-to-enable-remote-desktop-linux.md#teachers-connecting-to-the-template-vm-using-rdp)</li></ul></p>   |

De installatie kopie van Data Science Virtual Machine voor Linux biedt de nodige diepe leer raamwerken en hulp middelen die vereist zijn voor dit type klasse. Als gevolg hiervan hoeft u de computer niet verder aan te passen nadat u de sjabloon hebt gemaakt. Het kan worden gepubliceerd voor studenten om te gebruiken. Selecteer de knop **publiceren** op de sjabloon pagina om de sjabloon te publiceren naar het lab.  

## <a name="cost"></a>Kosten
Als u de kosten van dit Lab wilt schatten, kunt u het volgende voor beeld gebruiken: 

Voor een klasse van 25 studenten met 20 uur geplande en tien uur quota voor huis werk of-toewijzingen, is de prijs voor het lab-25 studenten * (20 + 10) uur * 139 Lab-eenheden * 0,01 USD per uur = 1042,5 USD

Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor meer informatie over prijzen.

## <a name="conclusion"></a>Conclusie
In dit artikel werd uitgelegd hoe u stapsgewijs door de stappen voor het maken van een Lab voor de natuurlijke taal verwerkings klasse. U kunt een vergelijk bare installatie gebruiken voor andere diep gaande lessen.

## <a name="next-steps"></a>Volgende stappen
De volgende stappen zijn gebruikelijk voor het instellen van elk lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](tutorial-setup-classroom-lab.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail registratie koppelingen naar studenten](tutorial-setup-classroom-lab.md#send-an-email-with-the-registration-link). 

