---
title: Registreren met behulp van CSP-partner gegevens met Cloudyn in azure
description: In deze quickstart vindt u de details van de registratieprocedure voor het maken van een Cloudyn-proefabonnement en het aanmelden bij de Cloudyn-portal.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/18/2019
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
manager: benshy
ms.openlocfilehash: 420bca89903b3b88d7930165f95742ebb4f74d5d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987668"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registreren met het CSP-partnerprogramma en kostengegevens weergeven

Als CSP-partner kunt u zich registreren bij Cloudyn. Uw registratie biedt toegang tot de Cloudyn-portal. In deze quickstart vindt u de details van de registratieprocedure voor het maken van een Cloudyn-proefabonnement en het aanmelden bij de Cloudyn-portal. U vindt er ook informatie over het weergeven van gegevenskosten.


> [!NOTE]
>
> Alleen directe CSP-partners en indirecte CSP-providers kunnen zich bij Cloudyn registeren.
>
> Voor verificatie en toegang tot gegevens is het vereist de Partner Center API te configureren. Een Partner Center Global Administrator-account is nodig voor het inrichten van de API-toegang.
> Zie [Verbinding maken met de Partner Center API](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx) voor meer informatie.
>
> Indirecte CSP-resellers kan toegang worden gegeven tot Cloudyn nadat hun indirecte CSP-provider zich bij Cloudyn heeft geregistreerd. Indirecte CSP-resellers kunnen vervolgens zorgen dat Azure-klanten en abonnementen toegang hebben tot Cloudyn.
>
>Cloudy is een klacht met het micro soft Secure Application model. Zie [het Framework van het veilige toepassings model inschakelen](/partner-center/develop/enable-secure-app-model)voor meer informatie.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registreren bij Cloudyn

1. Klik in Azure Portal, in de lijst met services, op **Cost Management en facturering**.
2. Klik onder **Overzicht** op **Cloudyn**  
    ![Cloudyn-pagina die wordt weergegeven in Azure Portal](./media/quick-register-csp/cost-mgt-billing-service.png)
3. Klik op de **Cloudyn**-pagina op **Go to Cloudyn** om de registratiepagina van Cloudyn te openen in een nieuw venster.
4. Typ op de pagina voor de registratie van het proefabonnement in de Cloudyn-portal de naam van uw bedrijf en selecteer **Microsoft CSP Partner Program Administrator** en klik op **Volgende**.  
5. Voer een **toepassings-id**, een **handels-id**, **geheime sleutel van de toepassing** in, en selecteer het **standaardprijsplan**. Als u de gegevens niet bij die hand hebt, meldt u zich aan bij de portal Partnercentrum [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) met uw primaire beheerdersaccount en voert u de volgende stappen uit:
   1. Ga naar **Dashboard** en klik achtereenvolgens op het symbool **Instellingen**, **Partnerinstellingen**en **App-beheer**.
   2. Als u eerder al een web-app hebt gemaakt, kunt u deze stap overslaan. Klik anders op **Nieuwe web-app toevoegen** in de sectie **Web-app**.
   3. Kopieer de **app-id**-GUID van uw webtoepassing.
   4. Kopieer de **handels-id**-GUID van uw webtoepassing.
   5. Selecteer de geldigheidsduur van de sleutel desgewenst voor één of twee jaar. Selecteer **Sleutel toevoegen**, kopieer vervolgens de waarde voor de geheime sleutel en sla deze op.  
    ![Partnerdashboard waar u de referentiegegevens kopieert](./media/quick-register-csp/csp-partner-center.png)
   6. Ga terug naar de Cloudyn-registratiepagina en plak de gegevens erin.  
      ![De referentiegegevens in de Cloudyn-registratiepagina plakken](./media/quick-register-csp/csp-reg.png)
6. Accepteer de Gebruiksvoorwaarden en controleer uw gegevens. Klik op **Volgende** om Cloudyn te autoriseren Azure-brongegevens te verzamelen. De gegevens die worden verzameld zijn onder meer gegevens over gebruik, prestaties, facturering en tags van uw abonnementen.  
7. Onder **Invite other stakeholders** kunt u gebruikers toevoegen door hun e-mailadressen in te voeren. Klik op **Volgende** als u klaar bent. Het duurt ongeveer twee uur voordat uw factureringsgegevens aan Cloudyn zijn toegevoegd.
8. Klik op **Go to Cloudyn** om de Cloudyn-portal te openen. Op de pagina **Cloud Accounts Management** zou u de geregistreerde CSP-accountgegevens moeten kunnen zien.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Indirecte CSP-toegang in Cloudyn configureren

De Partner Center API is standaard alleen toegankelijk voor directe CSP's. Een directe CSP-provider kan echter toegang configureren voor hun indirecte CSP-klanten of -partners met behulp van entiteitsgroepen in Cloudyn.

Als u toegang wilt verlenen voor indirecte CSP-klanten of-partners, volgt u de stappen in [registreren bij Cloudyn](#register-with-cloudyn) om een proef registratie in te stellen. Voer vervolgens de volgende stappen uit voor het segmenteren van indirecte CSP-gegevens met behulp van Cloudyn-entiteitsgroepen. Wijs daarna de juiste gebruikersmachtigingen toe aan de entiteitsgroepen.

1. Maak met deze gegevens een entiteitsgroep via [Entiteiten maken](tutorial-user-access.md#create-and-manage-entities).
2. Volg de stappen in [Abonnementen toewijzen aan kostentiteiten](https://www.youtube.com/watch?v=d9uTWSdoQYo). Koppel de indirecte CSP-klant en de bijbehorende Azure-abonnementen aan de entiteit die u eerder hebt gemaakt.
3. Volg de stappen in [Een gebruiker met beheerderstoegang maken](tutorial-user-access.md#create-a-user-with-admin-access) om een gebruikersaccount met beheerderstoegang te maken. Controleer vervolgens of het gebruikersaccount beheerderstoegang heeft tot de specifieke entiteiten die u eerder hebt gemaakt voor het indirecte account.

Indirecte CSP-partners kunnen zich aanmelden bij de Cloudyn-portal met behulp van de accounts die u voor hen hebt gemaakt.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de CSP-gegevens van uw Azure-abonnement gebruikt om u te registreren bij Cloudyn. U hebt zich ook aangemeld bij de Cloudyn-portal en de gegevenskosten weergegeven. Ga voor meer informatie over Cloudyn verder met de zelfstudie voor Cloudyn.

> [!div class="nextstepaction"]
> [Gebruik en kosten controleren](tutorial-review-usage.md)
