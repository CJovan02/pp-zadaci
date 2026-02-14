## CondStatement -> Statement if Expression │ Statement unless Expression 

## Medjukod:

```
- IF slucaj:
    IMC<exp>
    Load_Mem    R1, Result <exp>
    JumpIfZero  R1, kraj
    IMC<s>
kraj:

- unless slucaj:
    IMC<exp>
    Load_Mem    R1, Result <exp>
    JumpIfNotZero R1, kraj
    IMC<s>
kraj:
```

```java
class CondStatement extends Statement {
    public Expression exp;
    public Statement s;
    public boolean ifUnless; // true za if, false za unless

    public CondStatement(Expression exp, Statement s, boolean isUnless) {
        this.exp = exp;
        this.s = s;
        this.ifUnless = ifUnless;
    }

    @Override
    public void translate( BufferedOutput out ) throws IOException {
        String kraj = ASTNode.genLab();
        exp.translate( out );
        exp.genLoad( "R1", out );
        if (ifUnless) {
            out.write( "JumpIfZero R1" + kraj + '\t );
        } else {
            out.write( "JumpIfNotZero R1" + kraj + '\t );
        }
        s.translate(out);
        out.write( kraj + ":" + "\n" )
    }
}
```