# Zadatak

Definisati IA-32 kod i izgled aktivacionog sloga za funkciju verify_preconditions.
Za predstavljanje podataka tipa int kao i memorijskih adresa koriste se 4 bajta. Pretpostaviti da se
rezultat funkcije verify_preconditions smešta na stek, a rezultat funkcije exec_query u registar ECX.

```c

struct verification_rule
{
    int type;
    struct verification_rule *next;
    int query_id;
};

int verify_preconditions(
    struct verification_rule* vr) {
    int truth=0;
    int condition=1;

    if (vr->type == 0){
        condition = condition * exec_query(vr->query_id);
    }

    if(vr->next != 0)
        condition *= verify_preconditions(vr->next);

    return condition;
}

```

---

# Resenje

## Memorija

### Struct verification rule

```
+-----------------------+
| query_id              | +8
+-----------------------+
| next                  | +4
+-----------------------+
| type                  | +0
+-----------------------+

```

### Aktivacioni slog veriy_preconditions

```
+-----------------------+
| Return result         | [EBP+12]
+-----------------------+
| vr                    | [EBP+8]
+-----------------------+
| EIP (Adresa povratka) | [EBP+4]
+-----------------------+
| Staro EBP             | <- EBP
+-----------------------+
| truth                 | [EBP-4]
+-----------------------+
| condition             | <-ESP [EBP-8]
+-----------------------+

```

## Kod

```x86asm

verify:
    PUSH EBP
    MOV EBP, ESP

    ; Mislim da je ovako logicnije da se uradi svakom ko je barem polu normalan, ali naravno da su na CS-u drugacije uradili
    ;MOV EAX, 0 ; <- truth
    ;PUSH EAX
    ;MOV EAX, 1 ; <- condition
    ;PUSH EAX

    ;Rezervisemo prostor za 2 promenljive, pomerimo stack pointer za 2 mesta
    SUB ESP, 8
    MOV [EBP-4], 0 ; <- truth
    MOV [EBP-8], 1 ; <- condition

    ;vr->type
    MOV EBX, [EBP+8]
    MOV EAX, [EBX]
    CMP EAX, 0
    JNE lab1
    ;pozovi exec_query
    ;vr->queryId
    MOV EAX, [EBX+8]
    PUSH EAX
    CALL exec_query
    ADD ESP, 4
    MOV EDX, ECX
    MOV EAX, [EBP-8]
    MUL EDX
    MOV [EBP-8], EAX

    lab1:
        ;vr->next
        MOV EBX, [EBP+8]

        CMP [EBX+4], 0
        JE kraj

        ;pozivanje verify_preconditions
        ;alokacije mesta za return vrednost
        PUSH EAX
        ;alokacija za vr->next
        MOV EAX, [EBX+4]
        PUSH EAX
        CALL verify_preconditions
        ADD ESP, 4
        ;Nakon izvrsenja funkcije uzmemo vrh stack-a (to je rezultat funkcije)
        POP EAX
        ;pribavi condition
        MOV EDX, [EBP-8]
        MUL EDX
        MOV [EBP-8], EAX
    
    kraj:
        ;Upis na vrh stack-a
        MOV EAX, [EBP-8]
        MOV [EBP+12], EAX
        MOV ESP, EBP
        POP EBP
        RET



```