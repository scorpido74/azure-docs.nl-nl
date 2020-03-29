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
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 51d0880b7f56d523a01fbc993993b3caf7328134
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261072"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Prijscategorie wijzigen van naamruimte azure-meldingshubs
Notification Hubs wordt aangeboden in drie lagen: **gratis,** **basic**en **standaard**. In dit artikel ziet u hoe u de prijscategorie voor een naamruimte van Azure Notification Hubs wijzigt. 

## <a name="overview"></a>Overzicht
In Azure Notification Hubs is een **hub** de kleinste bron/entiteit. Het wordt over het algemeen toegewezen aan één toepassing en kan één certificaat bevatten voor elk platformmeldingssysteem dat we voor de app ondersteunen. De applicatie kan een hybride of een native en een cross-platform applicatie.

Een **naamruimte** is een verzameling meldingshubs. Elke naamruimte bestaat meestal uit hubs die gerelateerd zijn en worden gebruikt voor een specifiek doel. U bijvoorbeeld drie verschillende naamruimten hebben voor respectievelijk ontwikkelings-, test- en productiedoeleinden. 

U een prijscategorie koppelen aan het naamruimteniveau. Notification Hubs ondersteunt drie lagen: **gratis,** **basic**en **standaard**. U de laag gebruiken voor een naamruimte die aan uw eisen voldoet. In de volgende secties ziet u hoe u de prijscategorie van een naamruimte voor meldingshubs wijzigt. 

## <a name="use-azure-portal"></a>Azure Portal gebruiken 
Wanneer u Azure-portal gebruikt, u de prijscategorie voor een naamruimte op de naamruimtepagina of een hubpagina wijzigen.  Wanneer u deze wijzigt op een hubpagina, wijzigt u deze op naamruimteniveau. Hiermee wordt de prijscategorie voor de naamruimte en alle hubs in de naamruimte gewijzigd. 

### <a name="change-tier-on-the-namespace-page"></a>Laag wijzigen op de pagina naamruimte
Met de volgende procedure u de prijscategorie voor een naamruimte op de pagina naamruimte wijzigen. Wanneer u de laag voor een naamruimte wijzigt, is deze van toepassing op alle hubs in de naamruimte.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** in het linkermenu. 
3. Selecteer **Naamruimten voor meldingshub in** de sectie **Internet of Things.** Als u ster`*`( ) naast de tekst selecteert, wordt deze toegevoegd aan de linkernavigatiebalk onder **FAVORIETEN**. Het helpt u met het openen van de naamruimten pagina sneller de volgende keer verder. Nadat u deze hebt toegevoegd aan de favorieten, selecteert u **Naamruimten voor meldingshub .** 

    ![Alle services > naamruimten van de meldingshub](./media/change-pricing-tier/all-services-nhub.png)
1. Selecteer op de pagina **Naamruimten voor meldingshub** de naamruimte waarvoor u de prijscategorie wilt wijzigen. 
2. Op de pagina **Naamruimte van de meldingshub** voor uw naamruimte ziet u de huidige prijscategorie voor de naamruimte in de sectie **Essentials.** In de volgende afbeelding u zien dat de prijscategorie van de naamruimte **gratis**is. 

    ![Huidige prijscategorie op de pagina naamruimte](./media/change-pricing-tier/pricing-tier-before.png)
1. Selecteer op de pagina **Naamruimte van de meldingshub** voor uw naamruimte de optie **Prijslaag** onder sectie **Beheren.** 

    ![Prijscategorie selecteren op de pagina naamruimte](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Wijzig uw prijscategorie en klik op **selecteren.**    
7. U ziet de status van de actie laagwijziging in de **waarschuwingen**. 
8. Ga naar de **pagina Overzicht.** Controleer of de nieuwe laag wordt weergegeven voor het veld **Prijslaag** in de sectie **Essentials.**     
1. Deze stap is optioneel. Selecteer een hub in de naamruimte. Controleer of u dezelfde prijscategorie ziet in de sectie **Essentials.** U ziet dezelfde prijscategorie voor alle hubs in de naamruimte. 

### <a name="change-tier-on-the-hub-page"></a>Laag wijzigen op de hubpagina
De volgende procedure geeft u stappen om de prijscategorie voor een naamruimte op de hubpagina te wijzigen. Hoewel u deze stappen vanaf de hubpagina doet, wijzigt u de prijscategorie voor de naamruimte en alle hubs in de naamruimte. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** in het linkermenu.
3. Selecteer **Meldingshubs** in de sectie **Internet of Things.** 
4. Selecteer uw **meldingshub**. 
5. Selecteer **Prijsniveau** in het linkermenu. 
6. Wijzig de prijscategorie en klik op de knop **Selecteren.** Met deze actie wordt de instelling voor de prijslaag voor de naamruimte die de hub bevat, gewijzigd. U ziet dus de nieuwe prijscategorie op de pagina naamruimte en alle hubpagina's. 

## <a name="use-rest-api"></a>REST API gebruiken
U de volgende API's voor resourceproviderREST gebruiken om de huidige prijscategorie op te halen en bij te werken. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Huidige prijscategorie voor een naamruimte opvragen
Als u de **huidige naamruimtelaag wilt**krijgen, verzendt u een opdracht GET zoals weergegeven in het volgende voorbeeld: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Prijscategorie bijwerken voor een naamruimte
Als **u de naamruimtelaag wilt bijwerken,** verzendt u een opdracht PUT zoals weergegeven in het volgende voorbeeld: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Volgende stappen
Zie [Notificatiehubs-prijzen](https://azure.microsoft.com/pricing/details/notification-hubs/)voor meer informatie over deze lagen en prijzen.
