# Zadatak
RelExpression -> Expression # Expression

Izracunaju se izrazi i porede se:
- I > II = 1
- I == II = 0
- I < II = -1

---

# Medjukod

```
IMC<first>
IMC<second>
Load_Mem R1, Result<first>
Load_Mem R1, Result<second>

// Sigurno moze sa 2 Compare instrukcije da se resi ovo, al me mrzi iskreno
Compare_Greater R1, R2
JumpIfNotZero R1, veci

Load_Mem R1, Result<first>
Compare_Equal R1, R2
JumpIfNotZero R1, jednaki

Load_Mem R1, Result<second>
Compare_Less R1, R2
JumpIfNotZero R1, manji

veci:
  Load_Const R1, 1
  Store R1, result.name
  
jednaki:
  Load_Const R1, 0
  Store R1, result.name
  
manji:
  Load_Const R1, -1
  Store R1, result.name
  
```

---

# Kod
```js
public class RelExpression extends Expression {
  // Expression abstrakna klasa u sebi sadrzi result, pa cemo to koristiti da zapamtami rezultat (1 | 0 | -1)
  public Expression first;
  public Rxpression second;
  
  ctor...
  
  public void translate( BufferedOutput out ) throws IOException {
    // Mrzi me da ispistem, lagano je i ovo
  }
}
```
