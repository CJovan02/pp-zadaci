# Zadatak

```c
struct task
{
    int id;
    int type
    struct task *next;
};

int calculate_tasks(struct task* t) {
    int res = 0;

    if (t->type == 1) {
        res = res + validate_task(t-id);
    }

    if (t-next != 0)
        res *= calculate_tasks(t->next);

    return res;
}

```

>[!IMPORTANT]
>Rezultat *calculate_tasks* se smesta na **stack**, a *validate_task* u **ECX**

# Resenje

## Izgled strukture *task*
```
+---------------+
| next          | +8
+---------------+
| type          | +4
+---------------+
| id            | +0
+---------------+
```

## Aktivacioni slog za funkciju *calculate_tasks*

```
+--------------------------+
| return Result            | [EBP+12]
+--------------------------+
| t                        | [EBP+8]
+--------------------------+
| EID (Adresa povratka)    | [EBP+4]
+--------------------------+
| Staro EBP                | <- EBP
+--------------------------+
| res                      | <- ESP, [EBP-4]
+--------------------------+
```

## Asembler

```x86asm

caluclate_tasks:
    PUSH EBP
    MOV EBP, ESP

    MOV EAX, 0
    PUSH EAX

    ;ucitamo t->type u EAX
    MOV EBX, [EBP+8]
    MOV EAX, [EBX+4]

    ;if (t->type == 1)
    CMP EAX, 1
    JNE drugi_if

    ;telo prvog if-a
    ;validate_task(t->id)
    MOV EAX, [EBX]
    PUSH EAX
    CALL validate_task
    ADD ESP, 4

    ;ucitamo res
    MOV EAX, [EBP-4]
    ; rez prethodne funkcije je u ECX
    ADD EAX, ECX
    MOV [EBP-4], EAX
    JMP kraj

    drugi_if:
        ;if(t->next != 0)
        MOV EAX, [EBX+8]
        CMP EAX, 0
        JE kraj

        ;calculate_tasks(t->next);
        ;Prvo rezervisemo mesto za rezultat funkcije, pushujemo bilo koji register, ili mozemo i -> SUB ESP, 4
        PUSH EAX
        ;Onda tek pushujemo argumente (u eax se vec nalazi vrednost koja nam treba)
        PUSH EAX
        CALL calculate_tasks
        ADD ESP, 4

        ;uzmemo rezultat funkije i stavimo u EDX
        POP EDX

        ;ucitamo res u EAX
        MOV EAX, [EBP-4]
        ;MUL valjda mnozi navedeni registar sa registrom EAX i smesta u EAX
        MUL EDX
        MOV [EBP-4], EAX

    kraj:
        ;upisemo rezultat na dno stack-a jer se tu stavlja return vrednost funkcije
        MOV EAX, [EBP-4]
        MOV [EBP+12], EAX

        MOV ESP, EBP
        POP EBP
        RET

```