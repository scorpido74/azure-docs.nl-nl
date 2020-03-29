---
title: Hoe beheer je instellingen? - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Instellingen beheren, werkruimte maken, werkruimte delen en abonnementssleutel beheren in Aangepaste vertaler.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219509"
---
# <a name="how-to-manage-settings"></a>Instellingen beheren

Binnen de pagina Instellingen voor aangepaste vertalers u een nieuwe werkruimte maken, uw werkruimte delen en uw Microsoft Translation-abonnementssleutel toevoegen of wijzigen.

Ga als instellen op de pagina Instellingen:

1. Meld u aan bij de [portal Voor aangepaste vertalers.](https://portal.customtranslator.azure.ai/)
2. Klik op de portal Aangepaste vertaler op het tandwielpictogram in de zijbalk.

    ![Koppeling instellen](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Microsoft Translator-abonnement koppelen

U moet een Microsoft Translator Text API-abonnementssleutel hebben die aan uw werkruimte is gekoppeld om modellen te trainen of te implementeren.

Als je geen abonnement hebt, volg je de onderstaande stappen:

1. Abonneer u op de Microsoft Translator Text API. In dit artikel ziet u hoe u zich abonneren op de Microsoft Translator Text API.
2. Let op de sleutel voor uw vertalerabonnement. Een van de Key1 of Key2 zijn aanvaardbaar.
3. Navigeer terug naar de portal Voor aangepaste vertalers.

### <a name="add-existing-key"></a>Bestaande sleutel toevoegen

1.  Navigeer naar de pagina 'Instellingen' voor uw werkruimte.
2.  Klik op Sleutel toevoegen

    ![Abonnementssleutel toevoegen](media/how-to/how-to-add-subscription-key.png)

3. Voer in het dialoogvenster de sleutel voor uw abonnement op vertalers in en klik vervolgens op de knop Toevoegen.

    ![Abonnementssleutel toevoegen](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Nadat u een sleutel hebt toegevoegd, u de sleutel op elk gewenst moment wijzigen of verwijderen.

    ![Abonnementssleutel na toevoegen](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Uw werkruimte beheren

Een werkruimte is een werkgebied voor het samenstellen en bouwen van uw aangepaste vertaalsysteem. Een werkruimte kan meerdere projecten, modellen en documenten bevatten.

Als een ander deel van uw werk met verschillende personen moet worden gedeeld, kan het nuttig zijn om meerdere werkruimten te maken.

## <a name="create-a-new-workspace"></a>Een nieuwe werkruimte maken

1.  Navigeer naar de pagina 'Instellingen' van de werkruimte.
2.  Klik op de knop Nieuwe werkruimte in de sectie Nieuwe werkruimte maken.

    ![Nieuwe werkruimte maken](media/how-to/create-new-workspace.png)

4.  Voer in het dialoogvenster de naam van de nieuwe werkruimte in.
5.  Klik op 'Maken'.

    ![Dialoogvenster Nieuwe werkruimte maken](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Uw werkruimte delen

In Aangepaste vertaler u uw werkruimte delen met anderen, als een ander deel van uw werk met verschillende mensen moet worden gedeeld.

1.  Navigeer naar de pagina 'Instellingen' van de werkruimte.
2.  Klik op de knop 'Delen' in de sectie Instellingen delen.

    ![Werkruimte delen](media/how-to/share-workspace.png)

3.  Voer in het dialoogvenster een door komma's gescheiden lijst met e-mailadressen in waarmee u deze werkruimte wilt delen. Zorg ervoor dat u deelt met het e-mailadres waarmee de persoon wordt gebruikt om zich aan te melden bij Aangepaste vertaler. Selecteer vervolgens het juiste niveau van machtigingen voor delen.

4.  Als uw werkruimte nog steeds de standaardnaam 'Mijn werkruimte' heeft, moet u deze wijzigen voordat u uw werkruimte deelt.
5.  Klik op Opslaan.

## <a name="sharing-permissions"></a>Machtigingen voor delen

1.  **Lezer:** Een lezer in de werkruimte kan alle informatie in de werkruimte bekijken.

2.  **Redacteur:** Een editor in de werkruimte kan documenten toevoegen, modellen trainen en documenten en projecten verwijderen. Ze kunnen een abonnementssleutel toevoegen, maar kunnen niet wijzigen met wie de werkruimte wordt gedeeld, de werkruimte verwijderen of de naam van de werkruimte wijzigen.

3.  **Eigenaar:** Een eigenaar heeft volledige machtigingen voor de werkruimte.

## <a name="change-sharing-permission"></a>Machtigingen voor delen wijzigen

Wanneer een werkruimte wordt gedeeld, worden in de sectie Instellingen voor delen alle e-mailadressen weergegeven waarmee deze werkruimte wordt gedeeld. U de bestaande machtigingen voor delen voor elk e-mailadres wijzigen als u eigenaar toegang hebt tot de werkruimte.

1.  In de sectie 'Instellingen delen' voor elke e-mail wordt in een vervolgkeuzemenu het huidige machtigingsniveau weergegeven.

2.  Klik op het vervolgkeuzemenu en selecteer het nieuwe machtigingsniveau dat u aan dat e-mailadres wilt toewijzen.

    ![Machtigingsinstellingen voor delen](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Volgende stappen

- Meer [informatie over het migreren](how-to-migrate.md) van uw werkruimte en project vanuit Microsoft Translator [Hub](https://hub.microsofttranslator.com)
