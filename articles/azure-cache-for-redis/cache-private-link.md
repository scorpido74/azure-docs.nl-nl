---
title: Azure-cache voor redis met persoonlijke Azure-koppeling (preview-versie)
description: Persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met Azure cache voor redis die worden aangestuurd door een persoonlijke Azure-koppeling. In dit artikel leert u hoe u een Azure-cache, een Azure-Virtual Network en een persoonlijk eind punt maakt met behulp van de Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 4ab754cacc85bc9e7c7b850270df37290ad399b6
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650158"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure-cache voor redis met persoonlijke Azure-koppeling (open bare preview)
In dit artikel leert u hoe u een virtueel netwerk en een Azure-cache maakt voor een redis-exemplaar met een persoonlijk eind punt met behulp van de Azure Portal. U leert ook hoe u een persoonlijk eind punt kunt toevoegen aan een bestaand Azure-cache geheugen voor redis-instantie.

Persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met Azure cache voor redis die worden aangestuurd door een persoonlijke Azure-koppeling. 

## <a name="prerequisites"></a>Vereisten
* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)

> [!NOTE]
> Deze functie is momenteel beschikbaar als open bare Preview voor beperkte regio's. [Neem contact met ons op](mailto:azurecache@microsoft.com)als u niet over de mogelijkheid beschikt om een persoonlijk eind punt te maken. Als u persoonlijke eind punten wilt gebruiken, moet uw Azure-cache voor redis-exemplaar zijn gemaakt na 28 juli 2020.
>
> Regio's met open bare preview-toegang momenteel: West-Centraal VS, Noord-Centraal VS, VS-West, VS-West 2, VS-Oost, VS-Oost 2, VS-midden, Zuid-Centraal VS, Noor wegen Oost, Noor wegen West, Europa-Noord, Europa-West, Azië Oost, Zuidoost-Azië, Japan-Oost, japan-West, UAE-centraal, Korea-centraal, Korea-zuid Zwitserland-West Zwitserland-noord, UK-zuid, UK-west , Zuid-Afrika-noord, Frankrijk-zuid, Frankrijk-centraal, Canada-oost, Canada-centraal, Duitsland-noord, Duitsland-west-centraal, Australië-centraal, India-West, India-Zuid, Australië-oost, Brazilië-Zuidoost, Brazilië-zuid en Brazilië-zuid.
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Een persoonlijk eind punt maken met een nieuwe Azure-cache voor redis-exemplaar 

In deze sectie maakt u een nieuw Azure-cache geheugen voor een redis-exemplaar met een persoonlijk eind punt.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Create a resource**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecteer een resource maken.":::

2. Selecteer op de pagina **Nieuw** de optie **netwerken** en selecteer vervolgens **virtueel netwerk**.

3. Selecteer **toevoegen** om een virtueel netwerk te maken.

4. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit virtuele netwerk wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resource groep waarin u het virtuele netwerk en andere resources wilt maken. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Naam** | Voer een naam voor het virtuele netwerk in. | De naam moet beginnen met een letter of cijfer, eindigen met een letter, cijfer of onderstrepings teken en mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. | 
   | **Regio** | Vervolg keuzelijst en selecteer een regio. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gebruikmaken van uw virtuele netwerk. |

5. Selecteer het tabblad **IP-adressen** of klik op de knop **volgende: IP-adressen** aan de onderkant van de pagina.

6. Geef op het tabblad **IP-adressen** de **IPv4-adres ruimte** op als een of meer adres voorvoegsels in CIDR-notatie (bijvoorbeeld 192.168.1.0/24).

7. Klik onder **subnet naam**op **standaard** om de eigenschappen van het subnet te bewerken.

8. Geef in het deel venster **subnet bewerken** de **naam** van een subnet en het **adres bereik**van het subnet op. Het adres bereik van het subnet moet de CIDR-notatie hebben (bijvoorbeeld 192.168.1.0/24). Het moet deel uitmaken van de adres ruimte van het virtuele netwerk.

9. Selecteer **Opslaan**.

10. Selecteer het tabblad **controleren + maken** of klik op de knop **beoordeling + maken** .

11. Controleer of alle gegevens juist zijn en klik op **maken** om het virtuele netwerk in te richten.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Een Azure-cache maken voor een redis-exemplaar met een persoonlijk eind punt
Volg deze stappen om een cache-exemplaar te maken.

1. Ga terug naar de Azure Portal start pagina of open het menu Sidebar en selecteer vervolgens **een resource maken**. 
   
1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecteer een resource maken.":::
   
1. Configureer op de pagina **Nieuwe Redis-cache** de instellingen voor de nieuwe cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Open de vervolgkeuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
   | **Prijscategorie** | Open de vervolgkeuzelijst en selecteer een [Prijscategorie](https://azure.microsoft.com/pricing/details/cache/). |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |

1. Selecteer het tabblad **netwerken** of Klik onder aan de pagina op de knop **netwerk** .

1. Selecteer op het tabblad **netwerken** het **persoonlijke eind punt** voor de verbindings methode.

1. Klik op de knop **toevoegen** om uw persoonlijke eind punt te maken.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Selecteer een resource maken.":::

1. Configureer op de pagina **een persoonlijk eind punt maken** de instellingen voor uw persoonlijke eind punt met het virtuele netwerk en het subnet dat u hebt gemaakt in de laatste sectie en selecteer **OK**. 

1. Selecteer het tabblad **volgende: Geavanceerd** of klik op de knop **volgende: Geavanceerd** aan de onderkant van de pagina.

1. Selecteer op het tabblad **Geavanceerd** voor een basis-of Standard-cache-exemplaar de schakel optie inschakelen als u een niet-TLS-poort wilt inschakelen.

1. Configureer op het tabblad **Geavanceerd** voor het Premium-cache-exemplaar de instellingen voor niet-TLS-poort, clustering en gegevens persistentie.


1. Selecteer het tabblad **volgende: Labels** of klik op de knop **volgende: Labels** onder aan de pagina.

1. Geef desgewenst de naam en waarde op op het tabblad **Tags** als u de resource wilt categoriseren. 

1. Selecteer **controleren + maken**. U gaat naar het tabblad controleren + maken, waar Azure uw configuratie valideert.

1. Wanneer het bericht groene validatie is voltooid wordt weer gegeven, selecteert u **maken**.

Het duurt even voordat de cache is gemaakt. U kunt de voortgang controleren op de **overzichts**pagina van de Azure-cache voor redis   . Wanneer de **status**   wordt weer gegeven als **actief**, is de cache klaar voor gebruik. 
    

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Een persoonlijk eind punt maken met een bestaand Azure-cache geheugen voor redis-exemplaar 

In deze sectie voegt u een persoonlijk eind punt toe aan een bestaand Azure-cache geheugen voor redis-instantie. 

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken 
Voer de volgende stappen uit om een virtueel netwerk te maken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Create a resource**.

2. Selecteer op de pagina **Nieuw** de optie **netwerken** en selecteer vervolgens **virtueel netwerk**.

3. Selecteer **toevoegen** om een virtueel netwerk te maken.

4. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit virtuele netwerk wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resource groep waarin u het virtuele netwerk en andere resources wilt maken. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Naam** | Voer een naam voor het virtuele netwerk in. | De naam moet beginnen met een letter of cijfer, eindigen met een letter, cijfer of onderstrepings teken en mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. | 
   | **Regio** | Vervolg keuzelijst en selecteer een regio. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gebruikmaken van uw virtuele netwerk. |

5. Selecteer het tabblad **IP-adressen** of klik op de knop **volgende: IP-adressen** aan de onderkant van de pagina.

6. Geef op het tabblad **IP-adressen** de **IPv4-adres ruimte** op als een of meer adres voorvoegsels in CIDR-notatie (bijvoorbeeld 192.168.1.0/24).

7. Klik onder **subnet naam**op **standaard** om de eigenschappen van het subnet te bewerken.

8. Geef in het deel venster **subnet bewerken** de **naam** van een subnet en het **adres bereik**van het subnet op. Het adres bereik van het subnet moet de CIDR-notatie hebben (bijvoorbeeld 192.168.1.0/24). Het moet deel uitmaken van de adres ruimte van het virtuele netwerk.

9. Selecteer **Opslaan**.

10. Selecteer het tabblad **controleren + maken** of klik op de knop **beoordeling + maken** .

11. Controleer of alle gegevens juist zijn en klik op **maken** om het virtuele netwerk in te richten.

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken 

Volg deze stappen om een persoonlijk eind punt te maken.

1. Zoek in het Azure Portal naar **Azure cache voor redis** en druk op ENTER of selecteer dit in de zoek suggesties.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Selecteer een resource maken.":::

2. Selecteer het cache-exemplaar waaraan u een persoonlijk eind punt wilt toevoegen.

3. Selecteer aan de linkerkant van het scherm het **persoonlijke eind punt (preview-versie)**.

4. Klik op de knop **persoonlijk eind punt** om uw persoonlijke eind punt te maken.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Selecteer een resource maken.":::

5. Configureer op de **pagina een persoonlijk eind punt maken**de instellingen voor uw persoonlijke eind punt.

   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit privé-eind punt wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resource groep waarin u uw persoonlijke eind punt en andere resources wilt maken. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Naam** | Voer de naam van een persoonlijk eind punt in. | De naam moet beginnen met een letter of cijfer, eindigen met een letter, cijfer of onderstrepings teken en mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. | 
   | **Regio** | Vervolg keuzelijst en selecteer een regio. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die uw persoonlijke eind punt gaan gebruiken. |

6. Klik op de knop **volgende: resource** aan de onderkant van de pagina.

7. Selecteer uw abonnement op het tabblad **resource** , kies het resource type als `Microsoft.Cache/Redis` en selecteer vervolgens de cache waarmee u het persoonlijke eind punt wilt verbinden.

8. Klik onder aan de pagina op de knop **volgende: Configuratie** .

9. Op het tabblad **configuratie** selecteert u het virtuele netwerk en het subnet dat u in de vorige sectie hebt gemaakt.

10. Klik op de knop **volgende: Labels** onder aan de pagina.

11. Geef desgewenst de naam en waarde op op het tabblad **Tags** als u de resource wilt categoriseren.

12. Selecteer **controleren + maken**. U gaat naar het tabblad **controleren + maken**,   waar Azure uw configuratie valideert.

13. Wanneer het bericht groene **validatie is voltooid** wordt weer gegeven, selecteert u **maken**.


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure private link](https://docs.microsoft.com/azure/private-link/private-link-overview)voor meer informatie over persoonlijke Azure-koppelingen. 

