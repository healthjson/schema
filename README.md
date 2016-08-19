#Health JSON Schema

This is the first draft of the Health JSON schema. This example shows all
possible properties, the application health response can contain, but not all
of them are required. See the schema definition below for more details:

## Example Response
The example is also available under: http://example.health-json.org

```javascript
HTTP/1.1 500 Internal Server Error
Content-Type: application/json
Date: Fri, 19 Aug 2016 04:42:17 GMT
Server: nginx/1.11.1
Content-Length: 10487
Connection: Close

{
  "application": {
    "name": "app",
    "datetime": "2016-04-05T18:25:43.511Z",
    "startup": "2016-04-05T18:25:43.511Z",
    "uptime": "11 days, 14:22:25.12345",
    "hostname": "app.organization.com",
    "version": "1.2.3"
  },
  "health": {
    "databases": false,
    "integrationpoints": true,
    "circuitbreakers": false,
    "filesystem": true
  },
  "databases": [
    {
      "name": "postgres",
      "essential": true,
      "health": true,
      "latency": 0.4,
      "connections": 10
    },
    {
      "name": "redis",
      "essential": false,
      "health": false,
      "latency": 0.2,
      "connections": 1,
      "error": "Connection refused"
    }
  ],
  "integrationpoints": [
    {
      "name": "google-maps-api",
      "essential": false,
      "health": true,
      "latency": 74.5,
      "lastCall": "2016-04-05T18:25:41.454Z",
      "lastSuccess": "2016-04-05T18:25:41.454Z",
      "callsLastMinute": 31,
      "callsLastHour": 1728
    }
  ],
  "circuitbreakers": [
    {
      "name": "authenticator",
      "essential": false,
      "health": false,
      "state": "open",
      "timeout": "2016-04-05T18:26:12.454Z",
      "lastCall": "2016-04-05T18:25:12.454Z",
      "lastSuccess": "2016-04-05T18:25:10.454Z",
      "callsLastMinute": 38,
      "callsLastHour": 2144
    }
  ],
  "filesystems": [
    {
      "name": "/data",
      "essential": true,
      "health": true,
      "readable": true,
      "writable": true,
      "latency": 0.75
    }
  ]
}
```


## Schema
The schema definition is also available under: http://schema.health-json.org

```javascript
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "Application Health",
  "description": "Schema for media type application/health+json",
  "type": "object",
  "properties": {
    "application": {
      "$ref": "#/definitions/application"
    },
    "health": {
      "$ref": "#/definitions/health"
    },
    "databases": {
      "$ref": "#/definitions/databases"
    },
    "integrationpoints": {
      "$ref": "#/definitions/integrationpoints"
    },
    "circuitbreakers": {
      "$ref": "#/definitions/circuitbreakers"
    },
    "filesystems": {
      "$ref": "#/definitions/filesystems"
    }
  },
  "required": [
    "application",
    "health"
  ],
  "definitions": {
    "application": {
      "title": "Application Information",
      "description": "General information about application and current version",
      "type": "object",
      "properties": {
        "name": {
          "title": "Application name",
          "description": "Organization-wide unique name for identifying the application",
          "type": "string",
          "minLength": 1
        },
        "datetime": {
          "title": "Current date and time",
          "description": "The application's current date and time incl. timezone (in ISO 8601: https://tools.ietf.org/html/rfc3339#section-5.6)",
          "type": "string",
          "format": "date-time",
          "minLength": 1
        },
        "startup": {
          "title": "Startup timestamp",
          "description": "The date and time of when the application instance started (in ISO 8601: https://tools.ietf.org/html/rfc3339#section-5.6)",
          "type": "string",
          "format": "date-time",
          "minLength": 1
        },
        "uptime": {
          "title": "Application uptime",
          "description": "The duration how long the application instance is running in the format: \"2 days, 12:43:22\"",
          "type": "string",
          "pattern": "^\\d+ days, \\d{2}:\\d{2}:\\d{2}(.\\d+)*$",
          "minLength": 1
        },
        "hostname": {
          "title": "Hostname",
          "description": "Name of the host which is running the application instance",
          "type": "string",
          "format": "hostname",
          "minLength": 1
        },
        "version": {
          "title": "Version",
          "description": "Current application version",
          "type": "string",
          "minLength": 1
        }
      },
      "required": [
        "name",
        "datetime",
        "startup",
        "uptime",
        "hostname"
      ]
    },
    "health": {
      "title": "Health status",
      "description": "Global health status aggregated by groups",
      "type": "object",
      "properties": {
        "databases": {
          "type": "boolean"
        },
        "integrationpoints": {
          "type": "boolean"
        },
        "circuitbreakers": {
          "type": "boolean"
        },
        "filesystem": {
          "type": "boolean"
        }
      }
    },
    "database": {
      "title": "Database health",
      "description": "Detailed health information about a connected database",
      "type": "object",
      "properties": {
        "name": {
          "type": "string",
          "description": "Unique name/identifier of the database system",
          "minLength": 1
        },
        "essential": {
          "type": "boolean",
          "description": "Database is essential for overall application health"
        },
        "health": {
          "type": "boolean",
          "description": "Connection established without any limitations"
        },
        "latency": {
          "type": "number",
          "description": "Latency or duration for full roundtrip in milliseconds"
        },
        "connections": {
          "type": "integer",
          "description": "Number of connections established"
        },
        "error": {
          "type": "string",
          "description": "Connectivity error occured, if any"
        }
      },
      "required": [
        "name",
        "essential",
        "health",
        "latency"
      ]
    },
    "integrationpoint": {
      "title": "Integration point health",
      "description": "Detailed health information about an integration point",
      "type": "object",
      "properties": {
        "name": {
          "type": "string",
          "description": "Unique name/identifier of the integraiton point",
          "minLength": 1
        },
        "essential": {
          "type": "boolean",
          "description": "Integration point is essential for overall application health"
        },
        "health": {
          "type": "boolean",
          "description": "Integration point callable without any limitations"
        },
        "latency": {
          "type": "number",
          "description": "Latency or duration for full roundtrip in milliseconds"
        },
        "lastCall": {
          "type": "string",
          "format": "date-time",
          "description": "Date and time of last call to integration point (in ISO 8601: https://tools.ietf.org/html/rfc3339#section-5.6)",
          "minLength": 1
        },
        "lastSuccess": {
          "type": "string",
          "format": "date-time",
          "description": "Date and time of last successful call to integration point (in ISO 8601: https://tools.ietf.org/html/rfc3339#section-5.6)",
          "minLength": 1
        },
        "callsLastMinute": {
          "type": "integer",
          "description": "Total number of calls to integration point within the last minute"
        },
        "callsLastHour": {
          "type": "integer",
          "description": "Total number of calls to integration point within the last hour"
        },
        "error": {
          "type": "string",
          "description": "Error occured when calling integration point, if any"
        }
      },
      "required": [
        "name",
        "essential",
        "health",
        "latency"
      ]
    },
    "circuitbreaker": {
      "title": "Circuit breaker state",
      "description": "Detailed state information about a circuit breaker",
      "type": "object",
      "properties": {
        "name": {
          "type": "string",
          "description": "Unique name/identifier of the circuit breaker",
          "minLength": 1
        },
        "essential": {
          "type": "boolean",
          "description": "Service behind circuit breaker is essential for overall application health"
        },
        "health": {
          "type": "boolean",
          "description": "True if circuit breaker is closed"
        },
        "state": {
          "type": "string",
          "pattern": "^closed|half-open|open$",
          "description": "Current state of the circuit breaker"
        },
        "timeout": {
          "type": "string",
          "format": "date-time",
          "description": "If state is \"open\", the date and time when the circuit breaker will switch back to half-open (in ISO 8601: https://tools.ietf.org/html/rfc3339#section-5.6)"
        },
        "lastCall": {
          "type": "string",
          "format": "date-time",
          "description": "Date and time of last call through circuit breaker (in ISO 8601: https://tools.ietf.org/html/rfc3339#section-5.6)",
          "minLength": 1
        },
        "lastSuccess": {
          "type": "string",
          "format": "date-time",
          "description": "Date and time of last successful call through circuit breaker (in ISO 8601: https://tools.ietf.org/html/rfc3339#section-5.6)",
          "minLength": 1
        },
        "callsLastMinute": {
          "type": "integer",
          "description": "Total number of calls through circuit breaker within the last minute"
        },
        "callsLastHour": {
          "type": "integer",
          "description": "Total number of calls through circuit breaker within the last hour"
        },
        "error": {
          "type": "string",
          "description": "Error occured when calling circuit breaker, if any"
        }
      },
      "required": [
        "name",
        "essential",
        "health",
        "state"
      ]
    },
    "filesystem": {
      "title": "File system health",
      "description": "Detailed health information about a mounted file system",
      "type": "object",
      "properties": {
        "name": {
          "type": "string",
          "description": "Unique name/identifier of the file system",
          "minLength": 1
        },
        "essential": {
          "type": "boolean",
          "description": "File system access is essential for overall application health"
        },
        "health": {
          "type": "boolean",
          "description": "Filesystem is fully accessible with expected rights"
        },
        "readable": {
          "type": "boolean",
          "description": "Filesystem is readable"
        },
        "writable": {
          "type": "boolean",
          "description": "Filesystem is writable"
        },
        "latency": {
          "type": "number",
          "description": "Latency or duration for full roundtrip in milliseconds"
        },
        "error": {
          "type": "string",
          "description": "Connectivity error occured, if any"
        }
      },
      "required": [
        "name",
        "essential",
        "health",
        "latency"
      ]
    },
    "databases": {
      "title": "Database health",
      "description": "Detailed health information about all connected databases",
      "type": "array",
      "minItems": 1,
      "items": {
        "$ref": "#/definitions/database"
      }
    },
    "integrationpoints": {
      "title": "Integration point health",
      "description": "Detailed health information about all integration points",
      "type": "array",
      "minItems": 1,
      "items": {
        "$ref": "#/definitions/integrationpoint"
      }
    },
    "circuitbreakers": {
      "title": "Circuit breaker health",
      "description": "Detailed health information about all circuit breakers",
      "type": "array",
      "minItems": 1,
      "items": {
        "$ref": "#/definitions/circuitbreaker"
      }
    },
    "filesystems": {
      "title": "File system health",
      "description": "Detailed health information about all mounted file systems",
      "type": "array",
      "minItems": 1,
      "items": {
        "$ref": "#/definitions/filesystem"
      }
    }
  }
}
```
