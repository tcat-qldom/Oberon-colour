# Oberon colour 2/16 switchable

Presenting build supporting Oberon IN
{0..3} colour, the white really is {0,1,2,3} = 15, rather than {0} = 1.
But changing it, would invalidate clients, requiring all modules rebuild hanging on Display.

The resolution is operated with switch SW[6]= 0(1-bit) | 1(4-bit).


**Supported h/w**

As per PO2013 h/w specification, OberonStation board, Saanlima flavour of boards, Pepino, Pistpirello, etc.

FPGA board with 1MB SRAM(10ns) 256Kx32, MicroSD slot, PS/2 mouse keyboard ports, 8 LEDs, 8[4] switches, 8-pin SPI port (nRF24L01+ wireless module), RS232, 12-pin port (4 buttons, 8 GPIO), VGA, JTAG (USB).

**How to install**

There are two files needed, RISC5.FS with compressed Oberon file system, and RISC5.bit processor image for a specific board.
With your spare uSD card inserted, recognised as /dev/sdX, where X stands for the drive letter.

	$ dmesg | tail # shows drive /dev/sdX
	$ dd if=RISC5.FS of=/dev/sdX bs=1024 seek=262145

Boot with synthesis bit image for your board e.g.

	$ fpgaprog -f RISC5.bit

**Limits**

	CONST black* = 0; white* = 1(*5*); 
	  baseM = 0E7F00H; (* adr of 1024 x 768 pixel, monocolor display frame *)
	  baseC = 09FF00H; (* adr of 1024 x 768 pixel, 4-bit colour display frame *)
	  MemLimM = 0E7EF0H; stackOrgM = 80000H;
	  MemLimC = 09FEF0H; stackOrgC = 60000H;

The coulour map has 16 entries, and can be manipulated at addresses {-68..-128}, for 1-bit colour only these two are in effect {-68,-72}

**Colour Map**
  
	PROCEDURE Map*;
	  CONST ms = -128; me = -68;
	  VAR a, n: INTEGER;
	BEGIN
	  FOR a := ms TO me BY 4 DO
	    SYSTEM.GET(a, n); O.Hex(n); O.Ln
	  END
	END Map;

**The broad band**

	PROCEDURE Band*;
	  CONST X = 30; Y = 80; W = 30; H = 20;
	  VAR x, y, c: INTEGER;
	BEGIN c := 0;
	  FOR x := X TO 500 + X BY 30 DO
	    c := c MOD 10H;
	    Display.ReplConst(c, x, Y, W, H, Display.replace); INC(c)
	  END;
	  FOR x := X TO 500 + X BY 30 DO
	    c := c MOD 10H;
	    Display.ReplConst(c, x, Y+20, W, H, Display.replace);
	    Display.ReplPattern(c, Display.grey, x, Y+20, W, H, Display.paint); INC(c)
	  END;
	  FOR x := X TO 500 + X BY 30 DO
	    c := c MOD 10H;
	    Display.ReplConst(c, x, Y+40, W, H, Display.replace); INC(c);
	    Display.ReplPattern(c, Display.grey, x, Y+40, W, H, Display.paint)
	  END
	END Band;

**Clock ticks**

This version advances the clock, you may inspect by clicking System.Date. The clock is not very accurate +/- 16 secs a day. But may be synced by TIM message from RPI server.
Please see other repository here.
Have fun!

**:-)**

![RGB](Oberon-Colour.png?raw=true "Band")
