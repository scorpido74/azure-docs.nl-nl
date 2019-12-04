---
title: Azure AD Connect Cloud Provisioning-trans formaties
description: In dit document wordt beschreven hoe u trans formaties gebruikt om de standaard kenmerk toewijzingen te wijzigen.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794469"
---
# <a name="transformations"></a>Transformaties

Met een trans formatie kunt u het standaard gedrag wijzigen van de manier waarop een kenmerk wordt gesynchroniseerd met Azure AD met behulp van Cloud inrichting.  

Als u deze taak wilt uitvoeren, moet u het schema bewerken en vervolgens opnieuw verzenden via een webaanvraag.

Zie [informatie over het Azure AD-schema](concept-attributes.md)voor meer informatie over de inrichtings kenmerken in de Cloud.


## <a name="retrieve-the-schema"></a>Het schema ophalen
Als u het schema wilt ophalen, gebruikt u de stappen die worden beschreven in [het schema weer geven](concept-attributes.md#viewing-the-schema). 


## <a name="custom-attribute-mapping"></a>Toewijzing van aangepast kenmerk
Gebruik de volgende procedure om een aangepaste kenmerk toewijzing toe te voegen.

1. Kopieer het schema naar een tekst-of code-editor, zoals [Visual Studio code](https://code.visualstudio.com/).  
2. Zoek het object dat u wilt bijwerken in het schema ![](media/how-to-transformation/transform1.png)</br>
3. Zoek de code voor **ExtensionAttribute3** onder het gebruikers object.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 4.  Bewerk de code zodat het bedrijfs kenmerk wordt toegewezen aan ExtensionAttribute3.
    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([company])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[company]",
                                                "name": "company",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 5. Kopieer het schema terug naar Graph Explorer, wijzig het aanvraag type om de query op te slaan en **uit te voeren**.  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  Ga nu in het Azure Portal naar de inrichtings configuratie voor de Cloud en **Start het inrichten opnieuw**.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  Na een tijdje kunt u controleren of de kenmerken worden ingevuld door de volgende query uit te voeren in Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 8.  Nu ziet u de waarde.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>Toewijzing van aangepast kenmerk met functie
Voor een uitgebreidere toewijzing kunt u gebruikmaken van functies waarmee u de gegevens bewerkt en waarden voor het kenmerk kunt maken op basis van de behoeften van uw organisatie.

Volg de bovenstaande stappen om deze taak uit te voeren en bewerk vervolgens de functie die wordt gebruikt om de uiteindelijke waarde te maken.

Zie [expressies schrijven voor kenmerk toewijzingen in azure Active Directory](reference-expressions.md) voor meer informatie over de syntaxis en voor beelden van expressies.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)
