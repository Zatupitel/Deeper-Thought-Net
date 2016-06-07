#Deeper Thought Net
Deeper Thought Net is a fork of Deeper Thought 2 by David Eilering. It is currently a very very unpolished proof of concept.

It will be used to integrate the PIDP-8 into my home automation system. It will probably mainly run in mode ***111***, but if need be
it will display a scrolling message as long as it is not confirmed by on of the push switches.

The fork adds a network server that accepts commands to controll the leds and the behavior of the program and reports the switch state.

After starting the program (see below) it will accept one network connection on port 8888. You can use telnet to connect.

The moment you connect all leds will go blank, the function of the mode switches are disabled and the **Current Address** led will indicate that there is a connected client.

The following commands are accepted:
* ***mode***: reports the current mode
* ***mode &lt;mode&gt;***: sets the mode to &lt;mode&gt;
* ***mode saved***: sets the mode to the mode that was active directly before the client connected
* ***mode switches***: sets the mode to the mode indicated by the mode switches
* ***flash &lt;time&gt;***: flashes all leds for &lt;time&gt; * 1/10 seconds on
* ***regClear &lt;name&gt;***: clear the named register
* ***regGet &lt;name&gt;***: gets the named register
* ***regSet &lt;name&gt; &lt;value&gt;***: sets the named register to &lt;value&gt;
* ***text &lt;the text to be displayed&gt;***: switches to the text scroller (mode 100) and displays the given text
* ***quit***: close client connection
* ***exit***: end program

If the state of any of the 6 push switches from START to STOP changes this will be reported back to the connected client.

##The following original description still applies

A program that blinks the LEDs on the [PiDP-8 kit](http://obsolescence.wix.com/obsolescence#!pidp-8/cbie) created by Oscar Vermeulen. This program is C++ and does not run in the PDP-8 simulator. The default mode is designed to be similar to Steve Gibson's Deep Thought program for the PDP-8.

Deeper Thought 2 is a fork of [Deeper Thought by Norman Davie](https://groups.google.com/d/msg/pidp-8/tbciVNoZJbw/AMjywRKLAwAJ).  The file gpio.c and gpio.h were copied from Oscar Vermeulen's PDP-8/simH project.

#####Added modes by changing the 3 far left brown switches (0=down / 1=up)
* **111** = Normal mode with all LEDs flashing (Default / Undefined fallback)
* **011** = Sleep Mode (All LEDs off except for the columns on the right side of the panel)
* **101** = Dim Mode - Fewer LEDs Blink (Only the Program Counter, Memory Address and Memory Buffer groups)
* **110** = Binary Clock (From top to bottom: Hour, Minute, Second, Month, Day)
* **001** = Snake Mode (3 LEDs move across a row then down to the next row in the opposite direction)
* **000** = Test Mode (All LEDs on steady, except some of the columns of LEDs on the right blink off for 20ms)
* **010** = Pong Mode / Bouncing ball - by Norman Davie
* **100** = Text scroller

#####Expanded the timing switches from 6 to 12 switches
* The third brown and third white switch groups from the left control the maximum delay (slowest speed)
   * Up   = More delay (slower)
   * Down = Less delay (faster)
   * The value from the switches is multiplied by 50,000us (50ms).
* The second brown and second white switch groups from the left control the variability of the timing
   * Up   = More variability
   * Down = Less variability (all down results in steady timing)
   * The varied amount is subtracted from the max delay, making the LEDs change faster.
* The timing range should be the similar to version 1.0, but with more degrees of change
   * The maximum delay switch mask value has 1 added to it to prevent a delay of 0, which crashed the program.
   * The delay range (before variability) is 50ms to 3200ms

#####Changed the behavior of the LED columns on the right side of the panel
* All LEDs in the left column blink randomly.
  * Some of these LEDs are programmed to flash more often than others (see the rand_flag function).
* The left column of LEDs are turned off for 20ms at the end of each cycle.
  * This gives the left column a short blink even if that LED stays on in the next cycle.
  * The 20ms delay is subtracted from the main blink delay to keep the same timing.
* Instead of toggling the execute LED, it blinks for 20ms.
  * It is turned on at the beginning of the cycle and turned off before the 20ms delay described above.

#####Changed the stop switch so that it must be held for 3 seconds to quit the program

#####Added new command sequences:
* Shutdown system - Flip both the Sing Inst and Sing Step switches down and hold the Stop button for 3 seconds
* Reboot system - Flip both the Sing Inst and Sing Step switches down and hold the Start button for 3 seconds

#####Misc Notes:
* Added console output that shows switch values when the switches change.
* The blink delay is fixed to 1/2 second in Binary Clock mode.
* This should not be run simultaneously with the pidp8 simulator

#####Installation
* To install run "sudo ./install_deeper.sh" in the deeper directory (also builds)
* The install script enables auto-start and disables auto-start for the pidp8 simulator
  * To install without enabling auto-start, add the "--no-autostart" parameter
  * To later disable auto-start and restore the pidp8 simulator auto-start, add the "--restore-pidp8" parameter
* To just build run "make" in the deeper directory.

#####Running Deeper Thought 2 (if installed with the install script)
* Stop the pidp8 simulator before running this (sudo /etc/init.d/pidp8 stop)
* To run as a daemon in the background:
  * sudo /etc/init.d/deeper {start|stop|restart|status}
* To run in the terminal window run:
  * sudo /usr/bin/deeper
