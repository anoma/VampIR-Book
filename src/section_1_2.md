# Using Vamp-IR


A very basic example of a Vamp-IR program is the following;

```haskell
// This is a comment!
def x = 10;

x = 10;
```

Here, one can see the two main top-level commands available in Vamp-IR. That first line defines a constant, `x`, to which the value `10` is assigned. Names must start with a letter or an underline, and may contain any number of letters, numerals, or underlines after. The second line is an equation that is expected to be true. Every arithmetic circuit is interpreted as a proposition. Specifically, it is the proposition corresponding to the truth of all the equations appearing in the file which generated it. In this case, the compiled circuit will merely check that `10 = 10`. Notice that every line must end in a semicolon. Vamp-IR does not generally care about white space or newlines (beyond spaces separating individual tokens); this example would be interpreted the same if all newlines were removed and everything was put on a single line.

To compile this circuit, of course, Vamp-IR needs to be up and running. First clone Vamp-IR's directory.

```bash
$ git clone git@github.com:anoma/vamp-ir
```

Vamp-IR is implemented in Rust and can easily be compiled from source using Rust Cargo.

```bash
$ cd vamp-ir
$ cargo build
```

This will create the Vamp-IR binary at `/target/debug/vamp-ir` within the main Vamp-IR directory.

Vamp-IR does not possess any cryptographic capabilities on its own. This means that some specific parameters, such as the field size, cannot be determined by Vamp-IR, but are instead decided at compile time. To compile this circuit, a target must first be chosen. For this example, I will choose PLONK.

For the sake of this example, I will assume that the lines in our example program are saved into a file called `ex1.pir`, stored within a new folder called `examples` within the main Vamp-IR directory. 

```bash
$ mkdir examples
$ printf "def x = 10;\n\nx = 10;">examples/ex1.pir
```

Notice the file ends with `.pir`, the standard extension for Vamp-IR files. To compile this into a PLONK circuit, one must first set up public parameters.

```bash
$ target/debug/vamp-ir plonk setup -o examples/params.pp

> * Setting up public parameters...
> * Public parameter setup success!
```

This will create the file `params.pp` within our `examples` directory. The `-o` argument indicates an output file and is equivalent to `--output`. One can now create the circuit associated with our file.

```bash
$ target/debug/vamp-ir plonk compile -u examples/params.pp \
                                     -s examples/ex1.pir \
                                     -o examples/circuit.plonk

> * Compiling constraints...
> ** Inferring types...
> x[2]: int
> * Reading public parameters...
> * Synthesizing arithmetic circuit...
> * Serializing circuit to storage...
> * Constraint compilation success!
```

This will create our compiled circuit in the file `circuit.plonk` within the `examples` directory. The `-u` argument indicates a universal parameter file and is equivalent to `--universal-params`. The `-s` argument indicates a source file and is equivalent to `--source`.

Notice that types for defined expressions are inferred during compilation. Vamp-IR has a simple type system that is mostly implicit. This will be explained in more detail later on. In this simple example, `x` is inferred to be an `int`, that is, an integer that will be interpreted as a field element during compilation.

A zero-knowledge proof of circuit correctness can now be synthesized.

```bash
$ target/debug/vamp-ir plonk prove -u examples/params.pp \
                                   -c examples/circuit.plonk \
                                   -o examples/proof.plonk

> * Reading arithmetic circuit...
> * Soliciting circuit witnesses...
> * Reading public parameters...
> * Proving knowledge of witnesses...
> * Serializing proof to storage...
> * Proof generation success!
```

This will create our compiled proof in the file `proof.plonk` within the `examples` directory. The `-c` argument indicates a circuit file and is equivalent to `--circuit`.

The last thing one may want to do is verify the circuit.

```bash
$ target/debug/vamp-ir plonk verify -u examples/params.pp \
                                    -c examples/circuit.plonk \
                                    -p examples/proof.plonk

> * Reading arithmetic circuit...
> * Reading zero-knowledge proof...
> * Public inputs:
> * Reading public parameters...
> * Verifying proof validity...
> * Zero-knowledge proof is valid
```

This will verify the proof just created. In this case, we've just verified a zero-knowledge proof that 10 = 10. The `-p` argument indicates a proof file and is equivalent to `--proof`. 

Other than `help`, every available Vamp-IR command has now been used; `setup`, `compile`, `prove`, and `verify`. These commands define all current methods for interacting with Vamp-IR. It is a very simple system.


