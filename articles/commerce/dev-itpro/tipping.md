---
# required metadata

title: Support for tipping in payments SDK
description: This topic describes support in the payments software development kit (SDK) for accepting tips on payment terminals.
author: rubendel
manager: annbe
ms.date: 11/18/2020
ms.topic: article
ms.prod: 
ms.service: dynamics-365-retail
ms.technology: 

# optional metadata

# ms.search.form: 
# ROBOTS: 
audience: IT Pro
# ms.devlang: 
ms.reviewer: josaw
ms.search.scope: Operations, Retail
# ms.tgt_pltfrm: 
ms.custom: 141393
ms.assetid: e23e944c-15de-459d-bcc5-ea03615ebf4c
ms.search.region: Global
ms.search.industry: Retail
ms.author: rubendel
ms.search.validFrom: 2020-10-31
ms.dyn365.ops.version: AX 7.0.1

---

# Support for tipping in the POS payments SDK

[!include [banner](../includes/banner.md)]

This topic describes how the Dynamics 365 Commerce payments software development kit (SDK) supports tips entered through the payment terminal. To enable tipping support in point of sale (POS), extensions are needed. The purpose of this capability is to add first-class support to the payments SDK in the form of a discrete field for tip amount in authorization responses. 

This capability does not add support for tipping or tip reporting at the POS. To build full tipping capabilities, POS extensions are needed. 

## Key terms

| Term | Description |
|---|---|
| Tips | Also known as gratuities, tips are common in quick service and hospitalities to provide a payment directly to the store or restaurant employee who provides services. |
| Header level charge | A charge that can be applied to a purchase that is not for a specific line item. |

## Overview

Tipping is common in certain locales and industries. For example, in quick service and hospitality, tipping has become nearly ubiquitous in the United States. For many businesses, the ability to support tipping through payment terminals is becoming a differentiator when attracting employees. This feature adds a discrete **TipAmount** field to the payments SDK so tips selected at the payment terminal can flow back to the POS as part of the authorization response. 

While this feature adds support for tipping at the payments SDK level, it does not include support for other critical aspects of tipping support. For example, reporting to indicate tip payouts at the end of shifts, the ability to pool tips, and the ability to report tips for payroll. To enable full tipping support, those capabilities must be implemented with extensions. 

For more information about creating payment terminal integrations and SDK references in this article, see [Create an end-to-end payment integration for a payment terminal](end-to-end-payment-extension.md).

### Prerequisites

| Prerequisite | Description |
|---|---|
| Tip on device | Customer must be able to select tip amount on the payment terminal. |
| **isTippingEnabled** support | Payment connectors must support the **isTippingEnabled** variable for payment initialization. |
| **TipAmount** | The tip amount must be returned from the payment connector in the **TipAmount** authorization response field. |
| POS tip support by extension | The tip returned from the payment connector should be added to the sale as a header-level charge. In addition, tip reporting and management are not provided out-of-box. |

### Tipping support by payment processor

This feature adds a new variable called **isTippingEnabled** to the **AuthorizePaymentTerminalDeviceRequest**. This variable indicates at the time a payment is requested from the POS if the authorization request is eligible for a tip to be added. Payment connectors receiving this request can then request a tip eligible authorization from the connected payment service or terminal. 

If **isTippingEnabled** is set to **True** and the customer chooses a tip on the payment terminal, that amount should be returned in the **TipAmount** property of the **AuthorizePaymentCardPaymentResponse** returned from the payment connector. The tip amount is also included in the **ApprovedAmount** property of the authorization response. 

### Tipping support for the Adyen connector

Tip support is included with the Adyen connector. Customization is required to set the **isTippingEnabled** variable to **True** when the authorization response is passed to the connector, but if that is done and the customer selects a tip on the device, then the tip will be returned in the **TipAmount** field of the authorization response.

To prompt the customer to select a tip on the Adyen terminal, the terminal must be configured for tipping. This is done through the Adyen customer area. To enable tipping, navigate to the **Point of Sale** tab in the Adyen portal. Tipping can be enabled by device or through the fleet-wide **Terminal settings** option. Within the settings of both the individual terminal or fleet-wide terminal settings, tipping is enabled through the **Payment features** tab. Once tipping is enabled on your Adyen device, settings on the device will need to be updated for the change to take effect. 

### Suggested implementation

It is recommended that a header-level charge is used to add the tip amount to the transaction after the authorization response is returned from the payment connector. To support this, an extension should be created for the **PaymentTerminalAuthorizePaymentRequestHandler**. That extension should include logic to add a header level charge to the transaction if a **TipAmount** is returned in the authorization response. 

## Additional resources

- [Adyen connector overview](adyen-connector?tabs=10-0-14.md)
- [Create an end-to-end payment integration for a payment terminal](end-to-end-payment-extension.md)

