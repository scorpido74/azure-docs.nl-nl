---
title: Problemen met een werk ruimte oplossen
titleSuffix: ML Studio (classic) - Azure
description: Deze hand leiding bevat oplossingen voor enkele vaak voorkomende uitdagingen bij het instellen van Azure Machine Learning Studio (klassieke) werk ruimten.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: a4385d88912e7c4d391baa8f5bc50be1e07f4fa7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152785"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Probleemoplossings gids: maken en verbinding maken met een Azure Machine Learning Studio (klassieke) werk ruimte
Deze hand leiding bevat oplossingen voor enkele vaak voorkomende uitdagingen bij het instellen van Azure Machine Learning Studio (klassieke) werk ruimten.



## <a name="workspace-owner"></a>Eigenaar van werk ruimte
Als u een werk ruimte in Machine Learning Studio (klassiek) wilt openen, moet u zijn aangemeld bij het micro soft-account dat u hebt gebruikt voor het maken van de werk ruimte, of moet u een uitnodiging van de eigenaar ontvangen om deel te nemen aan de werk ruimte. Vanuit de Azure Portal kunt u de werk ruimte beheren, waaronder de mogelijkheid om toegang te configureren.

Zie [Een Azure Machine Learning Studio-werk ruimte (klassieke) beheren]voor meer informatie over het beheren van een werk ruimte.

[Een Azure Machine Learning Studio-werk ruimte (klassieke) beheren]: manage-workspace.md

## <a name="allowed-regions"></a>Toegestane regio's
Machine Learning is momenteel beschikbaar in een beperkt aantal regio's. Als uw abonnement niet een van deze regio's bevat, wordt het fout bericht ' u hebt geen abonnementen in de toegestane regio's ' weer gegeven.

Als u een regio wilt toevoegen aan uw abonnement, maakt u een nieuwe micro soft-ondersteunings aanvraag van de Azure Portal, kiest u **facturering** als het probleem type en volgt u de aanwijzingen om uw aanvraag in te dienen.

## <a name="storage-account"></a>Storage-account
De Machine Learning-service heeft een opslag account nodig om gegevens op te slaan. U kunt een bestaand opslag account gebruiken of u kunt een nieuw opslag account maken wanneer u de nieuwe Machine Learning Studio (klassieke) werk ruimte maakt (als u een quotum hebt om een nieuw opslag account te maken).

Nadat de nieuwe Machine Learning Studio (klassieke) werk ruimte is gemaakt, kunt u zich aanmelden bij Machine Learning Studio (klassiek) met behulp van de Microsoft-account die u hebt gebruikt om de werk ruimte te maken. Als het fout bericht ' werk ruimte niet gevonden ' (vergelijkbaar met de volgende scherm afbeelding) wordt weer gegeven, moet u de volgende stappen gebruiken om uw browser cookies te verwijderen.

![De werk ruimte is niet gevonden](media/troubleshooting-creating-ml-workspace/screen3.png)

**Browser cookies verwijderen**

1. Als u Internet Explorer gebruikt, klikt u op de knop **extra** in de rechter bovenhoek en selecteert u **Internet opties**.  

   ![Internet opties](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Klik op het tabblad **Algemeen** op **verwijderen...**

   ![Tabblad Algemeen](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Controleer in het dialoog venster **Browse geschiedenis verwijderen** of **cookies en website gegevens** zijn geselecteerd en klik op **verwijderen**.

   ![Cookies verwijderen](media/troubleshooting-creating-ml-workspace/screen6.png)

Nadat de cookies zijn verwijderd, start u de browser opnieuw en gaat u naar de pagina [Microsoft Azure machine learning Studio (klassiek)](https://studio.azureml.net) . Wanneer u wordt gevraagd om een gebruikers naam en wacht woord, voert u de Microsoft-account die u hebt gebruikt om de werk ruimte te maken.

## <a name="comments"></a>Opmerkingen

Ons doel is om de Machine Learning ervaring zo naadloos mogelijk te maken. Plaats opmerkingen en problemen in het Azure Machine Learning- [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) om ons te helpen u beter te bezorgen.
