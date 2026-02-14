# Zadatak

```c
struct list
{
    int info;
    struct list* next;
};

int sum(struct list* head)
{
    int s=0;

    if (head != 0)
        s = head->info + sum(head->next);

    return s;
}
```

**Rezultata funkcije se smesta u ECX(EX).**
**Adrese mogu da budu i 2 bajta, ovde je radjeno sa velicinom od 4.**

---

# Resenje

## Izgled strukture *list*

```
+------------------------+
| next                   | +4
+------------------------+
| info                   | +0
+------------------------+

```

## Aktivaconi slog za funkciju sum

```
+---------------------------+
| head                      | <- [EBP+8]
+---------------------------+
| EID (Adresa povratka)     | <- [EBP+4]
+---------------------------+
| Staro EBP                 | <- EBP
+---------------------------+
| s                         | <- ESP, [EBP-4]
+---------------------------+
```

## Asembler

```x86asm

sum:
    PUSH EBP
    MOV EBP, ESP

    ;s = 0
    MOV EAX, 0
    PUSH EAX

    ;ucitamo head
    MOV EAX, [EBP+8]
    CMP EAX, 0
    JE kraj

    ;     sum(head->next)
    ;ucitamo head->next
    MOV EBX, [EBP+8]
    MOV EAX, [EBX+4]
    PUSH EAX

    CALL sum
    ADD ESP, 4

    ;ucitamo head-info
    MOV EAX, [EBX]
    ADD EAX, ECX
    
    ;upisemo u s
    MOV [EBP-4], EAX

    kraj:
        MOV ECX, [EBP-4]

        MOV ESP, EBP
        POP EBP
        RETd


```