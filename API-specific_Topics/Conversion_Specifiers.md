# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Links](../Links.md)

##### [Next Topic: Conversion Functions](./Conversion_Functions.md)

## Conversion Specifiers

Conversion Specifiers are structures which define how to produce an output structure based on a given input structure.

They are used in a couple of different places in the Kragle system. You use a conversion specifier when creating a Stack with the API to indicate how to convert the output of the previous Block (or input to the Stack) to the Type which is expected as input to the current Block. They can also be used as an Action when creating your own Block types, to convert the output of the previous action into another form. Additionally, the 'action params' element of an Action in a Block definition is itself a Conversion Specifier, since that structure ('action params') is converted using the action input structure as source data. Lastly, when creating a custom Block type you also use (whether you realize it or not) a Conversion Specifier when defining the 'state' structure that is part of the Block 'definition'.

### An example Conversion Specifier

The best way to explain this is with a simple example, and then we'll get to some specifics/details afterwards.

Here's a simple example of a conversion specifier (Conversion Specifiers are always provided as JSON):

```
{
    "greeting": "Hello there, \u0001\u0001/account/screen_name\u0002\u0002!",
    "payload": "\u0001/recent_tweets",
    "cookie": "0123456789ABCDEF",
    "user_details": {
        "account_type": "twitter",
        "account_id": "\u0001/account/id"
    }
}
```

You'll notice there are a number of values in that structure that contain the ASCII `0x01` (`\u0001` in JSON-escaped Unicode) character, and one with an ASCII `0x02` (`\u0002` in JSON). This is how we define placeholders that will get replaced during the conversion process. In fact that's all there is to a 'convert' operation on a Conversion Specifier: any placeholders are identified, replaced with the relevant data from elsewhere, and the resulting structure is returned/output.

The replacement values come from the data 'source' for the conversion. The actual source of this data structure will vary depending on where the conversion is being done, but it can be anything, ie: an object/array/string/number/etc.

The characters we use to indicate our placeholders are regular ASCII non-printing characters: `SOH` (`0x01`, "Start of Header") and `STX` (`0x02`, "Start of Text").

Any sub-strings in a string which are prefixed with `\x01\x01` **and** suffixed with `\x02\x02` is an inline replacement marker - the substring will be replaced with the referenced value from the 'source' data and the result is a string with that substring replaced inline.

A whole string which begins with `\x01` is a regular replacement marker - in the final structure returned from the conversion, these will be replaced with the referenced value from the 'source' data, whateve it is (object/array/string/etc).

The **contents** of the replacement markers (text in between `\x01\x01` and `\x02\x02` for an inline replacement, or the rest of the string following `\x01` for a regular replacement, refers to a value (usually in the 'source' data) by path, which is to replace the marker.

The reference string is simply a slash-delimited path which provides the 'address' of the data we want to reference within the source data (leading slash is required). For example, the following regular replacement marker:

`"\u0001/message/recipient"`

would refer to the `obj['message']['recipient']` value if `obj` were used as the 'source' data for the conversion.

### Example 'source' data

Here is an example of a 'source' data structure that could be used with the above Conversion Specifier:

```
{
    "account": {
        "screen_name": "SandbenderCa", 
        "id": 1234567
    },
    "recent_tweets": [
        {"content": "This is a tweet", "tweet_id": 54321},
        {"content": "Another random post", "tweet_id": 98765}
    ]
}
```

With this structure as the 'source' data for the conversion, the following conversions would be performed:

```
"Hello there, \u0001\u0001/account/screen_name\u0002\u0002!"  -->  "Hello there, SandbenderCa!"
"\u0001/recent_tweets"                                        -->  [{"content": This is a tweet", "tweet_id': 54321},
                                                                    {"content": "Another random post", "tweet_id": 98765}]
"\u0001/account/id"                                           -->  1234567
```

### Example result

With the above sample Conversion Specifier and 'source' data, the following structure would be the result of the conversion:

```
{
    "greeting": "Hello there, SandbenderCa!",
    "payload": [
        {"content": This is a tweet", "tweet_id': 54321},
        {"content": "Another random post", "tweet_id": 98765}
    ],
    "cookie": "0123456789ABCDEF",
    "user_details": {
        "account_type": "twitter",
        "account_id": 1234567
    }
}
```

### Order of replacements (inline vs. regular)

It is important to note that inline replacement markers are processed **before** regular replacement markers. It is therefore possible to use an inline replacement marker within the reference string of another replacement marker.

For example, with the replacement marker "\u0001/dict/\u0001\u0001/key_name\u0002\u0002", and the following structure as the 'source' data:

```
{
    "key_name": "key_one",
    "dict": {"key_one": "value 1", "key_two": "value 2"}
}
```

The replacement marker would refer to the `obj['dict']['key_one']` element if `obj` were the 'source' data, and the replacement value would be `"value 1"`.

### Replacement markers referencing functions

It is also possible to reference one of a set of predefined functions with a replacement marker, instead of a path in the 'source' data.

To call a function in this way, the first character of the replacement path must be `%` instead of `/`. When this is the case, the string following the `%` is the function call, following this format:

`FUNCTION_NAME(['<arg>' [, '<arg2>' [...]]])`

Function names are always all-uppercase, and arguments to functions appear inside parentheses immediately following the function name.

Function arguments are separated by commas, and function arguments should always be passed as strings, ie: quoted with single quotes, even when their values are numeric.

For a list of available functions, see [Conversion Functions](./Conversion_functions.md).

### Nesting inline replacement markers...

... **is** allowed. This will work as expected.

For example, given the following string containing a replacement marker (JSON):

```json
"\u0001\u0001%URLENCODE_PLUS('Hello, \u0001\u0001/place\u0002\u0002!')\u0002\u0002"
```

and this "source" data for the conversion:

```json
{"place": "beautiful world"}
```

The resulting string will look like this:

```json
"""Hello%2C+beautiful+world%21"""
```

### Referencing array indices within replacement markers

In order to be able to reference a numeric array index in the 'source' data, the index must be prefixed with a single `0x01`. Otherwise, that portion of the path in question would be interpreted as a string index.

`"\u0001/my_array/0/something_important"` references `obj['my_array']['0']['something_important']` if `obj` is your 'source' data.

`"\u0001/my_array/\u00010/something_important"` references `obj['my_array'][0]['something_important']`.

(Note that the 0 index is an integer in the latter example, vs. the string '0' in the former.)

### Converting arrays in 'source' to arrays in the final structure

It will eventually happen that you wish to **convert** the **contents** of an array in your 'source' data, instead of just referencing and including an array from 'source' verbatim. This requires a special syntax:

`["\u0001", "\u0001/root/my_array", {"this is an array conversion specifier": "\u0001//id", "stamp": "\u0001/timestamp"}]`

To include **and** convert an array from 'source', you specify an array whose first element is a string consisting of a single `0x01` character, the second element is a reference to the array in 'source' you wish to convert, and the third element is the conversion you wish to apply to **each** element of the array in 'source'.

If the above sample array reference is used as a conversion specifier with the following as 'source' data:

```
{
    "root": {
        "my_array": [
            {"id": 23456, "name": "someone"},
            {"id": 65432, "name": "someone else entirely"}
        ],
    "timestamp": 1234567890
}
```

The conversion will result in the following structure:

```
[
    {"this is an array conversion specifier": 23456, "stamp": 1234567890},
    {"this is an array conversion specifier": 65432, "stamp": 1234567890},
]
```

### Local vs. Root references

In the example above, you may have noticed that the path for the 'id' element starts with **two** slashes (`//`), while the path for 'timestamp' starts with the usual *single* slash. This is significant - the double-slashes indicates a reference relative to the 'local root'... inside an array reference, the 'local root' is the current element of the array we're referencing. Conversely, the single slash prefix on a path indicates that the path is 'absolute' (or a 'root' reference, as opposed to 'local')... ie: this path is relative to the root of the whole 'source' data structure.

It is **important to note** that you can nest array references - ie: inside the conversion specifier for each element of the array you're referencing, you can have further array references.

**However**... the path of an array reference **always** refers to the 'local' root, ie: relative to the current array element, regardless of the number of slashes it begins with... when used as an array reference, `"/path"` and `"//path"` **both** refer to the 'path' value in the current array element being processed, even though the former only has a single slash and would normally be to a 'root' reference.

At the top level of a conversion specifier, ie: without already having entered an array reference, the 'local root' is implied to be the same as the actual root of 'source' - that is, the path of a non-nested array reference **always** refers to the root of a 'source' structure, while a nested array reference is **always** relative to the current array element being converted.

Because of this, you can process per-element conversions where the 'source' structure is itself an array, however you cannot create any 'recursion' while processing a conversion - an array reference **can only point to a value which is _contained in_ the _current root_**.

##### [Next Topic: Conversion Functions](./Conversion_Functions.md)

##### [Previous Topic: Links](../Links.md)

### [Back to Table of Contents](../README.md)

