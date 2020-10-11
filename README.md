# Metal

Metal is a collection of interoperable component interfaces. "Metallic" apps are those built from these components.

An "alloy" is an integration between two components, linking their interfaces.

## Design

### Arr.ai

Arr.ai is a data representation and transformation language. It's very powerful for that purpose, but not for other common needs like persistence and long-running processes. As such, arr.ai-based Metal components model these behaviours as data.

The simplest example in the core of arr.ai is file and directory output. Arr.ai programs typically print output to `stdout`, which a caller may redirect to another program or file. However if an arr.ai program needs to produce more than one file, this is not easily managed by the caller. As such, arr.ai provides the `--out` flag, which supports various mode prefixes. With `--out=dir:path/to/dir`, outputting a dict of [dicts of] strings will be interpreted as outputting a file system, writing those strings as files named by the dict keys within `path/to/dir`.

This metaphor of side-effects-as-output-data can be extended naturally (in a wrapper program if necessary to avoid bloating the arr.ai API). For example:

| `out` pattern | Types | Side-effect |
| --- | --- | --- |
| `dir:path`                |`{file <: string: content <: string, ...}` | write `content` to `path/file` |
| `dir:path`                | `{dir <: string: {file <: string: content <: string, ...}, ...}` | write `content` to `path/dir/file`. |
| `db.insert:conn/table`    | `{|cols, ...| (vals), ...}` | insert `vals` into `table` of DB with `conn`ection string. |
| `db.insert:conn`          | `{table <: string: {|cols, ...| (vals), ...}, ...}` | insert `vals` into corresponding `table` in DB with `conn`ection string. |
| `db.update:conn/table`    | `{|cols, ...| (vals), ...}` | update records in `table` to `vals` where PKs match in DB with `conn`ection string. Dict option as above. |
| `db.delete:conn/table`    | `{|cols, ...| (vals), ...}` | delete records from `table` where `vals` match in DB with `conn`ection string. Dict option as above. |
| `db.create:conn/table`    | `(name <: string, columns <: []column, ...)` | creates `table` with output as config in DB with `conn`ection string. Dict option as above. |
| `serve:url`               | `(protocol <: string, router <: \req, ...)` | starts a server for `protocol` using `url` to configure `address` and `port`. Once started, inbound requests are mapped to responses by function `router`. |

Like Terraform, this API is a kind of declarative infrastructure. Unlike Terraform, it is not intended to describe the infrastructure in minute detail. Instead it is a language for expressing constraints in terms of what should exist, from which a planner (like Mason) can select compliant concrete implementations. Less infrastructure-as-code, more design-as-code.

### Alloys

TODO
