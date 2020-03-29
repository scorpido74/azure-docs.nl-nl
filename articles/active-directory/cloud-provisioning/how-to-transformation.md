---
title: Azure AD Connect-transformaties voor cloudinrichting
description: In dit artikel wordt beschreven hoe u transformaties gebruikt om de standaardtoewijzingen voor kenmerken te wijzigen.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549292"
---
# <a name="transformations"></a>Transformaties

Met een transformatie u het standaardgedrag wijzigen van de manier waarop een kenmerk wordt gesynchroniseerd met Azure Active Directory (Azure AD) met behulp van cloudprovisioning.

Om deze taak uit te voeren, moet u het schema bewerken en vervolgens opnieuw indienen via een webaanvraag.

Zie Het [Azure AD-schema begrijpen](concept-attributes.md)voor meer informatie over cloudprovisioning-kenmerken.


## <a name="retrieve-the-schema"></a>Het schema ophalen
Als u het schema wilt ophalen, voert u de stappen in [Weergave van het schema](concept-attributes.md#view-the-schema)uit. 

## <a name="custom-attribute-mapping"></a>Aangepaste toewijzing van kenmerken
Als u een aangepaste kenmerktoewijzing wilt toevoegen, voert u deze stappen uit.

1. Kopieer het schema naar een tekst- of codeeditor, zoals [Visual Studio Code](https://code.visualstudio.com/).
1. Zoek het object dat u wilt bijwerken in het schema.

   ![Object in het schema](media/how-to-transformation/transform1.png)</br>
1. Zoek de `ExtensionAttribute3` code voor onder het gebruikersobject.

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
1. Bewerk de code zodat het bedrijfskenmerk `ExtensionAttribute3`is toegewezen aan .

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
 1. Kopieer het schema terug naar Grafiekverkenner, wijzig het **aanvraagtype** in **PUT**en selecteer **Query uitvoeren**.

    ![Query uitvoeren](media/how-to-transformation/transform2.png)

 1. Ga nu in de Azure-portal naar de configuratie voor cloudinrichting en selecteer **Inrichting opnieuw starten.**

    ![Inrichting opnieuw opstarten](media/how-to-transformation/transform3.png)

 1. Controleer na verloop van tijd of de kenmerken worden ingevuld door `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`de volgende query uit te voeren in Grafiek Verkenner: .
 1. Je zou nu de waarde moeten zien.

    ![De waarde wordt weergegeven](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Aangepaste kenmerktoewijzing met functie
Voor meer geavanceerde toewijzing u functies gebruiken waarmee u de gegevens manipuleren en waarden maken voor kenmerken die aan de behoeften van uw organisatie voldoen.

Als u deze taak wilt uitvoeren, volgt u de vorige stappen en bewerkt u de functie die wordt gebruikt om de uiteindelijke waarde te construeren.

Zie [Expressies schrijven voor kenmerktoewijzingen in Azure Active Directory voor](reference-expressions.md)informatie over de syntaxis en voorbeelden van expressies.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
