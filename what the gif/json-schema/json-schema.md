---
title: test
date updated: 2021-11-19 03:10
---

## so..what is json?

Im assuming you are here to learn about json schemas, but just to make sure we are on the same page, let me quickly explain json:

Json is a way of structuring data through text, that you want to either store or send between services; server->server, server->website, website->end user etc..

!!! info Json types
	the different types you can store in a json is:
	- string
	- number/double
	- boolean
	- array/list
	- object/map
	- null


The nice thing about json is that its both (mostly) human and computer readable.

```json
{
	"text":"this is some text",
	"number":42.23,
	"boolean":true,
	"array":[
		"array is also called 'list' in many languages"
		"first",
		"second",
		"third"
	],
	"object":{
		"subvalue":"object is also called a 'map' in many languages"
	},
	"nothing":null
}
```

Most languages have the ability to load json and use it as an object and save objects to json (serialise and deserialise)

if you input the text abouve into your favourite language and load it, you can  get the following (im using powershell)

```powershell
# assume the data is already loaded to $json
PS C:\> $json

text    : this is some text
number  : 42.23
boolean : True
array   : {array is also call..}
object  : @{subvalue=this is some text.. again}
nothing :

PS C:\> $json.array

array is also called 'list' in many languages
first
second
third

PS C:\> $json.object

subvalue
--------
object is also called a 'map' in many languages"

```

```ad-warning
title: about encoding
since json data is provided through text you need to care about text encoding so special characters (ie country or region specific characters).  
In most cases this is not a factor as data sent to and from web services is encoded in 'utf-8', but some languages encode text as utf-16 or Ascii
```

So in summary, you can write text in a way to organise, serialise and deserialise data.\
**BUT**; what if you write an application or a web service, and want to know excactly what kind of json you accept, and have a way of verifying data you have **Json schema!**

## Schema - basics

do di do di do.. add some text about json schema here..

in order to more easily create json schemas, i recomend the following:

- Install VsCode
- Inside schema, referece thet official "json schema" schema.
  - I will do this in all my examples, as it gives me automplete options when pressing "ctrl+space"

```ad-info
title: about schema version
there are several 'top-level' schemas avalible for usage, that adds features to your schema, however the latest version that works with vscode is 'draft-07', so we are using that.

when referencing schema (http://json-schema.org/{version}/schema) and documentation (https://json-schema.org/{version}/json-schema-validation.html), for versions draft-06 and draft-07, you use '../draft-07/..' in your url, but for the newer versions (2019-09 and 2020-12) you use '../draft/2020-12/..'
```

I wil try to start this off simple and explain as i go along.

## versions

all of the schemas you create will have a reference to a 'master' schema. this schema is created by the good folks at [json-schema.org](https://json-schema.org/), that also maintains the features of the 'json-schema schema'.

They have a couple of different versions of the schema you can choose from that defines what features are avalible within your schema, and how to write it.  
For most cases and the most widely supported version is `draft-07`, and what im going to use in all my examples.

all of the official schemas can be referenced with `http://json-schema.org/{version}/schema` and documentation looked up with `https://json-schema.org/{version}/{documentation}`

im going to dump some info down below, and this can be used as a lookup for schema and documentation if you want to know more.

version|url part|schema|documention|whats new
---|---|---|---|---
draft-06|`/draft-06/`|[schema][06-schema]|[core][06-doc-core], [validation][06-doc-validation]|[04->06][06-new]
draft-07|`/draft-07/`|[schema][07-schema]|[core][07-doc-core], [validation][07-doc-validation], [pointers][07-doc-pointer]|[06->07][07-new]
2019-09|`/draft/2019-09/`|[schema][2019-schema]|[core][2019-doc-core], [validation][2019-doc-validation], [pointers][2019-doc-pointer]|[06->2019][2019-new]
2020-12|`/draft/2020-12/`|[schema][2020-schema]|[core][2020-doc-core], [validation][2020-doc-validation], [pointers][2020-doc-pointer]|[2019->2020][2020-new]

### one-two-tree

in order to explain how a json schema works, lets create an example
Below is a 

```json
{
	"$schema": "./schema.json",
	"name":"philip",
	"year":"1987",
	"author":true,
	"city":"oslo"
}
```

## the basics

every definition in a json schema is a object and for every property you want to have, you need to define how that property is handled.

```json
"nameOfProperty":{
	//property definition is added here
}
```

```ad-info
title:required fields
for every property you want to define, you need to atleast define the type you expect
```

## create a new schema

to start a new schema, create a file named `schema.json`. the file name does not really matter, but you are referencing this later on.

inside the schema, add the following text:

```json
{
 "$schema": "http://json-schema.org/draft-07/schema",
 "type":"object",
 "properties": {
 
 }
}
```

| property   | description                                                                                                                                                   |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $schema    | reference to a schema we want to use when creating this file. as i explained earlier, it helps us create schemas faster by providing autocomplete.            |
| type       | what kind of type should be expected here. it supports all of the types normally used by json. because we define a basic json file, this should be an object. |
| properties | when you define type=object, one would expect that the object had properties. these are defined inside 'properties'                                           |

#### property

for every property you want to have, you need to define how that property is handled.

```json
"nameOfProperty":{
	//property definition is added here
}
```

### one-two-three

I want to create a schema that validates a json document for me.  
I create a new file called `json.json` in the same folder as  `schema.json`

```json
{
	"$schema": "./schema.json",
	"name":"philip",
	"year":"1987",
	"customer":true,
	"city":"oslo"
}
```

so now we need to define the actual properties we want to validate

```json
{
 "$schema": "http://json-schema.org/draft-07/schema",
 "type":"object",
 "properties": {
 	"name":{
		"type":"string"
	},
	"year":{
		"type":"integer"
	},
	"customer":{
		"type":"boolean"
	},
	"city":{
		"type":"string"
	}
 }
}

```

this defines all the items in the json and validates that they are of the correct type. 
It will also give me autocomplete when inside vscode:  
![img](./wtg-json-schema-img/propertyDropdown.png)

### one-two-three validate thee

this a good start, but we have only defined that when these properties are defined, that they have the correct type (ie `year` can only have numbers). it will still accept that you provide a empty json, or that year can be written as `0`.

i will 

#### enum
used with `string`
different types require different validation, so using the example above, we can start with `city`. 
I want to be able to have this as a selecton of cities, so i will use the `enum` validation. this validation is an array of strings, that `json.json` can check against to make sure the correct city is selected.
```json
"city": {
	"type": "string",
	"enum": [
		"Bergen",
		"Oslo",
		"Trondheim",
		"Bodø",
		"Sandnesjøen"
	]
}
```

when using that value in vscode it would look something like this:
![[Pasted image 20211119034955.png]]

#### minlength and maxlength

used with `string`
`minlength` and `maxlength` defines how short and long a string can be, so il use this on the name, to make sure that there is some value in the name

``` json
"name": {
	"type": "string",
	"minLength": 3
}
```

#### minimum and maximum

used with `integer`
defines the minimum and maximum number.  
in this case we use it to define a year value between 1900 and 2021
``` json
"year": {
	"type": "integer",
	"minimum": 1900,
	"maximum": 2021
}
```

``` ad-info
you also have 'exclusiveMinimum' and 'exclusiveMaximum' that 
```

#### required
in order to enforce that your properties actually is present, is with `required`. 
this is a array of strings, that represents the properties that need to be present in a object.
this is defined at object level, so for our schema, this means root level of the json schema.

``` json
	"type": "object",
	"properties": {
 		....
	},
	"required": [
		"name",
		"year",
		"city",
		"customer"
	]

```

#### the schema after validation

``` json
{
    "$schema": "http://json-schema.org/draft-07/schema",
    "type": "object",
    "properties": {
        "name": {
            "type": "string",
            "minLength": 3
        },
        "year": {
            "type": "integer",
            "minimum": 1900,
            "maximum": 2021
        },
        "customer": {
            "type": "boolean"
        },
        "city": {
            "type": "string",
            "enum": [
                "Bergen",
                "Oslo",
                "Trondheim",
                "Bodø",
                "Sandnesjøen"
            ]
        }
    },
    "required": [
        "name",
        "year",
        "city",
        "customer"
    ]
}
```

[06-schema]: http://json-schema.org/draft-06/schema
[06-doc-core]: https://json-schema.org/draft-06/json-schema-core.html
[06-doc-validation]: https://json-schema.org/draft-06/json-schema-validation.html
[06-doc-pointer]: https://json-schema.org/draft-06/relative-json-pointer.html
[06-new]: https://json-schema.org/draft-06/json-schema-release-notes.html

[07-schema]: http://json-schema.org/draft-07/schema
[07-doc-core]: https://json-schema.org/draft-07/json-schema-core.html
[07-doc-validation]: https://json-schema.org/draft-07/json-schema-validation.html
[07-doc-pointer]: https://json-schema.org/draft-07/relative-json-pointer.html
[07-new]: https://json-schema.org/draft-07/json-schema-release-notes.html

[2019-schema]: http://json-schema.org/draft/2019-09/schema
[2019-doc-core]: https://json-schema.org/draft/2019-09/json-schema-core.html
[2019-doc-validation]: https://json-schema.org/draft/2019-09/json-schema-validation.html
[2019-doc-pointer]: https://json-schema.org/draft/2019-09/relative-json-pointer.html
[2019-new]: https://json-schema.org/draft/2019-09/release-notes.html

[2020-schema]: http://json-schema.org/draft/2020-12/schema
[2020-doc-core]: https://json-schema.org/draft/2020-12/json-schema-core.html
[2020-doc-validation]: https://json-schema.org/draft/2020-12/json-schema-validation.html
[2020-doc-pointer]: https://json-schema.org/draft/2020-12/relative-json-pointer.html
[2020-new]: https://json-schema.org/draft/2020-12/release-notes.html