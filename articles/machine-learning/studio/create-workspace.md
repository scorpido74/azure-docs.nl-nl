---
title: Een werkruimte maken
titleSuffix: ML Studio (classic) - Azure
description: Als u Azure Machine Learning Studio (klassiek) wilt gebruiken, moet u een machine learning studio (klassieke) werkruimte hebben. Deze werkruimte bevat de hulpprogramma's die u nodig hebt om experimenten te maken, beheren en publiceren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1a391a7a061d1382b5e07b45625c44fc0f5dec54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204457"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Een Azure Machine Learning Studio -werkruimte (klassieke) werkruimte maken en delen

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Als u Azure Machine Learning Studio (klassiek) wilt gebruiken, moet u een machine learning studio (klassieke) werkruimte hebben. Deze werkruimte bevat de hulpprogramma's die u nodig hebt om experimenten te maken, beheren en publiceren.

## <a name="create-a-studio-classic-workspace"></a>Een Studio-werkruimte (klassiek) maken

1. Aanmelden bij de [Azure-portal](https://portal.azure.com/)

    > [!NOTE]
    > Als u zich wilt aanmelden en een Studio-werkruimte (klassieke) werkruimte wilt maken, moet u een Azure-abonnementsbeheerder zijn. 
    >
    > 

2. Klik **op +Nieuw**

3. Typ machine learning **studio (klassieke) werkruimte** in het zoekvak en selecteer het overeenkomende item. Klik vervolgens op **Maken** onder aan de pagina.

4. Voer uw werkruimtegegevens in:

   - De *naam van de werkruimte* kan maximaal 260 tekens zijn, die niet in een ruimte eindigen. De naam kan deze tekens niet bevatten:`< > * % & : \ ? + /`
   - Het *webserviceplan* dat u kiest (of maakt), samen met de bijbehorende *prijscategorie die* u selecteert, wordt gebruikt als u webservices implementeert vanuit deze werkruimte.

     ![Een nieuwe Studio-werkruimte (klassiek) maken](./media/create-workspace/create-new-workspace.png)

5. Klik **op Maken**.

> [!NOTE]
> Machine Learning Studio (klassiek) is afhankelijk van een Azure-opslagaccount dat u verstrekt om intermediaire gegevens op te slaan wanneer de werkstroom wordt uitgevoerd. Nadat de werkruimte is gemaakt, als het opslagaccount is verwijderd of als de toegangssleutels worden gewijzigd, werkt de werkruimte niet meer en mislukken alle experimenten in die werkruimte.
Als u het opslagaccount per ongeluk verwijdert, maakt u het opslagaccount met dezelfde naam opnieuw in hetzelfde gebied als het verwijderde opslagaccount en synchroniseert u de toegangssleutel opnieuw. Als u de toegangssleutel voor een opslagaccount hebt gewijzigd, moet u de toegangssleutel in Azure Portal opnieuw synchroniseren in de werkruimte.

Zodra de werkruimte is geïmplementeerd, u deze openen in Machine Learning Studio (klassiek).

1. Blader naar Machine Learning Studio [https://studio.azureml.net/](https://studio.azureml.net/)(klassiek) op .

2. Selecteer uw werkruimte in de rechterbovenhoek.

    ![Werkruimte selecteren](./media/create-workspace/open-workspace.png)

3. Klik op **mijn experimenten.**

    ![Open experimenten](./media/create-workspace/my-experiments.png)

Zie Een azure machine learning studio [(klassieke) werkruimte beheren](manage-workspace.md)voor informatie over het beheren van uw Studio(klassieke) werkruimte.
Zie [Probleemoplossingshandleiding: Maak en maak verbinding met een machine learning studio (klassieke) werkruimte](troubleshooting-creating-ml-workspace.md)als u een probleem ondervindt bij het maken van uw werkruimte.


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Een Azure Machine Learning Studio -werkruimte (klassieke) werkruimte delen
Zodra een Machine Learning Studio (klassieke) werkruimte is gemaakt, u gebruikers uitnodigen om uw werkruimte om toegang te delen tot uw werkruimte en al zijn experimenten, gegevenssets, notebooks, enz. U gebruikers toevoegen in een van de twee rollen:

* **Gebruiker** - Een gebruiker van een werkruimte kan experimenten, gegevenssets, enz.
* **Eigenaar** - Een eigenaar kan gebruikers in de werkruimte uitnodigen en verwijderen, naast wat een gebruiker kan doen.

> [!NOTE]
> Het beheerdersaccount waarmee de werkruimte wordt gemaakt, wordt automatisch toegevoegd aan de werkruimte als eigenaar van de werkruimte. Andere beheerders of gebruikers in dat abonnement krijgen echter niet automatisch toegang tot de werkruimte, u moet ze expliciet uitnodigen.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Een Studio-werkruimte (klassieke) werkruimte delen

1. Meld je aan bij Machine Learning Studio (klassiek) op[https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Klik in het linkerdeelvenster op **INSTELLINGEN**

3. Klik op het tabblad **GEBRUIKERS**

4. Klik onder aan de pagina op **MEER GEBRUIKERS UITNODIGEN**

    ![Studio-instellingen](./media/create-workspace/settings.png)

5. Voer een of meer e-mailadressen in. De gebruikers hebben een geldig Microsoft-account of een organisatieaccount nodig (uit Azure Active Directory).

6. Selecteer of u de gebruikers wilt toevoegen als eigenaar of gebruiker.

7. Klik **OK** op de knop OK-vinkje.

Elke gebruiker die u toevoegt, ontvangt een e-mail met instructies over hoe u zich aanmelden bij de gedeelde werkruimte.

> [!NOTE]
> Als gebruikers webservices in deze werkruimte kunnen implementeren of beheren, moeten ze een inzender of beheerder zijn in het Azure-abonnement. 



