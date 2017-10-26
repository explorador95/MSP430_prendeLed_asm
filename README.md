# MSP430_prendeLed_asm
;---------------------------------------------------------------
#include "msp430g2231.h"                     ; #define controlled include file
#define X R4

        NAME    main                    ; module name

        PUBLIC  main                    ; make the main label vissible
                                    
        ORG     0F800h
RESET   MOV     #0240h, SP        ; set up stack

main:	MOV.W   #WDTPW+WDTHOLD,&WDTCTL  ; Stop watchdog timer
;----------------------------------------------------------------------
        MOV.B   &CALBC1_1MHZ,&BCSCTL1
        MOV.B   &CALDCO_1MHZ,&DCOCTL 

P1BIOS  CLR.B &P1SEL
        MOV.B #11110111B,&P1DIR
        MOV.B #BIT3,P1OUT
        MOV.B #BIT3,P1REN
	MOV.B #BIT3,P1IES
        CLR.B &P1IFG
        MOV.B #BIT3,P1IE
;----------------------------------------------------------------------
P2BIOS  CLR.B &P2SEL
        MOV.B #0FFh,&P2DIR
        CLR.B &P2OUT
        CLR.B X
        MOV.B #GIE,SR
        nop
;----------------------------------------------------------------------
COMPARA CMP.B #0,X
        JEQ APAGA
        
        BIS.B #BIT6+BIT0,&P1OUT
        CALL #T50ms
        BIC.B #BIT6+BIT0,&P1OUT
        CALL #T50ms
        JMP COMPARA

APAGA   BIC.B #BIT6+BIT0,&P1OUT
        JMP COMPARA



BOTON   CALL #T30ms
        BIT.B #BIT3,&P1IN
        JNZ CASA
        CMP.B #1,X
        JHS CERO
        INC.B X
CASA    BIC.B #BIT3,&P1IFG
        RETI
        
CERO    CLR.B X
        JMP CASA
        
        

T30ms   MOV #7500,R6
OTRO    DEC R6
        NOP
        JNZ OTRO
        RET
          
T50ms   MOV #10000,R5
OTRA    DEC R5
        NOP
        JNZ OTRA
        RET

        ORG 0FFFEh
        DW RESET
        
	ORG 0FFE4H
	DW BOTON  
   
	END main
        END
        
