---
title: 'Zelfstudie: Toegang beheren met Cloudyn in Azure | Microsoft Docs'
description: In deze zelfstudie leest u hoe u de toegang tot gegevens van Cloudyn kunt beheren met behulp van gebruikersaccounts die toegangsniveaus voor entiteiten bepalen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: bd778e3731780a89560d8caa02363a4e49f77e20
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219056"
---
# <a name="tutorial-assign-access-to-cloudyn-data"></a>Zelfstudie: Toegang beheren tot Cloudyn-gegevens

De toegang tot gegevens van Cloudyn wordt beheerd door middel van gebruikers of -entiteitsbeheer. Gebruikersaccounts van Cloudyn bepalen de toegang tot *entiteiten* en beheerfuncties. Er zijn twee soorten toegang: beheerder en gebruiker. Tenzij dit per gebruiker is gewijzigd, biedt beheerderstoegang gebruikers onbeperkte toegang tot alle functies in de portal van Cloudyn, met inbegrip van: beheer van gebruikers en lijsten met ontvangers, toegang als hoofdentiteit tot alle entiteitsgegevens. Gebruikerstoegang is bedoeld voor eindgebruikers om rapporten weer te geven en om rapporten te maken met de toegang die ze hebben tot entiteitsgegevens.

Entiteiten worden gebruikt om de hiërarchische structuur van de organisatie van uw bedrijf voor te stellen en vertegenwoordigen afdelingen, divisies en teams in uw organisatie in Cloudyn. De entiteitshiërarchie helpt u om nauwkeurig de uitgave bij te houden van de verschillende entiteiten.

Op het moment dat u uw Azure overeenkomst of -account registreert, wordt er een account met beheerdersmachtigingen gemaakt in Cloudyn. Met behulp van dit account kunt u alle stappen in deze zelfstudie uitvoeren. In deze zelfstudie wordt aandacht besteed aan toegang tot gegevens van Cloudyn en entiteitsbeheer. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een gebruiker met beheerderstoegang maken
> * Een gebruiker met gebruikerstoegang maken
> * Een gebruiker verwijderen
> * Persoonlijke gegevens verwijderen of exporteren
> * Entiteiten maken en beheren


Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-account hebben.
- U moet een proefregistratie of een betaald abonnement voor Cloudyn hebben.

## <a name="create-a-user-with-admin-access"></a>Een gebruiker met beheerderstoegang maken

Hoewel u al beschikt over beheerderstoegang, hebben collega's in uw organisatie misschien ook beheerderstoegang nodig. Klik in de rechterbovenhoek van de portal van Cloudyn op het tandwielpictogram en selecteer **User Management**. Klik op **Add New User** om een nieuwe gebruiker toe te voegen.

Voer de vereiste gegevens in voor de gebruiker. De **aanmeldings-id** moet een geldig e-mailadres zijn. Selecteer Allow User Management om het beheer van gebruikers toe te staan, zodat de gebruiker andere gebruikers kan maken en wijzigen. Met Adressenlijstbeheer kan de gebruiker adressenlijsten bewerken. Op het moment dat u **Notify user by email** selecteert, wordt er vanuit Cloudyn per e-mail een koppeling met aanmeldingsgegevens verzonden naar de gebruiker. Bij de eerste aanmelding stelt de gebruiker een wachtwoord in.

Onder **User has admin access** is de hoofdentiteit van uw organisatie geselecteerd. Laat dit zo en sla vervolgens de gebruikersgegevens op. Als u de hoofdentiteit selecteert, heeft de gebruiker niet alleen beheerdersmachtigingen voor de hoofdentiteit in de structuur, maar ook voor alle onderliggende entiteiten.  
  ![Voorbeeld van beheerderstoegang in het vak Nieuwe gebruiker toevoegen](./media/tutorial-user-access/new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Een gebruiker met gebruikerstoegang maken
Meestal is het zo dat gebruikers die toegang nodig hebben tot gegevens van Cloudyn zoals dashboards en rapporten, moeten beschikken over gebruikerstoegang om deze onderdelen weer te geven. Maak een nieuwe gebruiker die toegang heeft die vergelijkbaar is met de gebruiker die u hebt gemaakt met beheerderstoegang, maar met de volgende verschillen:

- Schakel **Allow User Management** en **Allow Recipient lists Management** uit, evenals het selectievakje Select all in de lijst **User has admin access**.
- Selecteer in de lijst **User has user access** de entiteiten waartoe de gebruiker toegang moet hebben.
- Indien nodig kunt u in de lijst ernaast ook beheerderstoegang geven tot bepaalde entiteiten.

![Voorbeeld van gebruikerstoegang in het vak Nieuwe gebruiker toevoegen](./media/tutorial-user-access/new-user-access.png)

Als u een Engelstalige zelfstudievideo wilt bekijken over het toevoegen van gebruikers, gaat u naar [Adding Users to Cloudyn](https://youtu.be/Nzn7GLahx30).

## <a name="delete-a-user"></a>Een gebruiker verwijderen

Wanneer u een gebruiker verwijdert, blijven alle entiteiten waar de gebruiker toegang tot heeft intact. Opgeslagen *persoonlijke* rapporten worden verwijderd wanneer de gebruiker wordt verwijderd. Opgeslagen *openbare* rapporten die zijn gemaakt door de gebruiker worden niet verwijderd.

U kunt zichzelf niet als gebruiker verwijderen.

> [!WARNING]
> Wanneer u een gebruiker verwijdert, kan dit niet worden hersteld.

1.  Klik in de rechterbovenhoek van de portal van Cloudyn op het tandwielpictogram en selecteer vervolgens **User Management**.
2.  Selecteer in de lijst met gebruikers de gebruiker die u wilt verwijderen en klik vervolgens op **Gebruiker verwijderen** (prullenbaksymbool).
3.  Klik in het vak Gebruiker verwijderen op **Ja** en klik vervolgens op **OK**.


## <a name="delete-or-export-personal-data"></a>Persoonlijke gegevens verwijderen of exporteren

Als u persoonlijke gegevens wilt verwijderen of exporteren vanuit Cloudyn, moet u een ondersteuningsticket maken. Wanneer het ondersteuningsticket is gemaakt, fungeert het als een formele aanvraag: een AVG-aanvraag (van betrokkene). Microsoft gaat vervolgens onmiddellijk over tot het verwijderen van het account en eventuele klant- en persoonlijke gegevens. Ga naar [Data Subject Requests of Cloudyn Data](https://www.cloudyn.com/cloudyn-gdpr-requests) (AVG-aanvragen voor Cloudyn-gegevens) voor meer informatie over hoe u het verwijderen of exporteren van uw gegevens kunt aanvragen.

## <a name="create-and-manage-entities"></a>Entiteiten maken en beheren

Wanneer u de hiërarchie met kostenentiteiten definieert, wordt het aanbevolen om de structuur van uw organisatie te volgen. Entiteiten maken het mogelijk om uitgaven te segmenteren op individuele accounts of abonnementen. U definieert kostenentiteiten om logische groepen te maken voor het beheren en controleren van uitgaven. Bepaal tijdens het samenstellen van de structuur hoe u de kosten van entiteiten wilt of moet toerekenen aan bedrijfsonderdelen, kostenplaatsen, omgevingen en verkoopafdelingen. De entiteitsstructuur in Cloudyn is flexibel vanwege de overname van entiteiten.

Afzonderlijke abonnementen voor uw cloudaccounts worden gekoppeld aan specifieke entiteiten. U kunt een entiteit koppelen aan een account of abonnement van een cloudserviceprovider. Dit betekent dat entiteiten meerdere tenants kunnen hebben. Met behulp van entiteiten kunt u specifieke gebruikers alleen toegang geven tot hun segment van uw bedrijf. Hierdoor blijven gegevens geïsoleerd, zelfs in grote delen van een bedrijf zoals dochterondernemingen. Bovendien helpt gegevensisolatie bij governance.  

Op het moment dat u uw Azure overeenkomst of -account registreert bij Cloudyn, worden er allerlei gegevens naar uw Cloudyn-account gekopieerd. Denk hierbij aan gegevens van uw Azure-resources, zoals gegevens over het gebruik, de prestaties, facturering en tags uit uw abonnementen. De entiteitsstructuur moet u echter handmatig maken. Als u de registratie bij Azure Resource Manager hebt overgeslagen, zijn er alleen factureringsgegevens en enkele asset-rapporten beschikbaar in de portal van Cloudyn.

Klik in de Cloudyn-portal rechtsboven op het tandwielsymbool en selecteer **Cloud-accounts** . U begint met één entiteit (de hoofdentiteit of root) en gaat vervolgens de structuur van boven naar beneden uitwerken. Hier ziet u een voorbeeld van een entiteitshiërarchie die een goed beeld geeft van de opbouw van veel IT-organisaties als de structuur helemaal voltooid is:

![Voorbeeld van een entiteitsstructuur op de pagina Accountsbeheer](./media/tutorial-user-access/entity-tree.png)

Klik naast **Entities** op **Add Entity**. Voer gegevens in van de persoon of afdeling die u wilt toevoegen. De velden **Full Name** en **Email** hoeven niet overeen te komen met bestaande gebruikers. Als u een lijst met toegangsniveaus wilt weergeven, zoekt u in de Help naar *Adding an entity*.

![Voorbeeld met entiteitsnaam en toegangsniveaus in het vak Entiteit toevoegen](./media/tutorial-user-access/add-entity.png)

Sla de wijzigingen op met **Save** als u klaar bent.

### <a name="entity-access-levels"></a>Toegangsniveaus voor entiteiten

Door toegangsniveaus voor entiteiten te combineren met de toegang van een gebruiker kunt u definiëren wat voor soort acties er beschikbaar zijn in de Cloudyn-portal.

- **Enterprise**: biedt de mogelijkheid om onderliggende kostenentiteiten te maken en beheren.
- **Enterprise + Cost Allocation**: biedt de mogelijkheid om onderliggende kostenentiteiten te maken en beheren, met inbegrip van kostentoewijzing voor geconsolideerde accounts.
- **Enterprise, Cost based on parent cost allocation**: biedt de mogelijkheid om onderliggende kostenentiteiten te maken en beheren. Kosten voor het account worden gebaseerd op het model voor kostentoewijzing van het bovenliggende item.
- **Custom Dashboards Only**: biedt de gebruiker de mogelijkheid om alleen vooraf gedefinieerde aangepaste dashboards te zien.
- **Dashboards Only**: biedt de gebruiker de mogelijkheid om alleen dashboards te zien.

### <a name="create-a-cost-entity-hierarchy"></a>Een hiërarchie met kostenentiteiten maken

U hebt een account met de toegangsmachtiging Enterprise of Enterprise + Cost Allocation nodig voor het maken van een hiërarchie met kostenentiteiten.

Klik in de Cloudyn-portal rechtsboven op het tandwielsymbool en selecteer **Cloud Accounts** . De structuur **Entities** wordt in het linkerdeelvenster weergegeven. Vouw de structuur indien nodig uit, zodat u de entiteit kunt zien die u wilt koppelen aan een account.  De accounts van uw cloudserviceprovider vindt u terug op de tabbladen in het rechterdeelvenster. Selecteer een tabblad en sleep vervolgens een account of abonnement naar de entiteit. In het vak **Move** ziet u een bericht dat het account is verplaatst. Klik op **OK**.

U kunt ook meerdere accounts aan een entiteit koppelen. Selecteer de accounts en klik vervolgens op **Move**. Selecteer in het vak Move Accounts de entiteit waarnaar u het account wilt verplaatsen en klik vervolgens op **Save**. U wordt gevraagd te bevestigen dat u de accounts wilt verplaatsen. Klik op **Yes** en daarna op **OK**.

Als u een Engelstalige zelfstudievideo wilt bekijken over het maken van een hiërarchie voor entiteitskosten, gaat u naar [Creating a Cost Entity Hierarchy in Cloudyn](https://youtu.be/dAd9G7u0FmU).

Als u een gebruiker bent van een Enterprise Agreement voor Azure, kunt u een video bekijken over het koppelen van accounts en abonnementen aan entiteiten. Ga hiervoor naar [Connecting to Azure Resource Manager with Cloudyn](https://youtu.be/oCIwvfBB6kk) (ook Engelstalig vooralsnog).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een gebruiker met beheerderstoegang maken
> * Een gebruiker met gebruikerstoegang maken
> * Een gebruiker verwijderen
> * Persoonlijke gegevens verwijderen of exporteren
> * Entiteiten maken en beheren


Als u Azure Resource Manager API-toegang nog niet hebt ingeschakeld voor uw accounts, gaat u verder met het volgende artikel.

> [!div class="nextstepaction"]
> [Azure-abonnementen en -accounts activeren](activate-subs-accounts.md)
