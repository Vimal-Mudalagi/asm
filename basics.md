# 🧠 x86 Assembly Basics — From Zero to Reverse Engineering Mindset

---

# 📜 1. What is Assembly (ASM)?

Assembly Language (ASM) is a **low-level programming language** that directly communicates with the CPU.

Instead of writing:

```c
x = x + 1;
```

You write:

```asm
mov eax, x
add eax, 1
```

👉 Assembly is **one step above machine code (binary)**
👉 It is **human-readable representation of CPU instructions**

---

# ⚙️ 2. ASM vs x86 — What’s the difference?

* **Assembly (ASM)** → A general concept (low-level language)
* **x86** → A specific **CPU architecture**

👉 So:

* x86 assembly = assembly language for Intel/AMD CPUs
* ARM assembly = for mobile processors

---

# 🧠 3. Why learn Assembly?

### 🔥 Practical Uses:

* Reverse Engineering (analyzing binaries)
* Malware Analysis
* Exploit Development
* Debugging at low-level
* Understanding how programs actually run

---

# 💀 Reality:

You don’t learn ASM to build apps
You learn it to:

> **understand and control software at the lowest level**

---

# ⚙️ 4. CPU Basics — Registers

Registers are tiny storage inside CPU.

### Important Registers:

* `eax` → main register (used for calculations & return values)
* `ebx`, `ecx`, `edx` → general purpose
* `esp` → stack pointer
* `ebp` → base pointer

👉 For beginners: focus on `eax`

---

# 📦 5. Core Instructions

## 1. mov → Copy data

```asm
mov eax, 5
```

👉 eax = 5

---

## 2. add → Addition

```asm
add eax, 3
```

👉 eax = eax + 3

---

## 3. sub → Subtraction

```asm
sub eax, 2
```

👉 eax = eax - 2

---

# 🔁 6. Comparison

## cmp → Compare values

```asm
cmp eax, 10
```

👉 Internally:

```
eax - 10
```

But result is NOT stored — only flags are set

---

# 🚪 7. Control Flow (Jumps)

```asm
cmp eax, 10
je equal
jne notequal
```

* `je` → jump if equal
* `jne` → jump if not equal

---

## Example (if-else logic)

```asm
mov eax, 10
cmp eax, 10
je yes

mov eax, 0
jmp end

yes:
mov eax, 1

end:
```

Equivalent C:

```c
if (eax == 10)
    eax = 1;
else
    eax = 0;
```

---

# 🧠 8. Key Insight

Everything reduces to:

* `mov` → data
* `add/sub` → math
* `cmp` → checks
* `jmp` → control flow

---

# 🧪 9. Example 1

```asm
mov eax, 5
add eax, 5
cmp eax, 10
je good

mov eax, 0
jmp end

good:
mov eax, 1

end:
```

👉 Final result: `eax = 1`

---

# 📦 10. Stack Basics

Stack = LIFO (Last In First Out)

### Operations:

#### push → add to stack

```asm
push eax
```

#### pop → remove from stack

```asm
pop ebx
```

---

## Example:

```asm
mov eax, 10
push eax

mov eax, 20
push eax

pop ebx   ; ebx = 20
pop ecx   ; ecx = 10
```

---

# ⚙️ 11. Why Stack Exists?

* Function calls
* Storing arguments
* Local variables
* Return addresses

---

# 🚀 12. Functions — call & ret

## call

```asm
call func
```

👉 Internally:

1. push return address
2. jump to function

---

## ret

```asm
ret
```

👉 Internally:

1. pop return address
2. jump back

---

## Example:

```asm
mov eax, 5
call addone
jmp end

addone:
add eax, 1
ret

end:
```

👉 Final result: `eax = 6`

---

# 🧠 13. Mapping to C

```c
int add(int x) {
    return x + 1;
}
```

Assembly:

```asm
add:
    add edi, 1
    mov eax, edi
    ret
```

👉 Return value always in `eax`

---

# 🧠 14. Stack Internals — esp & ebp

## esp (Stack Pointer)

* Points to top of stack
* Changes constantly

## ebp (Base Pointer)

* Fixed reference point
* Used to access variables

---

# 📦 Stack Layout

```
[ebp+12] → 2nd argument
[ebp+8 ] → 1st argument
[ebp+4 ] → return address
[ebp   ] → old ebp
[ebp-4 ] → local variable
```

---

# ⚙️ 15. Function Prologue

```asm
push ebp
mov ebp, esp
sub esp, 8
```

👉 Meaning:

* save old base pointer
* create new stack frame
* allocate space for locals

---

# ⚙️ 16. Function Epilogue

```asm
mov esp, ebp
pop ebp
ret
```

👉 Restore stack and return

---

# 🧩 17. Accessing Data

## Arguments:

```asm
[ebp+8]   → first argument
[ebp+12]  → second argument
```

## Local variables:

```asm
[ebp-4]
[ebp-8]
```

---

# 🔥 18. Example 2

```asm
push ebp
mov ebp, esp
sub esp, 4

mov eax, [ebp+8]
add eax, 1

mov [ebp-4], eax
mov eax, [ebp-4]

mov esp, ebp
pop ebp
ret
```

Equivalent:

```c
return x + 1;
```

---

# 🧩 19. Final Challenge Example

```asm
push ebp
mov ebp, esp

mov eax, [ebp+8]
add eax, [ebp+12]

pop ebp
ret
```

---

## Step-by-step:

Assume:

```c
func(3, 4)
```

### Execution:

```asm
mov eax, [ebp+8]     ; eax = 3
add eax, [ebp+12]    ; eax = 3 + 4 = 7
ret                  ; return 7
```

---

## Final Meaning:

```c
int func(int a, int b) {
    return a + b;
}
```

---

# 🧠 20. Reverse Engineering Mindset

When you see:

```asm
cmp eax, 1337
jne fail
```

👉 Think:

> "This is a check"

---

When you see:

```asm
mov eax, [ebp+8]
```

👉 Think:

> "This is input"

---

When you see:

```asm
add eax, [ebp+12]
```

👉 Think:

> "This is combining inputs"

---

# ⚡ Final Insight

Assembly is not about memorizing instructions.

It’s about:

> **recognizing patterns and understanding program logic at the lowest level**

---

# 🚀 Where you are now

You understand:

* Registers
* Basic instructions
* Control flow
* Stack
* Functions
* Arguments & locals

👉 That’s enough to start **basic reverse engineering**

---

# 💀 Final Note

At first, ASM looks like random nonsense.

With practice, it becomes:

> readable logic

And that’s when:

> you stop just using software
> and start **understanding and controlling it**
