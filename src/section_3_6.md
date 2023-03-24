# Soliciting Witnesses from File


Entering witness values at the command line can be annoying, and placing generated witnesses into the circuit is not always prudent. To avoid this, one may solicit values from a `.json` file. Take the above program as an example along with the following file;

```javascript
{ "x": "5",
  "y": "2",
  "z": "27",
  "h": "28" }
```

which will be called `pub.json`. During proving, we may issue it via the `-i` (equivalently, `--inputs`) argument.

```bash
$ ./target/debug/vamp-ir prove -i examples/pub.json \
                               -u if/params.pp \
                               -c if/circuit.plonk \
                               -o if/proof.plonk

> * Reading arithmetic circuit...
> * Reading inputs from file: tests/pubt.json
> [...]
> * Proof generation success!
```

This is semantically equivalent to issuing these arguments at the command line. This procedure does not distinguish between public and private variables.

