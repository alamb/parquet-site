---
title: "Parquet format versions"
linkTitle: "Versions"
weight: 9
---

This page describes how features are added to the [Parquet format
specification](https://github.com/apache/parquet-format) and how those features
affect compatibility between readers and writers. See the [Implementation status](../implementationstatus/) page for which
specific implementations (arrow, parquet-java, arrow-rs, etc.) support each
feature.

*Note*: If you find out-of-date information, please help us improve the accuracy
of this page by opening an issue or submitting a pull request.

## Backwards compatible features

Some features are backwards compatible, which means older readers can still
**read the file**, though they may have a *degraded experience* — typically they
ignore new information rather than failing. Examples:

* **Bloom filters**: a reader that does not understand Bloom filters
  simply ignores metadata that could be used to prune row groups,
  but still reads the data correctly.
* **Logical type annotations** such as `VARIANT`: an older reader still sees 
  the underlying physical column (e.g. `BYTE_ARRAY`) and can read the raw bytes, 
  but does not interpret the values according to the new logical type.

## Backwards incompatible features

Some features are backwards **incompatible** — older software **cannot read
the data** if they are used. Examples:

* **New encodings** (such as the `DELTA_*` encodings, `BYTE_STREAM_SPLIT`, and
  `RLE_DICTIONARY`): a reader that does not implement them cannot decode the
  column values.
* **Data Page V2 headers**: a reader that only understands `DataPageHeader` cannot
  parse pages written with `DataPageHeaderV2`.

## `FileMetadata` version field

Each Parquet file has a `version` field in the [`thrift FileMetadata`], which
defines the features the file may contain and thus a reader **must** implement
to successfully read the file.  

**Note**: Many writers have chosen to set the version field to `1` for all files,
including those that use features introduced in format version 2.0, which has
caused confusion and interoperability issues. 

## `parquet-format` release versions

The Thrift definition is released independently of software implementations such
as parquet-java or parquet-rs, following the Apache release process. Version
numbers follow [semantic versioning]:

1. The major version of parquet-format corresponds to the 
   [`thrift FileMetadata`] field.
2. Minor releases (e.g. `2.10.0` to `2.11.0`) of parquet-format may add new
   backwards compatible features, but never breaking ones.

## Adding new features

New features are added to the specification through a process of discussion and
voting on the [parquet dev mailing list]. The full process is described [here]. 
When a feature is approved, it is added to the specification and included in 
the next release of parquet-format.

[parquet dev mailing list]: https://lists.apache.org/list.html?dev@parquet.apache.org
[semantic versioning]: https://semver.org/
[parquet-format release]: https://github.com/apache/parquet-format/releases
[`thrift FileMetadata`]: https://github.com/apache/parquet-format/blob/c42c2cb4ecfccb38153375e24b702a82fd763cc0/src/main/thrift/parquet.thrift#L1365-L1373
[parquet dev mailing list]: https://lists.apache.org/list.html?dev@parquet.apache.org
[here]: https://github.com/apache/parquet-format/blob/master/CONTRIBUTING.md#additionschanges-to-the-format

## Backwards incompatible features by version

The table below lists the **backwards incompatible** features and the Parquet
format version in which each became available.

* **V1**: the original Parquet format (version 1.0).
* **V2**: Parquet format version 2.0.
* **V3**: a potential future Parquet format version 3.0.
* **Not yet released**: Approved or proposed additions to the specification that
  are not yet part of a released format version.

The **Notes** column links to the Apache `[VOTE]` thread that adopted the
feature.

| Feature                                  | V1 | V2 | V3 | Not yet released | Released in | Source | Notes |
|------------------------------------------| ---- | ---- | ---- | ------------------ | ----------------------------- | --- | ------------------------- |
| BOOLEAN                                  | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| INT32                                    | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| INT64                                    | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| INT96 (deprecated)                       | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| FLOAT                                    | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| DOUBLE                                   | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| BYTE_ARRAY                               | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| FIXED_LEN_BYTE_ARRAY                     | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| Data Page V1                             | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| Data Page V2                             |  | ✅ | ✅ |  | [2.0.0] | [1.0.0..2.0.0] |  |
| [PLAIN]                                  | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| [PLAIN_DICTIONARY]                       | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| [RLE]                                    | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| BIT_PACKED (deprecated)                  | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| [RLE_DICTIONARY]                         |  | ✅ | ✅ |  | [2.0.0] | [1.0.0..2.0.0] |  |
| [DELTA_BINARY_PACKED]                    |  | ✅ | ✅ |  | [2.0.0] | [1.0.0..2.0.0] |  |
| [DELTA_LENGTH_BYTE_ARRAY]                |  | ✅ | ✅ |  | [2.0.0] | [1.0.0..2.0.0] |  |
| [DELTA_BYTE_ARRAY]                       |  | ✅ | ✅ |  | [2.0.0] | [1.0.0..2.0.0] |  |
| [BYTE_STREAM_SPLIT]                      |  | ✅ | ✅ |  | [2.8.0] | [2.7.0..2.8.0] | [[Approved 2019-12-03]] |
| BYTE_STREAM_SPLIT<br/>(Additional Types) |  | ✅ | ✅ |  | [2.11.0] | [2.10.0..2.11.0] | [[Approved 2024-03-18]] |
| Adaptive Floating Point (ALP)            |  |  | ✅ | ✅ | not yet released |  |  |
| Fast Static String Table (FSST)          |  |  | ✅ | ✅ | not yet released |  |  |
| FastLanes integer encoding               |  |  | ✅ | ✅ | not yet released |  |  |
| Remove path_in_schema                    |  |  | ✅ | ✅ | not yet released |  |  |
| UNCOMPRESSED                             | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| SNAPPY                                   | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| GZIP                                     | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| LZO                                      | ✅ | ✅ | ✅ |  | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| BROTLI                                   |  | ✅ | ✅ |  | [2.4.0] | [2.3.1..2.4.0] |  |
| LZ4 (deprecated)                         |  | ✅ | ✅ |  | [2.4.0] | [2.3.1..2.4.0] |  |
| LZ4_RAW                                  |  | ✅ | ✅ |  | [2.9.0] | [2.8.0..2.9.0] |  |
| ZSTD                                     |  | ✅ | ✅ |  | [2.4.0] | [2.3.1..2.4.0] |  |
| [Modular encryption]                     |  | ✅ | ✅ |  | [2.7.0] | [2.6.0..2.7.0] | [[Approved 2019-01-16]] |


> **Note:** Compression codecs and modular encryption are not strictly gated by
> the file's `version` field — a reader needs support for the specific codec or
> for encryption regardless of page version. 

## Backwards compatible additions

These features can be added to a file while still allowing older readers to read
it (with a degraded experience). 

| Feature | Released in | Source | Notes |
| ------------------------------------------- | ----------------------------- | --- | ------------------------- |
| [xxHash-based bloom filters] | [2.7.0] | [2.6.0..2.7.0] | [[Approved 2019-09-09]] |
| Bloom filter length | [2.10.0] | [2.9.0..2.10.0] |  |
| [Page index] | [2.4.0] | [2.3.1..2.4.0] |  |
| Page CRC32 checksum | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| Size statistics | [2.10.0] | [2.9.0..2.10.0] | [[Approved 2023-11-14]] |
| [Geospatial statistics] | [2.11.0] | [2.10.0..2.11.0] | [[Approved 2025-02-09]] |
| [Binary protocol extensions] | [2.11.0] | [2.10.0..2.11.0] | [[Approved 2024-09-06]] |
| IEEE 754 total order and NaN counts | not yet released | [#514] | [[Approved 2026-05-26]] |
| STRING | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| ENUM | [2.0.0] | [1.0.0..2.0.0] |  |
| UUID | [2.6.0] | [2.5.0..2.6.0] |  |
| Signed and unsigned integer logical types | [2.2.0] | [2.1.0..2.2.0] |  |
| DECIMAL (INT32) | [2.1.0] | [2.0.0..2.1.0] |  |
| DECIMAL (INT64) | [2.1.0] | [2.0.0..2.1.0] |  |
| DECIMAL (BYTE_ARRAY) | [2.1.0] | [2.0.0..2.1.0] |  |
| DECIMAL (FIXED_LEN_BYTE_ARRAY) | [2.1.0] | [2.0.0..2.1.0] |  |
| [FLOAT16] | [2.10.0] | [2.9.0..2.10.0] | [[Approved 2023-10-13]] |
| DATE | [2.2.0] | [2.1.0..2.2.0] |  |
| TIME (INT32) | [2.2.0] | [2.1.0..2.2.0] |  |
| TIME (INT64) | [2.4.0] | [2.3.1..2.4.0] |  |
| TIMESTAMP (INT64) | [2.2.0] | [2.1.0..2.2.0] |  |
| INTERVAL | [2.2.0] | [2.1.0..2.2.0] |  |
| JSON | [2.2.0] | [2.1.0..2.2.0] |  |
| BSON | [2.2.0] | [2.1.0..2.2.0] |  |
| [VARIANT] | [2.12.0] | [2.11.0..2.12.0] | [[Approved 2025-08-24]] |
| [Variant shredding] | [2.12.0] | [2.11.0..2.12.0] | [[Approved 2025-08-24]] |
| [GEOMETRY] | [2.11.0] | [2.10.0..2.11.0] | [[Approved 2025-02-09]] |
| [GEOGRAPHY] | [2.11.0] | [2.10.0..2.11.0] | [[Approved 2025-02-09]] |
| LIST | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| MAP | [1.0.0] | [1.0.0][tree-1.0.0] |  |
| UNKNOWN (always null) | [2.4.0] | [2.3.1..2.4.0] |  |

[PLAIN]: https://github.com/apache/parquet-format/blob/master/Encodings.md#plain-plain--0
[PLAIN_DICTIONARY]: https://github.com/apache/parquet-format/blob/master/Encodings.md#dictionary-encoding-plain_dictionary--2-and-rle_dictionary--8
[RLE]: https://github.com/apache/parquet-format/blob/master/Encodings.md#run-length-encoding--bit-packing-hybrid-rle--3
[RLE_DICTIONARY]: https://github.com/apache/parquet-format/blob/master/Encodings.md#dictionary-encoding-plain_dictionary--2-and-rle_dictionary--8
[DELTA_BINARY_PACKED]: https://github.com/apache/parquet-format/blob/master/Encodings.md#delta-encoding-delta_binary_packed--5
[DELTA_LENGTH_BYTE_ARRAY]: https://github.com/apache/parquet-format/blob/master/Encodings.md#delta-length-byte-array-delta_length_byte_array--6
[DELTA_BYTE_ARRAY]: https://github.com/apache/parquet-format/blob/master/Encodings.md#delta-strings-delta_byte_array--7
[BYTE_STREAM_SPLIT]: https://github.com/apache/parquet-format/blob/master/Encodings.md#byte-stream-split-byte_stream_split--9
[Modular encryption]: https://github.com/apache/parquet-format/blob/master/Encryption.md
[xxHash-based bloom filters]: https://github.com/apache/parquet-format/blob/master/BloomFilter.md
[Page index]: https://github.com/apache/parquet-format/blob/master/PageIndex.md
[FLOAT16]: https://github.com/apache/parquet-format/blob/master/LogicalTypes.md
[VARIANT]: https://github.com/apache/parquet-format/blob/master/VariantEncoding.md
[GEOMETRY]: https://github.com/apache/parquet-format/blob/master/Geospatial.md#logical-types
[GEOGRAPHY]: https://github.com/apache/parquet-format/blob/master/Geospatial.md#logical-types

[1.0.0]: https://github.com/apache/parquet-format/releases/tag/parquet-format-1.0.0
[2.0.0]: https://github.com/apache/parquet-format/releases/tag/parquet-format-2.0.0
[2.8.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.8.0
[2.11.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.11.0
[2.4.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.4.0
[2.9.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.9.0
[2.7.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.7.0
[2.10.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.10.0
[2.12.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.12.0

[Approved 2019-12-03]: https://lists.apache.org/thread/xs5qt2odm299pxgqb22mty2csc1so5yr
[Approved 2024-03-18]: https://lists.apache.org/thread/nlsj0ftxy7y4ov1678rgy5zc7dmogg6q
[Approved 2019-01-16]: https://lists.apache.org/thread/l8zcwnbrnhjh3w2k1lyb0v6ct5lnzr0h
[Approved 2019-09-09]: https://lists.apache.org/thread/ktdx1xp0d2gjfgkcvd29zxvt3cgg88bo
[Approved 2023-11-14]: https://lists.apache.org/thread/wgobz41mfldbhqpg9q4mdwypghg2cxg2
[Approved 2023-10-13]: https://lists.apache.org/thread/gyvqcx9ssxkjlrwogqwy7n4z6ofdm871
[Approved 2025-08-24]: https://lists.apache.org/thread/obn1yzhgm5zlznwrdpg7f66mswwooxw7
[Approved 2025-02-09]: https://lists.apache.org/thread/s6s714c98cn9gg22mnk5nsn7xymym8xo

[1.0.0..2.0.0]: https://github.com/apache/parquet-format/compare/parquet-format-1.0.0...parquet-format-2.0.0
[2.7.0..2.8.0]: https://github.com/apache/parquet-format/compare/apache-parquet-format-2.7.0...apache-parquet-format-2.8.0
[2.10.0..2.11.0]: https://github.com/apache/parquet-format/compare/apache-parquet-format-2.10.0...apache-parquet-format-2.11.0
[2.3.1..2.4.0]: https://github.com/apache/parquet-format/compare/apache-parquet-format-2.3.1...apache-parquet-format-2.4.0
[2.8.0..2.9.0]: https://github.com/apache/parquet-format/compare/apache-parquet-format-2.8.0...apache-parquet-format-2.9.0
[2.6.0..2.7.0]: https://github.com/apache/parquet-format/compare/apache-parquet-format-2.6.0...apache-parquet-format-2.7.0
[2.9.0..2.10.0]: https://github.com/apache/parquet-format/compare/apache-parquet-format-2.9.0...apache-parquet-format-2.10.0
[2.11.0..2.12.0]: https://github.com/apache/parquet-format/compare/apache-parquet-format-2.11.0...apache-parquet-format-2.12.0

[2.1.0]: https://github.com/apache/parquet-format/releases/tag/parquet-format-2.1.0
[2.2.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.2.0
[2.6.0]: https://github.com/apache/parquet-format/releases/tag/apache-parquet-format-2.6.0
[2.0.0..2.1.0]: https://github.com/apache/parquet-format/compare/parquet-format-2.0.0...parquet-format-2.1.0
[2.1.0..2.2.0]: https://github.com/apache/parquet-format/compare/parquet-format-2.1.0...apache-parquet-format-2.2.0
[2.5.0..2.6.0]: https://github.com/apache/parquet-format/compare/apache-parquet-format-2.5.0...apache-parquet-format-2.6.0

[tree-1.0.0]: https://github.com/apache/parquet-format/tree/parquet-format-1.0.0

[Variant shredding]: https://github.com/apache/parquet-format/blob/master/VariantShredding.md
[Geospatial statistics]: https://github.com/apache/parquet-format/blob/master/Geospatial.md
[Binary protocol extensions]: https://github.com/apache/parquet-format/blob/master/BinaryProtocolExtensions.md
[#514]: https://github.com/apache/parquet-format/pull/514
[Approved 2024-09-06]: https://lists.apache.org/thread/x3472kldrq5kjnld9ztj1jozz25f40hg
[Approved 2026-05-26]: https://lists.apache.org/thread/h0k0hqo0sojqphnbnrkp8b0gmwdzq9on
