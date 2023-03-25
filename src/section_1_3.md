# Proof Validity and Interaction

Values for variables do not need to be given upfront. If our file was instead 

```haskell
x = 10;
```

without declaring the value of `x`, Vamp-IR would interpret this unbound variable as an input needing to be specified during proof generation. If this is saved in the file `ex2.pir` and compiled to a circuit, one can see that Vamp-IR will ask for an input when it's needed.

```bash
$ printf "x = 10;">examples/ex2.pir
$ target/debug/vamp-ir plonk compile -u examples/params.pp \
                                     -s examples/ex2.pir \
                                     -o examples/circuit2.plonk

> * Compiling constraints...
> [...]
> * Constraint compilation success!

$ target/debug/vamp-ir plonk prove -u examples/params.pp \
                                   -c examples/circuit2.plonk \
                                   -o examples/proof2.plonk

> * Reading arithmetic circuit...
> * Soliciting circuit witnesses...
> ** x[2] (private): 

$ 9

> * Reading public parameters...
> [...]
> * Proof generation success!
```

It asked for the private value for `x`, to which I input `9`. This should create an invalid proof this time as 9 does not equal 10. If one tries verifying the proof, they will observe that it's invalid.

```bash
$ target/debug/vamp-ir plonk verify -u examples/params.pp \
                                    -c examples/circuit2.plonk \
                                    -p examples/proof2.plonk

> * Reading arithmetic circuit...
> [...]
> * Verifying proof validity...
> * Result from verifier: Err(ProofVerificationError)
```

As you can see, a verification error is given.

