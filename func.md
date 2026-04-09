```md
# 🔥 Step 20–27: Function Calls, Stack, and Real Reverse Engineering Flow (x86 cdecl)

---

## 🔥 Step 20: How programs REALLY pass arguments (cdecl calling convention)

Till now you saw:

```

[ebp+8], [ebp+12]

````

But you didn’t see how they got there.

### 🧠 The truth: arguments are pushed onto stack

### Example (C):
```c
add(3, 4);
````

### Assembly (caller side):

```asm
push 4        ; second argument
push 3        ; first argument
call add
add esp, 8    ; clean stack
```

⚠️ **Important detail**

Arguments are pushed **right → left**

👉 So stack becomes:

```
[ebp+8]  = 3
[ebp+12] = 4
```

---

## ⚙️ Step 21: Full function flow (REAL picture)

### Caller:

```asm
push 4
push 3
call add
```

### Inside function:

```asm
add:
    push ebp
    mov ebp, esp

    mov eax, [ebp+8]
    add eax, [ebp+12]

    pop ebp
    ret
```

👉 Full lifecycle:

```
push args → call → function runs → ret → back to caller
```

---

## 💀 Step 22: Stack cleanup (important for RE)

```asm
add esp, 8
```

👉 Why 8?

```
2 arguments × 4 bytes each = 8
```

### 🧠 Insight

If you see:

```asm
add esp, 12
```

👉 Your brain:

```
"3 arguments were passed"
```

---

## 🔥 Step 23: Real-world pattern recognition

### Pattern 1: Function call

```asm
push something
push something
call some_func
add esp, X
```

👉 This = function call with arguments

---

### Pattern 2: Return value check

```asm
call check
test eax, eax
je fail
```

👉 Means:

```
if return value == 0 → fail
```

---

## ⚙️ Step 24: New instructions

### `test`

```asm
test eax, eax
```

👉 Checks if `eax == 0`

Same as:

```asm
cmp eax, 0
```

---

### `lea` (looks scary but simple)

```asm
lea eax, [ebp-4]
```

👉 Means:

```
"get address, not value"
```

---

## 💀 Step 25: Real RE Example (this is gold)

```asm
call check_password
test eax, eax
je fail

mov eax, success_msg
call printf
```

### 🧠 Translate:

```
call function
check result
if failed → jump
else → success
```

👉 Equivalent C:

```c
if (check_password())
    printf("success");
else
    fail();
```

---

## 😈 Step 26: How hackers think

You see:

```asm
test eax, eax
je fail
```

👉 You patch:

```asm
nop
nop
```

OR:

```asm
jmp success
```

👉 Auth bypassed

⚠️ Turning point:

```
You are now reading program logic, not just assembly
```

---

## 🧪 Step 27: Challenge (Full Breakdown)

### 🔹 Caller:

```asm
push 5
push 2
call func
add esp, 8

cmp eax, 10
je good

mov eax, 0
jmp end

good:
mov eax, 1

end:
```

### 🔹 Function:

```asm
func:
    push ebp
    mov ebp, esp

    mov eax, [ebp+8]
    imul eax, [ebp+12]

    pop ebp
    ret
```

---

## 🧠 Step-by-step execution

### 1. Before call

```asm
push 5
push 2
```

Stack (top → bottom):

```
2
5
```

---

### 2. What does `call func` mean?

👉 Jump to label:

```asm
func:
```

💀 Important:

```
func = just a label (address in memory)
```

Also:

```
call → pushes return address → jumps
ret  → comes back
```

---

### 3. Inside function

```asm
mov eax, [ebp+8]
```

👉 First argument = `2`
👉 `eax = 2`

```asm
imul eax, [ebp+12]
```

👉 Second argument = `5`
👉 `eax = 2 × 5 = 10`

---

### 4. Return

```asm
ret
```

👉 Back to caller with:

```
eax = 10
```

---

### 5. Cleanup

```asm
add esp, 8
```

👉 Removes arguments from stack

---

### 6. Comparison

```asm
cmp eax, 10
je good
```

👉 `eax == 10` → jump taken

---

### 7. Final result

```asm
good:
mov eax, 1
```

👉 Final:

```
eax = 1
```

---

## 🎯 Final Meaning

### Function:

```c
int func(int a, int b) {
    return a * b;
}
```

### Main logic:

```c
if (func(2, 5) == 10)
    return 1;
else
    return 0;
```

---

## 🧠 Core Concepts (Lock these in)

### 🔥 `call func`

* jumps to label
* pushes return address

### 🔥 `func:`

* just a label (not magical)

### 🔥 `eax`

* CPU register (stores return value)

### 🔥 `imul`

* integer multiplication

### 🔥 `[ebp+8], [ebp+12]`

* function arguments

### 🔥 `add esp, X`

* stack cleanup

---

## ⚡ Key Insight

Your confusion:

```
"where is func?"
```

Reality:

```
Everything is one continuous code block
func is just a jump target
```

---

## 🚀 Mental Model

```
main → push args → call → jump → execute → ret → continue
```

---

## 🧪 Mini Check

```asm
mov eax, [ebp+8]
imul eax, [ebp+12]
add eax, 3
```

Inputs:

```
2 and 5
```

👉 Result:

```
eax = (2 × 5) + 3 = 13
```

---

## 🚀 Next Level

* Debugging (GDB / x64dbg)
* Real binary stepping
* Finding password checks in actual programs

👉 That’s where reverse engineering becomes real

```
```
