---
title: IRS Tax Transcripts
layout: api_ref
nav_order: 9
parent: Order
datatable: true
---
Stable
{: .label .label-green }

## Order

Ordering an IRS Tax Transcripts product.

{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to.


```bash
url="https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS4506TTranscript/PDF-001"
# use the `base64` program to encode the pdf file in base64
file=$( base64 -i test-pdf.pdf )
data='{
  "CorrelationID": "request-1",
  "Preferences": {
    "Preference": [
      {
        "Key": "FormRequestType",
        "Value": "1040ReturnTranscript-6a"
      },
      {
        "Key": "TaxClassificationType",
        "Value": "Joint"
      },
      {
        "Key": "YearList",
        "Value": "2025,2024"
      },
      {
        "Key": "JointTaxReturnSSN",
        "Value": "123121230"
      },
      {
        "Key": "JointTaxReturnPersonName",
        "Value": "Milly Duckerson"
      },
      {
        "Key": "USState",
        "Value": "NY"
      }
    ]
  },
  "Terms": {
    "Term": [
      {
        "Person": {
          "FirstName": "Daffy",
          "LastName": "Duckerson",
          "SSN": "123121234"
        }
      }
    ]
  },
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs4506t",
        "ContentType": "application/pdf",
        "Document": "%%FILE_DATA%%"
      }
    ]
  }
}'
# Replace the place holder %%FILE_DATA%% with the actual base64 encoded bytes
data=$(echo "$data" | sed "s#%%FILE_DATA%%#$file#g")

curl -X POST "${url}" \
-H "Authorization: Bearer your_access_token_here" \
-H "Content-Type: application/json" \
-H "Accept: application/json" \
-d "${data}"
```

#### Header Properties
{% include_relative api_includes/header_properties_json.md %}


#### Request Data Properties
<div class="datatable-begin"></div>

| Property      | Description                                                                                           | Type   |
|---------------|-------------------------------------------------------------------------------------------------------|--------|
| Attachments   | (mandatory) See section below for more details.                                                       | object |
| CorrelationID | (mandatory) A user-defined reference number to help identify the order.  Does not need to be unique.  | string |
| Preferences   | (mandatory) See section below for more details.                                                       | object |
| Terms         | (mandatory) See section below for more details.                                                       | object |

<div class="datatable-end"></div>

##### Attachments.Attachment
<div class="datatable-begin"></div>

| Property    | Description                                                                             | Type   |
|-------------|-----------------------------------------------------------------------------------------|--------|
| Classifier  | (mandatory) Must be the string `irs4506t`                                                | string |
| ContentType | (mandatory) The mime-type for the input file.  Must be `application/pdf`                 | string |
| Document    | (mandatory) The base64 encoded bytes of the file                                        | string |

<div class="datatable-end"></div>

Exactly one PDF attachment must be supplied.

##### Preferences.Preference
<div class="datatable-begin"></div>

| Key                      | Description                                                                                                                                                                                                                                                                                                                                                                  | Type   |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| FormRequestType          | (mandatory) Transcript form requested.  Must be one of <br/>`1040ReturnTranscript-6a`,<br/> `1040AccountTranscript-6b`,<br/> `1040RecordOfAccount-6c`,<br/> `1065ReturnTranscript-6a`,<br/> `1065AccountTranscript-6b`,<br/> `1065RecordOfAccount-6c`,<br/> `1120ReturnTranscript-6a`,<br/> `1120AccountTranscript-6b`,<br/> `1120RecordOfAccount-6c`,<br/> `W2_1099-7`,<br/> or `W2_1099-8`.<br/><br/>Note: `W2_1099-7` is not valid when `TaxClassificationType` is `Joint`. | string |
| TaxClassificationType    | (mandatory) Tax classification of the order.  Must be one of `Individual`, `Joint`, or `Company`.                                                                                                                                                                                                                                                                           | string |
| YearList                 | (mandatory) A comma separated list of years requested.  Valid years are current year and up to 4 years ago.  E.g. This year is 2026.  Therefore valid years are: 2026,2025,2024,2023,2022                                                                                                                                                                                    | string |
| JointTaxReturnSSN        | (mandatory when `TaxClassificationType` is `Joint`) Social Security Number of the joint/spouse taxpayer on the return.                                                                                                                                                                                                                                                       | string |
| JointTaxReturnPersonName | (mandatory when `TaxClassificationType` is `Joint`) Full name (`First Last`) of the joint/spouse taxpayer on the return.                                                                                                                                                                                                                                                     | string |
| USState                  | (optional) 2-character US state code.  Passed through to the external tax-return verification (TRV) vendor.                                                                                                                                                                                                                                                                  | string |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant
<div class="datatable-begin"></div>

| Property    | Description                                                                                                          | Type   |
|-------------|--------------------------------------------------------------------------------------------------------------------|--------|
| Person      | Required for `Individual` and `Joint` classifications.  See section below for more details.                          | object |
| Participant | Required for `Company` classification.  An EIN is mandatory.  See section below for more details. | object |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant.Person
<div class="datatable-begin"></div>

| Property   | Description                                            | Type   |
|------------|--------------------------------------------------------|--------|
| FirstName  | (mandatory) First name of the person                   | string |
| MiddleName | (optional) Middle name of the person                   | string |
| LastName   | (mandatory) Last name of the person                    | string |
| SSN        | (mandatory) Social Security Number of the person       | string |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant.Participant
<div class="datatable-begin"></div>

| Property    | Description                                                                                                      | Type         |
|-------------|-----------------------------------------------------------------------------------------------------------------|--------------|
| CompanyName | (mandatory) Company name                                                                                         | string       |
|             | (mandatory) Items.ParticipantItem                                                                                | object/array |
| Key         | (mandatory) Must be the value `TaxIdentificationEIN`                                                             | string       |
| Value       | (mandatory) Employer Identification Number                                                                       | string       |

<div class="datatable-end"></div>




### Responses

200
{: .label .label-green }

The input data is echo-ed back with following additional fields:

<div class="datatable-begin"></div>

| Property      | Description                                                                                                                                                                                                                                                                                 | Type   |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| TransactionID | A Pitchpoint generated identifying string to uniquely identify this loan                                                                                                                                                                                                                    | string |
|               | Messages.Message -- A list of messages regarding the result of the order                                                                                                                                                                                                                    |        |
| Category      | Typically one of `Info` or `Fault` to indicate whether or not this is an information message or an error message.                                                                                                                                                                           | string |
| Code          | Unique code for the `Message`.  Typically, this is `I004` when an order has initially been submitted, as PPS is still processing the order.  When the report has successfully been generated, you should see a value of `I001`.  A value starting with `EXXX` generally indicates an error. | string |
| Description   | Description of the message                                                                                                                                                                                                                                                                  | string |
| Ref           | The url to query to retrieve the results of the order                                                                                                                                                                                                                                       | string |
|               | Status -- An object that states the current status of the order as related to billing matters.                                                                                                                                                                                              |        |
| Code          | The status code.  This will typically be `U001` during the ordering phase as Pitchpoint has not completed generating the reports. However once the report has been successfully generated this will typically become `S001`                                                                 | string |
| Description   | A description of the code                                                                                                                                                                                                                                                                   | string |


<div class="datatable-end"></div>

Example:
```json
{
  "TransactionID": "0000000000000134076",
  "Messages": {
    "Message": [
      {
        "Category": "Fault",
        "Code": "E001",
        "Description": "1980 is not a valid YearList value. Valid values are 2022, 2023, 2024, 2025."
      }
    ]
  },
  "Ref": "http://localhost:8080/riskinsight-services-ws/resources/v1/sami/0000000000000134076",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "(echoed input data)": ...,
}
```

For a complete list of possible [Message](/api/order/enums/messagedef/)



