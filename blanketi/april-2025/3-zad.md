# Medjukod

```
this.result += expression.result;
this.result = this.result + expression.result;
```

```x86asm

IMC<exp>
Load_Mem R1, Result <exp>
Load_Mem R2, Result <AssignExpression>

// += -> ADD R1, R2 ||
// -= -> SUB R1, R2 ||
// *= -> MUL R1, R2 ||
// /= -> DIV R1, R2
IMC<operator>
Store R1, Result <AssignExpression>

```

# Kod

```java

public class AssignExpression extends Expression {
    public Expression exp;
    public AssignOperator operator;
    // result

    ctor...

    public void translate(BufferedWriter out) {
        exp.translate(out);
        exp.genLoad("R1", out);
        this.genLoad("R2", out);

        this.operator.translate(out);

        out.write("Store R1" + this.result.name + "\n");
    }
}

public class AssignOperator extends Statement {
    // ADD, SUB, MUL, DIV
    public String opCode;

    public void translate(BufferedOutput out) {
        out.write(opCode + " R1, R2\n");
    }
}

```