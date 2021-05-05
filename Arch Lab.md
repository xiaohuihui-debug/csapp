# Arch Lab

#Sample linked list

```
/* linked list element */
 typedef struct ELE {
            int val;
            struct ELE *next;
        } *list_ptr;
```

```
        .pos 0
        irmovq stack,%rsp
        call main
        halt
# Sample linked list
        .align 8
        ele1:
        .quad 0x00a
        .quad ele2
        ele2:
        .quad 0x0b0
        .quad ele3
        ele3:
        .quad 0xc00
        .quad 0
main:	irmovq ele1,%rdi
        call sum_list
	    ret
sum_list:
	    irmovq $0,%rax
	    jmp test
loop:	mrmovq 0(%rdi),%rsi
        addq %rsi,%rax
        mrmovq 8(%rdi),%rsi
        rrmovq %rsi,%rdi
test:   andq %rdi,%rdi
        jne loop
        ret
        .pos 0x100
stack:
```

```
rsum_list:
        pushl   %ebp
        rrmovl  %esp , %ebp
        pushl   %ebx
        irmovl  $4 , %esi
        subl    %esi , %esp
        xorl    %eax , %eax
        mrmovl  8(%ebp),%edx
        andl    %edx , %edx
        je  End
        mrmovl (%edx) , %ebx
        irmovl  $4 , %esi
        addl    %esi , %edx
        mrmovl (%edx) , %edi
        rmmovl  %edi , (%esp)
        call    rsum_list
        addl    %ebx , %eax

    End:    
        addl    %esi , %esp
        popl    %ebx
        popl    %ebp
        ret
```

```
    .pos    0
init:   irmovl  Stack,  %esp
        irmovl  Stack,  %ebp
        call    Main
        halt

    .align 4
# Source block
src:
        .long 0x00a
        .long 0x0b0
         .long 0xc00
# Destination block
dest:
         .long 0x111
         .long 0x222
        .long 0x333

Main:   pushl   %ebp
        rrmovl  %esp , %ebp
        irmovl  $12 , %esi
        subl    %esi , %esp
        irmovl  src , %eax
        rmmovl  %eax , (%esp)
        irmovl  dest , %eax
        rmmovl %eax , 4(%esp)
        irmovl  $3, %eax
        rmmovl %eax, 8(%esp)
        call    copy_block
        irmovl  $12 , %esi
        addl    %esi , %esp 
        popl    %ebp
        ret 

copy_block:
        pushl   %ebp
        rrmovl  %esp, %ebp
        xorl    %eax , %eax

        mrmovl  12(%ebp) , %edx     #edx <==>dest
        mrmovl  8(%ebp) , %esi      #esi <==> src
        mrmovl  16(%ebp),%ecx       #ecx <==> len
        andl    %ecx, %ecx
        je  End
Loop:   mrmovl   (%esi) , %ebx
        rmmovl  %ebx , (%edx)       #copy src value to dest
        xorl    %ebx , %eax         #compute the value ^= val
        irmovl  $4 ,  %edi      
        addl    %edi , %edx         #dest++
        addl    %edi , %esi         #src++
        irmovl  $1,%edi             
        subl    %edi , %ecx         #len--
        jne Loop

End:    popl    %ebp
        ret

        .pos    0x100
 Stack:
```

PartC

```
Loop:
    mrmovq (%rdi), %r10	# read val from src...
	mrmovq 8(%rdi),%r11
	rmmovq %r10, (%rsi)	# ...and store it to dst
	andq %r10, %r10		# val <= 0?
	jle Npos		# if so, goto Npos:
	iaddq $1,%rax
Npos:
	rmmovq %r11,8(%rsi)
	andq %r11,%r11
	jle Npos2
	iaddq $1,%rax
Npos2:
	mrmovq 16(%rdi),%r10
	mrmovq 24(%rdi),%r11
	rmmovq %r10, 16(%rsi)
	andq %r10,%r10
	jle Npos3
	iaddq $1,%rax
Npos3:
	rmmovq %r11,24(%rsi)
	andq %r11,%r11
	jle nLoop
	iaddq $1,%rax
nLoop:
	iaddq $32,%rdi
	iaddq $32,%rsi
	iaddq $-4,%rdx
	jge Loop

REM:
	iaddq $3,%rdx
	jl Done
    mrmovq (%rdi), %r10
	mrmovq 8(%rdi),%r11
	rmmovq %r10, (%rsi)
	andq %r10,%r10
	jle RENPO
	iaddq $1,%rax
RENPO:
	iaddq $-1,%rdx
	jl Done
	rmmovq %r11,8(%rsi)
	andq %r11,%r11
	jle RENPO1
	iaddq $1,%rax
RENPO1:
	iaddq $-1,%rdx
	jl Done
    mrmovq 16(%rdi), %r10
	rmmovq %r10, 16(%rsi)
	andq %r10,%r10
	jle Done
	iaddq $1,%rax
```

这个lab我是借鉴的知乎Name大佬和csdn上ethanyzf大佬的，这个lab整的我极其难受，无从下手，关于这个实验的csapp和视频看了两个礼拜还是没有头绪，已经准备暂时搁置，等完成其他lab之后再回来看。

