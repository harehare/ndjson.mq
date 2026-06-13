<h1 align="center">ndjson.mq</h1>

An [NDJSON (Newline Delimited JSON)](https://ndjson.org/) / [JSON Lines](https://jsonlines.org/) parser and serializer implemented as an [mq](https://github.com/harehare/mq) module.

## Features

- Parse NDJSON strings into arrays of mq values
- Serialize arrays of mq values to NDJSON strings
- Parse individual NDJSON lines
- Ignore empty and whitespace-only lines
- Handle strings, numbers, booleans, null, arrays, and objects
- No external dependencies (uses mq's built-in JSON parser)

## Installation

Copy `ndjson.mq` to your mq module directory:

```sh
cp ndjson.mq ~/.mq/
```

Or reference it directly with `-L`:

```sh
mq -L /path/to/ndjson.mq 'import "ndjson" | ...' file.ndjson
```

### HTTP Import (no local installation needed)

If `mq` was built with the `http-import` feature, you can import directly from GitHub without any local setup:

```sh
mq -I raw 'import "github.com/harehare/ndjson.mq" | ndjson::ndjson_parse(.) | map(fn(x): x["id"];)' events.ndjson
```

Pin to a specific release with `@vX.Y.Z`:

```sh
mq -I raw 'import "github.com/harehare/ndjson.mq@v1.0.0" | ndjson::ndjson_parse(.)' events.ndjson
```

## Usage

```sh
mq -I raw 'import "ndjson" | ndjson::ndjson_parse(.) | map(fn(x): x["id"];)' data.ndjson
```

## API

### `ndjson_parse(input)`

Parses an NDJSON string and returns an array of mq values. Empty and whitespace-only lines are ignored.

### `ndjson_parse_line(line)`

Parses a single NDJSON line into a mq value. Leading and trailing whitespace is trimmed.

### `ndjson_stringify(data)`

Serializes an array of mq values to an NDJSON string (one JSON value per line).
If a single non-array value is given, it is wrapped in an array first.

## Example

Given `events.ndjson`:

```ndjson
{"id":1,"event":"login","user":"alice"}
{"id":2,"event":"purchase","user":"bob","amount":49.99}
{"id":3,"event":"logout","user":"alice"}
```

```sh
# Extract all user names
mq -I raw 'import "ndjson" | ndjson::ndjson_parse(.) | map(fn(x): x["user"];)' events.ndjson
# => ["alice", "bob", "alice"]

# Filter by event type
mq -I raw 'import "ndjson" | ndjson::ndjson_parse(.) | filter(fn(x): x["event"] == "login";)' events.ndjson
# => [{"id":1,"event":"login","user":"alice"}]

# Re-serialize after transformation
mq -I raw 'import "ndjson" | ndjson::ndjson_stringify(ndjson::ndjson_parse(.))' events.ndjson
```

## Compatibility

Requires [mq](https://github.com/harehare/mq) v0.5 or later.

## License

MIT
