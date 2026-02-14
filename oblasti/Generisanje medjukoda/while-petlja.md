WhileStatement -> WHILE Expression DO Statement

Medjukod:

startLabel:
    IMC<Condition>
    Load_Mem    R1, Result <Condition>
    JumpIfZero  R1, endLabel
    IMC<Body>
    Jump    startLabel

endLabel:
    ...

class WhileStatement extends Statement {
    public Expression condition;
    public Statement body;

    public translate( BufferedWriter out ) throws IOException {
        String startLabel = ASTNode.genLab();
        String endLabel = ASTNode.genLab();

        out.write( startLabel + ":" );
        out.newLine();

        condition.translate( out );
        condition.genLoad( "R1", out );
        out.write( "\tJumpIfZero\tR1" + endLabel );
        out.newLine();

        body.transalte( out );
        out.write( "\tJump\t" + startLabel );
        out.newLine();

        out.write(endLabel + ":");
        out.newLine();
    }
}