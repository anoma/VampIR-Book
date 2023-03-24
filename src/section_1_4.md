# Choosing a Back-end

A back-end is automatically chosen based on the extensions of the generated files. If we want to generate a Halo2 circuit, we can simply ask it to make a `.halo2} file. Repeating the examples from the introduction, we could have done the following to get a Halo2 circuit.

```bash
$ target/debug/vamp-ir compile -u examples/params.pp \
                               -s examples/ex1.pir \
                               -o examples/circuit.halo2

> [...]
> * Constraint compilation success!

$ target/debug/vamp-ir prove -u examples/params.pp \
                             -c examples/circuit.halo2 \
                             -o examples/proof.halo2

> [...]
> * Proof generation success!

$ target/debug/vamp-ir verify -u examples/params.pp \
                              -c examples/circuit.halo2 \
                              -p examples/proof.halo2

> [...]
> * Zero-knowledge proof is valid
```

Currently, Vamp-IR supports the following proof systems;

1. PLONK via `.plonk`
2. Halo2 via `.halo2`

<p style="color:red;">TODO: What particularities are there with the different proof systems?</p>

<p style="color:red;">TODO: Contolling proofs, e.g. with the -m command.</p>

