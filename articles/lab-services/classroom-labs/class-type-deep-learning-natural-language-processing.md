---
title: Een lab opzetten dat is gericht op deep learning met Azure Lab Services | Microsoft Documenten
description: Meer informatie over het opzetten van een lab om shell scripting op Linux te leren.
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
ms.openlocfilehash: 49ef78ac5a7d58d86583d91bf072f0f3131796fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109392"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Een lab opzetten dat zich richt op deep learning in natuurlijke taalverwerking met Azure Lab Services
In dit artikel ziet u hoe u een lab instelt dat is gericht op deep learning in natural language processing (NLP) met Azure Lab Services. Natural language processing (NLP) is een vorm van kunstmatige intelligentie (AI) waarmee computers met vertaal-, spraakherkennings- en andere mogelijkheden voor taalbegrip kunnen worden gebruikt.  

Studenten die een NLP-klas volgen, krijgen een Linux virtuele machine (VM) om te leren hoe ze neurale netwerkalgoritmen kunnen toepassen om deep learning-modellen te ontwikkelen die worden gebruikt voor het analyseren van geschreven menselijke taal. 

## <a name="lab-configuration"></a>Labconfiguratie
Om dit lab in te stellen, hebt u een Azure-abonnement nodig om aan de slag te gaan. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Zodra u een Azure-abonnement hebt, u een nieuw labaccount maken in Azure Lab Services of een bestaand labaccount gebruiken. Zie de volgende zelfstudie voor het maken van een nieuw labaccount: [Zelfstudie om een Lab-account op te zetten](tutorial-setup-lab-account.md).
 
Nadat u het labaccount hebt gemaakt, schakelt u de volgende instellingen in het labaccount in: 

| Lab-accountinstelling | Instructies |
| ----------- | ------------ |  
| Marketplace-afbeeldingen | Schakel de Data Science Virtual Machine for Linux (Ubuntu) afbeelding in voor gebruik binnen uw labaccount.  Zie het volgende artikel voor instructies: [Geef marktplaatsafbeeldingen op die beschikbaar zijn voor makers van het lab.](specify-marketplace-images.md) | 

Volg [deze zelfstudie](tutorial-setup-classroom-lab.md) om een nieuw lab te maken en de volgende instellingen toe te passen:

| Lab-instellingen | Waarde/instructies | 
| ------------ | ------------------ |
| Virtuele machine (VM) grootte | Kleine GPU (Compute). Deze grootte is het meest geschikt voor rekenintensieve en netwerkintensieve toepassingen zoals Artificial Intelligence en Deep Learning. |
| VM-afbeelding | [Data Science Virtual Machine voor Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Dit beeld biedt deep learning frameworks en tools voor machine learning en data science. Zie het volgende artikel: Wat staat er op [de DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) |
| Verbinding met extern bureaublad inschakelen | Inschakelen. <p>Als u deze instelling inschakelt, kunnen docenten en studenten verbinding maken met hun virtuele machines (VM) via Extern bureaublad (RDP).</p><p>**Belangrijk:** RDP is al geïnstalleerd en geconfigureerd op de Data Science Virtual Machine voor Linux image. Hierdoor kunnen docenten/studenten zonder extra stappen verbinding maken met VM's via RDP. Als u verbinding wilt maken met het grafische bureaublad, heeft deze afbeelding [x2Go-server](https://wiki.x2go.org/doku.php/doc:newtox2go) al geïnstalleerd op de virtuele machine. Studenten moeten X2Go-client installeren op hun lokale machines en de client gebruiken voor verbinding. Zie de volgende hulplijnen voor meer informatie: <ul><li>[Toegang krijgen tot de Data Science Virtual Machine voor Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Verbinding maken met de sjabloon-VM om RDP- en GUI-pakketten te installeren](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

De Data Science Virtual Machine voor Linux-afbeelding biedt de nodige deep learning frameworks en tools die nodig zijn voor dit type klasse. Als gevolg hiervan hoeft u deze na het maken van de sjabloonmachine niet verder aan te passen. Het kan worden gepubliceerd voor studenten om te gebruiken. Selecteer de knop **Publiceren** op de sjabloonpagina om de sjabloon naar het lab te publiceren.  

## <a name="cost"></a>Kosten
Als u de kosten van dit lab wilt schatten, u het volgende voorbeeld gebruiken: 

Voor een klas van 25 studenten met 20 uur geplande lestijd en 10 uur quotum voor huiswerk of opdrachten, zou de prijs voor het lab zijn - 25 studenten * (20 + 10) uur * 139 Lab Units * 0,01 USD per uur = 1042,5 USD

Meer informatie over prijzen, zie [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusie
Dit artikel liep u door de stappen om een lab voor natuurlijke taal verwerking klasse te creëren. U een vergelijkbare instelling gebruiken voor andere deep learning-klassen.

## <a name="next-steps"></a>Volgende stappen
De volgende stappen zijn gebruikelijk voor het instellen van een lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links naar registratie via e-mail naar studenten](how-to-configure-student-usage.md#send-invitations-to-users). 

