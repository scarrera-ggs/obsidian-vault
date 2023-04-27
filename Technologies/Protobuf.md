Protocol Buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data, it is like `JSON`, `XML`, `YAML`, etc. 

Protobofu will serialize/deserialize your structure data in a faster way, this will allow you to write and read your structured data to and from a variety of data streams and using a variety of languages.

## How to define a proto message

Protocol buffers are a combination of the definition language, created in `.proto` files. As an example:

```proto
syntax = "proto3";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```


## When are Protocol Buffers not a Good Fit?
Protocol buffers do not fit all data. In particular:

-   Protocol buffers tend to assume that entire messages can be loaded into memory at once and are not larger than an object graph. For data that exceeds a few megabytes, consider a different solution; when working with larger data, you may effectively end up with several copies of the data due to serialized copies, which can cause surprising spikes in memory usage.
-   When protocol buffers are serialized, the same data can have many different binary serializations. You cannot compare two messages for equality without fully parsing them.
-   Messages are not compressed. While messages can be zipped or gzipped like any other file, special-purpose compression algorithms like the ones used by JPEG and PNG will produce much smaller files for data of the appropriate type.
-   Protocol buffer messages are less than maximally efficient in both size and speed for many scientific and engineering uses that involve large, multi-dimensional arrays of floating point numbers. For these applications, [FITS](https://en.wikipedia.org/wiki/FITS) and similar formats have less overhead.
-   Protocol buffers are not well supported in non-object-oriented languages popular in scientific computing, such as Fortran and IDL.
-   Protocol buffer messages don’t inherently self-describe their data, but they have a fully reflective schema that you can use to implement self-description. That is, you cannot fully interpret one without access to its corresponding `.proto` file.
-   Protocol buffers are not a formal standard of any organization. This makes them unsuitable for use in environments with legal or other requirements to build on top of standards.