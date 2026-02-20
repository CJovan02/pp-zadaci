IMC<exp>
Load_Mem R1, Result <exp>
Store R1, index.name
IMC<list[R1]>
Load_Mem R2, Result <list[R1]>
Store R2, result.name

```js

class SelectExpression extends Expression {
    public Expression exp;
    public ArrayList<Expression> list;
    public String index;
    // nasledjuje String result
    
    ctor...
    
    translate(out) {
        exp.translate(out);
        exp.genLoad("R1", out);
        
        out.write("Store R1, " + index.name);
        int intIndex = Integer.parseInt(index);
        list[intIndex].translate(out);
        list[intIndex].genLoad("R2", out);
        out.write("Store R2, " + this.result.name);
    }
    
}

```
