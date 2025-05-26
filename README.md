# Rengbis

this is a "technology preview" (meaning that it is still just a rough experiment) of a possible schema language for validating payloads; also the name is just a temporary placeholder.

It is a **content** schema definition, that allows to specify the expected content of a payload.
It's not focused on specific formats, and thus it is not able to validate the use of any specific serialization option (eg. using attributes instead of elements in an `xml` document).

The aim of this project is mostly to collect feedback, in order to decide how to push it forward (or just drop it altogether if a better alternative is found).

Thanks to [Tim Bray](https://www.tbray.org/ongoing/misc/Tim) for [suggesting](https://cosocial.ca/@timbray/114574799312311970) the name for the project!

## Why creating another data validator
Most schema definition languages are pretty tedious to use; the only "enjoyable" schema language I have met is the compact syntax of [RELAX NG](https://en.wikipedia.org/wiki/RELAX_NG); its project and tools are pretty stale (they were last updated in the early 2000), but they still work wonders for processing XML files.
RELAX NG affordance in defining a schema beates XSD hands down.

This project is an experiment to try to replicate the convenience and affordance of RELAX NG schema definition (some hints to this also in the temporary name of this project) getting rid of the tight binding with XML, making it suitable to validate payloads of multiple serialization protocols.

## How does it compare to alternative options
The closest alternative I have spotted is [CUE](https://cuelang.org/), even though I discoverd it only after having already worked on this project. I will have to investigate the CUE project further to understand if this is just an half baked duplicated effort, or there are some differences that may justify the effort to push this project forward.

# Example
Here is a simple example of a `rengbis` structure (taken from the tests).

## Simple structure
#### Rengbis schema
```rengbis
= {
	name: text
	age: number
	hobbies: text*
}
```

The above schema would validate the following documents:
#### JSON
```json
{
    "name": "John",
    "age":  30,
    "hobbies": ["reading", "hiking"]
}
```

#### Yaml
```yaml
name: John
age:  30
hobbies:
  - reading
  - hiking
```


#### XML
```xml
<root>
    <name>John</name>
    <age>30</age>
    <hobbies>reading</hobbies>
    <hobbies>hiking</hobbies>
</root>
```


# Implementation
The current implementation is written in `scala 3`, using [`ZIO`](https://zio.dev) and its [`parser` library](https://zio.dev/zio-parser/)

## Supported formats
The current implementation provides helpers to validate `xml`, `json`, and `yaml` documents. Adding support for more formats should be pretty easy, especially if other libraries are already available to desirialize the payload.

The current implementation encodes all the values using the `rengbis.Value` class before validating their content.


# Status of the project
This is a very early prototype, shared only to get some feedback; besides the few test included in the code base, it has not be used anywhere else.

## Missing pieces
There are some notable features missing from the current implementation. The most notable are:
- missing support for comments in the schema definition syntax; I briefely tried to add it, but failed miserably, so I have postpone the effort
- numeric values constraints: the options for numeric constraints are way more articulated than what has already been implemented for strings and lists, and they will require some thoughts on how to define them. Nothing conceptually difficult, but will probably require a lot of tries before finding a satisfying arrangement.

## Future enhancements
Before venturing in this experiment, I did many tries with [`zio-schema`](https://github.com/zio/zio-schema); unfortunately I was not able to find a way to use that library to achieve my goals.
[Lately](https://x.com/jdegoes/status/1919380595597090856) a [new version](https://github.com/zio/zio-blocks) of `zio-schema` has been announced; I haven't had the time to play with it yet, but the idea of using `rengbis` as a language to define `zio-schema` values that could be later leveraged to validate/parse actual payloads, getting rid of the custom  machinary I had to build myself seems an interesting option to validate.



