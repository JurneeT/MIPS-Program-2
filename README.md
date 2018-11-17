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
