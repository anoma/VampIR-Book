## Why Vamp-IR?

Vamp-IR is, at its core, a language for defining arithmetic circuits over finite fields. It's intended to be a universal intermediate language supporting many proof systems based on arithmetic circuits. Any higher-level language which intends to compile to arithmetic circuits may target Vamp-IR as an intermediate language. 

<table>
    <tr>
        <td style = "width: 75%; padding: 0; border: none">
            <p>
                The need for an intermediate language is obvious. Without an adequate intermediate, systems must support every desired proof system individually. This creates an ecosystem like that depicted in the right figure, where the total work required for interconnectedness is quadratic. This also creates more points of failure and more opportunities for support inconsistencies.
            </p>
        </td>
        <td style = "border: none">
            <img style = "height: 100%" src="../diagrams/diagram-1-1.svg">
        </td>
    </tr>
</table>
<table>
    <tr>
        <td style = "width: 25%; border: none">
            <img style = "height: 100%" src="../diagrams/diagram-1-2.svg">
        </td>
        <td style = "width: 75%; padding: 0; border: none">
            <p>
                With an adequate intermediate representation, the total amount of work necessary to fill out the ecosystem is only linear. This can be seen in the ecosystem depicted in the left figure. Languages can, instead of targeting a specific proof system, target the intermediate representation. That language would then have support for every proof system targeted by that intermediate representation.
            </p>
        </td>
    </tr>
</table>

Vamp-IR's goal is to fulfill this need by providing a minimal and expressive interface for describing the core data structure used by most modern zero-knowledge-proof systems. To that end, Vamp-IR aims to be flexible and easy to use but doesn't provide any cryptographic features of its own. It does not presuppose any particular implementation or design for a proof system. Vamp-IR files are sufficiently generic that they may even be used for applications that use arithmetic circuits but are not cryptographic in nature.
