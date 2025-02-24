---
title: DetailedBLJ
layout: dev_guide
parent: Orders
nav_order: 5
---
Stable
{: .label .label-green }
# DetailedBLJ Ordering Guide
{:.no_toc}


* TOC
{: toc}


## Overview
Your system is ordering a Detailed BLJ.


{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to.

### Step 1: Authenticate and Receive Tokens
Refer to the [Authentication Guide](/developer_guides/authentication/access_token/) for detailed steps on authentication and token retrieval.


### Step 2: Place an order

This request submits the order details.

Include the access token in the `Authorization` header following the `Bearer` keyword. Replace `your_access_token_here` with the actual token you obtained during the authentication process.

{: .api_ref }
For further explanation of required mandatory fields and their meanings, refer to [DetailedBLJ API Reference](/api/order/order_detailedblj)


#### Example request
The following example orders a DetailedBLJ report.  The configured product has been configured to return a PDF report.  



```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/DetailedBLJ/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json"
-H "Accept: application/json" 
-d '{
  "CorrelationID" : "SAMI_BLJ_1524141772750",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "Kelly",
        "LastName" : "russell",
        "SSN" : "000111000"
      }
    } ]
  }
}'
```





#### Example response

The following example response is from a product that was configured with only the PDF report to be returned. 

```json
{
  "TransactionID" : "0000000000001689427",
  "CorrelationID" : "SAMI_BLJ_1524141772750",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "Kelly",
        "LastName" : "russell",
        "SSN" : "000111000"
      }
    } ]
  },
  "Attachments" : {
    "Attachment" : [ {
      "Document" : "JVBERi0xLjUKJd/++LIKMSAwIG9iago8PC9UeXBlL0NhdGFsb2cvUGFnZXMgMyAwIFIvTA==",
      "Classifier" : "report",
      "ContentDisposition" : "inline; filename=\"PersonDetailedBLJPDF\"; creation-date=\"Thu, 19 Apr 2018 08:43:07 EDT\"; modification-date=\"Thu, 19 Apr 2018 08:43:07 EDT\"; read-date=\"Thu, 19 Apr 2018 08:43:07 EDT\"; size=6674",
      "ContentType" : "application/pdf"
    } ]
  },
  "Messages" : {
    "Message" : [ {
      "Category" : "Info",
      "Code" : "I001",
      "Description" : "Success"
    } ]
  },
  "Status" : {
    "Code" : "S001",
    "Description" : "Serviceable"
  },
  "Ref" : "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000001689427"
}
```


## Other Examples
### Two Reports Returned in Response
The following example orders a DetailedBLJ report.  The product has been configured to return both a PDF and an XML report. 

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/DetailedBLJ/PDF-001
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "CorrelationID" : "SAMI_BLJ_1524142585040",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "Janet",
        "LastName" : "Elahi",
        "SSN" : "111000111"
      }
    } ]
  }
}
'
```

#### Example Response
{:.no_toc}

Notice in the `Attachments` section there are two `Attachment`.  One for the base64 encode PDF file, the other is the XML report.  


```json
{
  "TransactionID" : "0000000000001689431",
  "CorrelationID" : "SAMI_BLJ_1524142585040",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "Janet",
        "LastName" : "Elahi",
        "SSN" : "111000111"
      }
    } ]
  },
  "Attachments" : {
    "Attachment" : [ {
      "Extension" : {
        "any" : "<?xml version=\"1.0\" encoding=\"UTF-16\"?>\n<DetailedBLJReport><Filters><ExcludeBankruptcies>false</ExcludeBankruptcies><SkipNameValidation>false</SkipNameValidation><FilterVerbiage>Judgment satisfied = 7 years; Judgment unsatisfied = 7 years; Lien released = 7 years; Lien unreleased = 10 years; Bankruptcy chapter 7 or 11 dismissed or discharged = 10 years; Bankruptcy chapter 7 or 11 not dismissed or discharged = 10 years; Bankruptcy chapter 13 dismissed or discharged = 7 years; Bankruptcy chapter 13 not dismissed or discharged = 10 years</FilterVerbiage>\n                    </Filters><BankruptciesFound>0</BankruptciesFound><JudgmentsFound>0</JudgmentsFound><LiensFound>0</LiensFound><Messages><Message><Code>0</Code><Text>No Errors</Text>\n                        </Message>\n                    </Messages>\n                </DetailedBLJReport>"
      },
      "ContentType" : "text/xml"
    }, {
      "Document" : "JVBERi0xLjUKJd/++LIKMSAwIG9iago8PC9UeXBlL0NhdGFsb2cvUGFnZXMg",
      "Classifier" : "report",
      "ContentDisposition" : "inline; filename=\"PersonDetailedBLJ\"; creation-date=\"Thu, 19 Apr 2018 08:56:29 EDT\"; modification-date=\"Thu, 19 Apr 2018 08:56:29 EDT\"; read-date=\"Thu, 19 Apr 2018 08:56:29 EDT\"; size=3766",
      "ContentType" : "application/pdf"
    } ]
  },
  "Messages" : {
    "Message" : [ {
      "Category" : "Info",
      "Code" : "I001",
      "Description" : "Success"
    } ]
  },
  "Status" : {
    "Code" : "S001",
    "Description" : "Serviceable"
  },
  "Ref" : "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000011689431"
}
```

### Missing Input Data 
First name is missing. 

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/DetailedBLJ/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "CorrelationID" : "first_name_missing",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "",
        "LastName" : "THOMAS",
        "SSN" : "111111111"
      }
    } ]
  }
}
'
```


#### Example Response
{:.no_toc}

In this response, we see that `Messagges.Message.Category = Fault` so we know that the submission of this order failed.  
Check the `Messages.Message.Description` for more details on how to correct.  

```json
{
  "TransactionID" : "0000000000010129045",
  "CorrelationID" : "first_name_missing",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "",
        "LastName" : "THOMAS",
        "SSN" : "111111111"
      }
    } ]
  },
  "Messages" : {
    "Message" : [ {
      "Category" : "Fault",
      "Code" : "E004",
      "Description" : "/Inputs[1]/Input[1]/Loan[1]/Borrower[1]/Name[1]/First is required."
    } ]
  },
  "Status" : {
    "Code" : "U001",
    "Description" : "Unserviceable"
  },
  "Ref" : "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000010129045"
}

```

### Error Report 
In this sample response, the product was configured to only return an XML report.  The request succeed, but there was a mismatch between name and SSN. 

```json
{
  "TransactionID" : "0000000000000208639",
  "CorrelationID" : "SAMI_BLJ_1524143057520",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "Michael",
        "MiddleName" : "D",
        "LastName" : "Henson",
        "SSN" : "111222333"
      }
    } ]
  },
  "Attachments" : {
    "Attachment" : [ {
      "Extension" : {
        "any" : "<?xml version=\"1.0\" encoding=\"UTF-16\"?>\n<DetailedBLJReport><Filters><ExcludeBankruptcies>false</ExcludeBankruptcies><SkipNameValidation>false</SkipNameValidation><FilterVerbiage>Judgment satisfied = 7 years; Judgment unsatisfied = 7 years; Lien released = 7 years; Lien unreleased = 10 years; Bankruptcy chapter 7 or 11 dismissed or discharged = 10 years; Bankruptcy chapter 7 or 11 not dismissed or discharged = 10 years; Bankruptcy chapter 13 dismissed or discharged = 7 years; Bankruptcy chapter 13 not dismissed or discharged = 10 years</FilterVerbiage>\n                    </Filters><Errors><Error><Code>4</Code><Message>SSN/Name mismatch; check input, if correct contact support@pitchpointsolutions.com</Message>\n                        </Error>\n                    </Errors>\n                </DetailedBLJReport>"
      },
      "ContentType" : "text/xml"
    } ]
  },
  "Messages" : {
    "Message" : [ {
      "Category" : "Info",
      "Code" : "I001",
      "Description" : "Success"
    } ]
  },
  "Status" : {
    "Code" : "S001",
    "Description" : "Serviceable"
  },
  "Ref" : "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000001208639"
}
```

