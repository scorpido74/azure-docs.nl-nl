---
title: Beheer van leads in de Market configureren | Azure Marketplace
description: Configureer het beheer van leads voor de klanten van Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288526"
---
# <a name="configure-lead-management-in-marketo"></a>Beheer van leads in de Market configureren

In dit artikel wordt beschreven hoe u Marketo instelt voor het afhandelen van verkoop leads van micro soft.

1. Meld u aan bij Marketo.
2. Selecteer **Design Studio**.
    ![Marketo-ontwerp studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Selecteer **nieuw formulier**.
    ![Nieuw formulier voor marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Vul de vereiste velden in het nieuwe formulier in en selecteer vervolgens **maken**.
    ![Marketo-nieuw formulier maken](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Selecteer **volt ooien**op veld Details.
    ![Formulier marketo-afwerking](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Goed keuren en sluiten.

6.  Selecteer **code insluiten**op het tabblad MarketplaceLeadBacked.
    ![Optie marketo-insluit code](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Met marketo-insluit code wordt code weer gegeven die vergelijkbaar is met het volgende voor beeld.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Kopieer de waarden die worden weer gegeven in de insluit code zodat u de **Server-id**, de **Munchkin-id**en de **formulier-id** in de marketo-velden op het Cloud Partner-Portal kunt configureren.

Gebruik het volgende voor beeld als richt lijn voor het ophalen van de Id's die u nodig hebt via het code voorbeeld van de Marketo-invoeg toepassing.

- Server-id = **ys12**
- Munchkin-id = **123-PQR-789**
- Formulier-id = **1179**
