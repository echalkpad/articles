# Fun C Micro-optimizations - restrict

[Original URL](http://jcdav.is/2015/11/23/Fun-C-Micro-optimizations-restrict/)

> 23 Nov 2015 Lets say we wanted to add a value to an every element of an array. Here's a slightly contrived example: void add_to_array(int* arr, int* v, int len) { for(int i = 0; i < len;...

<span class="post-date">23 Nov 2015</span>

 Lets say we wanted to add a value to an every element of an array. Here's a slightly contrived example:

```
void add_to_array(int* arr, int* v, int len) {
 for(int i = 0; i < len; i++) {
 arr[i] += *v;
 }
}

int main(int argc, char** argv) {
 int arr[] = {5, 4, 3};
 int v = -1;
 add_to_array(arr, &v, 3);
 for(int i = 0; i < 3; i++) {
 printf("%d\n", arr[i]);
 }
}
```

If we look at the assembly for `add_to_array` (using `gcc -O1 -S <file>`), there is an interesting peculiarity:

```
_add_to_array: ## @add_to_array
 pushq %rbp
 movq %rsp, %rbp
 testl %edx, %edx
 jle LBB0_2
LBB0_1: ## =>This Inner Loop Header: Depth=1
 movl (%rsi), %eax
 addl %eax, (%rdi)
 addq $4, %rdi
 decl %edx
 jne LBB0_1
LBB0_2: ## %._crit_edge
 popq %rbp
 retq
```

We are rereading the value of `v` every iteration, instead of just once. Why is that? Well, gcc has to assume that its possible that `arr` and `v` overlap, making a write to `arr` change the value of `v`. We could, for instance, call `add_to_array(arr, arr, 3)` and it must produce `{10, 14, 13}`.

In reality of course, this behavior is rarely intended. Thats where [restrict](https://en.wikipedia.org/wiki/Restrict) comes into play. Adding `restrict` to our parameters lets us signal to gcc that the regions don't overlap. We can change our function to reflect this:

```
void add_to_array(int* restrict arr, int* restrict v, int len) {
 for(int i = 0; i < len; i++) {
 arr[i] += *v;
 }
}
```

And now gcc will lift reading of `v` out of the loop:

```
_add_to_array: ## @add_to_array
 pushq %rbp
 movq %rsp, %rbp
 testl %edx, %edx
 jle LBB0_3
 movl (%rsi), %eax
LBB0_2: ## =>This Inner Loop Header: Depth=1
 addl %eax, (%rdi)
 addq $4, %rdi
 decl %edx
 jne LBB0_2
LBB0_3: ## %._crit_edge
 popq %rbp
 retq
```

This example, of course, seems pretty silly. After all, you would certainly just pass the integer rather than a pointer, and this wouldn't happen right? That certainly would make a lot more sense.

Where it gets a lot less obvious, however, is in more complicated situations. Let's say we have a simple struct:

```
typedef struct {
 float f;
 int i;
} st;
```

And now `add_to_array` takes an `st*` and adds `i`:

```
void add_to_array(int* arr, st* s, int len) {
 for(int i = 0; i < len; i++) {
 arr[i] += s->i;
 }
}
```

Pass this through `gcc -O1 -S` again and you'll find it looks pretty much identical to our first sitaution, just reading `movl 4(%rsi), %eax` instead. Even though it seems fairly obvious to us that the array and struct will refer to seperate memory regions, C's Wild West approach to memory means that `add_to_array(arr, (st*)arr, 3)` is possible, and gcc must generate assembly that produces the correct output `{9,8,11}`. Again here, we must specify `restrict` to lift the read of `s->i` out of the loop.

Are these details worth overly worrying about? In most cases, probably not. But its a fun trick to have in your pocket and another reminder of the value of looking over assembly in performance-critical areas.
