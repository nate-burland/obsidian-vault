### PATCH
 - This was an initialization request. It sets the default models for the cu-resource-nate Azure resource. This is a one time request.
#### Request:
``` bash
curl --location --request PATCH 'https://cu-resource-nate.services.ai.azure.com/contentunderstanding/defaults?api-version=2025-11-01' \
--header 'Ocp-Apim-Subscription-Key: {CU_RESOURCE_KEY}' \
--header 'Content-Type: application/json' \
--data '{
    "modelDeployments": {
        "gpt-4.1": "gpt-4.1-982065",
        "text-embedding-3-large": "text-embedding-3-large-383240"
    }
}'
```

Response was just confirming the models were set:
#### Response:
```json
{
    "modelDeployments": {
        "gpt-4.1": "gpt-4.1-982065",
        "text-embedding-3-large": "text-embedding-3-large-383240"
    }
}
```

### POST
 - This request actually starts the analysis. It pulls a file from the Azure blob storage and sends it to Content Understanding for analysis.
	 - loadDocumentAnalyzer is the name of the analyzer built on the Content Understanding portal.
	 - Input is the link to the file on the blob storage.

#### Request:
```bash
curl --location 'https://cu-resource-nate.services.ai.azure.com/contentunderstanding/analyzers/loadDocumentAnalyzer:analyze?api-version=2025-11-01' \
--header 'Ocp-Apim-Subscription-Key: {CU_RESOURCE_KEY}' \
--header 'Content-Type: application/json' \
--data '{
    "inputs":[{"url": "https://bldemodocumentstorage.blob.core.windows.net/blob-container/Invoice/invoice1.pdf"}]
}'
```

Response returned a confirmation that the analysis has started:

#### Response:
```json
{
    "id": "d21b8c90-95dc-4e4f-a0f8-a2fc2e83b756",
    "status": "Running",
    "result": {
        "analyzerId": "loadDocumentAnalyzer",
        "apiVersion": "2025-11-01",
        "createdAt": "2025-12-17T02:02:08Z",
        "warnings": [],
        "contents": []
    }
}
```


### GET
* This request uses the `id` field from the confirmation response to get the results of the analysis. 
	* I haven't tested, but if this call is made before the analysis is finished I'm assuming it will say `"status": "Running"` like in the previous request. TEST THIS
#### Request:
```bash
curl --location 'https://cu-resource-nate.services.ai.azure.com/contentunderstanding/analyzerResults/b5bafee8-95f7-49b8-bd5b-5bd5c8e29000?api-version=2025-11-01' \

--header 'Ocp-Apim-Subscription-Key: #{{CU_RESOURCE_KEY}}'
```
#### Response:
```json
{
    "id": "b5bafee8-95f7-49b8-bd5b-5bd5c8e29000",
    "status": "Succeeded",
    "result": {
        "analyzerId": "loadDocumentAnalyzer",
        "apiVersion": "2025-11-01",
        "createdAt": "2025-12-17T01:59:27Z",
        "warnings": [],
        "contents": [
            {
                "path": "input1",
                "markdown": "Dec 5, 2025\n\n\n# HERITAGE GRAIN LLC Caruthersville, MO\n\nInv: 541676\n\nDate: Dec 05, 2025\n\n\n<table>\n<tr>\n<th>Company</th>\n<th>HERITAGE GRAIN LLC</th>\n</tr>\n<tr>\n<th>Contact</th>\n<th>Chase Letner</th>\n</tr>\n<tr>\n<th>Dispatcher Email</th>\n<th>chaseheritagegrainllc@outlook. com</th>\n</tr>\n<tr>\n<th>BILLING Email</th>\n<th>apheritagegrainllc@outlook. com</th>\n</tr>\n</table>\n\n\nBill\nTo\nSMART FREIGHT FUNDING, LLC\nSmart Freight Funding\nOmaha NE\n800-838-2907\nBILLING@SMARTFREIGHTFUNDING.\nCOM\n\nHauled: 12/03/25\nHired by:\nINGREDIENT LOGISTICS SERVICES INC - St. Louis, MO\n\nLoad #: RLB-46703\n\nNotes: TIME IS ON PAPERWORK FOR DETENTION\n\n\n<table>\n<tr>\n<th>Origin</th>\n<th>Destination</th>\n<th>Cmdty</th>\n<th>Rate</th>\n<th>Bill Wt/Vol</th>\n<th>Amount</th>\n</tr>\n<tr>\n<td>Caruthersville, MO RIVERSIDE RICE LLC #101431-28 Ticket: 17021 25 tons</td>\n<td>Lebanon, TN Royal Canin USA #RLB-46703 Ticket: 46703 25 tons</td>\n<td>BROWN RICE</td>\n<td>Rate: $47/ton BOL: RR17021</td>\n<td>50,000 lbs</td>\n<td>$1,175.00</td>\n</tr>\n<tr>\n<td colspan=\"5\">Load Total</td>\n<td>$1,175.00</td>\n</tr>\n</table>\n\n\n<!-- PageFooter=\"This invoice was generated using the BulkLoads.com Mobile App. Visit BulkTMS.com for more details.\" -->\n",
                "fields": {
                    "DriverName": {
                        "type": "string",
                        "confidence": 0.782
                    },
                    "OriginCity": {
                        "type": "string",
                        "valueString": "Caruthersville",
                        "spans": [
                            {
                                "offset": 755,
                                "length": 14
                            }
                        ],
                        "confidence": 0.957,
                        "source": "D(1,0.6526,5.0330,1.4861,5.0309,1.4864,5.1731,0.6537,5.1728)"
                    },
                    "OriginState": {
                        "type": "string",
                        "valueString": "MO",
                        "spans": [
                            {
                                "offset": 51,
                                "length": 2
                            }
                        ],
                        "confidence": 0.969,
                        "source": "D(1,1.9648,1.2120,2.2806,1.2116,2.2806,1.4104,1.9650,1.4100)"
                    },
                    "Date": {
                        "type": "date",
                        "valueDate": "2025-12-05",
                        "spans": [
                            {
                                "offset": 74,
                                "length": 12
                            }
                        ],
                        "confidence": 0.875,
                        "source": "D(1,6.2841,1.0799,7.5413,1.0794,7.5414,1.2923,6.2842,1.2928)"
                    },
                    "BookedThroughCompany": {
                        "type": "string",
                        "valueString": "INGREDIENT LOGISTICS SERVICES INC",
                        "spans": [
                            {
                                "offset": 510,
                                "length": 33
                            }
                        ],
                        "confidence": 0.805,
                        "source": "D(1,0.6526,3.9547,3.4425,3.9598,3.4422,4.1272,0.6523,4.1222)"
                    },
                    "LoadNumber": {
                        "type": "string",
                        "valueString": "RLB-46703",
                        "spans": [
                            {
                                "offset": 569,
                                "length": 9
                            }
                        ],
                        "confidence": 0.964,
                        "source": "D(1,1.1874,4.1787,1.9320,4.1787,1.9320,4.3290,1.1874,4.3290)"
                    },
                    "DestinationCity": {
                        "type": "string",
                        "valueString": "Lebanon",
                        "spans": [
                            {
                                "offset": 835,
                                "length": 7
                            }
                        ],
                        "confidence": 0.964,
                        "source": "D(1,2.2848,5.0334,2.8040,5.0327,2.8040,5.1724,2.2848,5.1724)"
                    },
                    "DestinationState": {
                        "type": "string",
                        "valueString": "TN",
                        "spans": [
                            {
                                "offset": 844,
                                "length": 2
                            }
                        ],
                        "confidence": 0.963,
                        "source": "D(1,2.8776,5.0327,3.0568,5.0327,3.0568,5.1724,2.8776,5.1724)"
                    },
                    "CompanyName": {
                        "type": "string",
                        "valueString": "HERITAGE GRAIN LLC",
                        "spans": [
                            {
                                "offset": 16,
                                "length": 18
                            }
                        ],
                        "confidence": 0.956,
                        "source": "D(1,0.5925,0.9522,2.9823,0.9540,2.9821,1.1722,0.5923,1.1705)"
                    },
                    "Time": {
                        "type": "time",
                        "confidence": 0.904
                    },
                    "Commodity": {
                        "type": "string",
                        "valueString": "BROWN RICE",
                        "spans": [
                            {
                                "offset": 905,
                                "length": 10
                            }
                        ],
                        "confidence": 0.962,
                        "source": "D(1,3.5675,5.3039,4.3985,5.3229,4.3951,5.4721,3.5641,5.4531)"
                    },
                    "NetWeight": {
                        "type": "string",
                        "valueString": "50,000 lbs",
                        "spans": [
                            {
                                "offset": 961,
                                "length": 10
                            }
                        ],
                        "confidence": 0.874,
                        "source": "D(1,5.7977,5.3142,6.3958,5.3124,6.3962,5.4623,5.7981,5.4641)"
                    },
                    "RateAmount": {
                        "type": "string",
                        "valueString": "47",
                        "spans": [
                            {
                                "offset": 932,
                                "length": 2
                            }
                        ],
                        "confidence": 0.901,
                        "source": "D(1,5.0798,5.2468,5.2195,5.2471,5.2195,5.3792,5.0798,5.3792)"
                    },
                    "RateUnit": {
                        "type": "string",
                        "valueString": "ton",
                        "spans": [
                            {
                                "offset": 935,
                                "length": 3
                            }
                        ],
                        "confidence": 0.888,
                        "source": "D(1,5.2550,5.2472,5.4412,5.2474,5.4412,5.3792,5.2550,5.3792)"
                    },
                    "NumberOfLoads": {
                        "type": "string",
                        "confidence": 0.782
                    },
                    "DocumentCategory": {
                        "type": "string",
                        "valueString": "load invoice",
                        "spans": [
                            {
                                "offset": 1097,
                                "length": 7
                            }
                        ],
                        "confidence": 0.234,
                        "source": "D(1,1.0751,10.6237,1.5603,10.6229,1.5622,10.8070,1.0771,10.8064)"
                    },
                    "Surcharges": {
                        "type": "string",
                        "confidence": 0.782
                    },
                    "TicketNumber": {
                        "type": "string",
                        "valueString": "17021",
                        "spans": [
                            {
                                "offset": 812,
                                "length": 5
                            }
                        ],
                        "confidence": 0.872,
                        "source": "D(1,1.0720,5.4666,1.4080,5.4652,1.4080,5.5972,1.0725,5.5969)"
                    }
                },
                "kind": "document",
                "startPageNumber": 1,
                "endPageNumber": 1,
                "unit": "inch",
                "pages": [
                    {
                        "pageNumber": 1,
                        "angle": 0,
                        "width": 8.5,
                        "height": 11,
                        "spans": [
                            {
                                "offset": 0,
                                "length": 1196
                            }
                        ],
                        "words": [
                            {
                                "content": "Dec",
                                "span": {
                                    "offset": 0,
                                    "length": 3
                                },
                                "confidence": 0.989,
                                "source": "D(1,3.8308,0.2202,4.1121,0.2191,4.1121,0.3866,3.8308,0.3871)"
                            },
                            {
                                "content": "5",
                                "span": {
                                    "offset": 4,
                                    "length": 1
                                },
                                "confidence": 0.947,
                                "source": "D(1,4.1503,0.2189,4.2295,0.2185,4.2295,0.3864,4.1503,0.3865)"
                            },
                            {
                                "content": ",",
                                "span": {
                                    "offset": 5,
                                    "length": 1
                                },
                                "confidence": 0.963,
                                "source": "D(1,4.2322,0.2185,4.2677,0.2183,4.2677,0.3863,4.2322,0.3864)"
                            },
                            {
                                "content": "2025",
                                "span": {
                                    "offset": 7,
                                    "length": 4
                                },
                                "confidence": 0.977,
                                "source": "D(1,4.3059,0.2181,4.6609,0.2155,4.6609,0.3858,4.3059,0.3863)"
                            },
                            {
                                "content": "HERITAGE",
                                "span": {
                                    "offset": 16,
                                    "length": 8
                                },
                                "confidence": 0.997,
                                "source": "D(1,0.5925,0.9561,1.7144,0.9532,1.7144,1.1708,0.5925,1.1705)"
                            },
                            {
                                "content": "GRAIN",
                                "span": {
                                    "offset": 25,
                                    "length": 5
                                },
                                "confidence": 0.995,
                                "source": "D(1,1.7909,0.9531,2.4757,0.9536,2.4757,1.1713,1.7909,1.1708)"
                            },
                            {
                                "content": "LLC",
                                "span": {
                                    "offset": 31,
                                    "length": 3
                                },
                                "confidence": 0.994,
                                "source": "D(1,2.5522,0.9537,2.9821,0.9547,2.9821,1.1718,2.5522,1.1714)"
                            },
                            {
                                "content": "Caruthersville",
                                "span": {
                                    "offset": 35,
                                    "length": 14
                                },
                                "confidence": 0.996,
                                "source": "D(1,0.5961,1.2141,1.8562,1.2122,1.8565,1.4099,0.5971,1.4073)"
                            },
                            {
                                "content": ",",
                                "span": {
                                    "offset": 49,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,1.8595,1.2121,1.9023,1.2121,1.9025,1.4099,1.8598,1.4099)"
                            },
                            {
                                "content": "MO",
                                "span": {
                                    "offset": 51,
                                    "length": 2
                                },
                                "confidence": 0.996,
                                "source": "D(1,1.9648,1.212,2.2806,1.2116,2.2806,1.4104,1.965,1.41)"
                            },
                            {
                                "content": "Inv",
                                "span": {
                                    "offset": 55,
                                    "length": 3
                                },
                                "confidence": 0.876,
                                "source": "D(1,6.3501,0.8358,6.6495,0.8352,6.6495,1.0337,6.3501,1.0338)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 58,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,6.6593,0.8352,6.7113,0.8351,6.7113,1.0337,6.6593,1.0337)"
                            },
                            {
                                "content": "541676",
                                "span": {
                                    "offset": 60,
                                    "length": 6
                                },
                                "confidence": 0.995,
                                "source": "D(1,6.7732,0.8349,7.4956,0.8318,7.4956,1.0321,6.7732,1.0336)"
                            },
                            {
                                "content": "Date",
                                "span": {
                                    "offset": 68,
                                    "length": 4
                                },
                                "confidence": 0.997,
                                "source": "D(1,5.7026,1.083,6.1518,1.0826,6.1518,1.2909,5.7026,1.2892)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 72,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,6.1658,1.0825,6.218,1.0825,6.218,1.2912,6.1658,1.291)"
                            },
                            {
                                "content": "Dec",
                                "span": {
                                    "offset": 74,
                                    "length": 3
                                },
                                "confidence": 0.963,
                                "source": "D(1,6.2842,1.0824,6.6568,1.0817,6.6568,1.2921,6.2842,1.2914)"
                            },
                            {
                                "content": "05",
                                "span": {
                                    "offset": 78,
                                    "length": 2
                                },
                                "confidence": 0.879,
                                "source": "D(1,6.7229,1.0816,6.9528,1.0811,6.9528,1.2925,6.7229,1.2922)"
                            },
                            {
                                "content": ",",
                                "span": {
                                    "offset": 80,
                                    "length": 1
                                },
                                "confidence": 0.924,
                                "source": "D(1,6.9562,1.0811,7.0015,1.081,7.0015,1.2925,6.9562,1.2925)"
                            },
                            {
                                "content": "2025",
                                "span": {
                                    "offset": 82,
                                    "length": 4
                                },
                                "confidence": 0.93,
                                "source": "D(1,7.0642,1.0808,7.5413,1.0794,7.5413,1.2921,7.0642,1.2924)"
                            },
                            {
                                "content": "Company",
                                "span": {
                                    "offset": 106,
                                    "length": 7
                                },
                                "confidence": 0.999,
                                "source": "D(1,1.0241,1.6945,1.6685,1.696,1.6664,1.8528,1.0231,1.8547)"
                            },
                            {
                                "content": "HERITAGE",
                                "span": {
                                    "offset": 123,
                                    "length": 8
                                },
                                "confidence": 0.995,
                                "source": "D(1,1.7899,1.6844,2.5374,1.6823,2.5374,1.8382,1.7899,1.8384)"
                            },
                            {
                                "content": "GRAIN",
                                "span": {
                                    "offset": 132,
                                    "length": 5
                                },
                                "confidence": 0.992,
                                "source": "D(1,2.5841,1.6822,3.0384,1.6816,3.0384,1.8382,2.5841,1.8382)"
                            },
                            {
                                "content": "LLC",
                                "span": {
                                    "offset": 138,
                                    "length": 3
                                },
                                "confidence": 0.988,
                                "source": "D(1,3.0877,1.6815,3.368,1.6813,3.368,1.8383,3.0877,1.8383)"
                            },
                            {
                                "content": "Contact",
                                "span": {
                                    "offset": 162,
                                    "length": 7
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.1455,1.9641,1.6695,1.9628,1.6695,2.1011,1.1455,2.1033)"
                            },
                            {
                                "content": "Chase",
                                "span": {
                                    "offset": 179,
                                    "length": 5
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.795,1.9614,2.217,1.9619,2.217,2.1093,1.795,2.1079)"
                            },
                            {
                                "content": "Letner",
                                "span": {
                                    "offset": 185,
                                    "length": 6
                                },
                                "confidence": 0.998,
                                "source": "D(1,2.263,1.9619,2.6874,1.9616,2.6874,2.1094,2.263,2.1094)"
                            },
                            {
                                "content": "Dispatcher",
                                "span": {
                                    "offset": 212,
                                    "length": 10
                                },
                                "confidence": 0.997,
                                "source": "D(1,0.9525,2.2396,1.6664,2.2382,1.6664,2.3896,0.9525,2.394)"
                            },
                            {
                                "content": "Email",
                                "span": {
                                    "offset": 223,
                                    "length": 5
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.2887,2.41,1.6633,2.4081,1.6633,2.5464,1.2887,2.5472)"
                            },
                            {
                                "content": "chaseheritagegrainllc",
                                "span": {
                                    "offset": 238,
                                    "length": 21
                                },
                                "confidence": 0.183,
                                "source": "D(1,1.7888,2.2379,3.1803,2.2311,3.1806,2.3993,1.7899,2.4019)"
                            },
                            {
                                "content": "@",
                                "span": {
                                    "offset": 259,
                                    "length": 1
                                },
                                "confidence": 0.147,
                                "source": "D(1,3.183,2.2311,3.3111,2.2316,3.3113,2.3991,3.1834,2.3993)"
                            },
                            {
                                "content": "outlook",
                                "span": {
                                    "offset": 260,
                                    "length": 7
                                },
                                "confidence": 0.206,
                                "source": "D(1,3.3166,2.2316,3.8036,2.2335,3.8037,2.3983,3.3169,2.3991)"
                            },
                            {
                                "content": ".",
                                "span": {
                                    "offset": 267,
                                    "length": 1
                                },
                                "confidence": 0.988,
                                "source": "D(1,3.8009,2.2335,3.837,2.2337,3.837,2.3982,3.8009,2.3983)"
                            },
                            {
                                "content": "com",
                                "span": {
                                    "offset": 269,
                                    "length": 3
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.795,2.4294,2.0845,2.4259,2.0845,2.5487,1.795,2.5518)"
                            },
                            {
                                "content": "BILLING",
                                "span": {
                                    "offset": 293,
                                    "length": 7
                                },
                                "confidence": 0.992,
                                "source": "D(1,1.0656,2.6831,1.6612,2.683,1.6612,2.8249,1.0656,2.827)"
                            },
                            {
                                "content": "Email",
                                "span": {
                                    "offset": 301,
                                    "length": 5
                                },
                                "confidence": 0.997,
                                "source": "D(1,1.2607,2.8628,1.6653,2.8628,1.6653,2.9971,1.2607,2.9971)"
                            },
                            {
                                "content": "apheritagegrainllc",
                                "span": {
                                    "offset": 316,
                                    "length": 18
                                },
                                "confidence": 0.22,
                                "source": "D(1,1.7857,2.6838,3.0471,2.6803,3.0479,2.8509,1.7878,2.8497)"
                            },
                            {
                                "content": "@",
                                "span": {
                                    "offset": 334,
                                    "length": 1
                                },
                                "confidence": 0.188,
                                "source": "D(1,3.0471,2.6803,3.1857,2.68,3.1863,2.8502,3.0479,2.8509)"
                            },
                            {
                                "content": "outlook",
                                "span": {
                                    "offset": 335,
                                    "length": 7
                                },
                                "confidence": 0.278,
                                "source": "D(1,3.1886,2.68,3.7288,2.6793,3.7288,2.8458,3.1892,2.8501)"
                            },
                            {
                                "content": ".",
                                "span": {
                                    "offset": 342,
                                    "length": 1
                                },
                                "confidence": 0.992,
                                "source": "D(1,3.7259,2.6793,3.7769,2.6793,3.7769,2.8454,3.726,2.8458)"
                            },
                            {
                                "content": "com",
                                "span": {
                                    "offset": 344,
                                    "length": 3
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.7909,2.8789,2.1084,2.8789,2.1084,3.0032,1.7919,3.0032)"
                            },
                            {
                                "content": "Bill",
                                "span": {
                                    "offset": 370,
                                    "length": 4
                                },
                                "confidence": 0.993,
                                "source": "D(1,4.3787,1.694,4.582,1.6937,4.582,1.8278,4.3787,1.8261)"
                            },
                            {
                                "content": "To",
                                "span": {
                                    "offset": 375,
                                    "length": 2
                                },
                                "confidence": 0.948,
                                "source": "D(1,4.4036,1.8666,4.582,1.8664,4.582,1.9981,4.4036,1.9971)"
                            },
                            {
                                "content": "SMART",
                                "span": {
                                    "offset": 378,
                                    "length": 5
                                },
                                "confidence": 0.993,
                                "source": "D(1,4.7148,1.686,5.2453,1.6842,5.2453,1.8459,4.7148,1.845)"
                            },
                            {
                                "content": "FREIGHT",
                                "span": {
                                    "offset": 384,
                                    "length": 7
                                },
                                "confidence": 0.988,
                                "source": "D(1,5.2866,1.684,5.9215,1.6827,5.9215,1.8472,5.2866,1.8459)"
                            },
                            {
                                "content": "FUNDING",
                                "span": {
                                    "offset": 392,
                                    "length": 7
                                },
                                "confidence": 0.993,
                                "source": "D(1,5.9655,1.6826,6.6169,1.6825,6.6169,1.849,5.9655,1.8473)"
                            },
                            {
                                "content": ",",
                                "span": {
                                    "offset": 399,
                                    "length": 1
                                },
                                "confidence": 0.997,
                                "source": "D(1,6.6224,1.6825,6.6581,1.6826,6.6581,1.8491,6.6224,1.849)"
                            },
                            {
                                "content": "LLC",
                                "span": {
                                    "offset": 401,
                                    "length": 3
                                },
                                "confidence": 0.996,
                                "source": "D(1,6.7131,1.6826,7.0017,1.6828,7.0017,1.85,6.7131,1.8492)"
                            },
                            {
                                "content": "Smart",
                                "span": {
                                    "offset": 405,
                                    "length": 5
                                },
                                "confidence": 0.996,
                                "source": "D(1,4.7148,1.8581,5.1188,1.8613,5.1188,2.022,4.7148,2.0184)"
                            },
                            {
                                "content": "Freight",
                                "span": {
                                    "offset": 411,
                                    "length": 7
                                },
                                "confidence": 0.994,
                                "source": "D(1,5.1565,1.8616,5.6117,1.8635,5.6117,2.0251,5.1565,2.0223)"
                            },
                            {
                                "content": "Funding",
                                "span": {
                                    "offset": 419,
                                    "length": 7
                                },
                                "confidence": 0.995,
                                "source": "D(1,5.6494,1.8636,6.1799,1.8635,6.1799,2.0267,5.6494,2.0253)"
                            },
                            {
                                "content": "Omaha",
                                "span": {
                                    "offset": 427,
                                    "length": 5
                                },
                                "confidence": 0.997,
                                "source": "D(1,4.7148,2.0334,5.1984,2.0322,5.1984,2.1765,4.7148,2.1775)"
                            },
                            {
                                "content": "NE",
                                "span": {
                                    "offset": 433,
                                    "length": 2
                                },
                                "confidence": 0.997,
                                "source": "D(1,5.2446,2.0325,5.4536,2.034,5.4536,2.1778,5.2446,2.1767)"
                            },
                            {
                                "content": "800-838-2907",
                                "span": {
                                    "offset": 436,
                                    "length": 12
                                },
                                "confidence": 0.986,
                                "source": "D(1,4.7148,2.1971,5.6362,2.1991,5.6362,2.3477,4.7148,2.3447)"
                            },
                            {
                                "content": "BILLING",
                                "span": {
                                    "offset": 449,
                                    "length": 7
                                },
                                "confidence": 0.887,
                                "source": "D(1,4.7148,2.3644,5.2709,2.3629,5.2709,2.5254,4.7148,2.5236)"
                            },
                            {
                                "content": "@",
                                "span": {
                                    "offset": 456,
                                    "length": 1
                                },
                                "confidence": 0.913,
                                "source": "D(1,5.2873,2.3628,5.4099,2.3625,5.4099,2.5258,5.2873,2.5254)"
                            },
                            {
                                "content": "SMARTFREIGHTFUNDING",
                                "span": {
                                    "offset": 457,
                                    "length": 19
                                },
                                "confidence": 0.892,
                                "source": "D(1,5.4236,2.3624,7.2417,2.3575,7.2417,2.5224,5.4236,2.5259)"
                            },
                            {
                                "content": ".",
                                "span": {
                                    "offset": 476,
                                    "length": 1
                                },
                                "confidence": 0.985,
                                "source": "D(1,7.2499,2.3574,7.2881,2.3573,7.2881,2.5222,7.2499,2.5224)"
                            },
                            {
                                "content": "COM",
                                "span": {
                                    "offset": 478,
                                    "length": 3
                                },
                                "confidence": 0.994,
                                "source": "D(1,4.7148,2.5378,5.0801,2.5386,5.0801,2.6842,4.7148,2.6832)"
                            },
                            {
                                "content": "Hauled",
                                "span": {
                                    "offset": 483,
                                    "length": 6
                                },
                                "confidence": 0.997,
                                "source": "D(1,0.6516,3.5534,1.1154,3.5611,1.1161,3.7192,0.6526,3.7081)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 489,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,1.1259,3.5611,1.1573,3.5612,1.158,3.7195,1.1266,3.7193)"
                            },
                            {
                                "content": "12/03/25",
                                "span": {
                                    "offset": 491,
                                    "length": 8
                                },
                                "confidence": 0.993,
                                "source": "D(1,1.2071,3.5614,1.7836,3.5573,1.7836,3.716,1.2077,3.7198)"
                            },
                            {
                                "content": "Hired",
                                "span": {
                                    "offset": 500,
                                    "length": 5
                                },
                                "confidence": 0.998,
                                "source": "D(1,0.6532,3.7678,1.002,3.7678,1.002,3.9245,0.6532,3.9236)"
                            },
                            {
                                "content": "by",
                                "span": {
                                    "offset": 506,
                                    "length": 2
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.0496,3.7678,1.2028,3.7678,1.2028,3.9263,1.0496,3.9247)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 508,
                                    "length": 1
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.2028,3.7678,1.2451,3.7678,1.2451,3.9267,1.2028,3.9263)"
                            },
                            {
                                "content": "INGREDIENT",
                                "span": {
                                    "offset": 510,
                                    "length": 10
                                },
                                "confidence": 0.983,
                                "source": "D(1,0.6526,3.9579,1.5531,3.9581,1.5539,4.1225,0.6537,4.1187)"
                            },
                            {
                                "content": "LOGISTICS",
                                "span": {
                                    "offset": 521,
                                    "length": 9
                                },
                                "confidence": 0.986,
                                "source": "D(1,1.5998,3.9581,2.3712,3.9586,2.3718,4.125,1.6006,4.1227)"
                            },
                            {
                                "content": "SERVICES",
                                "span": {
                                    "offset": 531,
                                    "length": 8
                                },
                                "confidence": 0.989,
                                "source": "D(1,2.4151,3.9586,3.1536,3.9593,3.154,4.1267,2.4157,4.1251)"
                            },
                            {
                                "content": "INC",
                                "span": {
                                    "offset": 540,
                                    "length": 3
                                },
                                "confidence": 0.982,
                                "source": "D(1,3.203,3.9594,3.4419,3.9598,3.4422,4.1269,3.2034,4.1268)"
                            },
                            {
                                "content": "-",
                                "span": {
                                    "offset": 544,
                                    "length": 1
                                },
                                "confidence": 0.977,
                                "source": "D(1,3.4803,3.9598,3.5352,3.9599,3.5355,4.1269,3.4806,4.1269)"
                            },
                            {
                                "content": "St",
                                "span": {
                                    "offset": 546,
                                    "length": 2
                                },
                                "confidence": 0.908,
                                "source": "D(1,3.5764,3.96,3.7192,3.9602,3.7194,4.1269,3.5767,4.1269)"
                            },
                            {
                                "content": ".",
                                "span": {
                                    "offset": 548,
                                    "length": 1
                                },
                                "confidence": 0.955,
                                "source": "D(1,3.7219,3.9602,3.7521,3.9603,3.7523,4.1269,3.7221,4.1269)"
                            },
                            {
                                "content": "Louis",
                                "span": {
                                    "offset": 550,
                                    "length": 5
                                },
                                "confidence": 0.925,
                                "source": "D(1,3.8015,3.9604,4.1447,3.9609,4.1448,4.1269,3.8017,4.1269)"
                            },
                            {
                                "content": ",",
                                "span": {
                                    "offset": 555,
                                    "length": 1
                                },
                                "confidence": 0.996,
                                "source": "D(1,4.1502,3.961,4.1804,3.961,4.1805,4.1269,4.1503,4.1269)"
                            },
                            {
                                "content": "MO",
                                "span": {
                                    "offset": 557,
                                    "length": 2
                                },
                                "confidence": 0.98,
                                "source": "D(1,4.227,3.9611,4.4741,3.9615,4.4741,4.127,4.2271,4.127)"
                            },
                            {
                                "content": "Load",
                                "span": {
                                    "offset": 561,
                                    "length": 4
                                },
                                "confidence": 0.995,
                                "source": "D(1,0.6526,4.1787,0.9825,4.1787,0.9833,4.3285,0.6537,4.3276)"
                            },
                            {
                                "content": "#",
                                "span": {
                                    "offset": 566,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,1.0225,4.1787,1.0999,4.1787,1.1006,4.3288,1.0232,4.3286)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 567,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,1.1049,4.1787,1.1399,4.1787,1.1405,4.3288,1.1056,4.3288)"
                            },
                            {
                                "content": "RLB",
                                "span": {
                                    "offset": 569,
                                    "length": 3
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.1874,4.1787,1.4572,4.1787,1.4576,4.329,1.188,4.3288)"
                            },
                            {
                                "content": "-",
                                "span": {
                                    "offset": 572,
                                    "length": 1
                                },
                                "confidence": 0.998,
                                "source": "D(1,1.4647,4.1787,1.5172,4.1787,1.5176,4.329,1.4651,4.329)"
                            },
                            {
                                "content": "46703",
                                "span": {
                                    "offset": 573,
                                    "length": 5
                                },
                                "confidence": 0.991,
                                "source": "D(1,1.5072,4.1787,1.932,4.1787,1.932,4.3281,1.5076,4.329)"
                            },
                            {
                                "content": "Notes",
                                "span": {
                                    "offset": 580,
                                    "length": 5
                                },
                                "confidence": 0.998,
                                "source": "D(1,0.6501,4.3779,1.0347,4.3759,1.0356,4.5347,0.6511,4.5348)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 585,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,1.0427,4.3759,1.075,4.3757,1.0759,4.5347,1.0437,4.5347)"
                            },
                            {
                                "content": "TIME",
                                "span": {
                                    "offset": 587,
                                    "length": 4
                                },
                                "confidence": 0.996,
                                "source": "D(1,1.1154,4.3755,1.4596,4.3737,1.4604,4.5346,1.1163,4.5347)"
                            },
                            {
                                "content": "IS",
                                "span": {
                                    "offset": 592,
                                    "length": 2
                                },
                                "confidence": 0.996,
                                "source": "D(1,1.5134,4.3734,1.6398,4.3727,1.6406,4.5346,1.5142,4.5346)"
                            },
                            {
                                "content": "ON",
                                "span": {
                                    "offset": 595,
                                    "length": 2
                                },
                                "confidence": 0.995,
                                "source": "D(1,1.6829,4.3725,1.8954,4.3718,1.896,4.5345,1.6836,4.5345)"
                            },
                            {
                                "content": "PAPERWORK",
                                "span": {
                                    "offset": 598,
                                    "length": 9
                                },
                                "confidence": 0.994,
                                "source": "D(1,1.9465,4.3717,2.9013,4.371,2.9017,4.5348,1.9471,4.5345)"
                            },
                            {
                                "content": "FOR",
                                "span": {
                                    "offset": 608,
                                    "length": 3
                                },
                                "confidence": 0.997,
                                "source": "D(1,2.947,4.371,3.2456,4.3719,3.2458,4.535,2.9474,4.5348)"
                            },
                            {
                                "content": "DETENTION",
                                "span": {
                                    "offset": 612,
                                    "length": 9
                                },
                                "confidence": 0.996,
                                "source": "D(1,3.2886,4.3721,4.1359,4.3752,4.1359,4.5356,3.2889,4.535)"
                            },
                            {
                                "content": "Origin",
                                "span": {
                                    "offset": 641,
                                    "length": 6
                                },
                                "confidence": 0.999,
                                "source": "D(1,0.6532,4.8062,1.0008,4.8073,1.0008,4.9516,0.6532,4.9497)"
                            },
                            {
                                "content": "Destination",
                                "span": {
                                    "offset": 657,
                                    "length": 11
                                },
                                "confidence": 0.998,
                                "source": "D(1,2.2827,4.8044,2.9219,4.8044,2.9219,4.9383,2.2827,4.9359)"
                            },
                            {
                                "content": "Cmdty",
                                "span": {
                                    "offset": 678,
                                    "length": 5
                                },
                                "confidence": 0.995,
                                "source": "D(1,3.5693,4.8024,3.9491,4.8115,3.9491,4.9511,3.5693,4.9421)"
                            },
                            {
                                "content": "Rate",
                                "span": {
                                    "offset": 693,
                                    "length": 4
                                },
                                "confidence": 0.999,
                                "source": "D(1,4.665,4.8098,4.9431,4.8098,4.9431,4.9333,4.665,4.9333)"
                            },
                            {
                                "content": "Bill",
                                "span": {
                                    "offset": 707,
                                    "length": 4
                                },
                                "confidence": 0.996,
                                "source": "D(1,5.8022,4.8043,5.9703,4.8027,5.9703,4.9387,5.8022,4.9387)"
                            },
                            {
                                "content": "Wt",
                                "span": {
                                    "offset": 712,
                                    "length": 2
                                },
                                "confidence": 0.994,
                                "source": "D(1,5.9933,4.8025,6.166,4.8019,6.166,4.9387,5.9933,4.9387)"
                            },
                            {
                                "content": "/",
                                "span": {
                                    "offset": 714,
                                    "length": 1
                                },
                                "confidence": 0.997,
                                "source": "D(1,6.1545,4.8019,6.2143,4.8018,6.2143,4.9387,6.1545,4.9387)"
                            },
                            {
                                "content": "Vol",
                                "span": {
                                    "offset": 715,
                                    "length": 3
                                },
                                "confidence": 0.989,
                                "source": "D(1,6.1798,4.8018,6.3916,4.8023,6.3916,4.9387,6.1798,4.9387)"
                            },
                            {
                                "content": "Amount",
                                "span": {
                                    "offset": 728,
                                    "length": 6
                                },
                                "confidence": 0.998,
                                "source": "D(1,7.0349,4.8044,7.4915,4.8044,7.4915,4.9385,7.0349,4.9363)"
                            },
                            {
                                "content": "Caruthersville",
                                "span": {
                                    "offset": 755,
                                    "length": 14
                                },
                                "confidence": 0.995,
                                "source": "D(1,0.6526,5.033,1.4861,5.0309,1.4864,5.1731,0.6537,5.1728)"
                            },
                            {
                                "content": ",",
                                "span": {
                                    "offset": 769,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,1.4884,5.0309,1.5187,5.0308,1.5189,5.1732,1.4887,5.1731)"
                            },
                            {
                                "content": "MO",
                                "span": {
                                    "offset": 771,
                                    "length": 2
                                },
                                "confidence": 0.993,
                                "source": "D(1,1.5606,5.0306,1.7701,5.0299,1.7701,5.1745,1.5608,5.1734)"
                            },
                            {
                                "content": "RIVERSIDE",
                                "span": {
                                    "offset": 774,
                                    "length": 9
                                },
                                "confidence": 0.991,
                                "source": "D(1,0.6537,5.1784,1.3279,5.1755,1.3284,5.3124,0.6547,5.3123)"
                            },
                            {
                                "content": "RICE",
                                "span": {
                                    "offset": 784,
                                    "length": 4
                                },
                                "confidence": 0.989,
                                "source": "D(1,1.3717,5.1754,1.6616,5.175,1.6619,5.3136,1.3721,5.3125)"
                            },
                            {
                                "content": "LLC",
                                "span": {
                                    "offset": 789,
                                    "length": 3
                                },
                                "confidence": 0.986,
                                "source": "D(1,1.7031,5.1751,1.9424,5.1751,1.9424,5.3152,1.7032,5.3139)"
                            },
                            {
                                "content": "#",
                                "span": {
                                    "offset": 793,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,0.648,5.3308,0.7228,5.3308,0.7237,5.457,0.649,5.457)"
                            },
                            {
                                "content": "101431-28",
                                "span": {
                                    "offset": 794,
                                    "length": 9
                                },
                                "confidence": 0.934,
                                "source": "D(1,0.7314,5.3308,1.3323,5.3308,1.3323,5.457,0.7323,5.457)"
                            },
                            {
                                "content": "Ticket",
                                "span": {
                                    "offset": 804,
                                    "length": 6
                                },
                                "confidence": 0.996,
                                "source": "D(1,0.6547,5.4746,0.995,5.4676,0.9956,5.5975,0.6558,5.6031)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 810,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,0.995,5.4676,1.0228,5.4672,1.0234,5.5973,0.9956,5.5975)"
                            },
                            {
                                "content": "17021",
                                "span": {
                                    "offset": 812,
                                    "length": 5
                                },
                                "confidence": 0.997,
                                "source": "D(1,1.072,5.4666,1.408,5.4652,1.408,5.5972,1.0725,5.5969)"
                            },
                            {
                                "content": "25",
                                "span": {
                                    "offset": 818,
                                    "length": 2
                                },
                                "confidence": 0.992,
                                "source": "D(1,0.6501,5.6172,0.7965,5.6176,0.7968,5.7428,0.6506,5.7404)"
                            },
                            {
                                "content": "tons",
                                "span": {
                                    "offset": 821,
                                    "length": 4
                                },
                                "confidence": 0.987,
                                "source": "D(1,0.8254,5.6172,1.0791,5.6101,1.0791,5.7377,0.8257,5.7426)"
                            },
                            {
                                "content": "Lebanon",
                                "span": {
                                    "offset": 835,
                                    "length": 7
                                },
                                "confidence": 0.998,
                                "source": "D(1,2.2848,5.0334,2.804,5.0327,2.804,5.1724,2.2848,5.1724)"
                            },
                            {
                                "content": ",",
                                "span": {
                                    "offset": 842,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,2.8109,5.0327,2.8408,5.0327,2.8408,5.1724,2.8109,5.1724)"
                            },
                            {
                                "content": "TN",
                                "span": {
                                    "offset": 844,
                                    "length": 2
                                },
                                "confidence": 0.993,
                                "source": "D(1,2.8776,5.0327,3.0568,5.0327,3.0568,5.1724,2.8776,5.1724)"
                            },
                            {
                                "content": "Royal",
                                "span": {
                                    "offset": 847,
                                    "length": 5
                                },
                                "confidence": 0.997,
                                "source": "D(1,2.2848,5.1767,2.6072,5.1774,2.6079,5.3144,2.2848,5.3145)"
                            },
                            {
                                "content": "Canin",
                                "span": {
                                    "offset": 853,
                                    "length": 5
                                },
                                "confidence": 0.997,
                                "source": "D(1,2.6438,5.1774,2.9662,5.1766,2.9676,5.3145,2.6445,5.3144)"
                            },
                            {
                                "content": "USA",
                                "span": {
                                    "offset": 859,
                                    "length": 3
                                },
                                "confidence": 0.997,
                                "source": "D(1,3.0073,5.1764,3.2726,5.1746,3.2747,5.3149,3.0089,5.3146)"
                            },
                            {
                                "content": "#",
                                "span": {
                                    "offset": 863,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,2.2806,5.3182,2.3556,5.3202,2.3557,5.449,2.2806,5.4461)"
                            },
                            {
                                "content": "RLB",
                                "span": {
                                    "offset": 864,
                                    "length": 3
                                },
                                "confidence": 0.996,
                                "source": "D(1,2.3621,5.3204,2.5956,5.326,2.5958,5.4568,2.3621,5.4492)"
                            },
                            {
                                "content": "-",
                                "span": {
                                    "offset": 867,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,2.6042,5.3262,2.6514,5.327,2.6515,5.4578,2.6043,5.4569)"
                            },
                            {
                                "content": "46703",
                                "span": {
                                    "offset": 868,
                                    "length": 5
                                },
                                "confidence": 0.994,
                                "source": "D(1,2.6342,5.3267,3.0007,5.3312,3.0007,5.4595,2.6343,5.4575)"
                            },
                            {
                                "content": "Ticket",
                                "span": {
                                    "offset": 874,
                                    "length": 6
                                },
                                "confidence": 0.995,
                                "source": "D(1,2.2827,5.4719,2.6292,5.4665,2.6292,5.5981,2.2827,5.6035)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 880,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,2.627,5.4665,2.6555,5.4663,2.6556,5.5979,2.6271,5.5981)"
                            },
                            {
                                "content": "46703",
                                "span": {
                                    "offset": 882,
                                    "length": 5
                                },
                                "confidence": 0.996,
                                "source": "D(1,2.684,5.466,3.0547,5.4652,3.0547,5.5968,2.6841,5.5976)"
                            },
                            {
                                "content": "25",
                                "span": {
                                    "offset": 888,
                                    "length": 2
                                },
                                "confidence": 0.992,
                                "source": "D(1,2.2806,5.6174,2.428,5.6159,2.428,5.7448,2.2806,5.7463)"
                            },
                            {
                                "content": "tons",
                                "span": {
                                    "offset": 891,
                                    "length": 4
                                },
                                "confidence": 0.988,
                                "source": "D(1,2.4571,5.6153,2.7123,5.6094,2.7123,5.7383,2.457,5.7442)"
                            },
                            {
                                "content": "BROWN",
                                "span": {
                                    "offset": 905,
                                    "length": 5
                                },
                                "confidence": 0.998,
                                "source": "D(1,3.5673,5.3124,4.0424,5.3217,4.0425,5.464,3.5673,5.4516)"
                            },
                            {
                                "content": "RICE",
                                "span": {
                                    "offset": 911,
                                    "length": 4
                                },
                                "confidence": 0.998,
                                "source": "D(1,4.0894,5.3223,4.3953,5.3228,4.3953,5.4649,4.0896,5.4647)"
                            },
                            {
                                "content": "Rate",
                                "span": {
                                    "offset": 925,
                                    "length": 4
                                },
                                "confidence": 0.999,
                                "source": "D(1,4.6609,5.2455,4.9358,5.2465,4.9358,5.3792,4.6609,5.3792)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 929,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,4.9446,5.2465,4.9757,5.2466,4.9757,5.3792,4.9446,5.3792)"
                            },
                            {
                                "content": "$",
                                "span": {
                                    "offset": 931,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,5.0156,5.2467,5.0776,5.2468,5.0776,5.3792,5.0156,5.3792)"
                            },
                            {
                                "content": "47",
                                "span": {
                                    "offset": 932,
                                    "length": 2
                                },
                                "confidence": 0.998,
                                "source": "D(1,5.0798,5.2468,5.2195,5.2471,5.2195,5.3792,5.0798,5.3792)"
                            },
                            {
                                "content": "/",
                                "span": {
                                    "offset": 934,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,5.2173,5.2471,5.2616,5.2472,5.2616,5.3792,5.2173,5.3792)"
                            },
                            {
                                "content": "ton",
                                "span": {
                                    "offset": 935,
                                    "length": 3
                                },
                                "confidence": 0.997,
                                "source": "D(1,5.255,5.2472,5.4412,5.2474,5.4412,5.3792,5.255,5.3792)"
                            },
                            {
                                "content": "BOL",
                                "span": {
                                    "offset": 939,
                                    "length": 3
                                },
                                "confidence": 0.997,
                                "source": "D(1,4.6609,5.3911,4.9172,5.3901,4.9172,5.5212,4.6609,5.522)"
                            },
                            {
                                "content": ":",
                                "span": {
                                    "offset": 942,
                                    "length": 1
                                },
                                "confidence": 0.999,
                                "source": "D(1,4.9172,5.3901,4.9442,5.39,4.9441,5.5211,4.9172,5.5212)"
                            },
                            {
                                "content": "RR17021",
                                "span": {
                                    "offset": 944,
                                    "length": 7
                                },
                                "confidence": 0.996,
                                "source": "D(1,4.9869,5.3899,5.5242,5.3902,5.5242,5.528,4.9868,5.5213)"
                            },
                            {
                                "content": "50,000",
                                "span": {
                                    "offset": 961,
                                    "length": 6
                                },
                                "confidence": 0.895,
                                "source": "D(1,5.7981,5.3221,6.1881,5.3211,6.1879,5.4628,5.7981,5.4641)"
                            },
                            {
                                "content": "lbs",
                                "span": {
                                    "offset": 968,
                                    "length": 3
                                },
                                "confidence": 0.724,
                                "source": "D(1,6.2231,5.3198,6.3958,5.3124,6.3958,5.4542,6.2229,5.4615)"
                            },
                            {
                                "content": "$",
                                "span": {
                                    "offset": 981,
                                    "length": 1
                                },
                                "confidence": 0.998,
                                "source": "D(1,6.9021,5.3228,6.979,5.3228,6.979,5.4678,6.9021,5.468)"
                            },
                            {
                                "content": "1,175.00",
                                "span": {
                                    "offset": 982,
                                    "length": 8
                                },
                                "confidence": 0.918,
                                "source": "D(1,6.991,5.3228,7.479,5.3228,7.479,5.4677,6.991,5.4678)"
                            },
                            {
                                "content": "Load",
                                "span": {
                                    "offset": 1023,
                                    "length": 4
                                },
                                "confidence": 0.999,
                                "source": "D(1,5.7358,5.861,6.09,5.8549,6.09,6.0053,5.7358,6.0113)"
                            },
                            {
                                "content": "Total",
                                "span": {
                                    "offset": 1028,
                                    "length": 5
                                },
                                "confidence": 0.998,
                                "source": "D(1,6.1363,5.855,6.5161,5.8621,6.5161,6.0125,6.1362,6.0054)"
                            },
                            {
                                "content": "$",
                                "span": {
                                    "offset": 1043,
                                    "length": 1
                                },
                                "confidence": 0.997,
                                "source": "D(1,6.7942,5.8565,6.8803,5.8541,6.8803,6.0152,6.7942,6.0176)"
                            },
                            {
                                "content": "1,175.00",
                                "span": {
                                    "offset": 1044,
                                    "length": 8
                                },
                                "confidence": 0.923,
                                "source": "D(1,6.8911,5.8538,7.4832,5.8603,7.4832,6.0214,6.891,6.0149)"
                            },
                            {
                                "content": "This",
                                "span": {
                                    "offset": 1092,
                                    "length": 4
                                },
                                "confidence": 0.996,
                                "source": "D(1,0.7351,10.6242,1.0257,10.6237,1.0276,10.8064,0.7372,10.806)"
                            },
                            {
                                "content": "invoice",
                                "span": {
                                    "offset": 1097,
                                    "length": 7
                                },
                                "confidence": 0.992,
                                "source": "D(1,1.0751,10.6237,1.5603,10.6229,1.5622,10.807,1.0771,10.8064)"
                            },
                            {
                                "content": "was",
                                "span": {
                                    "offset": 1105,
                                    "length": 3
                                },
                                "confidence": 0.997,
                                "source": "D(1,1.5974,10.6229,1.8601,10.6224,1.8619,10.8073,1.5992,10.807)"
                            },
                            {
                                "content": "generated",
                                "span": {
                                    "offset": 1109,
                                    "length": 9
                                },
                                "confidence": 0.994,
                                "source": "D(1,1.9065,10.6224,2.5586,10.6214,2.5602,10.8082,1.9082,10.8074)"
                            },
                            {
                                "content": "using",
                                "span": {
                                    "offset": 1119,
                                    "length": 5
                                },
                                "confidence": 0.995,
                                "source": "D(1,2.5988,10.6213,2.9666,10.6207,2.968,10.8086,2.6003,10.8082)"
                            },
                            {
                                "content": "the",
                                "span": {
                                    "offset": 1125,
                                    "length": 3
                                },
                                "confidence": 0.996,
                                "source": "D(1,3.0037,10.6207,3.2077,10.6206,3.209,10.8088,3.0051,10.8087)"
                            },
                            {
                                "content": "BulkLoads.com",
                                "span": {
                                    "offset": 1129,
                                    "length": 13
                                },
                                "confidence": 0.758,
                                "source": "D(1,3.2509,10.6206,4.3141,10.6207,4.3151,10.8088,3.2523,10.8088)"
                            },
                            {
                                "content": "Mobile",
                                "span": {
                                    "offset": 1143,
                                    "length": 6
                                },
                                "confidence": 0.947,
                                "source": "D(1,4.3481,10.6207,4.8333,10.6208,4.8342,10.8089,4.3491,10.8089)"
                            },
                            {
                                "content": "App",
                                "span": {
                                    "offset": 1150,
                                    "length": 3
                                },
                                "confidence": 0.793,
                                "source": "D(1,4.8704,10.6208,5.1671,10.6208,5.1679,10.8089,4.8713,10.8089)"
                            },
                            {
                                "content": ".",
                                "span": {
                                    "offset": 1153,
                                    "length": 1
                                },
                                "confidence": 0.837,
                                "source": "D(1,5.1733,10.6208,5.2042,10.6208,5.205,10.8089,5.1741,10.8089)"
                            },
                            {
                                "content": "Visit",
                                "span": {
                                    "offset": 1155,
                                    "length": 5
                                },
                                "confidence": 0.773,
                                "source": "D(1,5.2475,10.6208,5.5813,10.6211,5.5819,10.8088,5.2482,10.8089)"
                            },
                            {
                                "content": "BulkTMS.com",
                                "span": {
                                    "offset": 1161,
                                    "length": 11
                                },
                                "confidence": 0.58,
                                "source": "D(1,5.6122,10.6212,6.6197,10.623,6.6201,10.8077,5.6128,10.8087)"
                            },
                            {
                                "content": "for",
                                "span": {
                                    "offset": 1173,
                                    "length": 3
                                },
                                "confidence": 0.974,
                                "source": "D(1,6.6537,10.623,6.8577,10.6234,6.858,10.8075,6.654,10.8077)"
                            },
                            {
                                "content": "more",
                                "span": {
                                    "offset": 1177,
                                    "length": 4
                                },
                                "confidence": 0.982,
                                "source": "D(1,6.8886,10.6234,7.2317,10.6241,7.2318,10.8071,6.8889,10.8075)"
                            },
                            {
                                "content": "details",
                                "span": {
                                    "offset": 1182,
                                    "length": 7
                                },
                                "confidence": 0.953,
                                "source": "D(1,7.2688,10.6241,7.7076,10.6249,7.7076,10.8066,7.2689,10.8071)"
                            },
                            {
                                "content": ".",
                                "span": {
                                    "offset": 1189,
                                    "length": 1
                                },
                                "confidence": 0.976,
                                "source": "D(1,7.7169,10.6249,7.7571,10.625,7.7571,10.8066,7.7169,10.8066)"
                            }
                        ],
                        "lines": [
                            {
                                "content": "Dec 5, 2025",
                                "source": "D(1,3.8305,0.2169,4.6609,0.2155,4.6612,0.3858,3.8308,0.3871)",
                                "span": {
                                    "offset": 0,
                                    "length": 11
                                }
                            },
                            {
                                "content": "HERITAGE GRAIN LLC",
                                "source": "D(1,0.5925,0.9522,2.9822,0.9533,2.9821,1.1718,0.5924,1.1705)",
                                "span": {
                                    "offset": 16,
                                    "length": 18
                                }
                            },
                            {
                                "content": "Caruthersville, MO",
                                "source": "D(1,0.5961,1.2116,2.2806,1.2116,2.2806,1.4104,0.5961,1.4104)",
                                "span": {
                                    "offset": 35,
                                    "length": 18
                                }
                            },
                            {
                                "content": "Inv: 541676",
                                "source": "D(1,6.3495,0.835,7.4956,0.8318,7.4962,1.0321,6.3501,1.0352)",
                                "span": {
                                    "offset": 55,
                                    "length": 11
                                }
                            },
                            {
                                "content": "Date: Dec 05, 2025",
                                "source": "D(1,5.7025,1.0807,7.5413,1.0794,7.5414,1.2921,5.7026,1.2934)",
                                "span": {
                                    "offset": 68,
                                    "length": 18
                                }
                            },
                            {
                                "content": "Company",
                                "source": "D(1,1.0232,1.6945,1.6685,1.6951,1.6683,1.8553,1.0231,1.8547)",
                                "span": {
                                    "offset": 106,
                                    "length": 7
                                }
                            },
                            {
                                "content": "HERITAGE GRAIN LLC",
                                "source": "D(1,1.7898,1.6814,3.368,1.6813,3.368,1.8383,1.7899,1.8384)",
                                "span": {
                                    "offset": 123,
                                    "length": 18
                                }
                            },
                            {
                                "content": "Contact",
                                "source": "D(1,1.1455,1.9641,1.6695,1.9628,1.6698,2.1047,1.1455,2.106)",
                                "span": {
                                    "offset": 162,
                                    "length": 7
                                }
                            },
                            {
                                "content": "Chase Letner",
                                "source": "D(1,1.795,1.9614,2.6874,1.9616,2.6874,2.1097,1.795,2.1095)",
                                "span": {
                                    "offset": 179,
                                    "length": 12
                                }
                            },
                            {
                                "content": "Dispatcher",
                                "source": "D(1,0.9518,2.2389,1.6664,2.2365,1.6673,2.3896,0.9525,2.394)",
                                "span": {
                                    "offset": 212,
                                    "length": 10
                                }
                            },
                            {
                                "content": "Email",
                                "source": "D(1,1.288,2.41,1.6633,2.4081,1.664,2.5477,1.2887,2.5496)",
                                "span": {
                                    "offset": 223,
                                    "length": 5
                                }
                            },
                            {
                                "content": "chaseheritagegrainllc@outlook.",
                                "source": "D(1,1.7888,2.2334,3.837,2.2298,3.8373,2.3982,1.7891,2.4019)",
                                "span": {
                                    "offset": 238,
                                    "length": 30
                                }
                            },
                            {
                                "content": "com",
                                "source": "D(1,1.7935,2.4295,2.0845,2.4259,2.0861,2.551,1.7951,2.5546)",
                                "span": {
                                    "offset": 269,
                                    "length": 3
                                }
                            },
                            {
                                "content": "BILLING",
                                "source": "D(1,1.0656,2.6831,1.6612,2.683,1.6612,2.827,1.0656,2.827)",
                                "span": {
                                    "offset": 293,
                                    "length": 7
                                }
                            },
                            {
                                "content": "Email",
                                "source": "D(1,1.2607,2.8628,1.6653,2.8628,1.6653,2.9971,1.2607,2.9971)",
                                "span": {
                                    "offset": 301,
                                    "length": 5
                                }
                            },
                            {
                                "content": "apheritagegrainllc@outlook.",
                                "source": "D(1,1.7857,2.683,3.7769,2.6787,3.7772,2.8493,1.7861,2.8536)",
                                "span": {
                                    "offset": 316,
                                    "length": 27
                                }
                            },
                            {
                                "content": "com",
                                "source": "D(1,1.7909,2.8789,2.1084,2.8789,2.1084,3.0032,1.7909,3.0032)",
                                "span": {
                                    "offset": 344,
                                    "length": 3
                                }
                            },
                            {
                                "content": "Bill",
                                "source": "D(1,4.3787,1.6937,4.582,1.6937,4.582,1.8284,4.3787,1.8284)",
                                "span": {
                                    "offset": 370,
                                    "length": 4
                                }
                            },
                            {
                                "content": "To",
                                "source": "D(1,4.4036,1.8663,4.582,1.8663,4.582,1.9981,4.4036,1.9981)",
                                "span": {
                                    "offset": 375,
                                    "length": 2
                                }
                            },
                            {
                                "content": "SMART FREIGHT FUNDING, LLC",
                                "source": "D(1,4.7148,1.6811,7.0018,1.6828,7.0017,1.85,4.7148,1.8483)",
                                "span": {
                                    "offset": 378,
                                    "length": 26
                                }
                            },
                            {
                                "content": "Smart Freight Funding",
                                "source": "D(1,4.7148,1.8581,6.1805,1.8636,6.1799,2.0273,4.7148,2.0219)",
                                "span": {
                                    "offset": 405,
                                    "length": 21
                                }
                            },
                            {
                                "content": "Omaha NE",
                                "source": "D(1,4.7148,2.0319,5.4537,2.0322,5.4536,2.1778,4.7148,2.1775)",
                                "span": {
                                    "offset": 427,
                                    "length": 8
                                }
                            },
                            {
                                "content": "800-838-2907",
                                "source": "D(1,4.7148,2.1971,5.6366,2.1991,5.6362,2.3498,4.7148,2.3478)",
                                "span": {
                                    "offset": 436,
                                    "length": 12
                                }
                            },
                            {
                                "content": "BILLING@SMARTFREIGHTFUNDING.",
                                "source": "D(1,4.7148,2.3644,7.2881,2.3573,7.2881,2.5235,4.7149,2.5299)",
                                "span": {
                                    "offset": 449,
                                    "length": 28
                                }
                            },
                            {
                                "content": "COM",
                                "source": "D(1,4.7148,2.5378,5.0801,2.5386,5.0801,2.6842,4.7148,2.6835)",
                                "span": {
                                    "offset": 478,
                                    "length": 3
                                }
                            },
                            {
                                "content": "Hauled: 12/03/25",
                                "source": "D(1,0.6516,3.5534,1.7842,3.5573,1.7836,3.7225,0.651,3.7186)",
                                "span": {
                                    "offset": 483,
                                    "length": 16
                                }
                            },
                            {
                                "content": "Hired by:",
                                "source": "D(1,0.6532,3.7678,1.2451,3.7678,1.2451,3.9267,0.6532,3.9267)",
                                "span": {
                                    "offset": 500,
                                    "length": 9
                                }
                            },
                            {
                                "content": "INGREDIENT LOGISTICS SERVICES INC - St. Louis, MO",
                                "source": "D(1,0.6526,3.9569,4.4743,3.9606,4.4741,4.128,0.6525,4.1244)",
                                "span": {
                                    "offset": 510,
                                    "length": 49
                                }
                            },
                            {
                                "content": "Load #: RLB-46703",
                                "source": "D(1,0.6526,4.1787,1.932,4.1787,1.932,4.329,0.6526,4.329)",
                                "span": {
                                    "offset": 561,
                                    "length": 17
                                }
                            },
                            {
                                "content": "Notes: TIME IS ON PAPERWORK FOR DETENTION",
                                "source": "D(1,0.6501,4.3704,4.1359,4.3712,4.1359,4.5356,0.65,4.5348)",
                                "span": {
                                    "offset": 580,
                                    "length": 41
                                }
                            },
                            {
                                "content": "Origin",
                                "source": "D(1,0.6532,4.8052,1.0015,4.8066,1.0008,4.9516,0.653,4.9497)",
                                "span": {
                                    "offset": 641,
                                    "length": 6
                                }
                            },
                            {
                                "content": "Destination",
                                "source": "D(1,2.2827,4.8044,2.9219,4.8044,2.9219,4.939,2.2827,4.939)",
                                "span": {
                                    "offset": 657,
                                    "length": 11
                                }
                            },
                            {
                                "content": "Cmdty",
                                "source": "D(1,3.5694,4.7997,3.9512,4.8072,3.9491,4.9511,3.5693,4.942)",
                                "span": {
                                    "offset": 678,
                                    "length": 5
                                }
                            },
                            {
                                "content": "Rate",
                                "source": "D(1,4.665,4.8098,4.9431,4.8098,4.9431,4.9333,4.665,4.9333)",
                                "span": {
                                    "offset": 693,
                                    "length": 4
                                }
                            },
                            {
                                "content": "Bill Wt/Vol",
                                "source": "D(1,5.8022,4.8018,6.3916,4.8018,6.3916,4.9387,5.8022,4.9387)",
                                "span": {
                                    "offset": 707,
                                    "length": 11
                                }
                            },
                            {
                                "content": "Amount",
                                "source": "D(1,7.0349,4.8044,7.4915,4.8044,7.4915,4.9385,7.0349,4.9385)",
                                "span": {
                                    "offset": 728,
                                    "length": 6
                                }
                            },
                            {
                                "content": "Caruthersville, MO",
                                "source": "D(1,0.6526,5.033,1.7701,5.0299,1.7705,5.1745,0.653,5.1761)",
                                "span": {
                                    "offset": 755,
                                    "length": 18
                                }
                            },
                            {
                                "content": "RIVERSIDE RICE LLC",
                                "source": "D(1,0.6537,5.1749,1.9424,5.1751,1.9424,5.3152,0.6537,5.315)",
                                "span": {
                                    "offset": 774,
                                    "length": 18
                                }
                            },
                            {
                                "content": "#101431-28",
                                "source": "D(1,0.648,5.3308,1.3323,5.3308,1.3323,5.457,0.648,5.457)",
                                "span": {
                                    "offset": 793,
                                    "length": 10
                                }
                            },
                            {
                                "content": "Ticket: 17021",
                                "source": "D(1,0.6547,5.4695,1.408,5.464,1.4091,5.5972,0.6557,5.6031)",
                                "span": {
                                    "offset": 804,
                                    "length": 13
                                }
                            },
                            {
                                "content": "25 tons",
                                "source": "D(1,0.6486,5.6172,1.0791,5.6101,1.0791,5.7395,0.6507,5.7464)",
                                "span": {
                                    "offset": 818,
                                    "length": 7
                                }
                            },
                            {
                                "content": "Lebanon, TN",
                                "source": "D(1,2.2848,5.0327,3.0568,5.0327,3.0568,5.1724,2.2848,5.1724)",
                                "span": {
                                    "offset": 835,
                                    "length": 11
                                }
                            },
                            {
                                "content": "Royal Canin USA",
                                "source": "D(1,2.2848,5.1746,3.2747,5.1746,3.2747,5.3149,2.2848,5.3149)",
                                "span": {
                                    "offset": 847,
                                    "length": 15
                                }
                            },
                            {
                                "content": "#RLB-46703",
                                "source": "D(1,2.2806,5.3182,3.0031,5.3312,3.0006,5.464,2.2799,5.4511)",
                                "span": {
                                    "offset": 863,
                                    "length": 10
                                }
                            },
                            {
                                "content": "Ticket: 46703",
                                "source": "D(1,2.2816,5.4695,3.0547,5.464,3.0547,5.5968,2.2827,5.6035)",
                                "span": {
                                    "offset": 874,
                                    "length": 13
                                }
                            },
                            {
                                "content": "25 tons",
                                "source": "D(1,2.2799,5.6175,2.7123,5.6094,2.7147,5.7394,2.2807,5.7464)",
                                "span": {
                                    "offset": 888,
                                    "length": 7
                                }
                            },
                            {
                                "content": "BROWN RICE",
                                "source": "D(1,3.5673,5.3124,4.397,5.3229,4.3952,5.4687,3.5654,5.4582)",
                                "span": {
                                    "offset": 905,
                                    "length": 10
                                }
                            },
                            {
                                "content": "Rate: $47/ton",
                                "source": "D(1,4.6609,5.2455,5.4412,5.2455,5.4412,5.3792,4.6609,5.3792)",
                                "span": {
                                    "offset": 925,
                                    "length": 13
                                }
                            },
                            {
                                "content": "BOL: RR17021",
                                "source": "D(1,4.6609,5.3886,5.5244,5.3902,5.5242,5.528,4.6606,5.5263)",
                                "span": {
                                    "offset": 939,
                                    "length": 12
                                }
                            },
                            {
                                "content": "50,000 lbs",
                                "source": "D(1,5.7958,5.3221,6.3958,5.3124,6.3971,5.4594,5.7982,5.4692)",
                                "span": {
                                    "offset": 961,
                                    "length": 10
                                }
                            },
                            {
                                "content": "$1,175.00",
                                "source": "D(1,6.9021,5.3228,7.479,5.3228,7.479,5.468,6.9021,5.468)",
                                "span": {
                                    "offset": 981,
                                    "length": 9
                                }
                            },
                            {
                                "content": "Load Total",
                                "source": "D(1,5.7359,5.8544,6.5164,5.8556,6.5161,6.0125,5.7356,6.0113)",
                                "span": {
                                    "offset": 1023,
                                    "length": 10
                                }
                            },
                            {
                                "content": "$1,175.00",
                                "source": "D(1,6.7942,5.8488,7.4841,5.8526,7.4832,6.0214,6.7933,6.0176)",
                                "span": {
                                    "offset": 1043,
                                    "length": 9
                                }
                            },
                            {
                                "content": "This invoice was generated using the BulkLoads.com Mobile App. Visit BulkTMS.com for more details.",
                                "source": "D(1,0.7351,10.6204,7.7571,10.6209,7.7571,10.8091,0.7351,10.8086)",
                                "span": {
                                    "offset": 1092,
                                    "length": 98
                                }
                            }
                        ]
                    }
                ],
                "paragraphs": [
                    {
                        "content": "Dec 5, 2025",
                        "source": "D(1,3.8305,0.2169,4.6609,0.2155,4.6612,0.3858,3.8308,0.3871)",
                        "span": {
                            "offset": 0,
                            "length": 11
                        }
                    },
                    {
                        "role": "title",
                        "content": "HERITAGE GRAIN LLC Caruthersville, MO",
                        "source": "D(1,0.5925,0.9522,2.9822,0.9533,2.982,1.4115,0.5922,1.4104)",
                        "span": {
                            "offset": 14,
                            "length": 39
                        }
                    },
                    {
                        "content": "Inv: 541676",
                        "source": "D(1,6.3495,0.835,7.4956,0.8318,7.4962,1.0321,6.3501,1.0352)",
                        "span": {
                            "offset": 55,
                            "length": 11
                        }
                    },
                    {
                        "content": "Date: Dec 05, 2025",
                        "source": "D(1,5.7025,1.0807,7.5413,1.0794,7.5414,1.2921,5.7026,1.2934)",
                        "span": {
                            "offset": 68,
                            "length": 18
                        }
                    },
                    {
                        "content": "Company",
                        "source": "D(1,0.6176,1.6404,1.7295,1.6405,1.7288,1.8986,0.6173,1.8986)",
                        "span": {
                            "offset": 106,
                            "length": 7
                        }
                    },
                    {
                        "content": "HERITAGE GRAIN LLC",
                        "source": "D(1,1.7295,1.6405,3.9169,1.6406,3.9171,1.898,1.7288,1.8986)",
                        "span": {
                            "offset": 123,
                            "length": 18
                        }
                    },
                    {
                        "content": "Contact",
                        "source": "D(1,0.6173,1.8986,1.7288,1.8986,1.7287,2.1725,0.6168,2.1724)",
                        "span": {
                            "offset": 162,
                            "length": 7
                        }
                    },
                    {
                        "content": "Chase Letner",
                        "source": "D(1,1.7288,1.8986,3.9171,1.898,3.9171,2.1722,1.7287,2.1725)",
                        "span": {
                            "offset": 179,
                            "length": 12
                        }
                    },
                    {
                        "content": "Dispatcher Email",
                        "source": "D(1,0.6168,2.1724,1.7287,2.1725,1.7286,2.6082,0.6166,2.6081)",
                        "span": {
                            "offset": 212,
                            "length": 16
                        }
                    },
                    {
                        "content": "chaseheritagegrainllc@outlook. com",
                        "source": "D(1,1.7287,2.1725,3.9171,2.1722,3.9173,2.6081,1.7286,2.6082)",
                        "span": {
                            "offset": 238,
                            "length": 34
                        }
                    },
                    {
                        "content": "BILLING Email",
                        "source": "D(1,0.6166,2.6081,1.7286,2.6082,1.7291,3.0502,0.617,3.0501)",
                        "span": {
                            "offset": 293,
                            "length": 13
                        }
                    },
                    {
                        "content": "apheritagegrainllc@outlook. com",
                        "source": "D(1,1.7286,2.6082,3.9173,2.6081,3.9176,3.0504,1.7291,3.0502)",
                        "span": {
                            "offset": 316,
                            "length": 31
                        }
                    },
                    {
                        "content": "Bill To SMART FREIGHT FUNDING, LLC Smart Freight Funding Omaha NE 800-838-2907 BILLING@SMARTFREIGHTFUNDING. COM",
                        "source": "D(1,4.3787,1.6809,7.2887,1.683,7.288,2.6859,4.3779,2.6837)",
                        "span": {
                            "offset": 370,
                            "length": 111
                        }
                    },
                    {
                        "content": "Hauled: 12/03/25 Hired by: INGREDIENT LOGISTICS SERVICES INC - St. Louis, MO",
                        "source": "D(1,0.6512,3.5534,4.4747,3.557,4.4741,4.128,0.6507,4.1244)",
                        "span": {
                            "offset": 483,
                            "length": 76
                        }
                    },
                    {
                        "content": "Load #: RLB-46703",
                        "source": "D(1,0.6526,4.1787,1.932,4.1787,1.932,4.329,0.6526,4.329)",
                        "span": {
                            "offset": 561,
                            "length": 17
                        }
                    },
                    {
                        "content": "Notes: TIME IS ON PAPERWORK FOR DETENTION",
                        "source": "D(1,0.6501,4.3704,4.1359,4.3712,4.1359,4.5356,0.65,4.5348)",
                        "span": {
                            "offset": 580,
                            "length": 41
                        }
                    },
                    {
                        "content": "Origin",
                        "source": "D(1,0.5987,4.7537,2.2362,4.7526,2.2352,4.9849,0.5972,4.9853)",
                        "span": {
                            "offset": 641,
                            "length": 6
                        }
                    },
                    {
                        "content": "Destination",
                        "source": "D(1,2.2362,4.7526,3.5183,4.7518,3.5174,4.9841,2.2352,4.9849)",
                        "span": {
                            "offset": 657,
                            "length": 11
                        }
                    },
                    {
                        "content": "Cmdty",
                        "source": "D(1,3.5183,4.7518,4.6093,4.7516,4.6086,4.9835,3.5174,4.9841)",
                        "span": {
                            "offset": 678,
                            "length": 5
                        }
                    },
                    {
                        "content": "Rate",
                        "source": "D(1,4.6093,4.7516,5.7454,4.7509,5.7449,4.9831,4.6086,4.9835)",
                        "span": {
                            "offset": 693,
                            "length": 4
                        }
                    },
                    {
                        "content": "Bill Wt/Vol",
                        "source": "D(1,5.7454,4.7509,6.5674,4.7504,6.5668,4.9824,5.7449,4.9831)",
                        "span": {
                            "offset": 707,
                            "length": 11
                        }
                    },
                    {
                        "content": "Amount",
                        "source": "D(1,6.5674,4.7504,7.5304,4.7514,7.5305,4.9825,6.5668,4.9824)",
                        "span": {
                            "offset": 728,
                            "length": 6
                        }
                    },
                    {
                        "content": "Caruthersville, MO RIVERSIDE RICE LLC #101431-28 Ticket: 17021 25 tons",
                        "source": "D(1,0.5972,4.9853,2.2352,4.9849,2.2344,5.7929,0.5955,5.7936)",
                        "span": {
                            "offset": 755,
                            "length": 70
                        }
                    },
                    {
                        "content": "Lebanon, TN Royal Canin USA #RLB-46703 Ticket: 46703 25 tons",
                        "source": "D(1,2.2352,4.9849,3.5174,4.9841,3.518,5.7931,2.2344,5.7929)",
                        "span": {
                            "offset": 835,
                            "length": 60
                        }
                    },
                    {
                        "content": "BROWN RICE",
                        "source": "D(1,3.5174,4.9841,4.6086,4.9835,4.6091,5.7929,3.518,5.7931)",
                        "span": {
                            "offset": 905,
                            "length": 10
                        }
                    },
                    {
                        "content": "Rate: $47/ton BOL: RR17021",
                        "source": "D(1,4.6086,4.9835,5.7449,4.9831,5.746,5.7938,4.6091,5.7929)",
                        "span": {
                            "offset": 925,
                            "length": 26
                        }
                    },
                    {
                        "content": "50,000 lbs",
                        "source": "D(1,5.7449,4.9831,6.5668,4.9824,6.5674,5.7932,5.746,5.7938)",
                        "span": {
                            "offset": 961,
                            "length": 10
                        }
                    },
                    {
                        "content": "$1,175.00",
                        "source": "D(1,6.5668,4.9824,7.5305,4.9825,7.5312,5.7939,6.5674,5.7932)",
                        "span": {
                            "offset": 981,
                            "length": 9
                        }
                    },
                    {
                        "content": "Load Total",
                        "source": "D(1,0.5955,5.7936,6.5674,5.7932,6.5689,6.0512,0.5951,6.0519)",
                        "span": {
                            "offset": 1023,
                            "length": 10
                        }
                    },
                    {
                        "content": "$1,175.00",
                        "source": "D(1,6.5674,5.7932,7.5312,5.7939,7.5314,6.0518,6.5689,6.0512)",
                        "span": {
                            "offset": 1043,
                            "length": 9
                        }
                    },
                    {
                        "role": "pageFooter",
                        "content": "This invoice was generated using the BulkLoads.com Mobile App. Visit BulkTMS.com for more details.",
                        "source": "D(1,0.7351,10.6204,7.7571,10.6209,7.7571,10.8091,0.7351,10.8086)",
                        "span": {
                            "offset": 1075,
                            "length": 120
                        }
                    }
                ],
                "sections": [
                    {
                        "span": {
                            "offset": 0,
                            "length": 1072
                        },
                        "elements": [
                            "/sections/1",
                            "/sections/2"
                        ]
                    },
                    {
                        "span": {
                            "offset": 0,
                            "length": 11
                        },
                        "elements": [
                            "/paragraphs/0"
                        ]
                    },
                    {
                        "span": {
                            "offset": 14,
                            "length": 1058
                        },
                        "elements": [
                            "/paragraphs/1",
                            "/paragraphs/2",
                            "/paragraphs/3",
                            "/tables/0",
                            "/paragraphs/12",
                            "/paragraphs/13",
                            "/paragraphs/14",
                            "/paragraphs/15",
                            "/tables/1"
                        ]
                    }
                ],
                "tables": [
                    {
                        "rowCount": 4,
                        "columnCount": 2,
                        "cells": [
                            {
                                "kind": "columnHeader",
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Company",
                                "source": "D(1,0.6176,1.6404,1.7295,1.6405,1.7288,1.8986,0.6173,1.8986)",
                                "span": {
                                    "offset": 106,
                                    "length": 7
                                },
                                "elements": [
                                    "/paragraphs/4"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 0,
                                "columnIndex": 1,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "HERITAGE GRAIN LLC",
                                "source": "D(1,1.7295,1.6405,3.9169,1.6406,3.9171,1.898,1.7288,1.8986)",
                                "span": {
                                    "offset": 123,
                                    "length": 18
                                },
                                "elements": [
                                    "/paragraphs/5"
                                ]
                            },
                            {
                                "kind": "columnHeader",
                                "rowIndex": 1,
                                "columnIndex": 0,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Contact",
                                "source": "D(1,0.6173,1.8986,1.7288,1.8986,1.7287,2.1725,0.6168,2.1724)",
                                "span": {
                                    "offset": 162,
                                    "length": 7
                                },
                                "elements": [
                                    "/paragraphs/6"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 1,
                                "columnIndex": 1,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Chase Letner",
                                "source": "D(1,1.7288,1.8986,3.9171,1.898,3.9171,2.1722,1.7287,2.1725)",
                                "span": {
                                    "offset": 179,
                                    "length": 12
                                },
                                "elements": [
                                    "/paragraphs/7"
                                ]
                            },
                            {
                                "kind": "columnHeader",
                                "rowIndex": 2,
                                "columnIndex": 0,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Dispatcher Email",
                                "source": "D(1,0.6168,2.1724,1.7287,2.1725,1.7286,2.6082,0.6166,2.6081)",
                                "span": {
                                    "offset": 212,
                                    "length": 16
                                },
                                "elements": [
                                    "/paragraphs/8"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 2,
                                "columnIndex": 1,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "chaseheritagegrainllc@outlook. com",
                                "source": "D(1,1.7287,2.1725,3.9171,2.1722,3.9173,2.6081,1.7286,2.6082)",
                                "span": {
                                    "offset": 238,
                                    "length": 34
                                },
                                "elements": [
                                    "/paragraphs/9"
                                ]
                            },
                            {
                                "kind": "columnHeader",
                                "rowIndex": 3,
                                "columnIndex": 0,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "BILLING Email",
                                "source": "D(1,0.6166,2.6081,1.7286,2.6082,1.7291,3.0502,0.617,3.0501)",
                                "span": {
                                    "offset": 293,
                                    "length": 13
                                },
                                "elements": [
                                    "/paragraphs/10"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 3,
                                "columnIndex": 1,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "apheritagegrainllc@outlook. com",
                                "source": "D(1,1.7286,2.6082,3.9173,2.6081,3.9176,3.0504,1.7291,3.0502)",
                                "span": {
                                    "offset": 316,
                                    "length": 31
                                },
                                "elements": [
                                    "/paragraphs/11"
                                ]
                            }
                        ],
                        "source": "D(1,0.6096,1.6261,3.9346,1.6207,3.9283,3.0454,0.6096,3.0508)",
                        "span": {
                            "offset": 89,
                            "length": 278
                        }
                    },
                    {
                        "rowCount": 3,
                        "columnCount": 6,
                        "cells": [
                            {
                                "kind": "columnHeader",
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Origin",
                                "source": "D(1,0.5987,4.7537,2.2362,4.7526,2.2352,4.9849,0.5972,4.9853)",
                                "span": {
                                    "offset": 641,
                                    "length": 6
                                },
                                "elements": [
                                    "/paragraphs/16"
                                ]
                            },
                            {
                                "kind": "columnHeader",
                                "rowIndex": 0,
                                "columnIndex": 1,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Destination",
                                "source": "D(1,2.2362,4.7526,3.5183,4.7518,3.5174,4.9841,2.2352,4.9849)",
                                "span": {
                                    "offset": 657,
                                    "length": 11
                                },
                                "elements": [
                                    "/paragraphs/17"
                                ]
                            },
                            {
                                "kind": "columnHeader",
                                "rowIndex": 0,
                                "columnIndex": 2,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Cmdty",
                                "source": "D(1,3.5183,4.7518,4.6093,4.7516,4.6086,4.9835,3.5174,4.9841)",
                                "span": {
                                    "offset": 678,
                                    "length": 5
                                },
                                "elements": [
                                    "/paragraphs/18"
                                ]
                            },
                            {
                                "kind": "columnHeader",
                                "rowIndex": 0,
                                "columnIndex": 3,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Rate",
                                "source": "D(1,4.6093,4.7516,5.7454,4.7509,5.7449,4.9831,4.6086,4.9835)",
                                "span": {
                                    "offset": 693,
                                    "length": 4
                                },
                                "elements": [
                                    "/paragraphs/19"
                                ]
                            },
                            {
                                "kind": "columnHeader",
                                "rowIndex": 0,
                                "columnIndex": 4,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Bill Wt/Vol",
                                "source": "D(1,5.7454,4.7509,6.5674,4.7504,6.5668,4.9824,5.7449,4.9831)",
                                "span": {
                                    "offset": 707,
                                    "length": 11
                                },
                                "elements": [
                                    "/paragraphs/20"
                                ]
                            },
                            {
                                "kind": "columnHeader",
                                "rowIndex": 0,
                                "columnIndex": 5,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Amount",
                                "source": "D(1,6.5674,4.7504,7.5304,4.7514,7.5305,4.9825,6.5668,4.9824)",
                                "span": {
                                    "offset": 728,
                                    "length": 6
                                },
                                "elements": [
                                    "/paragraphs/21"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 1,
                                "columnIndex": 0,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Caruthersville, MO RIVERSIDE RICE LLC #101431-28 Ticket: 17021 25 tons",
                                "source": "D(1,0.5972,4.9853,2.2352,4.9849,2.2344,5.7929,0.5955,5.7936)",
                                "span": {
                                    "offset": 755,
                                    "length": 70
                                },
                                "elements": [
                                    "/paragraphs/22"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 1,
                                "columnIndex": 1,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Lebanon, TN Royal Canin USA #RLB-46703 Ticket: 46703 25 tons",
                                "source": "D(1,2.2352,4.9849,3.5174,4.9841,3.518,5.7931,2.2344,5.7929)",
                                "span": {
                                    "offset": 835,
                                    "length": 60
                                },
                                "elements": [
                                    "/paragraphs/23"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 1,
                                "columnIndex": 2,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "BROWN RICE",
                                "source": "D(1,3.5174,4.9841,4.6086,4.9835,4.6091,5.7929,3.518,5.7931)",
                                "span": {
                                    "offset": 905,
                                    "length": 10
                                },
                                "elements": [
                                    "/paragraphs/24"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 1,
                                "columnIndex": 3,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "Rate: $47/ton BOL: RR17021",
                                "source": "D(1,4.6086,4.9835,5.7449,4.9831,5.746,5.7938,4.6091,5.7929)",
                                "span": {
                                    "offset": 925,
                                    "length": 26
                                },
                                "elements": [
                                    "/paragraphs/25"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 1,
                                "columnIndex": 4,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "50,000 lbs",
                                "source": "D(1,5.7449,4.9831,6.5668,4.9824,6.5674,5.7932,5.746,5.7938)",
                                "span": {
                                    "offset": 961,
                                    "length": 10
                                },
                                "elements": [
                                    "/paragraphs/26"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 1,
                                "columnIndex": 5,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "$1,175.00",
                                "source": "D(1,6.5668,4.9824,7.5305,4.9825,7.5312,5.7939,6.5674,5.7932)",
                                "span": {
                                    "offset": 981,
                                    "length": 9
                                },
                                "elements": [
                                    "/paragraphs/27"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 2,
                                "columnIndex": 0,
                                "rowSpan": 1,
                                "columnSpan": 5,
                                "content": "Load Total",
                                "source": "D(1,0.5955,5.7936,6.5674,5.7932,6.5689,6.0512,0.5951,6.0519)",
                                "span": {
                                    "offset": 1023,
                                    "length": 10
                                },
                                "elements": [
                                    "/paragraphs/28"
                                ]
                            },
                            {
                                "kind": "content",
                                "rowIndex": 2,
                                "columnIndex": 5,
                                "rowSpan": 1,
                                "columnSpan": 1,
                                "content": "$1,175.00",
                                "source": "D(1,6.5674,5.7932,7.5312,5.7939,7.5314,6.0518,6.5689,6.0512)",
                                "span": {
                                    "offset": 1043,
                                    "length": 9
                                },
                                "elements": [
                                    "/paragraphs/29"
                                ]
                            }
                        ],
                        "source": "D(1,0.5909,4.7642,7.5371,4.748,7.5371,6.0532,0.5909,6.0693)",
                        "span": {
                            "offset": 624,
                            "length": 448
                        }
                    }
                ],
                "analyzerId": "loadDocumentAnalyzer",
                "mimeType": "application/pdf"
            }
        ]
    },
    "usage": {
        "documentPagesStandard": 1,
        "contextualizationTokens": 1000,
        "tokens": {
            "gpt-4.1-input": 4169,
            "gpt-4.1-output": 361
        }
    }
}
```



### GET
 - This request returns all available analyzers. It includes all prebuilt analyzers so the response is rather large.
#### Request: 
```bash
curl --l 'https://cu-resource-nate.services.ai.azure.com/contentunderstanding/analyzers?api-version=2025-11-01' \

--h 'Ocp-Apim-Subscription-Key: #{{CU_RESOURCE_KEY}}'
```

### GET
 - This request returns the schema for the specified AnalayzerID.

#### Request:
```bash
curl --location 'https://cu-resource-nate.services.ai.azure.com/contentunderstanding/analyzers/loadDocumentAnalyzer?api-version=2025-11-01' \

--header 'Ocp-Apim-Subscription-Key: #{{CU_RESOURCE_KEY}}'
```

#### Response: the analyzer properties (including the field schema)
```json
{
    "analyzerId": "loadDocumentAnalyzer",
    "description": "Load Document Analyzer",
    "tags": {
        "projectId": "79278141-0dda-4dd3-8fac-37c93c5fe32a",
        "templateId": "prebuilt-documentAnalyzer"
    },
    "createdAt": "2025-12-10T03:46:31Z",
    "lastModifiedAt": "2025-12-10T03:46:33Z",
    "baseAnalyzerId": "prebuilt-document",
    "config": {
        "returnDetails": true,
        "enableOcr": true,
        "enableLayout": true,
        "enableFormula": true,
        "enableFigureDescription": true,
        "enableFigureAnalysis": true,
        "chartFormat": "chartjs",
        "tableFormat": "html",
        "estimateFieldSourceAndConfidence": true,
        "enableSegment": false,
        "omitContent": false,
        "segmentPerPage": false,
        "annotationFormat": "markdown"
    },
    "fieldSchema": {
        "fields": {
            "DriverName": {
                "type": "string",
                "method": "extract",
                "description": "Full name of the driver transporting the shipment."
                "examples": [
                    "Unknown"
                ]
            },
            "OriginCity": {
                "type": "string",
                "method": "extract",
                "description": "City where the shipment originated.",
                "examples": [
                    "Lithia Springs"
                ]
            },
            "OriginState": {
                "type": "string",
                "method": "extract",
                "description": "State abbreviation where the shipment originated.",
                "examples": [
                    "GA"
                ]
            },
            "Date": {
                "type": "date",
                "method": "extract",
                "description": "Date printed on the document. Could be shipped date or date document was printed.",
                "examples": [
                    "12/1/2025"
                ]
            },
            "BookedThroughCompany": {
                "type": "string",
                "method": "extract",
                "description": "Company or broker through which the load was booked.",
                "estimateSourceAndConfidence": true
            },
            "LoadNumber": {
                "type": "string",
                "method": "extract",
                "description": "Unique identifier assigned to the shipment or load for tracking and reference.",
                "estimateSourceAndConfidence": true
            },
            "DestinationCity": {
                "type": "string",
                "method": "extract",
                "description": "City where the shipment is to be delivered.",
                "estimateSourceAndConfidence": true
            },
            "DestinationState": {
                "type": "string",
                "method": "extract",
                "description": "State abbreviation where the shipment is to be delivered.",
                "estimateSourceAndConfidence": true
            },
            "CompanyName": {
                "type": "string",
                "method": "extract",
                "description": "Name of the trucking or logistics company handling the shipment.",
                "estimateSourceAndConfidence": true
            },
            "Time": {
                "type": "time",
                "method": "extract",
                "description": "Time printed on the document.",
                "estimateSourceAndConfidence": true
            },
            "Commodity": {
                "type": "string",
                "method": "extract",
                "description": "Type of goods or material being shipped.",
                "estimateSourceAndConfidence": true
            },
            "NetWeight": {
                "type": "string",
                "method": "extract",
                "description": "Total net weight of the shipment, typically in pounds; net = gross – tare. Example: 48,100 lbs; 26 tons",
                "estimateSourceAndConfidence": true
            },
            "RateAmount": {
                "type": "string",
                "method": "extract",
                "description": "Rate charged per unit of weight or commodity. Example 6.00",
                "estimateSourceAndConfidence": true
            },
            "RateUnit": {
                "type": "string",
                "method": "extract",
                "description": "Unit of measure for the rate. Example: Ton, Bushel, Flat, Pound. Is typically paired with RateAmount.",
                "estimateSourceAndConfidence": true
            },
            "NumberOfLoads": {
                "type": "string",
                "method": "extract",
                "description": "Total number of loads represented on the document.",
                "estimateSourceAndConfidence": true
            },
            "DocumentCategory": {
                "type": "string",
                "method": "extract",
                "description": "Type of document. For example: funding sheet (smart freight load funding sheet), rate confirmation (broker/company rate confirmation), scale ticket (commodity scale ticket), bill of lading, load invoice (invoice from trucking/logistics company), surcharge receipt (document listing surcharge details)",
                "estimateSourceAndConfidence": true
            },
            "Surcharges": {
                "type": "string",
                "method": "extract",
                "description": "List of surcharge items for the load. Each surcharge includes: surcharge type (fuel, wait time, demurrage), surcharge value (dollar amount).",
                "estimateSourceAndConfidence": true
            },
            "TicketNumber": {
                "type": "string",
                "method": "extract",
                "description": "Unique number usually printed on the document.",
                "estimateSourceAndConfidence": true
            }
        }
    },
    "warnings": [],
    "status": "ready",
    "processingLocation": "geography",
    "supportedModels": {
        "completion": [
            "gpt-4o",
            "gpt-4o-mini",
            "gpt-4.1",
            "gpt-4.1-mini",
            "gpt-4.1-nano"
        ],
        "embedding": [
            "text-embedding-3-small",
            "text-embedding-3-large",
            "text-embedding-ada-002"
        ]
    },
    "models": {
        "completion": "gpt-4.1",
        "embedding": "text-embedding-3-large"
    }
}
```

#### PUT/PATCH
 - Using the same request above but:

### PUT - Insert new Analyzer

* **Request**:

Test spot 
```json
{
  "description": "This is a test",
  "models": {
    "completion": "gpt-4.1",
    "embedding": "text-embedding-3-large"
  },
  "baseAnalyzerId": "prebuilt-document",
  "fields": [
    {
      "name": "DriverName",
      "type": "string",
      "method": "extract",
      "description": "Full name of the driver transporting the shipment.",
      "examples": []
    },
    {
      "name": "OriginCity",
      "type": "string",
      "method": "extract",
      "description": "City where the shipment originated.",
      "examples": []
    }
  ]
}
```
* **Response:**
```json
{
    "analyzerId": "loadDocAnalyzerTest",
    "description": "This is a test",
    "createdAt": "2026-01-09T04:00:54Z",
    "lastModifiedAt": "2026-01-09T04:00:54Z",
    "baseAnalyzerId": "prebuilt-document",
    "config": {
        "returnDetails": false,
        "enableOcr": true,
        "enableLayout": true,
        "enableFormula": true,
        "enableFigureDescription": false,
        "enableFigureAnalysis": false,
        "chartFormat": "chartjs",
        "tableFormat": "html",
        "enableSegment": false,
        "omitContent": false,
        "segmentPerPage": false,
        "annotationFormat": "markdown"
    },
    "warnings": [],
    "status": "ready",
    "processingLocation": "geography",
    "supportedModels": {
        "completion": [
            "gpt-4o",
            "gpt-4o-mini",
            "gpt-4.1",
            "gpt-4.1-mini",
            "gpt-4.1-nano"
        ],
        "embedding": [
            "text-embedding-3-small",
            "text-embedding-3-large",
            "text-embedding-ada-002"
        ]
    },
    "models": {
        "completion": "gpt-4.1",
        "embedding": "text-embedding-3-large"
    }
}
```

- This indicates a new analyzer has been set up. 
- When PUTing, an existing analyzer cannot be used.
- This response gives me a lot of insight as to what makes up an analyzer.
	- This article: https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/tutorial/create-custom-analyzer?tabs=portal%2Cdocument contains a lot of info about creating an analyzer. Lots of good stuff here
