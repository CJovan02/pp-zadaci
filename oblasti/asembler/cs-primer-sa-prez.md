# Zadatak

Prikazati izgled generisanog koda za funkciju
f, aktivacioni slog i deo koda koji odgovaraju
sledećem pozivu funkcije f.

```c
f(1,2)
void f( int a, int b )
{
    int c;
    c=a+b;
}
```

---

# Resenje

## Stack (aktivacioni slog)

> [!NOTE]
> Stack se crta naopacke jer je intuitivno ljudima da stvarni parametri 
> neke funkcije budu na vrhu a lokalni parametri da budu na dnu jer tako lici na kod funkcije.

```
+------------------------------+
|   b: 2                       | [EBP+16]
+------------------------------+
|   a: 1                       | [EBP+8]
+------------------------------+
| Adresa povratka              |
| Adresa instrukcije ADD ESP, 8| [EBP+4]
+------------------------------+
|  Staro EBP                   | [EBP]
+------------------------------+ <-- EBP (base pointer)
|  local c                     | [EBP-4]
+------------------------------+ <-- EST (stack pointer)
```


## Asembler

> [!IMPORTANT]
> Kada dodamo neku promeljivu u stack-u onda stack pointer moramo da SMANJIMO. Kada zelimo da skinemo nesto steka onda moramo da ga POVECAMO. Nemam pojma sto je ovako. Koliko se secam iz `AOR2` `PUSH`i `POP` instrukcije same modifikuju `ESP`, ovde u resenju oni rucno modifikuju `ESP`, ne znam zasto :)

```x86asm

;  Sekvenca pozivanja  

;Na stek stavljamo stvarne parametre funkcije pocevsi od zadnjeg parametra

MOV EBX, 2 <-- b
PUSH EBX
MOV EBX, 1 <-- a
PUSH EBX

;Poziv funkcije f, call zapravo ovo izvrsava:
;PUSH EIP <-- ovo zapravo stavlja sledecu instrukciju (ADD ESP, 8) na stack
;JUMP f
CALL f

;Stack pointer vracamo NAZAD za 2 polja, ovo se izvrsava NAKON izvrsenja funkcije i sluzi za skidanje svih argumenata sa stack-a NAKON sto se funkcija izvrsi
ADD ESP, 8


;  Izvrsavanje tela funkcije
f:
;Sacuvamo staru vrednost base pointer-a i u base pointer ucitamo stack pointer, da zakucamo base pointer na trenutni vrh stack-a (base pointer ce uvek pokizavi sam na sebe a stack pointer ce da se menja)
PUSH EBP
MOV EBP, ESP


;Pomeramo stack pointer za jos jedno polje jer moramo i lokalnu promenljivu c da alociramo.

SUB ESP, 4

;Sada na osnovu base pointera mozemo da pristupamo promenljivama u stack-u kao gore na slici

MOV AX, [EBP+8]
ADD AX, [EBP+16]
MOV [EBP-4], AX

;  Sekvenca povratka

;Kada staru vrednost base pointer-a upisemo u stack pointer maltene mi "skidamo" sa stack-a sve lokalne promenljive (one promenljive koje su bile u "-", za ovaj primer je to samo promenljiva c)
MOV ESP, EBP

;Onda skinemo i BP sa stack-a
POP BP

;Povratak u pozivajuci modul.
RET

```