---
title: IRS Tax Transcripts
layout: dev_guide
parent: Orders
nav_order: 20
---
Stable
{: .label .label-green }
# IRS Tax Transcripts Ordering Guide
{:.no_toc}

* TOC
{: toc}



## Overview
Your system is ordering an IRS Transcript through the 4506-T form.


{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to.


### Step 1: Authenticate and Receive Tokens
Refer to the [Authentication Guide](/developer_guides/authentication/access_token/) for detailed steps on authentication and token retrieval.


### Step 2: Place an order

This request submits the order details along with a PDF that includes a completed 4506-T form.

Include the access token in the `Authorization` header following the `Bearer` keyword. Replace `your_access_token_here` with the actual token you obtained during the authentication process.

{: .api_ref }
For further explanation of required mandatory fields and their meanings, refer to [IRS Tax Transcripts](/api/order/order_irs4506ttranscript)


#### Example Joint request
The following example orders a 1040 Return Transcript (6a) for a joint return for the years 2025 and 2024.



```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS4506TTranscript/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-H "Accept: application/json" 
-d '{
  "CorrelationID": "JointIRS4506T1040",
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
        "Document": "#BASE64PDF"
      }
    ]
  }
}'
```






#### Example response

```json
{
  "CorrelationID": "JointIRS4506T1040",
  "Messages": {
    "Message": [
      {
        "Category": "Info",
        "Code": "I004",
        "Description": "Evaluation was delayed."
      }
    ]
  },
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
  "Ref": "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000000000",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
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
        "Document": "#BASE64PDF"
      }
    ]
  },
  "TransactionID": "0000000000000000000"
}
```


### Step 3: Check the response for a result using the URL provided in the Ref field
As you can see from the above response, it may take some time for the final report to be generated.  
(`Messages.Message.Code = I004` -- The order was successfully submitted, but the PPS system has yet to complete the order.)  
Using the url returned in the `Ref`, poll (we suggest every 10 minutes) until you receive the report.  


```bash
curl -X GET https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000000000 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 

```


#### Example response


```json
{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "report",
        "ContentDisposition": "inline; filename=\"1040 Return Transcript.2025.pdf\"; creation-date=\"Thu, 30 May 2024 14:35:52 EDT\"; modification-date=\"Thu, 30 May 2024 14:35:52 EDT\"; read-date=\"Thu, 30 May 2024 14:35:52 EDT\"; size=253797",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  },
  "CorrelationID": "JointIRS4506T1040",
  "Messages": {
    "Message": [
      {
        "Category": "Info",
        "Code": "I001",
        "Description": "Success"
      }
    ]
  },
  "Status": {
    "Code": "S001",
    "Description": "Serviceable"
  },
  "TransactionID": "0000000000000134063"
}

```

The response came back with `Messages.Message.Code = I001` which indicates that the order has successfully completed.  The PDF bytes are in the 
`Document` field, encoded as base64.  Decode to get the pdf.   

Assuming the response json was saved into the file called `file.json`

```bash
# Using the json (bash) program called `jq`, select the object in the Attachments with classifier = report 
# and use the (bash) program called `base64` to decode the file into `mypdf.pdf`

jq -r '.Attachments.Attachment[] | select(.Classifier == "report") | .Document' file.json | base64 --decode > mypdf.pdf    
```` 

## Other Examples
### Individual Request
The following example orders a 1040 Return Transcript (6a) for an individual for the years 2025 and 2024.

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS4506TTranscript/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "CorrelationID": "IndividualIRS4506T1040",
  "Preferences": {
    "Preference": [
      {
        "Key": "FormRequestType",
        "Value": "1040ReturnTranscript-6a"
      },
      {
        "Key": "TaxClassificationType",
        "Value": "Individual"
      },
      {
        "Key": "YearList",
        "Value": "2025,2024"
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
        "Document": "#BASE64PDF"
      }
    ]
  }
}
'
```

### Company Request
The following example demonstrates how to order a 1120 Return Transcript (6a) for a company for the years 2025 and 2024 by providing a company `Participant`.

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS4506TTranscript/PDF-001
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "CorrelationID": "CompanyIRS4506T1120",
  "Preferences": {
    "Preference": [
      {
        "Key": "FormRequestType",
        "Value": "1120ReturnTranscript-6a"
      },
      {
        "Key": "TaxClassificationType",
        "Value": "Company"
      },
      {
        "Key": "YearList",
        "Value": "2025,2024"
      }
    ]
  },
  "Terms": {
    "Term": [
      {
        "Participant": {
          "CompanyName": "Company!",
          "Items": {
            "ParticipantItem": [
              {
                "Key": "TaxIdentificationEIN",
                "Value": "100001234"
              }
            ]
          }
        }
      }
    ]
  },
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs4506t",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  }
}
'
```

### Invalid Request
This would order a 1040 Return Transcript (6a) for a Joint return, but omits the mandatory `JointTaxReturnSSN` and `JointTaxReturnPersonName` preferences.

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS4506TTranscript/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "CorrelationID": "JointIRS4506T1040",
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
        "Document": "#BASE64PDF"
      }
    ]
  }
}

'
```


#### Example Response
{:.no_toc}

In this response, we see that `Messages.Message.Category = Fault` so we know that the submission of this order failed.  
Check the `Messages.Message.Description` for more details on how to correct.  

```json
{
  "CorrelationID": "JointIRS4506T1040",
  "Messages": {
    "Message": [
      {
        "Category": "Fault",
        "Code": "E012",
        "Description": "Value not supplied for preference key JointTaxReturnSSN"
      },
      {
        "Category": "Fault",
        "Code": "E012",
        "Description": "Value not supplied for preference key JointTaxReturnPersonName"
      }
    ]
  },
  "Ref": "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000000000",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "TransactionID": "0000000000000000000"
}

```
