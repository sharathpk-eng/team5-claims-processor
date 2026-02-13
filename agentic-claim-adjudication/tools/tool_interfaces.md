# Tools Interface Schema Documentation

---

## Overview

This document describes the JSON Schema structure for the tools interface. The schema defines three main components: input request, errors, and output response.

**Schema Standard**: JSON Schema Draft-07

---

## Root Object Structure

The root object is of type `object` and contains three required properties:

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `inputRequest` | object | Yes | Contains claim data and tool execution information |
| `errors` | array | Yes | Contains error objects if any errors occurred |
| `outputResponse` | object | Yes | Contains the response data from tool execution |

---

## 1. inputRequest

**Type**: Object  
**Required**: Yes

The `inputRequest` object contains the following properties:

### 1.1 Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `claim_id` | string | Yes | Claim identifier |
| `member_id` | string | Yes | Member identifier |
| `policy_id` | string | Yes | Policy identifier |
| `billed_amount` | string | Yes | Billed amount value |
| `service_date` | string | Yes | Service date |
| `diagnosis_codes` | array | Yes | Array of diagnosis codes |
| `procedure_codes` | array | Yes | Array of procedure codes |
| `provider` | array | Yes | Array of provider information |
| `toolExecuted` | array | Yes | Array of tool execution records |

### 1.2 diagnosis_codes

**Type**: Array  
**Items**: No type specified (empty schema)

### 1.3 procedure_codes

**Type**: Array  
**Items**: No type specified (empty schema)

### 1.4 provider

**Type**: Array  
**Items**: No type specified (empty schema)

### 1.5 toolExecuted

**Type**: Array  
**Items**: Object

Each item in the `toolExecuted` array is an object with the following structure:

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `toolname` | string | Yes | Name of the tool |
| `status` | string | Yes | Execution status |
| `message` | string | Yes | Status message |

### 1.6 Example

```json
{
  "inputRequest": {
    "claim_id": "CLM-001",
    "member_id": "MEM-001",
    "policy_id": "POL-001",
    "billed_amount": "1000.00",
    "service_date": "2024-02-13",
    "diagnosis_codes": [],
    "procedure_codes": [],
    "provider": [],
    "toolExecuted": [
      {
        "toolname": "ExampleTool",
        "status": "success",
        "message": "Tool executed successfully"
      }
    ]
  }
}
```

---

## 2. errors

**Type**: Array  
**Required**: Yes  
**Items**: Object

Each item in the `errors` array is an object with the following structure:

### 2.1 Error Object Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `errorCode` | string | Yes | Error code identifier |
| `errorMessage` | string | Yes | Error message description |

### 2.2 Example

```json
{
  "errors": [
    {
      "errorCode": "ERR001",
      "errorMessage": "Error description"
    }
  ]
}
```

---

## 3. outputResponse

**Type**: Object  
**Required**: Yes

The `outputResponse` object has no defined properties or required fields in the schema. The structure is flexible and can contain any properties as needed.

### 3.1 Schema Definition

```json
{
  "type": "object",
  "properties": {},
  "required": []
}
```

### 3.2 Example

```json
{
  "outputResponse": {}
}
```

---

## Complete Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Generated schema for Root",
  "type": "object",
  "properties": {
    "inputRequest": {
      "type": "object",
      "properties": {
        "claim_id": {
          "type": "string"
        },
        "member_id": {
          "type": "string"
        },
        "policy_id": {
          "type": "string"
        },
        "billed_amount": {
          "type": "string"
        },
        "service_date": {
          "type": "string"
        },
        "diagnosis_codes": {
          "type": "array",
          "items": {}
        },
        "procedure_codes": {
          "type": "array",
          "items": {}
        },
        "provider": {
          "type": "array",
          "items": {}
        },
        "toolExecuted": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "toolname": {
                "type": "string"
              },
              "status": {
                "type": "string"
              },
              "message": {
                "type": "string"
              }
            },
            "required": [
              "toolname",
              "status",
              "message"
            ]
          }
        }
      },
      "required": [
        "claim_id",
        "member_id",
        "policy_id",
        "billed_amount",
        "service_date",
        "diagnosis_codes",
        "procedure_codes",
        "provider",
        "toolExecuted"
      ]
    },
    "errors": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "errorCode": {
            "type": "string"
          },
          "errorMessage": {
            "type": "string"
          }
        },
        "required": [
          "errorCode",
          "errorMessage"
        ]
      }
    },
    "outputResponse": {
      "type": "object",
      "properties": {},
      "required": []
    }
  },
  "required": [
    "inputRequest",
    "errors",
    "outputResponse"
  ]
}
```

---

## Complete Example

```json
{
  "inputRequest": {
    "claim_id": "CLM-001",
    "member_id": "MEM-001",
    "policy_id": "POL-001",
    "billed_amount": "1000.00",
    "service_date": "2024-02-13",
    "diagnosis_codes": [],
    "procedure_codes": [],
    "provider": [],
    "toolExecuted": [
      {
        "toolname": "ExampleTool",
        "status": "success",
        "message": "Tool executed successfully"
      }
    ]
  },
  "errors": [],
  "outputResponse": {}
}
```
