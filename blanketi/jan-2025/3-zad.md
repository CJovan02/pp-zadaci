DoWhile -> do StatementList while ( Expression )
Statement -> BreakStatement | ContinueStatement | ...

Značenje naredbi je sledeće: U DoWhile petlji se izvršava lista naredbi najmanje jednom i ponavlja se sve dok se izraz u zagradama evaluira kao tačan. Naredba break prekida izvršenje petlje. Naredba continue prekida izvršenje trenutne interacije petlje i nastavlja sa izvršenjem naredbe iteracije ukoliko je izraz u zagradama još uvek tačan.
Smatrati da su klase za predstavljanje ostalih naredbi već potpuno implementirane.

# Medjukod

```

IMC<exp>
petlja:
  IMC<statement[0]>
  IMC<statement[1]>
  ...
  IMC<statement[n-1]>

  provera:
    Load_Mem R1, Result exp
    JumpIfZero R1, kraj;
    Jump petlja;

kraj:


```

# Kod
```js
class DoWhile extends Statement {
  private Expression uslov;
  private ArrayList<Statement> statements;
  
  ctor...
  
  public void translate(BufferedWriter out) throws IOException {
    String petlja = ASTNode.getLab();
    String provera = ASTNode.getLab();
    String kraj = ASTNode.getLab();
    
    this.uslov.translate(out);
    out.write(petlja + ":\n");
    
    for (int i = 0; i < statements.size(); i++) {
      Statement curr = statement[i];
      
      if (curr instanceof BreakStatement) {
        ((BreakStatement) curr).setKrajLabel(kraj);
      }
      if (curr instanceof ContinueStatement) {
        ((ContinueStatement) curr).setProveraLabel(provera);
      }
      curr.translate(out);
    }
    
    // i onda ispises ovo, mrzi me
      provera:
        Load_Mem R1, Result exp
        JumpIfZero R1, kraj;
        Jump petlja;
    
    kraj:
  }
}

class BreakStatement extends Statement {
  private String krajLabel;
  
  ctor...
  
  public setKrajLabel(String lab) {
    this.krajLabel = lab;
  }
  
  public void translate(BufferedWriter out) throws IOException {
    out.write("Jump" + krajLabel + "\n");
  }
}

class ContinueStatement extends Statement {
  private String proveraLabel;
  
  ctor...
  
    
    public setProveraLabel(String lab) {
      this.proveraLabel = lab;
    }
  
  public void translate(BufferedWriter out) throws IOException {
    out.write("Jump" + proveraLabel + "\n");
  }
}
```
