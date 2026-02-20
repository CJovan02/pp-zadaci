# Zadatak
ForStatement -> FOR ID = Expression1 TO Expression2 BY CONST DO Statement

# Medjukod

```

// u R1 je brojac petlje
// u R2 je gornja granica
// u R3 je CONST

IMC<startExp>
IMC<endExp>

Load_Mem R1, RESULT <startExp>
Store R1, id.name

petlja:
    Load R2, RESULT <endExp>
    // if start > end => exit
    Compare_Greater R1, R2
    JumpIfNotZero kraj

    IMC<stat>

    // id = id + CONST
    Load_Mem R1, id.name
    Load_Const R3, CONST
    Add R1, R3
    Store R1, id.name
    Jump petlja

kraj:

```

# Kod

```java

class ForStatement extends Statement {
    public Expression startExp;
    public Expression endExp;
    public Statement stat;
    // id je brojac petlje
    public Variable id;

    ctor...

    public void translate(BufferedWriter out) throws IOException {
        String petlja = ASTNode.genLab();
        String kraj = ASTNode.genLab();

        startExp.genLoad("R1", out);
        out.write("Store R1, " + id.name);

        out.write(petlja + ":\n");
        endExp.genLoad("R2", out);
        out.write("Compare_Greater R1, R2\n");
        out.write("JumpIfNotZero" + kraj + "\n");

        stat.translate(out);

        out.write("Load_Mem R1, " id.name + "\n");
        out.write("Load_Const R3, CONST\n");
        out.write("R1, R3");
        out.write("Store R1, " + id.name + "\n");
        out.write("Jump " + petlja + "\n");

        out.write(kraj + ":\n");
    }
}

```