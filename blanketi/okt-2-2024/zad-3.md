# Medjukod

```

IMC<list[0]>
IMC<list[1]>
...
IMC<list[n-1]>

IMC<alt>

exit:

```

# Kod

```java

class SelectStatement extends Statement {
    public ArrayList<Alternative> list;
    public Alternative alt;

    ctor...

    public void translate(BufferedWriter out) {
        String exitLab = ASTNode.genLab();

        for (int i = 0; i < list.Size(); i++) {
            Alternative curr = list[i];
            
            curr.setExitLab(exitLab);                
            curr.translate(out);
        }

        out.write(exitLab + ":\n");
    }
}

class Alternative extends Statement {
    public Expression exp;
    public Statement stat;
    public String exitLab;

    ctor...

    void setExitLab(String exitLab) {
        this.exitLab = exitLab;
    }


    public void translate(BufferedWriter out ) {
        exp.translate(out);
        exp.genLoad("R1", out);
        if (exp.getResult() != 1)
            stat.translate(out);
        out.write("JumpIfNotZero R1" + exitLab + "\n");
    }
}

```