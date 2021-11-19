---
title: test
date updated: 2021-11-19 03:10
---

## so..what is json?

Im assuming you are here to learn about json schemas, but just to make sure we are on the same page, let me quickly explain json:

Json is a way of structuring data through text, that you want to either store or send between services; server->server, server->website, website->end user etc..

```ad-info
the different types you can store in a json is:
- string
- number/double
- boolean
- array/list
- object/map
- null
```

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

## Schema

do di do di do.. add some text about json schema here..

in order to more easily create json schemas, i recomend the following:

- Install VsCode
- Inside schema, referece thet official "json schema" schema.
  - I will do this in all my examples, as it gives me automplete options when pressing "ctrl+space"

```ad-info
title: about schema version used
there are several 'top-level' schemas avalible for usage, that adds features to your schema, however the latest version that works with vscode is 'draft-07', so we are using that.
```

I wil try to start this off simple and explain as i go along.

### the basics

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

#### schema begining

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

i want to create a schema that validates some properties for me. create a new file called `json.json` in the same folder as  `schema.json`

```json
{
	"$schema": "./schema.json"
	"name":"philip",
	"year":"1987",
	"customer":true
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

If `json.json` was empty and i just added `"$schema": "./schema.json"` and then in vscode pressed `ctrl+space`, i would get something like this:
![[Pasted image 20211119033320.png]]

now this a good start, but we have only defined that when these properties are defined, that they have the correct type (ie `year` can only have numbers). it will still accept that you provide a empty json, or that year can be written as `0`.

### one-two-three validate thee

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
