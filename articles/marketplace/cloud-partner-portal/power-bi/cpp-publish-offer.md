---
title: Power BI app-aanbieding publiceren | Azure Marketplace
description: Publiceer een Power BI-app-aanbieding op de Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: eb31520c81a4064edbe54a0256b694c4ad88fb49
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141703"
---
# <a name="publish-a-power-bi-app-offer"></a>Een Power BI-app-aanbieding publiceren

>[!Important]
>Vanaf 13 april 2020 gaan we het beheer van uw Power BI app-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [Power bi overzicht](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) van het maken van apps voor het beheren van uw gemigreerde aanbiedingen.

De laatste stap, nadat u een aanbieding in de Cloud Partner-portal hebt gedefinieerd en de gekoppelde technische activa hebt gemaakt, is het aanbieden van de aanbieding voor publicatie. Als u dit proces wilt starten, selecteert u in het linkerdeel venster van het venster **nieuwe aanbieding** de optie **publiceren**. Zie [Azure Marketplace en AppSource-aanbiedingen publiceren](../manage-offers/cpp-publish-offer.md)voor meer informatie.


## <a name="publishing-steps"></a>Publicatie stappen

Dit zijn de belangrijkste stappen in het publicatie proces:

![Proces stappen voor het publiceren van Power BI app-aanbieding](./media/publishing-process-steps.png)

In deze tabel vindt u een beschrijving van elke stap en de geschatte voltooiings tijd:

|   Publicatie stap            |   Time     |   Beschrijving                                                                  |
| --------------------         |------------| ----------------                                                               |
| Vereisten valideren       | 15 minuten     | De aanbiedings gegevens en aanbiedings instellingen worden gevalideerd.                            |
| Certificering                | 1-7 dagen   | Het Power BI-certificerings team analyseert uw aanbieding. Het team voert uw Power BI-app uit via een hand matige verificatie test door de app te installeren via de meegeleverde installatie-URL. Primaire validaties worden uitgevoerd als onderdeel van het app-certificerings proces (verderop in dit document beschreven).         |
| Verpakking                    | \<1 uur  | De technische activa van de aanbieding zijn verpakt voor gebruik door de klant.                        |
| Registratie lead genereren | \<1 uur  | Lead systemen worden geconfigureerd en geïmplementeerd.                                      |
| Publisher-aftekening            | \-         | U voltooit de laatste beoordeling en bevestiging voordat de aanbieding live gaat. U hebt nu ook een koppeling om uw aanbieding te bekijken. Wanneer u tevreden bent met het uiterlijk van uw preview, selecteert u **Live** kijken op het tabblad **status** . Hiermee verzendt u een aanvraag naar het voorbereidings team om uw app op AppSource weer te geven.    |
| Live                         | \<3 uur | Uw aanbieding is nu openbaar vermeld (' live ') op AppSource en klanten kunnen uw app bekijken en implementeren in hun Power BI-abonnementen. U ontvangt ook een bevestigings-e-mail. In de rechter kolom op het tabblad **alle aanbiedingen** kunt u de status van al uw aanbiedingen bekijken. Op het tabblad **status** ziet u de gedetailleerde publicatie stroom status voor uw aanbieding. |
|   |   |

Maxi maal acht dagen toestaan dat dit proces is voltooid. Nadat u deze publicatie stappen hebt door lopen, wordt uw Power BI-app-aanbieding weer gegeven in de sectie [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power bi apps.


### <a name="app-certification-process"></a>App-certificerings proces

Het micro soft-voorbereidings team gebruikt dit proces om uw Power BI voor het indienen van de app-aanbieding te valideren:

1. Bekijk juridische documenten en Help-koppelingen.
2. De contact gegevens voor ondersteuning valideren.
3. Gebruik de installatie-URL om de juiste installatie te controleren.
4. Scan de app op malware en andere schadelijke inhoud.
5. Controleer of de weer gegeven inhoud overeenkomt met de beschrijving van de app.
6. Controleer of aan de app gerelateerde bewerkingen werken zoals verwacht in Power BI. Het team opent rapporten en dash boards met voorbeeld gegevens, maakt verbinding met aangepaste gegevens bronnen, vernieuwt gegevens, enzovoort.

Het certificerings team geeft feedback als er problemen zijn gevonden.  Zie de [documentatie van Power bi app](https://go.microsoft.com/fwlink/?linkid=2028636)voor meer informatie over Power bi app-vereisten.


## <a name="next-steps"></a>Volgende stappen

U wordt aangeraden uw app regel matig te controleren in de [AppSource Marketplace](https://appsource.microsoft.com).  U moet ook de functie voor de [verkoop inzichten](../../cloud-partner-portal-orig/si-getting-started.md) van de [Cloud Partner-Portal](https://cloudpartner.azure.com/#insights) gebruiken om inzicht te krijgen in uw Marketplace-klanten en het gebruik van apps. Ten slotte kunt u [uw aanbieding bijwerken](./cpp-update-existing-offer.md).
