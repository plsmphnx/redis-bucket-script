# redis-bucket-script

A Redis-backed rate limiter, based on the
[leaky-bucket algorithm](https://en.wikipedia.org/wiki/Leaky_bucket#As_a_meter).
This repository provides the Lua script for use with EVAL, to allow libraries
across multiple languages to utilize the same script and data format. It was
extracted from [redis-bucket](https://github.com/plsmphnx/redis-bucket), which
can be referenced for a more detailed description and validation of correctness.

## Requirements

In order to make changes to this script, [Node.js](https://nodejs.org/) and
[npm](https://npmjs.com/) must be installed to generate the minified script and
SHA1 hashes used by EVALSHA.

## API

The script takes a single key, along with arguments in the following order:

```
cost flow burst [flow burst]*
```

`cost` is the cost of the current action. It is followed by `flow`/`burst` pairs
defining the buckets being limited against; `flow` is specified in cost per
second, and `burst` in total cost.

Buckets are stored in the same order as they are provided, so this ordering must
be consistent. The recommended ordering is one of strictly increasing `flow` and
strictly decreasing `burst`. This ordering also filters out any buckets which
are larger than any other buckets in both values, which makes them superfluous
(since the smaller limit will always be more restrictive).

The script returns an array with the following values:

```
allowed, value, index
```

`allowed` is either 1 (allow) or 0 (deny). `value` is a stringified number
representing either the remaining free capacity (if allowed) or the running
denied capacity (if denied). `index` is the numeric index of the bucket with the
lowest remaining capacity (which will be the cause of the limiting if denied).

## Contributing

This project has adopted the
[Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the
[Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any
additional questions or comments.
