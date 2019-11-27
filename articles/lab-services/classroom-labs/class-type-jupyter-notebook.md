---
title: Stel een lab in om data Science te leren met python-en Jupyter-notebooks | Microsoft Docs
description: Meer informatie over het instellen van een lab om data Science te leren werken met python-en Jupyter-notebooks.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: b69abf098ba7646ebc98d126c7c0d949205d6275
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383975"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Stel een lab in om data Science te leren met python-en Jupyter-notebooks

In dit artikel wordt beschreven hoe u een sjabloon machine kunt instellen in Lab-Services met de hulpprogram ma's die nodig zijn om studenten te leren hoe u [Jupyter-notebooks](http://jupyter-notebook.readthedocs.io)kunt gebruiken.  Jupyter-notebooks is een open-source project waarmee u eenvoudig uitgebreide tekst en uitvoer bare [python](https://www.python.org/) -bron code kunt combi neren op één canvas dat een notitie blok wordt genoemd.  Het uitvoeren van een notitie blok resulteert in een lineaire record met invoer en uitvoer.  Deze uitvoer kan tekst, tabel met gegevens, spreidings grafieken en meer zijn.

## <a name="lab-configuration"></a>Lab-configuratie

Als u dit Lab wilt instellen, hebt u een Azure-abonnement en een Lab-account nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services. Zie [zelf studie voor het instellen van een Lab-account](tutorial-setup-lab-account.md)voor meer informatie over het maken van een nieuw Lab-account.  U kunt ook een bestaand Lab-account gebruiken.

### <a name="lab-account-settings"></a>Instellingen van Lab-account

Schakel de instellingen die worden beschreven in de onderstaande tabel voor het lab-account in. Zie [Marketplace-installatie kopieën die beschikbaar zijn voor Lab-makers opgeven](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators)voor meer informatie over het inschakelen van Marketplace-installatie kopieën.

| Account instelling Lab | Instructies |
| ------------------- | ------------ |
| Marketplace-installatie kopie | Schakel de [Data Science virtual machine-](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) installatie kopie van Windows 2016 in voor gebruik binnen uw Lab-account. |

>[!TIP]
>In dit artikel wordt aandacht besteed aan het configureren van een sjabloon machine die gebruikmaakt van het Windows Server-besturings systeem.  Het is ook mogelijk om een Data Science-klasse met python-en Jupyter-notebooks in te stellen met behulp van [Data Science virtual machine voor Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) of [Data Science virtual machine voor Linux (Ubuntu)-](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) installatie kopieën van de Azure Marketplace.

### <a name="lab-settings"></a>Lab-instellingen

Gebruik de instellingen in de onderstaande tabel bij het instellen van een leslokaal Lab.  Voor meer informatie over het maken van een leslokaal Lab raadpleegt u [een klassikale Lab-zelf studie instellen](tutorial-setup-classroom-lab.md).

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ |
|Grootte van virtuele machine| Kleine GPU (Compute). Deze grootte is het meest geschikt voor computerintensieve en netwerkintensieve toepassingen, zoals kunst matige intelligentie en diep gaande lessen. |
|Installatie kopie van virtuele machine| SQL Server 2019-standaard op Windows Server 2019|

## <a name="template-machine"></a>Sjabloon machine

De [Data Science virtual machine-](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) installatie kopie van Windows 2016 biedt de nodige diepe leer raamwerken en hulp middelen die vereist zijn voor dit type klasse.  De afbeelding bevat Jupyter-notebooks en Visual Studio code.  [Jupyter-notebooks](http://jupyter-notebook.readthedocs.io) is een webtoepassing waarmee gegevens wetenschappers onbewerkte gegevens kunnen maken, berekeningen kunnen uitvoeren en de resultaten in dezelfde omgeving worden weer gegeven.  Voor onze sjabloon machine wordt de webtoepassing lokaal uitgevoerd.  [Visual Studio code](https://code.visualstudio.com/) is een IDE die een rijke interactieve ervaring biedt bij het schrijven en testen van een notitie blok.  Zie [werken met Jupyter-notebooks in Visual Studio code](https://code.visualstudio.com/docs/python/jupyter-support)voor meer informatie.

De resterende taak voor het instellen van de klasse is het leveren van lokale notitie blokken.  Zie [How to Configure an environment with Jupyter notebooks](../../machine-learning/service/how-to-configure-environment.md#jupyter)voor instructies over het gebruik van de Azure machine learning-voor beelden.  U kunt ook uw eigen notitie blokken op de sjabloon machine opgeven.  De notitie blokken worden gekopieerd naar alle studenten machines wanneer de sjabloon wordt gepubliceerd.

## <a name="cost-estimate"></a>Kostenschatting

Laten we een mogelijke schatting van de kosten voor deze klasse beslaan.  We gebruiken een klasse van 25 studenten.  Er zijn 20 uur geplande tijd voor de klasse.  Daarnaast krijgt elke student tien uur quota voor huis werk of toewijzingen buiten een geplande klasse tijd.  De grootte van de virtuele machine die u hebt gekozen, is een kleine GPU (Compute). Dit zijn 139 Lab-eenheden.

Hier volgt een voor beeld van een mogelijke schatting van de kosten voor deze klasse:

25 studenten \* (20 geplande uren + 10 quotum uren) \* 139 Lab-eenheden \* 0,01 USD per uur = 1042,5 USD

Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor meer informatie over prijzen.

## <a name="conclusion"></a>Conclusie

In dit artikel werd uitgelegd hoe de stappen voor het maken van een Lab voor een Jupyter-notitie klasse. U kunt een vergelijk bare installatie gebruiken voor andere machine learning klassen.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)
