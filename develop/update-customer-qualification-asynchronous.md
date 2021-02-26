---
title: Update a customer's qualifications
description: Learn how to update a customer's qualifications via asynchronous screening or vetting, including the address associated with the profile.
ms.date: 12/07/2020
ms.service: partner-dashboard
ms.subservice:  partnercenter-sdk
author: JoeyBytes
ms.author: jobiesel
# Relates to asynchronous screening or vetting. 
---

# Update a customer's qualifications asynchronously

**Applies To**

- Partner Center

Learn how to update a customer's qualifications asynchronously via Partner Center APIs. To learn how to do this synchronously, see [Update a customer's qualification via synchronous validation](update-customer-qualification-synchronous.md).

A partner can update a customer's qualifications asynchronously to be "Education" or "GovernmentCommunityCloud". Other values, "None" and "Nonprofit", cannot be set.

## Prerequisites

- Credentials as described in [Partner Center authentication](partner-center-authentication.md). This scenario supports authentication with App+User credentials only.

- A customer ID (`customer-tenant-id`). If you don't know the customer's ID, you can look it up in the Partner Center [dashboard](https://partner.microsoft.com/dashboard). Select **CSP** from the Partner Center menu, followed by **Customers**. Select the customer from the customer list, then select **Account**. On the customer's Account page, look for the **Microsoft ID** in the **Customer Account Info** section. The Microsoft ID is the same as the customer ID  (`customer-tenant-id`).

## C\#

To create a customer's qualification for "Education", first create an object representing the qualification type. Then, call the [**IAggregatePartner.Customers.ById**](/dotnet/api/microsoft.store.partnercenter.customers.icustomercollection.byid) method with the customer identifier. Then use the [**Qualification**](/dotnet/api/microsoft.store.partnercenter.customers.icustomer.qualification) property to retrieve a [**ICustomerQualification**](/dotnet/api/microsoft.store.partnercenter.qualification.icustomerqualification) interface. Finally, call `CreateQualifications()` or `CreateQualificationsAsync()` with the qualification type object as an input parameter.

``` csharp

var qualificationType = { Qualification = "education" };

var eduCustomerQualification = partnerOperations.Customers.ById(existingCustomer.Id).Qualification.CreateQualifications(qualificationType);
```

**Sample**: [Console Sample App](https://github.com/microsoft/Partner-Center-DotNet-Samples). **Project**: SdkSamples **Class**: CreateCustomerQualification.cs

To update a customer's qualification to **GovernmentCommunityCloud** on an existing customer without a qualification, the partner is also required to include the customer's [**ValidationCode**](utility-resources.md#validationcode). First, create an object representing the qualification type. Then, call the [**IAggregatePartner.Customers.ById**](/dotnet/api/microsoft.store.partnercenter.customers.icustomercollection.byid) method with the customer identifier. Then use the [**Qualification**](/dotnet/api/microsoft.store.partnercenter.customers.icustomer.qualification) property to retrieve a [**ICustomerQualification**](/dotnet/api/microsoft.store.partnercenter.qualification.icustomerqualification) interface. Finally, call `CreateQualifications()` or `CreateQualificationsAsync()` with the qualification type object and the validation code as input parameters.

``` csharp
// GCC validation is type ValidationCode

var qualificationType = { Qualification = "GovernmentCommunityCloud" };

var gccCustomerQualification = partnerOperations.Customers.ById(existingCustomer.Id).Qualification.CreateQualifications(qualificationType, gccValidation);
```

**Sample**: [Console Sample App](https://github.com/microsoft/Partner-Center-DotNet-Samples). **Project**: SdkSamples **Class**: CreateCustomerQualificationWithGCC.cs

## REST request

### Request syntax

| Method  | Request URI                                                                                             |
|---------|---------------------------------------------------------------------------------------------------------|
| **POST** | [*{baseURL}*](partner-center-rest-urls.md)/v1/customers/{customer_id}/qualifications?code={validationCode} HTTP/1.1 |

### URI parameter

Use the following query parameter to update the qualification.

| Name                   | Type | Required | Description                                                                                                                                            |
|------------------------|------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| **customer-tenant-id** | GUID | Yes      | The value is a GUID formatted **customer-tenant-id** that allows the reseller to filter the results for a given customer that belongs to the reseller. |
| **validationCode**     | int  | No       | Only needed for Government Community Cloud.                                                                                                            |

### Request headers

For more information, see [Partner Center REST headers](headers.md).

### Request body

The integer value from the [**CustomerQualification**](/dotnet/api/microsoft.store.partnercenter.models.customers.customerqualification) enum.

### Request example

```http
POST https://api.partnercenter.microsoft.com/v1/customers/<customer-tenant-id>/qualifications?code=<validation-code> HTTP/1.1
Accept: application/json
Content-Type: application/json
MS-CorrelationId: 7d2456fd-2d79-46d0-9f8e-5d7ecd5f8745
MS-RequestId: 037db222-6d8e-4d7f-ba78-df3dca33fb68

```

## REST response

If successful, this method returns a qualifications object in the response body. Below is an example of the **POST** call on a customer (with a previous qualification of **None**) with the **Education** qualification.

### Response success and error codes

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For the full list, see [Error Codes](error-codes.md).

### Response example

```http
HTTP/1.1 201 CREATED
Content-Length: 29
Content-Type: application/json
MS-CorrelationId: 7d2456fd-2d79-46d0-9f8e-5d7ecd5f8745
MS-RequestId: 037db222-6d8e-4d7f-ba78-df3dca33fb68
{
    "qualification": "Education",
    "vettingStatus": "InReview",
    "vettingCreateDate": "2020-12-04T20:54:24Z" // UTC
}
```

## Related articles

- [Get a customer's qualifications](get-a-customer-s-qualifications.md)
- [Get a partner's validation codes](get-a-partner-s-validation-codes.md)