# lhef

A library for interacting with files in the Les Houches Event File (LHEF) format.

Currently, lhef only supports reading via the `Reader`
struct. Information about the generator run is provided in a `HEPRUP`
object and each event is stored in a `HEPEUP` object. These structs
correspond to the Fortan common blocks of the same names in the [original proposal](https://arxiv.org/abs/hep-ph/0109068v1), but
contain extra `info` fields corresponding to the "optional information"
specified in the LHEF standard.

As of now, only [version 1.0](https://arxiv.org/abs/hep-ph/0609017) of
the LHEF format</a> is fully supported. Files in [version 2.0](http://www.lpthe.jussieu.fr/LesHouches09Wiki/index.php/LHEF_for_Matching)
and [3.0](https://phystev.cnrs.fr/wiki/2013:groups:tools:lhef3) are
parsed exactly like for version 1.0. This means that the additional XML
tags have to be extracted manually from the `info` fields of the
`HEPRUP` and `HEPEUP` objects.

# Usage

Add this to your Cargo.toml:

```toml
[dependencies]
lhef = "0.1.0"
```

and this to your crate root:
```rust
extern crate lhef;
```

# Examples

```rust,no_run
extern crate lhef;
use lhef::Reader;
use std::fs::File;
use std::io::BufReader;

let input = BufReader::new(File::open("events.lhe")?);

let mut reader = Reader::new(input)?;

println!("Information in header:\n{}", reader.header());
println!("Generator run information:\n{:?}", reader.heprup());

let event = reader.event()?;
if let Some(event) = event {
    println!("Found an event: {:?}", event);
}
```

# Notes on (non-)compliance

- The LHEF standard mandates at least one event in each file. This is
  not enforced.
- Contrary to the standard, attributes in the `<init>`, `<event>`, and
  `<header>` tags are not allowed.
