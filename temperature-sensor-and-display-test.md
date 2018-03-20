# Temperature Sensor and Display Test

---

#TODO

---

Connect the Arduino board to the computer and upload the program.

Watch the display. **Make sure that the temperature it displays is close to room temperature.**

Grab the black plastic portion of the temperature sensor between two fingers (Being careful not to disconnect any wires or short circuit any exposed leads on the sensor or capacitor). **You should see the temperature rise over time.**

Let go of the temperature sensor. **You should see the temperature drop over time.**


> #### Success::The Temperature Display Works!
>
> If the temperature on the display is a reasonable value and changes, the temperature display is working.
>
> If the display doesn't seem right, double check your code. If you can't figure out the problem, ask a mentor for help.

## Final Note

---

# _TODO Scope Explaination_
Capture of displaying 21 on display with scope. Yellow is SRCLK, Green is SER.

Note that in time order, data is 00000110 01011011
One digit goes first (second display), two goes next (first display)
Data is transmitted LSB first, hence reversal from the LUT values

---

![](/assets/scope_display_21.png)