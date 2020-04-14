---
title: Een lab opzetten om data science te onderwijzen met Python en Jupyter Notebooks | Microsoft Documenten
description: Meer informatie over het opzetten van een lab om gegevenswetenschap te onderwijzen met Python- en Jupyter-notitieblokken.
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
ms.openlocfilehash: 222f5647248d27c3bdfabd0feaeb66dd9f543652
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257722"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Een lab opzetten om data science te onderwijzen met Python- en Jupyter-laptops

In dit artikel wordt beschreven hoe u een sjabloonmachine in Lab Services instellen met de tools die nodig zijn om studenten te leren hoe ze [Jupyter-notitieblokken](http://jupyter-notebook.readthedocs.io)kunnen gebruiken.  Jupyter Notebooks is een open-source project waarmee u [Python](https://www.python.org/) eenvoudig rijke tekst en uitvoerbare Python-broncode combineren op één canvas dat een notitieblok wordt genoemd.  Het uitvoeren van een notebook resulteert in een lineaire registratie van ingangen en uitgangen.  Deze uitvoer kan tekst, tabellen met informatie, spreidingsplots en meer bevatten.

## <a name="lab-configuration"></a>Labconfiguratie

Om dit lab in te stellen, hebt u een Azure-abonnement en labaccount nodig om aan de slag te gaan. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Zodra u een Azure-abonnement hebt, u een nieuw labaccount maken in Azure Lab Services. Zie [Zelfstudie om een labaccount in te stellen voor](tutorial-setup-lab-account.md)meer informatie over het maken van een nieuw labaccount.  U ook een bestaand labaccount gebruiken.

### <a name="lab-account-settings"></a>Lab-accountinstellingen

Schakel de instellingen in die in de onderstaande tabel worden beschreven voor het labaccount. Zie [Marketplace-afbeeldingen opgeven die beschikbaar zijn voor makers van het lab voor](specify-marketplace-images.md)meer informatie over het inschakelen van marketplace-afbeeldingen.

| Lab-accountinstelling | Instructies |
| ------------------- | ------------ |
| Afbeelding van Marktplaats | Schakel de [Data Science Virtual Machine - Windows 2019-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) in voor gebruik binnen uw labaccount. |

>[!TIP]
>In dit artikel wordt zich gericht op het configureren van een sjabloonmachine die het Windows Server-besturingssysteem gebruikt.  Het is ook mogelijk om een data science-les in te stellen met Python- en Jupyter-laptops met Behulp van [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) afbeeldingen van de Azure Marketplace.

### <a name="lab-settings"></a>Lab-instellingen

Gebruik de instellingen in de onderstaande tabel bij het instellen van een klaslokaallab.  Zie een lesprogramma voor een klaslokaal voor meer informatie over het maken van een [leslokaallab.](tutorial-setup-classroom-lab.md)

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ |
|Virtuele machinegrootte| Kleine GPU (Compute). Deze grootte is het meest geschikt voor rekenintensieve en netwerkintensieve toepassingen zoals Artificial Intelligence en Deep Learning. |
|Afbeelding van virtuele machine| Virtual Machine voor datawetenschap - Windows 2016|

## <a name="template-machine"></a>Sjabloonmachine

De [Data Science Virtual Machine - Windows 2019-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) biedt de nodige deep learning-frameworks en tools die nodig zijn voor dit type klasse.  De afbeelding bevat Jupyter-notitieblokken en Visual Studio-code.  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) is een webapplicatie waarmee gegevenswetenschappers ruwe gegevens kunnen maken, berekeningen kunnen uitvoeren en de resultaten allemaal in dezelfde omgeving kunnen bekijken.  Voor onze sjabloonmachine wordt de webapplicatie lokaal uitgevoerd.  [Visual Studio Code](https://code.visualstudio.com/) is een IDE die een rijke interactieve ervaring biedt bij het schrijven en testen van een notitieblok.  Zie [Werken met Jupyter-notitieblokken in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)voor meer informatie.

De resterende taak om de klasse in te stellen, is het verstrekken van lokale notitieblokken.  Zie hoe u [een omgeving configureert met Jupyter-notitieblokken](../../machine-learning/how-to-configure-environment.md#jupyter)voor instructies over het gebruik van de voorbeelden van Azure Machine Learning.  U ook uw eigen notitieblokken op de sjabloonmachine verstrekken.  De notitieblokken worden gekopieerd naar alle studentenmachines wanneer de sjabloon wordt gepubliceerd.

## <a name="cost-estimate"></a>Kostenraming

Laten we een mogelijke kostenraming voor deze klasse bespreken.  We gebruiken een klas van 25 studenten.  Er zijn 20 uur geplande lestijd.  Ook krijgt elke student een quotum van 10 uur voor huiswerk of opdrachten buiten de geplande lestijd.  De virtuele machine grootte die we kozen was kleine GPU (compute), dat is 139 lab units.

Hier volgt een voorbeeld van een mogelijke kostenraming voor deze klasse:

25 \* studenten (20 geplande uren + \* 10 quota-uren) 139 labunits \* 0,01 USD per uur = 1042,5 USD

Meer informatie over prijzen, zie [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusie

In dit artikel, liepen we door de stappen om een lab te maken voor een Jupyter Notebooks klasse. U een vergelijkbare instelling gebruiken voor andere machine learning-klassen.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het opzetten van een lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Koppelingen naar e-mailregistratie naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)
