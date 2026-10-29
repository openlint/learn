---
layout: default
title: Built-in rules
summary: Every rule in the built-in OpenAPI, AsyncAPI and Arazzo rulesets, whether it's on by default, and its severity.
crumbs:
  - name: built-in rules
    url: /builtin-rules/
---
# Built-in rules

OpenLint ships three built-in rulesets, still under their Spectral names: `spectral:oas`, `spectral:asyncapi` and `spectral:arazzo`. This page lists every rule in them, generated from `@openlint/openlint-rulesets` in OpenLint 1.0.0.

**On by default** means the rule runs when you extend the ruleset with a bare name, such as `extends: spectral:oas`. Rules marked **no** only run when you extend with `[spectral:oas, all]`, or when you turn them on by name, for example `contact-properties: true`. Severity is the rule's own default, which you can change with a shorthand such as `info-contact: error`.
## spectral:oas

57 rules, 46 on by default.

| Rule | On by default | Severity | Formats | Description |
| -- | -- | -- | -- | -- |
| `array-items` | yes | error | oas2, oas3, oas3_0, oas3_1, oas3_2 | Schemas with "type: array", require a sibling "items" field |
| `contact-properties` | **no** | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Contact object must have "name", "url" and "email". |
| `duplicated-entry-in-enum` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Enum values must not have duplicate entry. |
| `info-contact` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Info object must have "contact" object. |
| `info-description` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Info "description" must be present and non-empty string. |
| `info-license` | **no** | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Info object must have "license" object. |
| `license-url` | **no** | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | License object must include "url". |
| `no-$ref-siblings` | yes | error | oas2, oas3_0 | Property must not be placed among $ref |
| `no-eval-in-markdown` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Markdown descriptions must not have "eval(". |
| `no-script-tags-in-markdown` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Markdown descriptions must not have "<script>" tags. |
| `oas2-anyOf` | yes | warn | oas2 | anyOf is not available in OpenAPI v2, it was added in OpenAPI v3 |
| `oas2-api-host` | yes | warn | oas2 | OpenAPI "host" must be present and non-empty string. |
| `oas2-api-schemes` | yes | warn | oas2 | OpenAPI host "schemes" must be present and non-empty array. |
| `oas2-discriminator` | yes | error | oas2 | discriminator property must be defined and required |
| `oas2-host-not-example` | **no** | warn | oas2 | Host URL must not point at example.com. |
| `oas2-host-trailing-slash` | yes | warn | oas2 | Server URL must not have trailing slash. |
| `oas2-oneOf` | yes | warn | oas2 | oneOf is not available in OpenAPI v2, it was added in OpenAPI v3 |
| `oas2-operation-formData-consume-check` | yes | warn | oas2 | Operations with "in: formData" parameter must include "application/x-www-form-urlencoded" or "multipart/form-data" in their "consumes" property. |
| `oas2-operation-security-defined` | yes | warn | oas2 | Operation "security" values must match a scheme defined in the "securityDefinitions" object. |
| `oas2-parameter-description` | **no** | warn | oas2 | Parameter objects must have "description". |
| `oas2-schema` | yes | error | oas2 | Validate structure of OpenAPI v2 specification. |
| `oas2-unused-definition` | yes | warn | oas2 | Potentially unused definition has been detected. |
| `oas2-valid-media-example` | yes | error | oas2 | Examples must be valid against their defined schema. |
| `oas2-valid-schema-example` | yes | error | oas2 | Examples must be valid against their defined schema. |
| `oas3_1-callbacks-in-webhook` | yes | warn | oas3_1, oas3_2 | Callbacks should not be defined in a webhook. |
| `oas3_1-servers-in-webhook` | yes | warn | oas3_1, oas3_2 | Servers should not be defined in a webhook. |
| `oas3_2-no-deprecated-xml-attribute` | yes | warn | oas3_2 | "xml.attribute" is deprecated in OAS 3.2; use "xml.nodeType: attribute" instead. |
| `oas3-api-servers` | yes | warn | oas3 | OpenAPI "servers" must be present and non-empty array. |
| `oas3-callbacks-in-callbacks` | yes | warn | oas3 | Callbacks should not be defined within a callback |
| `oas3-examples-value-or-externalValue` | yes | warn | oas3 | Examples must have either "value" or "externalValue" field. |
| `oas3-operation-security-defined` | yes | warn | oas3 | Operation "security" values must match a scheme defined in the "components.securitySchemes" object. |
| `oas3-parameter-description` | **no** | warn | oas3 | Parameter objects must have "description". |
| `oas3-schema` | yes | error | oas3 | Validate structure of OpenAPI v3 specification. |
| `oas3-server-not-example.com` | **no** | warn | oas3 | Server URL must not point at example.com. |
| `oas3-server-trailing-slash` | yes | warn | oas3 | Server URL must not have trailing slash. |
| `oas3-server-variables` | yes | error | oas2, oas3, oas3_0, oas3_1, oas3_2 | Server variables must be defined and valid and there must be no unused variables. |
| `oas3-unused-component` | yes | warn | oas3 | Potentially unused component has been detected. |
| `oas3-valid-media-example` | yes | error | oas3 | Examples must be valid against their defined schema. |
| `oas3-valid-schema-example` | yes | error | oas3 | Examples must be valid against their defined schema. |
| `openapi-tags` | **no** | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | OpenAPI object must have non-empty "tags" array. |
| `openapi-tags-alphabetical` | **no** | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | OpenAPI object must have alphabetical "tags". |
| `openapi-tags-uniqueness` | yes | error | oas2, oas3, oas3_0, oas3_1, oas3_2 | Each tag must have a unique name. |
| `operation-description` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Operation "description" must be present and non-empty string. |
| `operation-operationId` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Operation must have "operationId". |
| `operation-operationId-unique` | yes | error | oas2, oas3, oas3_0, oas3_1, oas3_2 | Every operation must have unique "operationId". |
| `operation-operationId-valid-in-url` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | operationId must not contain characters that are invalid when used in URL. |
| `operation-parameters` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Operation parameters are unique and non-repeating. |
| `operation-singular-tag` | **no** | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Operation must not have more than a single tag. |
| `operation-success-response` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Operation must have at least one "2xx" or "3xx" response. |
| `operation-tag-defined` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Operation tags must be defined in global tags. |
| `operation-tags` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Operation must have non-empty "tags" array. |
| `path-declarations-must-exist` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Path parameter declarations must not be empty, ex."/given/{}" is invalid. |
| `path-keys-no-trailing-slash` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Path must not end with slash. |
| `path-not-include-query` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Path must not include query string. |
| `path-params` | yes | error | oas2, oas3, oas3_0, oas3_1, oas3_2 | Path parameters must be defined and valid. |
| `tag-description` | **no** | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Tag object must have "description". |
| `typed-enum` | yes | warn | oas2, oas3, oas3_0, oas3_1, oas3_2 | Enum values must respect the specified type. |

## spectral:asyncapi

55 rules, 47 on by default.

| Rule | On by default | Severity | Formats | Description |
| -- | -- | -- | -- | -- |
| `asyncapi-3-channel-no-empty-parameter` | yes | warn | aas3 | Channel address must not have empty parameter substitution pattern. |
| `asyncapi-3-channel-no-query-nor-fragment` | yes | warn | aas3 | Channel address must not include query ("?") or fragment ("#") delimiter. |
| `asyncapi-3-channel-no-trailing-slash` | yes | warn | aas3 | Channel address must not end with slash. |
| `asyncapi-3-channel-servers` | yes | error | aas3 | Channel servers must be defined in the "servers" object. |
| `asyncapi-3-document-resolved` | yes | error | aas3 | Checking if the AsyncAPI v3 document has valid structure after resolving references. |
| `asyncapi-3-document-unresolved` | yes | error | aas3 | Checking if the AsyncAPI v3 document has valid structure before resolving references. |
| `asyncapi-3-headers-schema-type-object` | yes | error | aas3 | Headers schema type must be "object". |
| `asyncapi-3-operation-description` | yes | warn | aas3 | Operation "description" must be present and non-empty string. |
| `asyncapi-3-operation-security` | yes | error | aas3 | Operation have to reference a defined security schemes. |
| `asyncapi-3-payload-unsupported-schemaFormat` | yes | info | aas3 | Message schema validation is only supported with default unspecified "schemaFormat". |
| `asyncapi-3-server-no-empty-variable` | yes | warn | aas3 | Server host and pathname must not have empty variable substitution pattern. |
| `asyncapi-3-server-no-trailing-slash` | yes | warn | aas3 | Server host must not end with slash. |
| `asyncapi-3-server-not-example-com` | **no** | warn | aas3 | Server host must not point at example.com. |
| `asyncapi-3-tag-description` | **no** | warn | aas3 | Tag object must have "description". |
| `asyncapi-3-tags` | yes | warn | aas3 | AsyncAPI document must have non-empty "tags" array. |
| `asyncapi-3-tags-alphabetical` | **no** | warn | aas3 | AsyncAPI object must have alphabetical "tags". |
| `asyncapi-3-tags-uniqueness` | yes | error | aas3 | Each tag must have a unique name. |
| `asyncapi-channel-no-empty-parameter` | yes | warn | aas2 | Channel path must not have empty parameter substitution pattern. |
| `asyncapi-channel-no-query-nor-fragment` | yes | warn | aas2 | Channel path must not include query ("?") or fragment ("#") delimiter. |
| `asyncapi-channel-no-trailing-slash` | yes | warn | aas2 | Channel path must not end with slash. |
| `asyncapi-channel-parameters` | yes | error | aas2, aas3 | Channel parameters must be defined and there must be no redundant parameters. |
| `asyncapi-channel-servers` | yes | error | aas2 | Channel servers must be defined in the "servers" object. |
| `asyncapi-headers-schema-type-object` | yes | error | aas2 | Headers schema type must be "object". |
| `asyncapi-info-contact` | yes | warn | aas2, aas3 | Info object must have "contact" object. |
| `asyncapi-info-contact-properties` | yes | warn | aas2, aas3 | Contact object must have "name", "url" and "email". |
| `asyncapi-info-description` | yes | warn | aas2, aas3 | Info "description" must be present and non-empty string. |
| `asyncapi-info-license` | yes | warn | aas2, aas3 | Info object must have "license" object. |
| `asyncapi-info-license-url` | **no** | warn | aas2, aas3 | License object must include "url". |
| `asyncapi-latest-version` | yes | info | aas2, aas3 | Checking if the AsyncAPI document is using the latest version. |
| `asyncapi-message-examples` | yes | error | aas2 | Examples of message object should follow by "payload" and "headers" schemas. |
| `asyncapi-message-messageId-uniqueness` | yes | error | aas2 | "messageId" must be unique across all the messages. |
| `asyncapi-operation-description` | yes | warn | aas2 | Operation "description" must be present and non-empty string. |
| `asyncapi-operation-operationId` | yes | error | aas2 | Operation must have "operationId". |
| `asyncapi-operation-operationId-uniqueness` | yes | error | aas2 | "operationId" must be unique across all the operations. |
| `asyncapi-operation-security` | yes | error | aas2 | Operation have to reference a defined security schemes. |
| `asyncapi-parameter-description` | **no** | warn | aas2, aas3 | Parameter objects must have "description". |
| `asyncapi-payload` | yes | error | aas2 | Payloads must be valid against AsyncAPI Schema object. |
| `asyncapi-payload-default` | yes | error | aas2 | Default must be valid against its defined schema. |
| `asyncapi-payload-examples` | yes | error | aas2 | Examples must be valid against their defined schema. |
| `asyncapi-payload-unsupported-schemaFormat` | yes | info | aas2 | Message schema validation is only supported with default unspecified "schemaFormat". |
| `asyncapi-schema` | yes | error | aas2 | Validate structure of AsyncAPI specification. |
| `asyncapi-schema-default` | yes | error | aas2 | Default must be valid against its defined schema. |
| `asyncapi-schema-examples` | yes | error | aas2 | Examples must be valid against their defined schema. |
| `asyncapi-server-no-empty-variable` | yes | warn | aas2 | Server URL must not have empty variable substitution pattern. |
| `asyncapi-server-no-trailing-slash` | yes | warn | aas2 | Server URL must not end with slash. |
| `asyncapi-server-not-example-com` | **no** | warn | aas2 | Server URL must not point at example.com. |
| `asyncapi-server-security` | yes | error | aas2 | Server have to reference a defined security schemes. |
| `asyncapi-server-variables` | yes | error | aas2 | Server variables must be defined and there must be no redundant variables. |
| `asyncapi-servers` | yes | warn | aas2, aas3 | AsyncAPI object must have non-empty "servers" object. |
| `asyncapi-tag-description` | **no** | warn | aas2 | Tag object must have "description". |
| `asyncapi-tags` | yes | warn | aas2 | AsyncAPI object must have non-empty "tags" array. |
| `asyncapi-tags-alphabetical` | **no** | warn | aas2 | AsyncAPI object must have alphabetical "tags". |
| `asyncapi-tags-uniqueness` | yes | error | aas2 | Each tag must have a unique name. |
| `asyncapi-unused-components-schema` | yes | warn | aas2, aas3 | Potentially unused components schema has been detected. |
| `asyncapi-unused-components-server` | yes | warn | aas2, aas3 | Potentially unused components server has been detected. |

## spectral:arazzo

22 rules, 22 on by default.

| Rule | On by default | Severity | Formats | Description |
| -- | -- | -- | -- | -- |
| `arazzo-document-schema` | yes | error | arazzo1_0 | Arazzo Document must be valid against the Arazzo schema. |
| `arazzo-info-description` | yes | warn | arazzo1_0 | Info "description" should be present and non-empty string. |
| `arazzo-info-summary` | yes | hint | arazzo1_0 | Info "summary" is recommended be present and be a non-empty string. |
| `arazzo-no-script-tags-in-markdown` | yes | warn | arazzo1_0 | Markdown descriptions must not have "<script>" tags. |
| `arazzo-source-descriptions-type` | yes | warn | arazzo1_0 | Source Description "type" should be present. |
| `arazzo-step-description` | yes | warn | arazzo1_0 | Step "description" should be present and non-empty string. |
| `arazzo-step-failure-actions-validation` | yes | error | arazzo1_0 | Every failure action must have a unique "name", and the fields "workflowId" and "stepId" are mutually exclusive. |
| `arazzo-step-operationPath` | yes | hint | arazzo1_0 | It is recommended to use "operationId" rather than "operationPath". |
| `arazzo-step-output-validation` | yes | error | arazzo1_0 | Every step output must have unique name and its value must be a valid runtime expression. |
| `arazzo-step-parameters-validation` | yes | error | arazzo1_0 | Step parameters and workflow parameters must valid. |
| `arazzo-step-request-body-validation` | yes | error | arazzo1_0 | Every step request body must have a valid `contentType` and use of runtime expressions. |
| `arazzo-step-stepId` | yes | warn | arazzo1_0 | Step "stepId" should follow the pattern "^[A-Za-z0-9_\-]+$". |
| `arazzo-step-success-actions-validation` | yes | error | arazzo1_0 | Every success action must have a unique "name", and the fields "workflowId" and "stepId" are mutually exclusive. |
| `arazzo-step-success-criteria-validation` | yes | error | arazzo1_0 | Every success criteria must have a valid context, conditions, and types. |
| `arazzo-step-validation` | yes | error | arazzo1_0 | Every step must have a valid "stepId" and an valid "operationId" or "operationPath" or "workflowId". |
| `arazzo-workflow-depends-on-validation` | yes | error | arazzo1_0 | Every workflow dependency must be valid. |
| `arazzo-workflow-description` | yes | warn | arazzo1_0 | Workflow "description" should be present and non-empty string. |
| `arazzo-workflow-output-validation` | yes | error | arazzo1_0 | Every workflow output must have unique name and its value must be a valid runtime expression. |
| `arazzo-workflow-stepId-unique` | yes | error | arazzo1_0 | Every step must have unique "stepId". |
| `arazzo-workflow-summary` | yes | hint | arazzo1_0 | Workflow "summary" should be present and non-empty string. |
| `arazzo-workflow-workflowId` | yes | warn | arazzo1_0 | Workflow "workflowId" should follow the pattern "^[A-Za-z0-9_\-]+$". |
| `arazzo-workflowId-unique` | yes | error | arazzo1_0 | Every workflow must have unique "workflowId". |
