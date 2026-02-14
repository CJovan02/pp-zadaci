# Zadatak

```c
struct list
{
    int info;
    struct list* next;
};

int find(struct list* l, int x)
{
    int res;
    if ( l == 0 )
        res = 0;
    else if ( l->info == x )
        res = 1;
    else
        res = find(l->next, x);
    return res;
}

void count(struct list* head,int a)
{
    static int s;
    s+=find( head, a );
}
```

# Resenje

## Slog za count funkciju

```
+-------------------+
|   a               | [EBP + 12]
+-------------------+
|   head            | [EBP + 8]
+-------------------+ 
|   EID             | [EBP + 4]
+-------------------+
|   Stari EBP       | 
+-------------------+ <- EBP, ESP

```

## Slog za find funkciju

```
+-------------------+
| x                 | [EBP+12]
+-------------------+
| l                 | [EBP+8]
+-------------------+
| Adresa (EID)      | [EBP+4]
+-------------------+
| Stari EBP         | <- EBP
+-------------------+ 
| res               | [EBP-4]
+-------------------+ <- ESP

```

## Assembler

```x86asm

count:
    PUSH EBP
    MOV EBP, ESP

    MOV EAX, [EBP+12]
    PUSH EAX
    MOV EAX, [EBP+8]
    PUSH EAX
    CALL find

    ADD ESP, 8
    MOV EAX, [100]
    ADD EAX, ECX
    MOV [100], EAX

find:
    PUSH EBP
    MOV EBP, ESP

    SUB ESP, 4 ;Alociramo memoriju na stack-u za "res" lokalnu promenljivu

    MOV EAX, [EBP+8]
    CMP EAX, 0
    JNE lab1
    MOV [EBP-4], 0
    JMP kraj

    lab1:
        MOV EBX, [EBP+8]
        MOV EAX, [EBX]
        CMP EAX, [EBP+12]
        JNE lab2
        MOV [EBP-4], 1
        JMP kraj

    lab2:
        MOV EAX, [EBP+12]
        PUSH EAX
        MOV EBX, [EBP+8]
        MOV EAX, [EBX+4]
        PUSH EAX
        CALL find
        ADD ESP, 8
        MOV [EBP-4], ECX
    
    kraj:
        MOV ECX, [EBP-4]
        MOV ESP, EBS
        POP EBP
        RET

```