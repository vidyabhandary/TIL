# Types of UUIDs

There are 8 versions of UUIDs (v1 through v8), each with different generation methods and use cases.

• UUID v1: Timestamp + counter + MAC address
• UUID v2: Reserved for security IDs (details unknown)
• UUID v3: MD5 hash of provided data (e.g., DNS, URLs)
• UUID v4: Random data (most common)
• UUID v5: SHA1 hash of provided data (e.g., DNS, URLs)
• UUID v6: Like v1, but sortable by creation time
• UUID v7: Timestamp + random data
• UUID v8: Custom implementation

Key points:
• UUID v4: Generated from random data. Good default choice for most applications.
• UUID v7: Created from timestamp and random data. Useful for sortable IDs (e.g., database keys).
• When choosing a UUID version, you'll usually be picking between v4 and v7.

References:

- [8 versions of UUID and when to use them](https://ntietz.com/blog/til-uses-for-the-different-uuid-versions)
