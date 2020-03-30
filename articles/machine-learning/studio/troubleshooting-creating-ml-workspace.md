---
title: Problemen met een werkruimte oplossen
titleSuffix: ML Studio (classic) - Azure
description: Deze handleiding biedt oplossingen voor een aantal vaak ondervonden uitdagingen bij het instellen van Azure Machine Learning Studio (klassieke) werkruimten.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217837"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Handleiding voor probleemoplossing: maak en maak verbinding met een Azure Machine Learning Studio (klassieke) werkruimte

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Deze handleiding biedt oplossingen voor een aantal vaak ondervonden uitdagingen bij het instellen van Azure Machine Learning Studio (klassieke) werkruimten.

## <a name="workspace-owner"></a>Eigenaar van werkruimte
Als u een werkruimte wilt openen in Machine Learning Studio (klassiek), moet u zijn aangemeld bij het Microsoft-account dat u hebt gebruikt om de werkruimte te maken, of moet u een uitnodiging van de eigenaar ontvangen om lid te worden van de werkruimte. Vanuit de Azure-portal u de werkruimte beheren, inclusief de mogelijkheid om toegang te configureren.

Zie [Een azure machine learning studio (klassieke) werkruimte beheren]voor meer informatie over het beheren van een werkruimte.

[Een Azure Machine Learning Studio -werkruimte (klassieke) werkruimte beheren]: manage-workspace.md

## <a name="allowed-regions"></a>Toegestane regio's
Machine Learning is momenteel beschikbaar in een beperkt aantal regio's. Als uw abonnement een van deze regio's niet bevat, ziet u mogelijk de foutmelding 'U hebt geen abonnementen in de toegestane regio's'.

Als u wilt vragen dat een regio aan uw abonnement wordt toegevoegd, maakt u een nieuw Microsoft-ondersteuningsverzoek vanuit de Azure-portal, kiest **u Facturering** als probleemtype en volgt u de aanwijzingen om uw aanvraag in te dienen.

## <a name="storage-account"></a>Storage-account
De Machine Learning-service heeft een opslagaccount nodig om gegevens op te slaan. U een bestaand opslagaccount gebruiken of u een nieuw opslagaccount maken wanneer u de nieuwe machine learning studio (klassieke) werkruimte maakt (als u quota hebt om een nieuw opslagaccount te maken).

Nadat de nieuwe Machine Learning Studio (klassieke) werkruimte is gemaakt, u zich aanmelden bij Machine Learning Studio (klassiek) met behulp van het Microsoft-account dat u hebt gebruikt om de werkruimte te maken. Als u het foutbericht 'Werkruimte niet gevonden' tegenkomt (vergelijkbaar met de volgende schermafbeelding), gebruikt u de volgende stappen om uw browsercookies te verwijderen.

![Werkruimte niet gevonden](media/troubleshooting-creating-ml-workspace/screen3.png)

**Browsercookies verwijderen**

1. Als u Internet Explorer gebruikt, klikt u op de knop **Extra** in de rechterbovenhoek en selecteert u **internetopties**.  

   ![Internetopties](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Klik onder het tabblad **Algemeen** op **Verwijderen...**

   ![Tabblad Algemeen](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Controleer in het dialoogvenster **Browsegeschiedenis verwijderen** of **cookies en websitegegevens** zijn geselecteerd en klik op **Verwijderen**.

   ![Cookies verwijderen](media/troubleshooting-creating-ml-workspace/screen6.png)

Nadat de cookies zijn verwijderd, start u de browser opnieuw op en gaat u naar de pagina [Microsoft Azure Machine Learning Studio (klassieke)](https://studio.azureml.net) pagina. Wanneer u wordt gevraagd om een gebruikersnaam en wachtwoord, voert u hetzelfde Microsoft-account in dat u hebt gebruikt om de werkruimte te maken.

## <a name="comments"></a>Opmerkingen

Ons doel is om de Machine Learning-ervaring zo naadloos mogelijk te maken. Plaats eventuele opmerkingen en problemen op het [Azure Machine Learning-forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) om ons te helpen u beter van dienst te zijn.
