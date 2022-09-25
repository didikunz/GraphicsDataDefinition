# GDD - Graphics Data Definition

_NOTE: This is a draft!_

_Everything in this document is subject to change and should be considered a proposal only!_

**Table of contents**

- [Gettings started](#getting-started)
- [General Definition](#general-definition)
- [Basic Types](#basic-types)
- [GDD Types](#gdd-types)
- [For GUI Developers](#for-gui-developers)

## Getting started

The GDD Schema can either be defined in a separate JSON-file, or inline in the HTML-template-file.

**[A live demo of a Reference GUI can be found here!](https://superflytv.github.io/GraphicsDataDefinition/reference-gui/dist/)**

**HTML Graphics file, "example.html":**

The `<script name="graphics-data-definition">` tag refers to a JSON-file that contains the GDD Schema definitions.
The `src=""` is a relative path to the Schema json file.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script
      name="graphics-data-definition"
      type="application/json+gdd"
      src="example.json"
    ></script>
  </head>
  <body>
    *** Content goes here ***
  </body>
</html>
```

**JSON file, "example.json"**

```json
{
  "title": "One-Line GFX Template",
  "type": "object",
  "properties": {
    "text0": {
      "description": "Text content",
      "type": "string",
      "maxLength": 50,
      "gddType": "single-line",
      "gddOptions": {}
    }
  }
}
```

**HTML Graphics file, "example-inline.html":**

The `<script name="graphics-data-definition">` tag can also contain the GDD Schema definitions inline:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script name="graphics-data-definition" type="application/json+gdd">
      {
        "title": "One-Line GFX Template",
        "type": "object",
        "properties": {
          "text0": {
            "description": "Text content",
            "type": "string",
            "maxLength": 50,
            "gddType": "single-line",
            "gddOptions": {

            }
          }
        },
      }
    </script>
    <script type="text/javascript">
      // This is optional, but often helpful.
      // Expose the gddSchema globally:
      window.gddSchema = JSON.parse(
        document.querySelector('head > script[name="graphics-data-definition"]')
          .innerHTML
      );
    </script>
  </head>
  <body>
    *** Content goes here ***
  </body>
</html>
```

**Example data**

```json
{ "text0": "Hello world!" }
```

## General Definition

The GDD is a subset of the [JSON-schema definition](https://json-schema.org/draft/2020-12/json-schema-validation.html#name-a-vocabulary-for-structural), with a few exceptions, see below.

It supports all the basic JSON types (such as strings and numbers) but also extends them with **GDD Types**,
which can be used by _Graphics client GUIs_ to auto-generate input forms for the data.

All **GDD Types** are designed to gracefully degrade in case the GUI can't handle that particular type.
One example is the `["string", "RRGGBB"]` type which (when supported) can provide a color-picker in the GUI,
but if not supported will gracefully degrade to a simple text input.

The GUIs are expected to validate the form-data before submitting it to the GFX Clients.
Examples of how to validate can be found here: _---TODO---_

### Schema

```json
{
  "title": "", // [optional] string, a short name of the GFX-template. Used for informational purposes only.
  "description": "", // [optional] string, a description GFX-template. Used for informational purposes only.
  "type": "object", // MUST be "object"
  "properties": {
    // MUST be an object
    // Properties goes here:
    "myProperty": {}
  },
  "required": [] // [optional]
}
```

### Property

All properties share these definitions: ([JSON-schema definition](https://json-schema.org/draft/2020-12/json-schema-validation.html#section-6.1))

```json
{
  "type": array, // See below
  "label": string, // [Optional] Short label to name the property
  "description": string, // [Optional] Longer description of the property
  "gddType": string, // [Optional unless required by GDD Type] A string containing the GDD Type name, see below
  "gddOptions": object // [Optional unless required by GDD Type] An object containing options for a certain GDD Type, see below
}
```

#### The `type` property

In the standard JSON-schema definition, the `type` property is allowed to be either a string or an array comtaining any combination of the basic types `"boolean"`, `"string"`, `"number"`, `"integer"`, `"array"`, `"object"` or `"null"`.
To reduce the complexity for the GDD GUI implementation however, the valid values for the `type` are reduced to these:

- `"boolean"`
- `"string"`
- `"number"`
- `"integer"` (this is a number with a zero fractional part)
- `"array"`
- `"object"`
- `["boolean", "null"]` ie a `boolean` or `null`
- `["string", "null"]` ie a `string` or `null`
- `["number", "null"]` ie a `number` or `null`
- `["integer", "null"]` ie a `integer` or `null`
- `["array", "null"]` ie a `array` or `null`
- `["object", "null"]` ie a `object` or `null`

## Basic Types

Here follows a list of the basic JSON types supported in GDD.

_All of these types are supported by all GUIs_

### Boolean

```json
{
  "type": "boolean",
  "default": boolean // [Optional] default value
}
```

### String

_[JSON-schema definition](https://json-schema.org/draft/2020-12/json-schema-validation.html#section-6.3)_

```json
{
  "type": "string",
  "default": string, // [Optional] default value
  "maxLength": number, // [Optional] See JSON-schema definition ^
  "minLength": number, // [Optional] See JSON-schema definition ^
  "pattern": Regular Expression, // [Optional] See JSON-schema definition ^
}
```

### Number / Integer

_[JSON-schema definition](https://json-schema.org/draft/2020-12/json-schema-validation.html#section-6.2)_

```json
{
  "type": "number", // or "integer"
  "default": number, // [Optional] default value
  "multipleOf": number, // [Optional] See JSON-schema definition ^
  "maximum": number, // [Optional] See JSON-schema definition ^
  "exclusiveMaximum": number, // [Optional] See JSON-schema definition ^
  "minimum": number, // [Optional] See JSON-schema definition ^
  "exclusiveMinimum": number, // [Optional] See JSON-schema definition ^
}
```

### Array

_[JSON-schema definition](https://json-schema.org/draft/2020-12/json-schema-validation.html#section-6.4)_

```json
{
  "type": "array",
  "items": { // Contains a definition of the items in the array
    "type": "string" // example
  },
  "default": array, // [Optional] default value
  "maxItems": number, // [Optional] See JSON-schema definition ^
  "minItems": number, // [Optional] See JSON-schema definition ^
  "uniqueItems": boolean, // [Optional] See JSON-schema definition ^
  "maxContains": number, // [Optional] See JSON-schema definition ^
  "minContains": number, // [Optional] See JSON-schema definition ^
}
```

### Object

_[JSON-schema definition](https://json-schema.org/draft/2020-12/json-schema-validation.html#section-6.5)_

```json
{
  "type": "object",
  "items": { // Contains a definition of the items in the array
    "type": "string" // example
  },
  "default": object, // [Optional] default value
  "maxProperties": number, // [Optional] See JSON-schema definition ^
  "minProperties": number, // [Optional] See JSON-schema definition ^
  "required": boolean, // [Optional] See JSON-schema definition ^
  "dependentRequired": object, // [Optional] See JSON-schema definition ^
}
```

## GDD Types

A **GDD Type** is an extension of one of the basic types described above,
intended to be displayed to the user in a certain way.

The GDD Type is identified by the `gddType` property, a string on the form `"basic-type/advanced-type"`,
containing more and more specialized GDD Types, separated by `"/"`.

The GDD Types are designed in such a way to allow _graceful drgradation_.
If a GUI doesn't support the display of a specific GDD Type (such as `gddType: "multi-line/rich-formatted-text"`),
it will instead pick the next best thing (`gddType: "multi-line"`, or perhaps even the most basic `type: "string"`).

Below follows a list of the GDD Types, which provides more advanced functionality in GUIs.

Note: All `type`-properties below can be defined as optional by defining it as `["myType", "null"]`.

### Single-line text

A variant of the text input, which specifically is a single line.

```json
{
  "type": "string",
  "gddType": "single-line"
}
```

Example: `"Hello World!"`

### Multi-line text

A variant of the text input, which specifically is a multi-line.

```json
{
  "type": "string",
  "gddType": "multi-line"
}
```

Example: `"Hello World!\nI'm alive!"`

### File path

Lets the user pick a file from disk

```json
{
  "type": "string",
  "gddType": "file-path"
  "gddOptions": {
    "extensions": Array<string> // [Optional] Limit which files can be chosen by the user
  }
}
```

Example: `C:\images\myFile.xml` or `folder/myFile.zip`

### Image File path

Lets the user pick an image file from disk

```json
{
  "type": "string",
  "gddType": "file-path/image-path",
  "gddOptions": {
    "extensions": Array<string> // [Optional] Limit which files can be chosen by the user
  }
}
```

Example: `C:\images\myImage.jpg` or `folder/myImage.png`

### Color - RRGGBB

Let's the user pick a color.

```json
{
  "type": "string",
  "pattern": "^#[0-9a-f]{6}$",
  "gddType": "color-rrggbb"
}
```

The value is stored as a string on the form "#RRGGBB", eg `"#61138e"`.

### Percentage

A number presented as a pecentage

```json
{
  "type": "number",
  "gddType": "percentage"
}
```

The value is stored as a number, eg "25%" -> 0.25

### Duration in milliseconds

A duration value, to be presented in a human readable format (like "HH:MM:SS.xxx")

```json
{
  "type": "integer",
  "gddType": "durationMs"
}
```

The value is stored as a number in milliseconds, eg "1m23s" -> `83000`

## For GUI Developers

When implementing a GUI to support the GDD definitions, you don't have to implement support for all GDD Types - since the GDD Types are designed to degrade gracefully. The only types that are mandatory to implement are the basic types `"boolean"`, `"string"`, `"number"`, `"integer"`, `"array"`and `"object"`.

To degrade gracefully, it is recommended that you follow these practices when implementing the GUI:

```javascript
function determineComponent(prop) {

  // List of supported GUI components, starting with "longest gddType" first:
  if (prop.gddType === "file-path/image-path"))
    return componentImagePicker(prop, allowOptional);
  if (prop.gddType === "file-path"))
    return componentFilePicker(prop, allowOptional);
  if (prop.gddType === "rrggbb"))
    return componentRRGGBB(prop, allowOptional);

  // Fall back to handle the basic types:
  const basicType = Array.isArray(prop.type) ? prop.type[0] : prop.type;
  if (basicType === "boolean") return componentBoolean(prop);
  if (basicType === "string") return componentString(prop);
  if (basicType === "number") return componentNumber(prop);
  if (basicType === "integer") return componentInteger(prop);
  if (basicType === "array") return componentArray(prop);
  if (basicType === "object") return componentObject(prop);

  return null;
}
```

Please have a look at a [reference GUI implementation here](/blob/main/reference-gui/src/gdd-gui.jsx), and [its live demo here.](https://superflytv.github.io/GraphicsDataDefinition/reference-gui/dist/)
