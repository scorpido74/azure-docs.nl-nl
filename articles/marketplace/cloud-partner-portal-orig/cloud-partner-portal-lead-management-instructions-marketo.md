---
title: Leadmanagement configureren in Marketo | Azure Marketplace
description: Configureer leadbeheer voor Marketo voor Azure-marktplaatsklanten.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288526"
---
# <a name="configure-lead-management-in-marketo"></a>Leadmanagement configureren in Marketo

In dit artikel wordt beschreven hoe u Marketo instelt om microsoft-verkoopleads te verwerken.

1. Log hier in
2. Selecteer **Ontwerpstudio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Selecteer **Nieuw formulier**.
    ![Marketo nieuwe vorm](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Vul de vereiste velden in het nieuwe formulier en selecteer **Vervolgens Maken**.
    ![Marketo maakt nieuwe vorm](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Selecteer **Voltooien**in Velddetails .
    ![Marketo finishformulier](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Goedkeuren en sluiten.

6.  Selecteer op het tabblad MarketplaceLeadBacked de optie **Code insluiten**.
    ![Marketo-insluitcode, optie](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo Embed Code geeft code weer die vergelijkbaar is met het volgende voorbeeld.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Kopieer de waarden in Embed Code, zodat u de **server-id,** **Munchkin Id**en **Formulier-id** configureren in de velden Marketo op de Cloud Partner Portal.

Gebruik het volgende voorbeeld als een handleiding voor het verkrijgen van de id's die u nodig hebt in het voorbeeld Marketo Embed Code.

- Server-id = **ys12**
- Munchkin Id = **123-PQR-789**
- Formulier-id = **1179**
