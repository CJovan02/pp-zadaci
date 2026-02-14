# Zadatak

 RepeatStatement -> repeat (TIMES_CONST ; Condition) { StetementList }

Telo petlje (lista naredbi) se ponavlja dok je ispunjen Condition, a najvise se sme ponoviti TIMES_CONST broj puta, sto je jedna celobrojna konstanta.

---

# Medjukod

```
Load_Const R1, TIMES_CONST
Load_Const R2, 0
Store R2, count.name

IMC<condition>

petlja:
  Compare_Greater R2, R1
  JumpIfNotZero R2, kraj
  
  Load_Mem R1, Result condition
  JumpIfZero R1, kraj
  
  IMC<statement[0]>
  IMC<statement[1]>
  ...
  IMC<statement[n-1]>
  
  Load_Const R1, 1
  Load_Mem R2, count.name
  Add R2, R1
  Store R2, count.name
  Load_Const R1, TIMES_CONST
  
kraj:

```

---

# Kod
```js
class RepeatStatement extends Statement {
  public Expression condition;
  public Variable count;
  public ArrayList<Statement> statements;
  
  ctor...
  
  public void translate( BufferedWriter out ) throws IOException {
    String petlja = ASTNode.genLab();
    String kraj = ASTNode.genLab();
    
    out.write("Load_Const R1, TIMES_CONST" + "\n");
    out.write("Load_Const R2, 0" + "\n");
    out.write("Store R2" + this.count.name + "\n");
    
    this.condition.translate(out);
    
    out.write(petlja + ":\n");
    out.write("Compare_Greater R2, R1\n");
    out.write("JumpIFNotZero R2" + kraj + "\n");
    
    this.condition.genLoad("R1", out);
    out.write("JumpIfZero R1" + kraj + "\n");
    
    for (int i = 0; i < this.statements.length(); i++) {
      Statement current = statements[i];
      current.translate(out);
    }
    
    out.write("Load_Const R1, 1\n");
    out.write("Load_Mem R2" + count.name + "\n");
    out.write("Add R2, R1\n");
    out.write("Store R2" + count.name + "\n");
    out.write("Load_Const R1, TIMES_CONST\n");
    
    out.write(kraj + ":\n");
  }
}
```
