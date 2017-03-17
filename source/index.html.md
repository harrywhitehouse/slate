---
title: RedBrick247 API

language_tabs:
  - shell

toc_footers:
  - <a href='https://www.redbrick247.com/#/register'>Create a RedBrick247 account</a>

includes:
  - errors

search: true
---

# Introduction

The RedBrick247 API (RBAPI) allows developers to integrate shipping labels and fullfillment within their e-commerce businesses and online marketplaces. The RBAPI offers price comparison, label printing, manifest generation, transactions details and tracking features. The guideline below will help get you started. For additional help, please email us at [support@redbrick247.com](mailto:support@redbrick247.com).

## Create Account

To create an account, please visit [www.redbrick247.com](https://www.redbrick247.com/#/register) and sign up for a free account. You will need to select between an eVS account and an ePostage account. If you are a high volume shipper and/or have USPS credentials (CAPS account, Mailer ID, Permit Number) then select the eVS option. Otherwise select the ePostage option. If you have more questions about account types and how they affect postage discounts, please email us at [support@redbrick247.com](mailto:support@redbrick247.com). During the account signup, you'll need to provide your personal details and payment details. For eVS accounts, you will also need to provide your CAPS account number, Mailer ID and Permit Number. After completing signup, you can start printing labels right away (for eVS accounts, 24 business hours are required for us to verify your credentials with the USPS).

## Sandbox and Production

The Sandbox environment will help you get started, test your requests and the RBAPI responses before you generate Production labels. You cannot induct mailpieces into the mailstream using labels generated in the Sandbox environment. There are no additional charges associated with requests in the Sandbox environment. When you are ready to generate live labels, switch the RBAPI Sandbox URL with the RBAPI Production URL. The requests (including headers) do not change.

## Endpoints

<aside class="notice">
Sandbox: https://rbapi.sandbox.redbrick247.com/v1
</aside>

<aside class="notice">
Production: https://rbapi.redbrick247.com/v1
</aside>

## RESTful API

The RBAPI is a RESTful API that supports JSON requests and responses. HATEOAS is not supported. This documentation provides code samples and libraries for different platforms to help you get started.

Requests must include "Content-Type: application/json" in the headers. Successful responses return HTTP status code 200 and 201. Depending on the request, responses return a JSON body or not. Unsuccessful responses return HTTP status codes 4XX and 5XX with additional error details (code and message). Click [here](#errors) for details on the RBAPI errors.

## Versioning

The API version is specified in the endpoint URL. The current API version is v1.

## Authentication

All RBAPI must be authenticated. RBAPI uses basic HTTP authentication to authenticate user requests. The username and the password are the email and the password associated with the user's account created on the RedBrick247 website (www.redbrick247.com).

## Request-Response Structure and Multiple Carriers

The structure of the RBAPI has been designed to support multiple carriers, and to easily allow developers to switch between carriers while keeping common sections unchanged. For example, the address and dimensions sections are universal and their structure wouldn't change between carrier A and carrier B. Carrier specific data is passed in carrier specific attributes. For example, a USPS shipping label which includes signature confirmation is included in the usps attribute ("usps" : { ..., "services" : ["SignatureConfirmation"] }).

Responses follow the same structure. Shipping metadata which is carrier specific is returned in carrier specific attributes.

Currently the RBAPI supports the following carriers: USPS.

# Labels

## Create a Shipping Label

```shell
curl -u user@email.com:password -X POST --header "Content-Type: application/json" --header "Accept: application/json" -d "{
  \"request_id\": \"AQX90000000234\",
  \"from_address\": {
    \"first_name\": \"John\",
    \"last_name\": \"Michaels\",
    \"line1\": \"247 High St\",
    \"city\": \"Palo Alto\",
    \"state\": \"CA\",
    \"postal_code\": \"94301\"
  },
  \"to_address\": {
    \"first_name\": \"Ted\",
    \"last_name\": \"Resnick\",
    \"line1\": \"350 5th Ave\",
    \"line2\": \"Suite 7510\",
    \"city\": \"New York\",
    \"state\": \"NY\",
    \"postal_code\": \"10118\"
  },
  \"shape\": \"Parcel\",
  \"weight\": 2,
  \"weight_unit\": \"lb\",
  \"image_format\": \"png\",
  \"usps\": {
    \"mail_class\": \"Priority\",
    \"image_size\": \"4x6\",
    \"services\": [
      \"SignatureConfirmation\"
    ]
  }
}" "https://rbapi.redbrick247.com/v1/labels"
```

> The above command returns JSON structured like this:

```json
{
  "request_id" : "AQX90000000234",
  "status" : "created",
  "postage_amount" : 10.44,
  "fees_amount" : 2.35,
  "total_amount" : 12.79,
  "usps": {
    "tracking_numbers" : ["9210890188666700000058"],
    "pricing" : "Commercial Plus",
    "fees" : [{ "name" : "Signature Confirmation", "fee" : 2.35 }],
    "zone": 1
  },
  "base64_labels" : ["iVBORw0KGgoAAAA..."]
}
```

### HTTP Request

`POST https://rbapi.redbrick247.com/v1/labels`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
request_id | (null/empty) | Request ID. Used by API developers to match requests and responses. Echoed back in the request_id tag of the response.
metadata | (null/empty) | Metadata details like references and rubberstamps. See details [here](#metadata-query-parameters)
from_address | (required) | From shipping address. See details [here](#address-query-parameters).
to_address | (required) | To shipping address. See details [here](#address-query-parameters).
shape | (required) | One of Parcel, Flat, FlatRateEnvelope, LegalFlatRateEnvelope, PaddedFlatRateEnvelope, SmallFlatRateBox, MediumFlatRateBox, LargeFlatRateBox, RegionalRateBoxA, RegionalRateBoxB.
weight | (required) | Weight.
weight_unit | (required) | Weight unit. One of oz, lb, g, kg.
dimensions | (null/empty) | Dimensions. See details [here](#dimensions-query-parameters).
dimensions_unit | (required if dimensions provided) | Dimensions unit. One of in, ft, mm, cm, m.
non_delivery_option | Return | Non-delivery option. One of Abandon, Return.
postmark_date | (UTC current date time) | UTC postmark date.
image_format | (required) | Image format. One of png, pdf, svg, zpl.
image_resolution | 300 | Image resolution in DPI. One of 150, 203, 300, 600.
customs_form | (null/empty) | Customs form for military (APO/FPO/DPO) and international shipping. See details [here](#customs_form-query-parameters).
usps | (null/empty) | USPS carrier data. See details [here](#usps-label-query-parameters).

### metadata Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
rubberstamp1 | (null/empty) | User defined rubberstamp. Prints at different locations depending on the label type and size.
rubberstamp2 | (null/empty) | User defined rubberstamp. Prints at different locations depending on the label type and size.
rubberstamp3 | (null/empty) | User defined rubberstamp. Prints at different locations depending on the label type and size.
reference1 | (null/empty) | User defined reference. Becomes searchable field.
reference2 | (null/empty) | User defined reference. Becomes searchable field.
reference3 | (null/empty) | User defined reference. Becomes searchable field.

### address Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
company_name | (conditional) | Company name. Required when first name and last name are not provided.
first_name | (conditional) | First name. Required when company name is not provided.
middle_name | (null/empty) | Middle name.
last_name | (conditional) | Last name. Required when company name is not provided.
phone_number | (null/empty) | Phone number.
email | (null/empty) | Email.
sms | (null/empty) | Sms phone number.
line1 | (required) | Address line 1.
line2 | (null/empty) | Address line 2.
line3 | (null/empty) | Address line 3.
city | (required) | City.
state | (conditional) | State. Required for USPS carrier.
postal_code | (conditional) | Postal code. Zip code for USPS carrier. Required for USPS carrier.
country_code | (conditional) | Required for international requests.

### dimensions Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
width | (required) | Width.
length | (required) | Length.
height | (required) | Height.

### usps Label Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
mail_class | (required) | Mail class. One of Priority, Express, FirstClass, ParcelSelect, ParcelSelectLightweight, LibraryMail, MediaMail, BoundPrintedMatter, FirstClassInternational, PriorityInternational, PriorityExpressInternational.
services | (null/empty) | Services. One of OpenAndDistribute, Fragile, Perishable, LiveAnimal, HazMat, CrematedRemains, Certified, Restricted, Adult, COD, SignatureConfirmation, Insurance, Registered, ReturnReceipt, ReturnReceiptElectronic, ReturnReceiptMerchandise, HoldForPickup.
image_size | 4x6 | Image size.
po_zip_code | (see Description) | Induction zip code. If not provided, it defaults to the mailpiece from address postal code.
presort_level | (conditional) | Presort level. Required for Library Mail, Media Mail, Parcel Select Destination Entry and Parcel Select Lightweight. One of None, 5-Digit, 3-Digit, SCF, NDC, MixedNDC, DDU, Basic.
entry_facility | (conditional) | Entry facility. Required for Parcel Select Destination Entry and Parcel Select Lightweight. One of None, DNDC, DSCF, DDU.
softpack | (conditional) | Boolean softpack indicator. Applies to cubic.

### customs_form Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
contents_type | (required) | Contents type. One of "Documents", "Gift", "Merchandise", "ReturnedGoods", "Sample", "HumanitarianDonation", "DangerousGoods", "Other".
restriction_type | (null/empty) | Restriction type. One of "Quarantine", "SanitaryInspection", "PhytosanitaryInspection", "Other", "None".
restriction_comments | (null/empty) | Restriction comments.
senders_customs_reference | (null/empty) | Sender's customs reference.
importers_customs_reference | (null/empty) | Importer's customs reference.
importers_customs_reference_type | (null/empty) | Importer's customs reference type. One of "TaxCode", "VATNumber", "ImporterCode".
license_number | (null/empty) | License number.
certificate_number | (null/empty) | Certificate number.
invoice_number | (null/empty) | Invoice number.
importers_contact | (null/empty) | Importer's contact phone number.
pfc_itn | (null/empty) | Proof of filing citation ITN number.
insured_number | (null/empty) | Insured reference number.
customs_items | (required) | Customs items collection. See details [here](#customs_item-query-parameters).

### customs_item Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
description | (required) | Description.
quantity | (required) | Quantity.
weight | (required) | Weight.
weight_unit | (required) | Weight unit. One of oz, lb, g, kg.
value | (required) | Value.
hs_tariff_number | (null/empty) | HTS code.
origin_country_code | US | Origin country code.

## Get (Reprint) a Shipping Label

```shell
curl -u user@email.com:password -X GET --header "Content-Type: application/json" --header "Accept: application/json" "https://rbapi.redbrick247.com/v1/labels/9210890188666700000058"
```

> The above command returns JSON structured like this:

```json
{
  "request_id" : "AQX90000000234",
  "status" : "created",
  "postage_amount" : 10.44,
  "fees_amount" : 2.35,
  "total_amount" : 12.79,
  "usps": {
    "tracking_numbers" : ["9210890188666700000058"],
    "pricing" : "Commercial Plus",
    "fees" : [{ "name" : "Signature Confirmation", "fee" : 2.35 }],
    "zone": 1
  },
  "base64_labels" : ["iVBORw0KGgoAAAA..."]
}
```

### HTTP Request

`GET https://rbapi.redbrick247.com/v1/labels/[tracking_number]`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
tracking_number | (required) | Tracking number.

## Delete (Void) a Shipping Label

```shell
curl -u user@email.com:password -X DELETE --header "Content-Type: application/json" --header "Accept: application/json" "https://rbapi.redbrick247.com/v1/labels/9210890188666700000058"
```

> The above command does not return any JSON.

### HTTP Request

`DELETE https://rbapi.redbrick247.com/v1/labels/[tracking_number]`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
tracking_number | (required) | Tracking number.

## Get Shipping Labels

```shell
curl -u user@email.com:password -X GET --header "Content-Type: application/json" --header "Accept: application/json" "https://rbapi.redbrick247.com/v1/labels?start_date=011016&end_date=171016"
```

> The above command returns JSON structured like this:

```json
[
  {
    "request_id" : "AQX90000000234",
    "status" : "created",
    "postage_amount" : 10.44,
    "fees_amount" : 2.35,
    "total_amount" : 12.79,
    "usps": {
      "tracking_numbers" : ["9210890188666700000058"],
      "pricing" : "Commercial Plus",
      "fees" : [{ "name" : "Signature Confirmation", "fee" : 2.35 }],
      "zone": 1
    }
  },
  {
    "request_id": null,
    "status" : "voided",
    "postage_amount": 5.6,
    "fees_amount": 0,
    "total_amount": 5.6,
    "usps": {
      "tracking_numbers": ["9205590188666700000406"],
      "pricing": "Commercial Plus",
      "fees": [{ "name": "USPS Tracking", "fee": 0 }],
      "zone": 2
    }
  }
]
```

### HTTP Request

`GET https://rbapi.redbrick247.com/v1/labels?start_date=[start_date]&end_date=[end_date]`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
start_date | (required) | Start date. Format: ddmmYY.
end_date | (required) | End date. Format: ddmmYY.

# Price

## Calculate Shipping Price

```shell
curl -u user@email.com:password -X POST --header "Content-Type: application/json" --header "Accept: application/json" -d "{
  \"request_id\": \"PRIC920021-3AC\",
  \"from_address\": {
    \"first_name\": \"John\",
    \"last_name\": \"Michaels\",
    \"line1\": \"247 High St\",
    \"city\": \"Palo Alto\",
    \"state\": \"CA\",
    \"postal_code\": \"94301\"
  },
  \"to_address\": {
    \"first_name\": \"Ted\",
    \"last_name\": \"Resnick\",
    \"line1\": \"350 5th Ave\",
    \"line2\": \"Suite 7510\",
    \"city\": \"New York\",
    \"state\": \"NY\",
    \"postal_code\": \"10118\"
  },
  \"shape\": \"Parcel\",
  \"weight\": 2,
  \"weight_unit\": \"lb\",
  \"usps\": {
    \"mail_class\": \"Priority\",
    \"services\": [
      \"SignatureConfirmation\"
    ]
  }
}" "https://rbapi.redbrick247.com/v1/price"
```

> The above command returns JSON structured like this:

```json
{
  "request_id" : "PRIC920021-3AC",
  "postage_amount" : 10.44,
  "fees_amount" : 2.35,
  "total_amount" : 12.79,
  "usps": {
    "pricing" : "Commercial Plus",
    "fees" : [{ "name" : "Signature Confirmation", "fee" : 2.35 }]
  }
}
```

### HTTP Request

`POST https://rbapi.redbrick247.com/v1/price`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
request_id | (null/empty) | Request ID. Used by API developers to match requests and responses. Echoed back in the request_id tag of the response.
from_address | (required) | From shipping address. See details [here](#address-query-parameters).
to_address | (required) | To shipping address. See details [here](#address-query-parameters).
shape | (required) | One of Parcel, Flat, FlatRateEnvelope, LegalFlatRateEnvelope, PaddedFlatRateEnvelope, SmallFlatRateBox, MediumFlatRateBox, LargeFlatRateBox, RegionalRateBoxA, RegionalRateBoxB.
weight | (required) | Weight.
weight_unit | (required) | Weight unit. One of oz, lb, g, kg.
dimensions | (null/empty) | Dimensions. See details [here](#dimensions-query-parameters).
dimensions_unit | (required if dimensions provided) | Dimensions unit. One of in, ft, mm, cm, m.
non_delivery_option | Return | Non delivery option. One of Abandon, Return.
postmark_date | (UTC current date time) | UTC postmark date.
customs_form | (null/empty) | Customs form for military (APO/FPO/DPO) and international shipping. See details [here](#customs_form-query-parameters).
usps | (null/empty) | USPS carrier data. See details [here](#usps-price-query-parameters).

### usps Price Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
mail_class | (required) | Mail class. One of Priority, Express, FirstClass, ParcelSelect, ParcelSelectLightweight, LibraryMail, MediaMail, BoundPrintedMatter, FirstClassInternational, PriorityInternational, PriorityExpressInternational.
services | (null/empty) | Services. One of OpenAndDistribute, Fragile, Perishable, LiveAnimal, HazMat, CrematedRemains, Certified, Restricted, Adult, COD, SignatureConfirmation, Insurance, Registered, ReturnReceipt, ReturnReceiptElectronic, ReturnReceiptMerchandise, HoldForPickup.
po_zip_code | (see Description) | Induction zip code. If not provided, it defaults to the mailpiece from address postal code.
presort_level | (conditional) | Presort level. Required for Library Mail, Media Mail, Parcel Select Destination Entry and Parcel Select Lightweight. One of None, 5-Digit, 3-Digit, SCF, NDC, MixedNDC, DDU, Basic.
entry_facility | (conditional) | Entry facility. Required for Parcel Select Destination Entry and Parcel Select Lightweight. One of None, DNDC, DSCF, DDU.
softpack | (conditional) | Boolean softpack indicator. Applies to cubic.

# Manifests

## Create a Manifest

```shell
curl -u user@email.com:password -X POST --header "Content-Type: application/json" --header "Accept: application/json" -d "{
  \"request_id\": \"MANX000000000C1\",
  \"image_format\": \"png\",
  \"image_resolution\" : 300,
  \"usps\": { }
}" "https://rbapi.redbrick247.com/v1/manifests"
```

> The above command returns JSON structured like this:

```json
{
  "request_id" : "MANX000000000C1",
  "usps" : 
  {
    "manifest_number" : "9275090188665400000024",
    "priority_count" : 1,
    "express_count" : 0,
    "pmi_count" : 0,
    "emi_count" : 0,
    "gxg_count" : 0,
    "other_count" : 0
  },
  "base64_manifests" : ["iVBORw0KGgoAAA..."]
}
```

### HTTP Request

`POST https://rbapi.redbrick247.com/v1/manifests`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
request_id | (null/empty) | Request ID. Used by API developers to match requests and responses. Echoed back in the request_id tag of the response.
image_format | (required) | Image format. One of png, pdf, svg, zpl.
image_resolution | 300 | Image resolution in DPI. One of 150, 203, 300, 600.
usps | (null/empty) | USPS carrier data. See details [here](#usps-manifest-query-parameters).

### usps Manifest Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
zip_code | (see Description) | Induction zip code. Defaults to the user's account address zip code.
zip4 | (see Description) | Induction zip4 code. Defaults to the user's account address zip4 code.
tracking_numbers | (null/empty) | Tracking numbers collection to be included in this manifest. If omitted, all tracking numbers not included in the previous manifest are returned.

## Get (Reprint) a Manifest

```shell
curl -u user@email.com:password -X GET --header "Content-Type: application/json" --header "Accept: application/json" "https://rbapi.redbrick247.com/v1/manifests/9210890188666700000058"
```

> The above command returns JSON structured like this:

```json
{
  "request_id" : "MANX000000000C1",
  "usps" : 
  {
    "manifest_number" : "9275090188665400000024",
    "priority_count" : 1,
    "express_count" : 0,
    "pmi_count" : 0,
    "emi_count" : 0,
    "gxg_count" : 0,
    "other_count" : 0
  },
  "base64_manifests" : ["iVBORw0KGgoAAA..."]
}
```

### HTTP Request

`GET https://rbapi.redbrick247.com/v1/manifests/[tracking_number]`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
tracking_number | (required) | Tracking number.

## Get Manifests

```shell
curl -u user@email.com:password -X GET --header "Content-Type: application/json" --header "Accept: application/json" "https://rbapi.redbrick247.com/v1/manifests?start_date=20161001&end_date=20161031"
```

> The above command returns JSON structured like this:

```json
[
  {
    "usps": {
      "manifest_number": "9275090188665400000017",
      "priority_count": 42,
      "express_count": 1,
      "pmi_count": 0,
      "emi_count": 0,
      "gxg_count": 0,
      "other_count": 0
    }
  },
  {
    "usps": {
      "manifest_number": "9275090188665400000024",
      "priority_count": 1,
      "express_count": 0,
      "pmi_count": 0,
      "emi_count": 0,
      "gxg_count": 0,
      "other_count": 0
    }
  }
]
```

### HTTP Request

`GET https://rbapi.redbrick247.com/v1/manifests?start_date=[start_date]&end_date=[end_date]`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
start_date | (required) | Start date.
end_date | (required) | End date.

# USPS Services

## Get USPS Services

```shell
curl -u user@email.com:password -X POST --header "Content-Type: application/json" --header "Accept: application/json" -d "{
  \"request_id\": \"SVCXXC009282111\",
  \"mail_class\": \"Priority\",
  \"services\" : [\"COD\"]
}" "https://rbapi.redbrick247.com/v1/usps_services"
```

> The above command returns JSON structured like this:

```json
{
  "request_id" : "SVCXXC009282111",
  "services" : ["COD", "ReturnReceipt", "ReturnReceiptElectronic", "Restricted", "SignatureConfirmation"]
}
```

### HTTP Request

`POST https://rbapi.redbrick247.com/v1/usps_services`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
request_id | (null/empty) | Request ID. Used by API developers to match requests and responses. Echoed back in the request_id tag of the response.
mail_class | (required) | Mail class. One of Priority, Express, FirstClass, ParcelSelect, ParcelSelectLightweight, LibraryMail, MediaMail, BoundPrintedMatter, FirstClassInternational, PriorityInternational, PriorityExpressInternational.
services | (null/empty) | Services. One of OpenAndDistribute, Fragile, Perishable, LiveAnimal, HazMat, CrematedRemains, Certified, Restricted, Adult, COD, SignatureConfirmation, Insurance, Registered, ReturnReceipt, ReturnReceiptElectronic, ReturnReceiptMerchandise, HoldForPickup.
value | 0 | Package insured value.
waive_signature | true | Waive signature flag. Used for Express Mail.
country_code | (null/empty) | Required for international mail classes.