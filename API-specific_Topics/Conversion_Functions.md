# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Conversion Specifiers](./Conversion_Specifiers.md)

##### [Next Topic: API Reference](../../../tree/master/API_Reference)

## Conversion Functions

#### TIMESTAMP

Produces a unix timestamp.

Arguments:

- None (Any arguments passed will be ignored)

Return Value: The current unix timestamp (seconds since the Epoch), with microsecond precision (**up to** 6 decimal places), as a numeric, decimal value.

#### TIMESTAMP_FORMAT

Returns a date/time string formatted according to the specified format string.

Arguments:
1. The format string - see https://docs.python.org/2/library/datetime.html#strftime-strptime-behavior for information on how to construct format strings - Kragle uses the 1999 C standard date/time format codes.
1. (Optional) The timestamp to format. If omitted, the current timestamp will be used.

#### URLENCODE

URL encodes a string so that special characters are replaced with %xx hex codes. Letters, digits, dashes, underscores, periods, and slashes (`/`) are not quoted.

Arguments:

1. The string to be URL-encoded.

Return Value: The url-encoded string.

#### URLENCODE_PLUS

Similar to URLENCODE , with two differences:
1. Spaces are not encoded as `%20` but by `+` instead (`+`'s in the string are encoded as %2B).
1. Slashes *are* encoded (as `%2F`).

Arguments:

1. The string to be URL-encoded.

Return Value: The url-encoded string.

#### SPLIT_ON_CHAR

Split a string on a given character, returning an array of strings.

Arguments:

1. The string to split.
1. The character on which to split. If omitted, an array with a single element (the original string) will be returned.

Return Value: The array of strings derived from splitting the original string.

#### ADD

Add two (or more) numbers.

Arguments:

1. The first number.
1. The second number.
1. (more numbers)

Return Value: The sum of all arguments.

#### SUB

Subtract two (or more) numbers, in order.

Arguments:

1. The first number.
1. The number to subtract from the first.
1. (more numbers to subtract sequentially)

Return Value: The difference of all arguments, ie: arg1 - arg2 - ... - argn.

#### HASH_SHA256, HASH_SHA512, HASH_MD5

Calculate a hash for an input string.

Arguments:

1. The string to hash. **If no string is provided, the empty string is used.**

Return Value: The hash (either md5, sha256 or sha512 depending on which function is used) of the string, output as a hex digest string.

##### [Next Topic: API Reference](../../../tree/master/API_Reference)

##### [Previous Topic: Conversion Specifiers](./Conversion_Specifiers.md)

### [Back to Table of Contents](../README.md)

