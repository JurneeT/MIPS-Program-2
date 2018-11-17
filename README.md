# MIPS-Program-2
.data
  isEmpty:   .asciiz "Input is empty."
  isInvalid: .asciiz "Invalid base-35 number."
  tooLong:    .asciiz "Input is too long."
  userInput:    .space  512
.text

empty_input:
  la $a0, isEmpty
  li $v0, 4
  syscall
  j exit
  
invalid_input:
  la $a0, isInvalid
  li $v0, 4
  syscall
  j exit
  
long_input:
  la $a0, tooLong
  li $v0, 4
  syscall
  j exit

main:
  li $v0, 8
  la $a0, userInput
  li $a1, 100
  syscall

del_left_pad:
	li $t8, 32 # space
	lb $t9, 0($a0)
	beq $t8, $t9, del_first_char
	move $t9, $a0
	j input_len
	
del_first_char:
	addi $a0, $a0, 1
	j del_left_pad
