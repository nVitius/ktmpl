# ktmpl

**ktmpl** is a tool for processing [Kubernetes](http://kubernetes.io/) manifest templates.
It is a very simple client-side implementation of the [Templates + Parameterization proposal](https://github.com/kubernetes/kubernetes/blob/master/docs/proposals/templates.md).

## Synopsis

```
ktmpl 0.4.0
Produces a Kubernetes manifest from a parameterized template

USAGE:
    ktmpl [OPTIONS] <template>

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

OPTIONS:
    -b, --base64-parameter <NAME> <VALUE>
        Same as --parameter, but for values already encoded in Base64
    -p, --parameter <NAME> <VALUE>
        Supplies a value for the named parameter

ARGS:
    <template>    Path to the template file to be processed
```

## Usage

Run `ktmpl TEMPLATE` where TEMPLATE is a path to a Kubernetes manifest template in YAML format.
The included [example.yml](example.yml) is a working example template.

To provide values for template parameters, use the `--parameter` option to supply key-value pairs.
Using the provided example.yml, this would mean:

``` bash
ktmpl example.yml --parameter MONGODB_PASSWORD secret
```

Template parameters that have default values can be overridden with the same mechanism:


``` bash
ktmpl example.yml --parameter MONGODB_USER carl --parameter MONGODB_PASSWORD secret
```

The processed template will be output to stdout, suitable for piping into a `kubectl` command:

``` bash
ktmpl example.yml --parameter MONGODB_PASSWORD=password | kubectl create -f -
```

If a parameter's `parameterType` is `base64`, the value passed for that parameter via `--parameter` will be Base64-encoded before being inserted into the template.
If the value passed via `--parameter` is already Base64-encoded, and hence encoding it again would be an error, use the `--base64-parameter` option instead:

``` bash
ktmpl example.yml --base64-parameter MONGODB_PASSWORD c2VjcmV0 | kubectl create -f -
```

This can be handy when working with Kubernetes secrets, or anywhere else binary or opaque data is needed.

## Installing ktmpl

### Cargo

```
cargo install ktmpl
```

### Precompiled binary

[Releases](https://github.com/InQuicker/ktmpl/releases)

### Docker

```
docker pull inquicker/ktmpl
```

### Building from source

1. Install the appropriate version of [Rust](https://www.rust-lang.org/) for your system.
2. Run `git clone git@github.com:InQuicker/ktmpl.git`.
3. Inside the freshly cloned repository, run `cargo install --path .`.

Make sure Cargo's bin directory is added to your PATH environment variable.

## Development

To package the current release for distribution, update `TAG` in the Makefile and then run `make`.
Release artifacts will be written to the `dist` directory.
Your GPG secret key will be required to sign `sha256sums.txt`.

Docker images for `inquicker/ktmpl` and `inquicker/ktmpl:$TAG` will be created, but you must push them manually.
`cargo publish` must be run manually to release to crates.io.

## Legal

ktmpl is released under the MIT license. See `LICENSE` for details.
