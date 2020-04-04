---
title: Prijscategorie van naamruimte van meldingshubs wijzigen | Microsoft Documenten
description: Meer informatie over het wijzigen van de prijscategorie van een naamruimte van Azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/02/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 855a050afa14144f8963f24398c6b7b3939ef562
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656474"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Prijscategorie wijzigen van naamruimte azure-meldingshubs

Notification Hubs wordt aangeboden in drie lagen: **gratis,** **basic**en **standaard**. In dit artikel ziet u hoe u de prijscategorie voor een naamruimte van Azure Notification Hubs wijzigt.

## <a name="overview"></a>Overzicht

In Azure Notification Hubs is een hub de kleinste bron/entiteit. Het wordt over het algemeen toegewezen aan één toepassing en kan één certificaat bevatten voor elk Platform Notification System (PNS) dat we voor de app ondersteunen. De toepassing kan een hybride, of een native en een cross-platform applicatie.

Een **naamruimte** is een verzameling meldingshubs. Elke naamruimte bestaat meestal uit hubs die gerelateerd zijn en worden gebruikt voor een specifiek doel. U bijvoorbeeld drie verschillende naamruimten hebben voor respectievelijk ontwikkelings-, test- en productiedoeleinden.

U een naamruimte koppelen aan de **gratis,** **basis-** of **standaardprijsniveaus.** U de laag gebruiken voor een naamruimte die aan uw eisen voldoet. In de volgende secties ziet u hoe u de prijscategorie van een naamruimte voor meldingshubs wijzigt.

## <a name="use-azure-portal"></a>Azure Portal gebruiken

Wanneer u de Azure-portal gebruikt, u de prijscategorie voor een naamruimte op de naamruimtepagina of op een hubpagina wijzigen. Wanneer u deze wijzigt op een hubpagina, wijzigt u deze op naamruimteniveau. Hiermee wordt de prijscategorie voor de naamruimte en alle hubs in de naamruimte gewijzigd.

### <a name="change-tier-on-the-namespace-page"></a>Laag wijzigen op de pagina naamruimte

In de volgende procedure ziet u hoe u de prijscategorie voor een naamruimte op de pagina naamruimte wijzigen. Wanneer u de laag voor een naamruimte wijzigt, is deze van toepassing op alle hubs in die naamruimte.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** in het linkermenu.
3. Selecteer **Naamruimten voor meldingshub in** de sectie **Internet of Things.** Als u het sterretje`*`( ) naast de tekst selecteert, wordt deze toegevoegd aan de linkernavigatiebalk onder **FAVORIETEN**. Hiermee u sneller toegang krijgen tot de pagina naamruimten. Nadat u deze hebt toegevoegd aan FAVORIETEN, selecteert u **Naamruimten voor meldingshub .**

    ![Alle services > naamruimten van de meldingshub](./media/change-pricing-tier/all-services-nhub.png)

4. Selecteer op de pagina **Naamruimten voor meldingshub** de naamruimte waarvoor u de prijscategorie wilt wijzigen.
5. Op de pagina **Naamruimte van de meldingshub** voor uw naamruimte ziet u de huidige prijscategorie voor de naamruimte in de sectie **Essentials.** In de volgende afbeelding u zien dat de prijscategorie van de naamruimte **gratis**is.

    ![Huidige prijscategorie op de pagina naamruimte](./media/change-pricing-tier/pricing-tier-before.png)

6. Selecteer op de pagina **Naamruimte van de meldingshub** voor uw naamruimte de optie **Prijslaag** onder sectie **Beheren.**

    ![Prijscategorie selecteren op de pagina naamruimte](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Wijzig de prijscategorie en klik op de knop **Selecteren.**
8. U de status van de actie laagwijziging in de **waarschuwingen**zien.
9. Ga naar de **pagina Overzicht.** Controleer of de nieuwe laag wordt weergegeven voor het veld **Prijslaag** in de sectie **Essentials.**
10. Deze stap is optioneel. Selecteer een hub in de naamruimte. Controleer of u dezelfde prijscategorie ziet in de sectie **Essentials.** U ziet dezelfde prijscategorie voor alle hubs in de naamruimte.

### <a name="change-tier-on-the-hub-page"></a>Laag wijzigen op de hubpagina

In de volgende procedure ziet u hoe u de prijscategorie voor een naamruimte op de hubpagina wijzigt. Hoewel u deze stappen vanaf de hubpagina doet, wijzigt u de prijscategorie voor de naamruimte en alle hubs in de naamruimte.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** in het linkermenu.
3. Selecteer **Meldingshubs** in de sectie **Internet of Things.**
4. Selecteer uw **meldingshub**.
5. Selecteer **Prijsniveau** in het linkermenu.
6. Wijzig de prijscategorie en klik op de knop **Selecteren.** Met deze actie wordt de instelling voor de prijslaag voor de naamruimte die de hub bevat, gewijzigd. U ziet dus de nieuwe prijscategorie op de pagina naamruimte en alle hubpagina's.

> [!NOTE]
> Alle wijzigingen in de prijsniveau zijn onmiddellijk van kracht.

## <a name="use-rest-api"></a>REST API gebruiken

U de volgende API's voor resourceproviderREST gebruiken om de huidige prijscategorie op te halen en bij te werken.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Huidige prijscategorie voor een naamruimte opvragen

Als u de huidige naamruimtelaag wilt krijgen, verzendt u een opdracht GET, zoals in het volgende voorbeeld wordt weergegeven:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Prijscategorie bijwerken voor een naamruimte

Als u de naamruimtelaag wilt bijwerken, verzendt u een opdracht PUT, zoals in het volgende voorbeeld wordt weergegeven:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Volgende stappen

Zie [Notificatiehubs-prijzen](https://azure.microsoft.com/pricing/details/notification-hubs/)voor meer informatie over deze lagen en prijzen.
