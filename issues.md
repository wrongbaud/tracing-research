Data is being transmitted from the TPIU with the baseline ETM configuration, however it's not making any sense in the decoder

It's not working with our test firmware, will it work with his test firmware? 

Path to ELF: /home/wrongbaud/testworkspce/stm32f4-dev-board-itm-etm/Debug/stm32f4-dev-board-itm-etm.elf

I think that the current issue is actually in the decoder

ETM ID Register: https://developer.arm.com/documentation/ihi0014/q/Programmers--Model/Detailed-register-descriptions/ID-Register--ETMIDR--ETMv2-0-and-later?lang=en

ETM Register Description: https://developer.arm.com/documentation/ddi0337/h/embedded-trace-macrocell/etm-programmers-model/register-summary

ETM IDR Val (MCU): 4114f250

This is decoded as ETMv3.5: CoreSight ETM-M4	0x4114F250	ETMv3.5

We can see this in the table here: https://developer.arm.com/documentation/ihi0014/q/Programmers--Model/Detailed-register-descriptions/ID-Register--ETMIDR--ETMv2-0-and-later?lang=en#CHDIHJJA

Let's start by looking at the current decoder - and see what changes there are between 3v3 and 3v5

Ok, when we ONLY enable the TPIU we see the toggle with no real structure at the beginning followed by no data whatsoever.

NOTE: WE NEED TO BE RESETTING BETWEEN EACH TEST

# Building Pulseview Analyzers

- If we're going to get anywhere with this, we need to figure out how to properly decode these damn packets. ITM seems to be working, but we need to focus on ETM

- ISSUE Solved - if we look at bits 21 and 6:4 of the ETM control register, we can see that we specify the bit width of the bus. Remember that in our case we are setting this width to one as we're just using the SWO pin as a UART via the TPIU. The default example provided in the SMT32F4 datasheet assumes that we have a four bit width and all of the public examples using an STM32F1 don't have to concern themselves with this as they don't have a parallel ETM output peripheral (from what I can tell anyways!)

So what does this _actually_ look like?


