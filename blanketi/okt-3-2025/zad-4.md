# Zadatak

```c
int sum(int n, int* a) {
    int s;
    
    if (n == 0)
        s = 0;
    else
        s = *a + sum(n - 1, a + 1);

    return s;
}
```

**Rezultat funkcije se upisuje u registar ECX (CX)**

> [!NOTE]
> U zadatku ne pise koje velicine su podaci i adrese. Ja cu ovde koristiti velicinu od **4 bajta**.

---

# Kod

## Aktivacioni slog za funkciju sum

```
+------------------------+
| a                      | [EBP+12] 
+------------------------+
| n                      | [EBP+8] 
+------------------------+
| EID (Adresa povratka)  | [EBP+4] 
+------------------------+
| Staro EBP              | <- EBP
+------------------------+
| s                      | <- ESP, [EBP-4]
+------------------------+

```

## Asembler

```x86asm

sum:
    PUSH EBP
    MOV EBP, ESP

    SUB ESP, 4

    MOV EAX, [EBP+8]
    CMP EAX, 0
    JNE else

    MOV EAX, 0
    MOV [EBP+8], 0
    JMP kraj    

    else:
        ;POZIV FUNKIJE SUM
        ;stavljamo argumente na stack
        ;a+1
        MOV EAX, [EBP+12]
        ADD EAX, 1
        PUSH EAX
        ;n-1
        MOV EAX, [EBP+8]
        SUB EAX, 1
        PUSH EAX
        ;poziv funkcije
        CALL sum ; -> PUSH EID && JUMP sum
        
        ;NAKON izvrsenja funkcije, skidamo pushovane argumente sa stack-a
        ADD ESP, 8

        ;Ucitavamo povratnu vrednost izvrsene funkcije SUM (EAX)
        MOV EDX, ECX

        ;Ucitamo ADRESU *a
        MOV EBX, [EBP+12]
        MOV EAX, [EBX]

        ;U s upisujemo sumu ove dve vrednosti
        ADD EAX, EDX

        MOV [EBP-4], EAX

    kraj:
        MOV ECX, [EBP-4]

        ;Vracamo stari kontekst procesora
        MOV ESP, EBS
        POP EBP
        RET

```
