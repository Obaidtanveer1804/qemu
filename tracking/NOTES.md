Notes:

#reproduction of the hanging crytal oscillator in the main QEMU, for the stm32vl disocvery

firmware: led.elf (built BOARD=f100, no -DQEMU), #the board=f100 is my custom make rule, so we can 
          build the code for the f100/f103/qemu (The qemu one comments out the clock parts -> a har          d fix for the issue we are working on)
symptom : no output
cause   : stuck in rcc_wait_for_osc_ready(RCC_HSE), PC=0x08000d3e (rcc.c:353)
          loop polls RCC_CR (0x40021000) bit 17 HSERDY, reads 0 forever
          -d unimp shows: RCC: unimplemented device write value 0x00010000 (HSEON)
          then read of offset 0 repeated ~2M times 

Registers: x/wx 0x40021000 = 0x00000000 (real reset = 0x83)
source   : hw/arm/stm32f100_soc.c: create_unimplemented_device("RCC", 0x40021000, 0x400)


Outputs/commands/procedure->gdb/used a simple elf from the TP

-> qn -M stm32vldiscovery -kernel led.elf -display none -serial null -S -gdb tcp::1234
         here qn is my version of the QEMU which i am editing and keeping the record on git, (it equals the build path of this qemu, (qemu-system-arm)which is in another directory than the main QEMU) 
