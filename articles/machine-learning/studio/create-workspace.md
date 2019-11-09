---
title: Een werkruimte maken
titleSuffix: ML Studio (classic) - Azure
description: Als u Azure Machine Learning Studio (klassiek) wilt gebruiken, moet u een Machine Learning Studio (klassieke) werk ruimte hebben. Deze werkruimte bevat de hulpprogramma's die u nodig hebt om experimenten te maken, beheren en publiceren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1652febf6c3eb906990b2405590274911a45348e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839922"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Een Azure Machine Learning Studio-werk ruimte (klassieke) maken en delen

Als u Azure Machine Learning Studio (klassiek) wilt gebruiken, moet u een Machine Learning Studio (klassieke) werk ruimte hebben. Deze werkruimte bevat de hulpprogramma's die u nodig hebt om experimenten te maken, beheren en publiceren.

## <a name="create-a-studio-classic-workspace"></a>Een studio-werk ruimte maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/)

    > [!NOTE]
    > Als u zich wilt aanmelden en een studio-werk ruimte wilt maken, moet u een beheerder van het Azure-abonnement zijn. 
    >
    > 

2. Klik op **+ Nieuw**

3. Typ in het zoekvak **machine learning Studio (klassieke) werk ruimte** en selecteer het overeenkomende item. Selecteer vervolgens onder aan de pagina op **maken** .

4. Voer uw werkruimte gegevens in:

   - De *naam van de werk ruimte* mag maxi maal 260 tekens bevatten en mag niet eindigen met een spatie. De naam mag niet de volgende tekens bevatten: `< > * % & : \ ? + /`
   - Het *webservice-abonnement* dat u kiest (of maakt), samen met de bijbehorende *prijs categorie* die u selecteert, wordt gebruikt als u webservices uit deze werk ruimte implementeert.

     ![Een nieuwe studio-werk ruimte maken](./media/create-workspace/create-new-workspace.png)

5. Klik op **Maken**.

> [!NOTE]
> Machine Learning Studio (klassiek) is afhankelijk van een Azure Storage-account dat u opgeeft om tussenliggende gegevens op te slaan wanneer de werk stroom wordt uitgevoerd. Nadat de werk ruimte is gemaakt, wordt het opslag account verwijderd, of als de toegangs sleutels zijn gewijzigd, werkt de werk ruimte niet meer en mislukken alle experimenten in deze werk ruimte.
Als u het opslag account per ongeluk verwijdert, maakt u het opslag account opnieuw met dezelfde naam in dezelfde regio als het verwijderde opslag account en synchroniseert u de toegangs sleutel opnieuw. Als u de toegangssleutel voor een opslagaccount hebt gewijzigd, moet u de toegangssleutel in Azure Portal opnieuw synchroniseren in de werkruimte.

Nadat de werk ruimte is geïmplementeerd, kunt u deze openen in de klassieke versie van Machine Learning Studio.

1. Ga naar Machine Learning Studio (klassiek) op [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selecteer uw werk ruimte in de rechter bovenhoek.

    ![Werkruimte selecteren](./media/create-workspace/open-workspace.png)

3. Klik op **mijn experimenten**.

    ![Experimenten openen](./media/create-workspace/my-experiments.png)

Voor informatie over het beheren van uw studio-werk ruimte (klassiek), Zie [een Azure machine learning Studio (klassieke) werk ruimte beheren](manage-workspace.md).
Als er een probleem optreedt bij het maken van uw werk ruimte, raadpleegt u de [hand leiding voor probleem oplossing: maken en verbinding maken met een machine learning Studio (klassieke) werk ruimte](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Een Azure Machine Learning Studio-werk ruimte (klassieke) delen
Zodra een Machine Learning Studio (klassieke) werk ruimte is gemaakt, kunt u gebruikers uitnodigen voor uw werk ruimte om de toegang tot uw werk ruimte en alle experimenten, gegevens sets, notitie blokken, enzovoort te delen. U kunt gebruikers toevoegen in een van de twee rollen:

* **Gebruiker** : een werkruimte gebruiker kan experimenten, gegevens sets, enzovoort, maken, openen, wijzigen en verwijderen in de werk ruimte.
* **Eigenaar** : een eigenaar kan gebruikers uitnodigen en verwijderen in de werk ruimte, naast wat een gebruiker kan doen.

> [!NOTE]
> Het beheerders account waarmee de werk ruimte wordt gemaakt, wordt automatisch toegevoegd aan de werk ruimte als eigenaar van de werk ruimte. Andere beheerders of gebruikers in dat abonnement hebben echter niet automatisch toegang tot de werk ruimte, maar u moet ze expliciet uitnodigen.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Een studio-werk ruimte delen (klassiek)

1. Meld u aan bij de klassieke versie van Machine Learning Studio op [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Klik in het linkerdeel venster op **instellingen**

3. Klik op het tabblad **gebruikers**

4. Klik op **meer gebruikers uitnodigen** onder aan de pagina

    ![Studio-instellingen](./media/create-workspace/settings.png)

5. Voer een of meer e-mail adressen in. De gebruikers hebben een geldig Microsoft-account of een organisatie account (van Azure Active Directory) nodig.

6. Geef aan of u de gebruikers als eigenaar of gebruiker wilt toevoegen.

7. Klik op de knop voor het selectie vakje **OK** .

Elke gebruiker die u toevoegt, ontvangt een e-mail met instructies over hoe u zich aanmeldt bij de gedeelde werk ruimte.

> [!NOTE]
> Gebruikers kunnen alleen webservices in deze werk ruimte implementeren of beheren als ze een Inzender of beheerder zijn in het Azure-abonnement. 



