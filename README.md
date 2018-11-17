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

input_len:
	addi $t0, $t0, 0
	addi $t1, $t1, 10
	add $t4, $t4, $a0

len_iteration:
	lb $t2, 0($a0)
	beqz $t2, after_len_found
	beq $t2, $t1, after_len_found
	addi $a0, $a0, 1
	addi $t0, $t0, 1
	j len_iteration
	
after_len_found:
	beqz $t0, empty_input
	slti $t3, $t0, 5
	beqz $t3, long_input
	move $a0, $t4
	j check_validity
	
check_validity:
	lb $t5, 0($a0)
	beqz $t5, prepare_for_conversion
	beq $t5, $t1, prepare_for_conversion
	slti $t6, $t5, 48                 # if char < ascii(48),  input invalid;   ascii(48) = 0
        bne $t6, $zero, invalid_input
	slti $t6, $t5, 58                 # if char < ascii(58),  input is valid;  ascii(58) = 9
	bne $t6, $zero, step_char_forward
        slti $t6, $t5, 65                 # if char < ascii(65),  input invalid;   ascii(97) = A
	bne $t6, $zero, invalid_input
        slti $t6, $t5, 89                 # if char < ascii(88),  input is valid;  ascii(89) = Y
	bne $t6, $zero, step_char_forward
	slti $t6, $t5, 97                 # if char < ascii(97),  input invalid;   ascii(97) = a
	bne $t6, $zero, invalid_input
	slti $t6, $t5, 122                # if char < ascii(122), input is valid; ascii(122) = z
	bne $t6, $zero, step_char_forward
	bgt $t5, 121, invalid_input   # if char > ascii(121), input invalid;  ascii(121) = y
	
step_char_forward:
	addi $a0, $a0, 1
	j check_validity

prepare_for_conversion:
	move $a0, $t4
	addi $t7, $t7, 0
	add $s0, $s0, $t0
	addi $s0, $s0, -1	
	li $s3, 3
	li $s2, 2
	li $s1, 1
	li $s5, 0
	
base_convert_input:
	lb $s4, 0($a0)
	beqz $s4, print_result
	beq $s4, $t1, print_result
	slti $t6, $s4, 58
	bne $t6, $zero, base_ten_conv
	slti $t6, $s4, 88
	bne $t6, $zero, base_35_upper_conv
	slti $t6, $s4, 122
	bne $t6, $zero, base_35_lower_conv
	
	base_ten_conv:
	addi $s4, $s4, -48
	j serialize_result

base_35_upper_conv:
	addi $s4, $s4, -54
	j serialize_result

base_35_lower_conv:
	addi $s4, $s4, -87
