---
title: Azure peering-service registreren-Azure Portal
description: Meer informatie over het registreren van de Azure peering-service met behulp van de Azure Portal
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 6646016a4529f46431ecb622c7546140a93a5d88
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84872144"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>De peering-service registreren met behulp van de Azure Portal

Azure peering service is een netwerk service die de connectiviteit van klanten verbetert met micro soft-Cloud Services, zoals Office 365, Dynamics 365, software as a Service (SaaS)-Services, Azure of andere micro soft-services die toegankelijk zijn via het open bare Internet.

In dit artikel leert u hoe u een peering service-verbinding kunt registreren met behulp van de Azure Portal.

Als u nog geen abonnement op Azure hebt, maak dan nu een [account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

> 

## <a name="prerequisites"></a>Vereisten

U moet het volgende hebben:

### <a name="azure-account"></a>Azure-account

U moet een geldig en actief Microsoft Azure-account hebben. Dit account is vereist voor het instellen van de peering-service verbinding. Peering-service is een resource binnen Azure-abonnementen. 

### <a name="connectivity-provider"></a>Connectiveitsprovider

U kunt samen werken met een Internet serviceprovider of een Internet Exchange-partner om peering service te verkrijgen om verbinding te maken met uw netwerk met het micro soft-netwerk.

Zorg ervoor dat de [connectiviteits providers](location-partners.md) zijn gekoppeld aan micro soft.



## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Ga in een browser naar de Azure Portal en meld u aan met uw Azure-account.

## <a name="register-a-peering-service-connection"></a>Een Peering Service-verbinding registreren

1. Als u een peering service-verbinding wilt registreren, selecteert u **een resource**  >  **peering-service**maken.

    ![Peering Service registreren](./media/peering-service-portal/peering-servicecreate.png)
1. Voer de volgende gegevens in op het tabblad **basis beginselen** op de pagina **een peering-service verbinding maken** .

 
1. Selecteer het abonnement en de resource groep die aan het abonnement is gekoppeld.

   ![Tabblad basis van peering registreren](./media/peering-service-portal/peering-servicebasics.png)

1. Voer een **naam** in voor het peering service-exemplaar dat moet worden geregistreerd.
 
1. Selecteer nu de knop **volgende: Configuratie** onder aan de pagina. De pagina **configuratie** wordt weer gegeven.

## <a name="configure-the-peering-service-connection"></a>De peering service-verbinding configureren

1. Selecteer op de pagina **configuratie** de locatie waar de peering-service moet worden ingeschakeld door dezelfde optie te selecteren in de vervolg keuzelijst **service locatie van peering** .

1. Selecteer de service provider waarvan de peering service moet worden verkregen door een provider naam te selecteren in de vervolg keuzelijst van de **peering-service provider** .
 
1. Selecteer **Nieuw voor voegsel maken** onder aan de sectie voor **voegsels** en tekst vakken worden weer gegeven. Voer nu de naam in van de voorvoegsel resource en de voor voegsels die zijn gekoppeld aan de service provider.

1. Selecteer **prefix sleutel** en voeg de prefix sleutel toe die u hebt ontvangen van uw provider (ISP of IXP). Met deze sleutel kan MS het voor voegsel en de provider valideren die uw IP-voor voegsel hebben toegewezen.
   > ![Tabblad Configuratie van peering-service registreren](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selecteer de knop **beoordeling + maken** linksonder op de pagina. De pagina **controleren + maken** wordt weer gegeven en Azure valideert uw configuratie.
    

1. Wanneer het bericht **door gegeven validatie** wordt weer gegeven, selecteert u **maken**.

   > ![Tabblad Configuratie van peering-service registreren](./media/peering-service-portal/peering-service-prefix.png)


1. Nadat u een peering service-verbinding hebt geregistreerd, wordt er aanvullende validatie uitgevoerd voor de meegeleverde voor voegsels. U kunt de validatie status bekijken in de sectie voor **voegsels** van de resource naam. Als de validatie mislukt, wordt een van de volgende fout berichten weer gegeven:

   - Ongeldig voor voegsel voor peering-service, het voor voegsel moet een geldige indeling zijn. alleen IPv4-voor voegsel wordt ondersteund.
   - Er is geen voor voegsel ontvangen van de peering-service provider.
   - De voorvoegsel aankondiging heeft geen geldige BGP-community. Neem contact op met de peering service provider.
   - De back-uproute is niet gevonden. Neem contact op met de peering service provider.
   - Het voor voegsel dat langer is ontvangen als pad, neemt contact op met de peering service provider.
   - Het voor voegsel dat is ontvangen met privé als in het pad, neemt contact op met de peering service provider.

### <a name="add-or-remove-a-prefix"></a>Een voor voegsel toevoegen of verwijderen

Selecteer voor voegsels **toevoegen** op de pagina voor **voegsels** om voor voegsels toe te voegen.

Selecteer het beletsel teken (...) naast het vermelde voor voegsel en selecteer de optie **verwijderen** .

### <a name="delete-a-peering-service-connection"></a>Een peering service-verbinding verwijderen

Schakel op de pagina **alle resources** het selectie vakje in de peering-service in en selecteer de optie **verwijderen** boven aan de pagina.

> [!NOTE]
> U kunt een bestaand voor voegsel niet wijzigen.
>

## <a name="next-steps"></a>Volgende stappen

- Zie [verbinding met peering service](connection.md)voor meer informatie over peering service-verbinding.
- Zie [telemetrie-verbinding met peering service](connection-telemetry.md)voor meer informatie over de telemetrie van de peering-service verbinding.
- Zie [verbindings-telemetrie meten](measure-connection-telemetry.md)om telemetrie te meten.
- Als u de verbinding wilt registreren met behulp van Azure PowerShell, raadpleegt u [een peering service-verbinding registreren-Azure PowerShell](powershell.md).
- Als u de verbinding wilt registreren met behulp van de Azure CLI, raadpleegt u [een peering service-verbinding registreren-Azure cli](cli.md).
