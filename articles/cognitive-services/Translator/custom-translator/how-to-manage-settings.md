---
title: Hoe kunt u instellingen beheren? -Aangepaste Translator
titleSuffix: Azure Cognitive Services
description: Het beheren van instellingen, het maken van de werk ruimte, het delen van de werk ruimte en het beheren van de abonnements sleutel in Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381791"
---
# <a name="how-to-manage-settings"></a>Instellingen beheren

U kunt op de pagina aangepaste Vertaal instellingen een nieuwe werk ruimte maken, uw werk ruimte delen en uw micro soft-Vertaal abonnements sleutel toevoegen of wijzigen.

Toegang krijgen tot de pagina instellingen:

1. Meld u aan bij de portal voor [aangepaste vertalers](https://portal.customtranslator.azure.ai/) .
2. Klik in de aangepaste Vertaal Portal op het tandwiel pictogram in de zijbalk.

    ![Koppeling instellen](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Micro soft Translator-abonnement koppelen

U moet beschikken over een micro soft Translator Text-API Subscription-sleutel die is gekoppeld aan uw werk ruimte om modellen te trainen of implementeren.

Als u geen abonnement hebt, volgt u de onderstaande stappen:

1. Abonneer u op de micro soft-Translator Text-API. In dit artikel wordt beschreven hoe u zich kunt abonneren op de micro soft-Translator Text-API.
2. Let op de sleutel voor uw Vertaal abonnement. Een van de Key1 of Key2 is acceptabel.
3. Ga terug naar de portal voor aangepaste vertalers.

### <a name="add-existing-key"></a>Bestaande sleutel toevoegen

1.  Ga naar de pagina instellingen voor uw werk ruimte.
2.  Klik op sleutel toevoegen

    ![Abonnements sleutel toevoegen](media/how-to/how-to-add-subscription-key.png)

3. Voer in het dialoog venster de sleutel voor uw Vertaal abonnement in en klik vervolgens op de knop toevoegen.

    ![Abonnements sleutel toevoegen](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Nadat u een sleutel hebt toegevoegd, kunt u de sleutel op elk gewenst moment wijzigen of verwijderen.

    ![Abonnements sleutel na toevoegen](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Uw werk ruimte beheren

Een werk ruimte is een werk ruimte voor het opstellen en bouwen van uw aangepaste Vertaal systeem. Een werk ruimte kan meerdere projecten, modellen en documenten bevatten.

Als een ander deel van uw werk moet worden gedeeld met andere personen, kan het handig zijn om meerdere werk ruimten te maken.

## <a name="create-a-new-workspace"></a>Een nieuwe werkruimte maken

1.  Navigeer naar de pagina instellingen van de werk ruimte.
2.  Klik op de knop nieuwe werk ruimte in de sectie ' nieuwe werk ruimte maken '.

    ![Nieuwe werk ruimte maken](media/how-to/create-new-workspace.png)

4.  Voer in het dialoog venster de naam van de nieuwe werk ruimte in.
5.  Klik op maken.

    ![Dialoog venster nieuwe werk ruimte maken](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Uw werk ruimte delen

In aangepaste vertaler kunt u uw werk ruimte delen met anderen, als een ander deel van uw werk moet worden gedeeld met andere personen.

1.  Navigeer naar de pagina instellingen van de werk ruimte.
2.  Klik op de knop delen in de sectie instellingen voor delen.

    ![Werk ruimte delen](media/how-to/share-workspace.png)

3.  Voer in het dialoog venster een door komma's gescheiden lijst met e-mail adressen in die met deze werk ruimte moeten worden gedeeld. Zorg ervoor dat u deelt met het e-mail adres dat wordt gebruikt om u aan te melden bij een aangepaste vertaler met. Selecteer vervolgens het juiste niveau van de machtiging voor delen.

4.  Als uw werk ruimte nog steeds de standaard naam mijn werk ruimte heeft, moet u deze wijzigen voordat u uw werk ruimte deelt.
5.  Klik op opslaan.

## <a name="sharing-permissions"></a>Machtigingen voor delen

1.  **Lezer:** Met een lezer in de werk ruimte kunnen alle gegevens in de werk ruimte worden weer gegeven.

2.  **Redacteur:** Met een editor in de werk ruimte kunnen documenten worden toegevoegd, modellen worden getraind en documenten en projecten worden verwijderd. Ze kunnen een abonnements sleutel toevoegen, maar kunnen niet wijzigen met wie de werk ruimte wordt gedeeld, de werk ruimte verwijderen of de naam van de werk ruimte wijzigen.

3.  **Eigenaar:** Een eigenaar heeft volledige machtigingen voor de werk ruimte.

## <a name="change-sharing-permission"></a>Machtiging voor delen wijzigen

Wanneer een werk ruimte wordt gedeeld, worden in de sectie instellingen delen alle e-mail adressen weer gegeven waarop deze werk ruimte is gedeeld. U kunt de bestaande machtiging voor delen voor elk e-mail adres wijzigen als u de eigenaar toegang tot de werk ruimte hebt.

1.  In de sectie instellingen voor delen voor elk e-mail bericht wordt het huidige machtigings niveau weer gegeven.

2.  Klik op het vervolg keuzemenu en selecteer het nieuwe machtigings niveau dat u wilt toewijzen aan het e-mail adres.

    ![Instellingen voor delen van machtigingen](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over het migreren van uw werk ruimte en project](how-to-migrate.md) van [micro soft Translator hub](https://hub.microsofttranslator.com)
