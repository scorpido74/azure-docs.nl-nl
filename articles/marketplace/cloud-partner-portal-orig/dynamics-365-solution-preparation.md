---
title: Dynamics 365-oplossing voorbereiden
description: Framework voor het inpakken, installeren en verwijderen van onderdelen
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278583"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365-oplossing voorbereiden

Het Dynamics 365 Solution System is een raam werk voor het verpakken, installeren en verwijderen van onderdelen die specifieke bedrijfs functionaliteit bieden. Oplossingen worden door Isv's en andere micro soft Dynamics 365-partners gebruikt om uitbrei dingen te distribueren die ze maken.

Als u een bestaande Dynamics 365 (xRM) ISV bent, hebt u waarschijnlijk al een beheerde oplossing gemaakt en hebt u een zip-bestand van de oplossing. Controleer in uw oplossing of de velden ' weergave naam ' en ' Beschrijving ' overeenkomen met wat u door klanten wilt laten zien. Deze worden weer gegeven in het CRM Online-beheer centrum.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Opmerking:** In het voor beeld van het pakket gaan we ervan uit dat de oplossings naam ' SampleSolution. zip ' is._

Als u een nieuwe ISV bent, kunt u hier meer informatie vinden over het maken van een oplossing:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Als uw oplossing ondersteunende gegevens vereist:

* De voorbeeld gegevens in uw test omgeving maken
* Gebruik het hulp programma voor migratie van de configuratie om een schema te maken met vergelijkings regels voor uw gegevens.
* Sla uw configuratie schema op met uw project bestanden. U hebt dit later nodig als u de configuratie gegevens bijwerkt.
* Exporteer uw configuratie gegevens. Zorg ervoor dat u het export bestand een naam geeft die zinvol is voor uw export.
