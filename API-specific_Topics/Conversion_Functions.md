# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Conversion Specifiers](./Conversion_Specifiers.md)

##### [Next Topic: API Reference](../../../tree/master/API_Reference)

## Conversion Functions

#### TIMESTAMP

Produces a unix timestamp.

Arguments: None (Any arguments passed will be ignored)

Return Value: The current unix timestamp (seconds since the Epoch), with microsecond precision (**up to** 6 decimal places), as a numeric, decimal value.

#### QUOTE

URL encodes a string so that special characters are replaced with %xx hex codes. Letters, digits, dashes, underscores, periods, and slashes (`/`) are not quoted.

Arguments: The string to be URL-encoded.

Return Value: The url-encoded string.

#### QUOTE_PLUS

Similar to QUOTE, with two differences:
1. Spaces are not encoded as `%20` but by `+` instead (`+`'s in the string are encoded as %2B).
1. Slashes *are* encoded (as `%2F`).

Arguments: The string to be URL-encoded.

Return Value: The url-encoded string.

##### [Next Topic: API Reference](../../../tree/master/API_Reference)

##### [Previous Topic: Conversion Specifiers](./Conversion_Specifiers.md)

### [Back to Table of Contents](../README.md)

