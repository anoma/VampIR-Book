# Choosing a Back-end

A back-end is automatically chosen based on the first argument issued to the executable. If we want to generate a Halo2 circuit, we can simply issue `halo2` instead of `plonk`. Repeating the examples from [the introduction](section_1_2.md), we could have done the following to get a Halo2 circuit.

```bash
$ target/debug/vamp-ir halo2 compile -s examples/ex1.pir \
                                     -o examples/circuit.halo2
                                     
$ target/debug/vamp-ir halo2 compile -s examples/ex1.pir \
                                     -o examples/circuit.halo2

> [...]
> * Constraint compilation success!

$ target/debug/vamp-ir halo2 prove -c examples/circuit.halo2 \
                                   -o examples/proof.halo2

> [...]
> * Proof generation success!

$ target/debug/vamp-ir halo2 verify -c examples/circuit.halo2 \
                                    -p examples/proof.halo2

> [...]
> * Zero-knowledge proof is valid
```

Notice that there is no `-u` argument for universal parameters when compiling Halo2 circuits.

Also note that Vamp-IR does not care about the extensions to the generated files; they can be named anything of the user's choosing.

Currently, Vamp-IR supports the following proof systems;

1. PLONK via `plonk`
2. Halo2 via `halo2`

<p style="color:red;">TODO: Contolling proofs, e.g. with the -m command.</p>

