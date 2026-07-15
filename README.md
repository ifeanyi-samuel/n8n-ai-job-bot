
n8n workflow that finds AI Automation jobs and send daily alrt to telegram
# n8n AI Job Bot 🤖
{
  "name": "My workflow",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "hours",
              "hoursInterval": 6
            }
          ]
        }
      },
      "id": "db697993-92aa-45cc-bcbf-5141c400b451",
      "name": "Every 6 Hours",
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.3,
      "position": [
        -80,
        208
      ]
    },
    {
      "parameters": {
        "url": "https://remoteok.com/api",
        "options": {
          "response": {
            "response": {
              "responseFormat": "json"
            }
          }
        }
      },
      "id": "818d7b01-1f3e-437f-9611-ab28835afe49",
      "name": "Search AI Jobs",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.4,
      "position": [
        272,
        240
      ],
      "onError": "continueErrorOutput"
    },
    {
      "parameters": {
        "mode": "raw",
        "jsonOutput": "={\n  \"job_id\": \"{{ $json[\"Job-id\"] }}\",\n  \"title\": \"{{ $json[\"Job-title\"].replace(/-/g, \" \") }}\",\n  \"company\": \"{{ $json[\"Position\"] || \"Not listed\" }}\",\n  \"location\": \"{{ $json[\"Location\"] || \"Remote\" }}\",\n  \"url\": \"{{ $json[\"Apply-Url\"] }}\",\n  \"posted_date\": \"Recent\"\n}",
        "options": {}
      },
      "id": "337e902a-3be3-4ef6-a064-933131d20e82",
      "name": "Extract Job Fields",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        592,
        32
      ]
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "leftValue": "={{ $json.title.toLowerCase() }}",
              "rightValue": "ai",
              "operator": {
                "type": "string",
                "operation": "contains"
              },
              "id": "a5bcf71c-cdd5-4996-93cf-a2fd0b64092d"
            },
            {
              "leftValue": "={{ $json.job_type }}",
              "rightValue": "",
              "operator": {
                "type": "string",
                "operation": "isNotEmpty"
              },
              "id": "42194438-649b-406e-aa41-88c2703e7c1f"
            },
            {
              "id": "c5b90b1f-c1e8-416b-938a-79bdcead50d4",
              "leftValue": "={{ $json.title.toLowerCase() }}",
              "rightValue": "remote",
              "operator": {
                "type": "string",
                "operation": "contains"
              }
            }
          ],
          "combinator": "or"
        },
        "options": {}
      },
      "id": "56a90f86-c1f0-431a-98f3-7c4ca3b6f36d",
      "name": "Filter Full-Time & Internships",
      "type": "n8n-nodes-base.filter",
      "typeVersion": 2.3,
      "position": [
        832,
        368
      ]
    },
    {
      "parameters": {
        "operation": "rowNotExists",
        "dataTableId": {
          "__rl": true,
          "value": "zfsyNJLJ7zGXQ6qk",
          "mode": "list",
          "cachedResultName": "Ai job sent",
          "cachedResultUrl": "/projects/07xVcyi7XGpkLNoV/datatables/zfsyNJLJ7zGXQ6qk"
        },
        "filters": {
          "conditions": [
            {
              "keyName": "job_id",
              "keyValue": "={{ $json.job_id }}"
            },
            {
              "keyName": "url",
              "keyValue": "={{ $json.url }}"
            }
          ]
        }
      },
      "id": "e462e317-f04c-4d07-8415-78c1b44e9d29",
      "name": "Check If New Job",
      "type": "n8n-nodes-base.dataTable",
      "typeVersion": 1.1,
      "position": [
        880,
        96
      ]
    },
    {
      "parameters": {
        "chatId": "8347394087",
        "text": "=🚀 New AI Automation Engineer Job!\n\n📋 Title: {{ $('Extract Job Fields').item.json.title }}\n🏢 Company: {{ $('Extract Job Fields').item.json.company }}\n💼 Type: {{ $('Extract Job Fields').item.json.job_type }}\n📅 Posted: {{ $('Extract Job Fields').item.json.posted_date }}\n🔗 Apply: {{ $('Extract Job Fields').item.json.url }}",
        "additionalFields": {
          "disable_web_page_preview": false,
          "parse_mode": "HTML"
        }
      },
      "id": "278c57b2-eac1-47b9-94a2-a8615c3e1397",
      "name": "Send Telegram Notification",
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1.2,
      "position": [
        1072,
        288
      ],
      "webhookId": "064d4f09-b31f-4f44-aaca-68be7f09ab38",
      "credentials": {
        "telegramApi": {
          "id": "srgiMXv2WWYvq05e",
          "name": "Telegram account"
        }
      }
    },
    {
      "parameters": {
        "operation": "append",
        "documentId": {
          "__rl": true,
          "value": "15tMi0LcmUS2kbl6jrfMWOpqeUmLjMi00vbkJdzZKn2E",
          "mode": "list",
          "cachedResultName": "job list",
          "cachedResultUrl": "https://docs.google.com/spreadsheets/d/15tMi0LcmUS2kbl6jrfMWOpqeUmLjMi00vbkJdzZKn2E/edit?usp=drivesdk"
        },
        "sheetName": {
          "__rl": true,
          "value": "gid=0",
          "mode": "list",
          "cachedResultName": "Sheet1",
          "cachedResultUrl": "https://docs.google.com/spreadsheets/d/15tMi0LcmUS2kbl6jrfMWOpqeUmLjMi00vbkJdzZKn2E/edit#gid=0"
        },
        "columns": {
          "mappingMode": "defineBelow",
          "value": {
            "Position": "={{ $json.position }}",
            "Location": "={{ $json.location }}",
            "Apply-Url": "={{ $json.apply_url }}",
            "Job-id": "={{ $json.id }}",
            "Job-title": "={{ $json.slug }}"
          },
          "matchingColumns": [
            "id"
          ],
          "schema": [
            {
              "id": "Job-id",
              "displayName": "Job-id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true,
              "removed": false
            },
            {
              "id": "Position",
              "displayName": "Position",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true,
              "removed": false
            },
            {
              "id": "Location",
              "displayName": "Location",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true,
              "removed": false
            },
            {
              "id": "Apply-Url",
              "displayName": "Apply-Url",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true,
              "removed": false
            },
            {
              "id": "Job-title",
              "displayName": "Job-title",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true,
              "removed": false
            }
          ],
          "attemptToConvertTypes": false,
          "convertFieldsToString": false
        },
        "options": {}
      },
      "type": "n8n-nodes-base.googleSheets",
      "typeVersion": 4.7,
      "position": [
        576,
        288
      ],
      "id": "67cbfd04-c220-43c3-9ac2-825f090f4d27",
      "name": "Append row in sheet",
      "credentials": {
        "googleSheetsOAuth2Api": {
          "id": "3bDTUGHHpQq1iqcQ",
          "name": "Google Sheets OAuth2 API"
        }
      }
    },
    {
      "parameters": {
        "jsCode": "return items.slice(2);"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [
        288,
        0
      ],
      "id": "aefece29-f74e-4e8d-87d6-bdfae4f6caf8",
      "name": "Code in JavaScript"
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.errorTrigger",
      "typeVersion": 1,
      "position": [
        1520,
        384
      ],
      "id": "030ec488-20b5-49b1-a9e6-c997aaade49a",
      "name": "Error Trigger"
    },
    {
      "parameters": {
        "chatId": "8347394087",
        "text": "=🚨 n8n ALERT: Workflow Failed!          Workflow: {{ $workflow.name }}     Node: {{ $json.node.name }}     Error: {{ $json.error.message }}     Time: {{ $now }}",
        "additionalFields": {}
      },
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1.2,
      "position": [
        1504,
        128
      ],
      "id": "9ae117fa-88ab-4d65-aa1c-5fe6d78fa2c2",
      "name": "Send a text message",
      "webhookId": "4dc6a51b-8d70-4cb2-86c4-baccf242516f",
      "credentials": {
        "telegramApi": {
          "id": "srgiMXv2WWYvq05e",
          "name": "Telegram account"
        }
      }
    }
  ],
  "pinData": {},
  "connections": {
    "Every 6 Hours": {
      "main": [
        [
          {
            "node": "Search AI Jobs",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Search AI Jobs": {
      "main": [
        [
          {
            "node": "Code in JavaScript",
            "type": "main",
            "index": 0
          }
        ],
        []
      ]
    },
    "Extract Job Fields": {
      "main": [
        [
          {
            "node": "Filter Full-Time & Internships",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Filter Full-Time & Internships": {
      "main": [
        [
          {
            "node": "Check If New Job",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Check If New Job": {
      "main": [
        [
          {
            "node": "Send Telegram Notification",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Append row in sheet": {
      "main": [
        [
          {
            "node": "Extract Job Fields",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Code in JavaScript": {
      "main": [
        [
          {
            "node": "Append row in sheet",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Error Trigger": {
      "main": [
        [
          {
            "node": "Send a text message",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": true,
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate",
    "availableInMCP": false,
    "timeSavedMode": "fixed",
    "callerPolicy": "workflowsFromSameOwner"
  },
  "versionId": "726d77f7-eafb-4702-bd39-637d5f6b31b3",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "1f4c84b7e94c42a066029ba9e29b31d26bc93236737c953400eeba9851804200"
  },
  "nodeGroups": [],
  "id": "AGiIJCT1SreFxU3j",
  "tags": []
}
DM me on X @EL_ifeanyi234 if you need a custom one
