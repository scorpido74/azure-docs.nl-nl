---
title: Microsoft Azure Data Box Disk zelf beheerde verzen ding | Microsoft Docs in gegevens
description: Beschrijving van een zelf beheerde verzend werk stroom voor Azure Data Box Disk apparaten
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: alkohli
ms.openlocfilehash: 7b069d1bf16e8cbf4dfc245fbb3c66863cd0b994
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055565"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Zelf-beheerde verzen ding voor Azure Data Box Disk gebruiken in de Azure Portal

In dit artikel worden zelf beheerde verzend taken beschreven voor het best Ellen, ophalen en verwijderen van Azure Data Box Disk. U kunt Data Box Disk beheren met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Zelf-beheerde verzen ding is beschikbaar als optie wanneer u [Azure data Box Disk order](data-box-disk-deploy-ordered.md). Zelf-beheerde verzen ding is alleen beschikbaar in de volgende regio's:

* Amerikaanse overheid
* West-Europa
* Japan
* Singapore
* Zuid-Korea
* Zuid-Afrika
* India (preview-versie)

## <a name="use-self-managed-shipping"></a>Zelfbeheerde verzending gebruiken

Wanneer u een Data Box Disk order plaatst, kunt u de optie voor zelf-beheerde verzen ding kiezen.

1. Selecteer in uw Azure Data Box Disk bestelling, onder de **contact gegevens**, **+ Verzend adres toevoegen**.

   ![Zelfbeheerde verzending](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Wanneer u verzend type kiest, selecteert u de optie **zelf beheerde verzen ding** . Deze optie is alleen beschikbaar als u zich in een ondersteunde regio bevindt, zoals beschreven in de vereisten.

3. Zodra u uw verzend adres hebt ingevoerd, moet u dit valideren en uw bestelling volt ooien.

   ![Zelfbeheerde verzending](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Zodra het apparaat is voor bereid en u een e-mail melding hebt ontvangen, kunt u een ophaling plannen. Ga in uw Azure Data Box Disk order naar **overzicht** en selecteer vervolgens **ophalen plannen**.

   ![Een Data Box-apparaat best Ellen voor ophalen](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Volg de instructies in de **planning ophalen voor Azure**. Voordat u uw autorisatie code kunt ophalen, moet u een e-mail sturen [adbops@microsoft.com](mailto:adbops@microsoft.com) om het ophalen van apparaten te plannen vanuit het Data Center van uw regio.

   ![Ophalen plannen](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Nadat u het ophalen van uw apparaten hebt gepland, kunt u uw autorisatie code bekijken in het  **ophalen van plannen voor Azure**.

   ![Uw autorisatie code weer geven](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Noteer deze **autorisatie code**.

   Volgens de beveiligings vereisten, op het moment van het plannen van de planning, is het nodig om de naam van de persoon op te geven die wordt opgehaald voor het ophalen.

   U moet ook details opgeven van wie gaat naar het Data Center voor ophalen. U of het contact punt moet een door de overheid goedgekeurde foto-ID hebben die wordt gevalideerd op het Data Center.

   Daarnaast moet de persoon die het apparaat ophaalt ook de **autorisatie code**hebben. De autorisatie code is uniek voor een ophaling of een afgifte en wordt gevalideerd op het Data Center.

7. Uw bestelling wordt automatisch verplaatst naar de status **opgehaald** wanneer het apparaat is opgehaald van het Data Center.

   ![Opgehaald](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Nadat het apparaat is opgehaald, kunt u gegevens naar de Data Box Disk (s) op uw site kopiëren. Wanneer het kopiëren van de gegevens is voltooid, kunt u voorbereiden om de Data Box Disk te verzenden.

   Wanneer u klaar bent met het kopiëren van gegevens, moet u contact opnemen met de bewerkingen om een afspraak voor de vervolg keuzelijst te plannen. U moet de gegevens van de persoon die binnenkomt op het Data Center delen om de schijven uit te kunnen zetten. Het Data Center moet ook de autorisatie code controleren op het moment van de vervolg keuzelijst. De autorisatie code voor uitschakeling is beschikbaar in Azure Portal onder **Schedule drop**.

   > [!NOTE]
   > Deel de autorisatie code niet via e-mail. Dit wordt alleen gecontroleerd bij het Data Center tijdens het verwijderen.

9. Als u een afspraak hebt ontvangen die u wilt verwijderen, moet u nu de status **gereed hebben om te ontvangen bij Azure Data Center** in de Azure Portal.

   ![Uw autorisatie code weer geven](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Nadat uw ID en autorisatie code zijn geverifieerd en u het apparaat in het Data Center hebt verwijderd, moet de status van de bestelling worden **ontvangen**.

    ![Ontvangen voltooid](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Zodra het apparaat is ontvangen, wordt het kopiëren van de gegevens voortgezet. Wanneer de Kopieer bewerking is voltooid, wordt de volg orde voltooid.

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids: Azure Data Box Disk implementeren met behulp van de Azure Portal](data-box-disk-quickstart-portal.md)
