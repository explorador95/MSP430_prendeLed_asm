# MSP430_prendeLed_asm
;---------------------------------------------------------------
#include "msp430g2231.h"                     ; #define controlled include file

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
        MOV.B #GIE + CPUOFF,SR
       nop
;----------------------------------------------------------------------
BOTON   CALL #T30ms
        BIT.B #BIT3,&P1IN
        JNZ ENCE
        
      
        
       
       XOR.B #BIT0+BIT6,&P1OUT
       
ENCE   BIC.B #BIT3,&P1IFG
        RETI
     
T30ms   MOV #7500,R4
OTRO    DEC R4
        NOP
        JNZ OTRO
        RET
        
        ORG 0FFFEh
        DW RESET
        
	ORG 0FFE4H
	DW BOTON  
   
	END main
        END
        
